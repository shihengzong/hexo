---
title: markdown语法
date: 2018-10-29 13:27:40
tags:
---
##### 1.兼容html
例:

    <table>
        <tr>
            <td>Foo</td>
        </tr>
    </table>
效果为:
<table>
    <tr>
        <td>Foo</td>
    </tr>
</table>

---

##### 2.标题
    --(1).下划线
    This is an H1
    =============

    This is an H2
    -------------
效果为:

This is an H1
=============

This is an H2
-------------

    --(2).1到6个#

    # 这是 H1

    ## 这是 H2

    ###### 这是 H6
效果为:
# 这是 H1

## 这是 H2

###### 这是 H6

---
##### 3.列表
无序列表使用星号、加号或是减号作为列表标记,三种效果相同：

    * Red
    * Green
    * Blue

    + AAA
    + BBB
    + CCC

    - EEE
    - DDD
    - GGG

效果为:
* Red
* Green
* Blue

+ AAA
+ BBB
+ CCC

- EEE
- DDD
- GGG


---
##### 3.缩进
    例:四个空格为缩进两个字符:
        (8个空格)这是一段缩进后的文字

效果为:

        这是一段缩进后的文字

---
##### 4.分割线

    * * *

    ***

    *****

    - - -

    ---------------------------------------

效果相同为:
* * *

##### 5.链接
    例:
    [百度一下](http://www.baidu.com)
效果为:

[百度一下](http://www.baidu.com)

---
##### 6.强调
    例:
    *single asterisks*

    _single underscores_

    **double asterisks**

    __double underscores__

效果为:
*single asterisks*

_single underscores_

**double asterisks**

__double underscores__


---
##### 7.代码

    三个反引号标注,例:
    ```
    package main
    import "fmt"

    func HelloWorld(){
        fmt.Println("hello world")
    }
    ```

效果为:

```
package main
import "fmt"

func HelloWorld(){
    fmt.Println("hello world")
}

```

