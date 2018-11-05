---
title: etcd服务发现
date: 2018-11-01 11:45:14
tags:
---
### 什么是服务发现
服务发现:一个服务就是一个功能,输入一个域名,DNS服务器会根据我们的域名解析出一个ip地址，返回ip地址中对应链接包含的内容。我们根据域名 来获取我们所需要的服务，这就是服务发现
例: 
用户输入:
192.168.1.105:8080/

DNS服务器:
解析出这个服务在 ip 为 "192.168.1.105"的服务器上

这台服务器上有n个服务   
/         #首页
/login    #登录
/regist   #注册
...

通过域名 :8080/  找到首页服务 并在浏览器上显示


### 实现原理
1.服务启动时向etcd注册一个地址
2.etcd保持时刻监听
3.监听到服务断开的时候立刻释放


### 代码实现
#### 服务注册

```bash
package discovery

import (
    "github.com/coreos/etcd/clientv3"
    "context"
    "time"
    "log"
    "encoding/json"
    "errors"
)

//the detail of service 
type ServiceInfo struct{
    IP   string
}

type Service struct {
    Name        string
    Info        ServiceInfo
    stop        chan error
    leaseid     clientv3.LeaseID
    client      *clientv3.Client
}

func NewService(name string, info ServiceInfo,endpoints []string) (*Service, error) {
    cli, err := clientv3.New(clientv3.Config{
        Endpoints:    endpoints,
        DialTimeout: 2*time.Second,
    })

    if err != nil {
        log.Fatal(err)
        return nil, err
    }

    return &Service {
        Name:        name,
        Info:        info,
        stop:        make (chan error),
        client:     cli,
    },err
}

func (s *Service)  Start() error {
    
    ch, err := s.keepAlive()
    if err != nil {
        log.Fatal(err)
        return err
    }

    for {
        select {
        case err := <- s.stop:
            s.revoke()
            return err
        case <- s.client.Ctx().Done():
            return errors.New("server closed")
        case ka, ok := <-ch:
            if !ok {
                log.Println("keep alive channel closed")
                s.revoke()
                return nil
            } else {
                log.Printf("Recv reply from service: %s, ttl:%d", s.Name, ka.TTL)
            }
        }
    }
}

func (s *Service) Stop()  {
    s.stop <- nil 
}

func (s *Service) keepAlive() (<-chan *clientv3.LeaseKeepAliveResponse, error){

    info := &s.Info

    key := "services/" + s.Name
    value, _ := json.Marshal(info)

    // minimum lease TTL is 5-second
    resp, err := s.client.Grant(context.TODO(), 5)
    if err != nil {
        log.Fatal(err)
        return nil, err
    }

    _, err = s.client.Put(context.TODO(), key, string(value), clientv3.WithLease(resp.ID))
    if err != nil {
        log.Fatal(err)
        return nil, err
    }
    s.leaseid = resp.ID

    return  s.client.KeepAlive(context.TODO(), resp.ID)
}

func (s *Service) revoke() error {

    _, err := s.client.Revoke(context.TODO(), s.leaseid)
    if err != nil {
        log.Fatal(err)
    }
    log.Printf("servide:%s stop\n", s.Name)
    return err
}
```

### 监听服务
```bashpackage discovery

import (
    "github.com/coreos/etcd/clientv3"
    "context"
    "log"
    "time"
    "encoding/json"
    "fmt"
)

type Master struct {
    Path         string
    Nodes         map[string] *Node
    Client         *clientv3.Client
}

//node is a client 
type Node struct {
    State    bool
    Key        string
    Info    ServiceInfo
}


func NewMaster(endpoints []string, watchPath string) (*Master,error) {
    cli, err := clientv3.New(clientv3.Config{
        Endpoints:    endpoints,
        DialTimeout: time.Second,
    })

    if err != nil {
        log.Fatal(err)
        return nil,err
    }

    master := &Master {
        Path:    watchPath,
        Nodes:    make(map[string]*Node),
        Client: cli,
    }

    go master.WatchNodes()
    return master,err
}

func (m *Master) AddNode(key string,info *ServiceInfo) {
    node := &Node{
        State:    true,
        Key:    key,
        Info:    *info,
    }

    m.Nodes[node.Key] = node
}


func GetServiceInfo(ev *clientv3.Event) *ServiceInfo {
    info := &ServiceInfo{}
    err := json.Unmarshal([]byte(ev.Kv.Value), info)
    if err != nil {
        log.Println(err)
    }
    return info
}

func (m *Master) WatchNodes()  {
    rch := m.Client.Watch(context.Background(), m.Path, clientv3.WithPrefix())
    for wresp := range rch {
        for _, ev := range wresp.Events {
            switch ev.Type {
                case clientv3.EventTypePut:
                    fmt.Printf("[%s] %q : %q\n", ev.Type, ev.Kv.Key, ev.Kv.Value)
                    info := GetServiceInfo(ev)    
                    m.AddNode(string(ev.Kv.Key),info)
                case clientv3.EventTypeDelete:
                    fmt.Printf("[%s] %q : %q\n", ev.Type, ev.Kv.Key, ev.Kv.Value)
                    delete(m.Nodes, string(ev.Kv.Key))
            }
        }
    }
}
```

#### service使用示例，20s后断开
```bash
	serviceName := "s-test"
	serviceInfo := dis.ServiceInfo{IP:"192.168.1.26"}

	s, err := dis.NewService(serviceName, serviceInfo,[]string {
		"http://192.168.1.17:2379",
 		"http://192.168.1.17:2479",
 		"http://192.168.1.17:2579",
	})

	if err != nil {
		log.Fatal(err)
	}

	fmt.Printf("name:%s, ip:%s\n", s.Name, s.Info.IP)


	go func() {
		time.Sleep(time.Second*20)
		s.Stop()
	}()
	
	s.Start()
```

#### master使用示例

```bash
m, err := dis.NewMaster([]string{
    "http://192.168.1.17:2379",
    "http://192.168.1.17:2479",
    "http://192.168.1.17:2579",
}, "services/")

if err != nil {
    log.Fatal(err)
}

for {
    for k, v := range  m.Nodes {
        fmt.Printf("node:%s, ip=%s\n", k, v.Info.IP)
    }
    fmt.Printf("nodes num = %d\n",len(m.Nodes))
    time.Sleep(time.Second * 5)
}

```