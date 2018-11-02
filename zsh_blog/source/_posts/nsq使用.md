---
title: nsq使用
date: 2018-11-01 11:45:14
tags:
---

### 安装
[下载](https://nsq.io/deployment/installing.html)解压
解压出来的bin目录地址添加到环境变量

### 在golang中使用
##### 启动nsq:
运行Nsq服务集群
首先启动nsqlookud(相当于一个nsqd的容器)
```bash
nsqlookupd (nsqd可以通过"127.0.0.1:4160"接入, nsqadmin可以通过"127.0.0.1:4161"接入)
```
启动nsqd，并接入刚刚启动的nsqlookud。这里为了方便接下来的测试，启动了两个nsqd
```bash
nsqd --lookupd-tcp-address=127.0.0.1:4160 ("4160"是nsqlookup的默认端口)
nsqd --lookupd-tcp-address=127.0.0.1:4160 -tcp-address=0.0.0.0:4152 -http-address=0.0.0.0:4153
```
启动nqsadmin,并接入刚刚启动的nsqlookud.
nsqadmin --lookupd-http-address=127.0.0.1:4161



##### 生产端:
```bash
func main(){
    add := "127.0.0.1:8080"
    InitProducer(add)
    data:= &struct{
        name:"张三",
        sex:"男",
    }{}
    strData, _ := json.Marshal(&data)    //把结构体转成json字符串
    Public("topic",data) 
}

// 初始化生产者
func InitProducer(add string) {
    var err error
    fmt.Println("address: ", add)
    producer, err = nsq.NewProducer(add, nsq.NewConfig())
    if err != nil {
     panic(err)
    }
}

//发布消息
func Publish(topic string, message string) error {
    var err error
    if producer != nil {
    if message == "" { //不能发布空串，否则会导致error
        return nil
    }
    err = producer.Publish(topic, []byte(message)) // 发布消息
        return err
    }
    return fmt.Errorf("producer is nil", err)
}
```

##### 消费端(另一个项目):
```bash
// 消费者
type ConsumerT struct{}

// 主函数
func main() {
    InitConsumer("test", "test-channel", "127.0.0.1:4161")
    for {
        time.Sleep(time.Second * 10)
    }
}

//处理消息
func (*ConsumerT) HandleMessage(msg *nsq.Message) error {
    fmt.Println("receive", msg.NSQDAddress, "message:", string(msg.Body))
    req:= &struct{
        name string
        sex string
    }{}
    json.Unmarshal(msg.Body, &req)
    return nil
}

//初始化消费者
func InitConsumer(topic string, channel string, address string) {
    cfg := nsq.NewConfig()
    cfg.LookupdPollInterval = time.Second          //设置重连时间
    c, err := nsq.NewConsumer(topic, channel, cfg) // 新建一个消费者
    if err != nil {
        panic(err)
    }
    c.SetLogger(nil, 0)        //屏蔽系统日志
    c.AddHandler(&ConsumerT{}) // 添加消费者接口

    //建立NSQLookupd连接("NSQLookupd"连接以后,就可以通过"4161"端口对产看ui页面)
    if err := c.ConnectToNSQLookupd(address); err != nil {
        panic(err)
    }
    //建立nsqd连接(可以多个, 接收生产端的消息) 
    if err := c.ConnectToNSQDs([]string{"127.0.0.1:8080"}); err != nil {
      panic(err)
    }
}
 ```