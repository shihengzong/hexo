---
title: http请求方式
date: 2018-11-01 11:45:14
tags:
---

### 1. Get请求

```bash
func httpGet() {
    resp, err := http.Get("http://www.baidu.com")
    if err != nil {
        // handle error
    }
 
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        // handle error
    }
 
    fmt.Println(string(body))
}
```

### 2. Post请求
#### json形式
```bash
func httpPost() {
    data :=`name:"zhangsan"`
    resp, err := http.Post("http://www.baidu.com",
        "application/x-www-form-urlencoded",
        strings.NewReader(data))
    if err != nil {
        fmt.Println(err)
    }
 
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        // handle error
    }
 
    fmt.Println(string(body))
}
```
##### Tips：使用这个方法的话，第二个参数要设置成”application/x-www-form-urlencoded”，否则post参数无法传递。

#### 表单形式
```bash
func httpPostForm() {
    resp, err := http.PostForm("http://www.baidu.com",
        url.Values{"key": {"Value"}, "id": {"123"}})
 
    if err != nil {
        // handle error
    }
 
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        // handle error
    }
 
    fmt.Println(string(body))
 
}
```

### 2. 复杂的请求
有时需要在请求的时候设置头参数、cookie之类的数据，就可以使用http.Do方法。
```bash
func httpDo() {
    client := &http.Client{}
 
    req, err := http.NewRequest("POST", "http://www.baidu.com", strings.NewReader("name=cjb"))
    if err != nil {
        // handle error
    }
 
    req.Header.Set("Content-Type", "application/x-www-form-urlencoded")
    req.Header.Set("Cookie", "name=anny")
 
    resp, err := client.Do(req)
 
    defer resp.Body.Close()
 
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        // handle error
    }
 
    fmt.Println(string(body))
}
```