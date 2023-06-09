# `Assert`关键字

IDEA中默认`assert`(断言)是关闭，开启方式如下：

简单来说：就是设置一下`jvm`的参数，参数是`-enableassertions`或者`-ea`（推荐）



`assert [boolean 表达式]`

- 如果[boolean表达式]为true，则程序继续执行。
- 如果为false，则程序抛出AssertionError，并终止执行。

`assert [boolean 表达式 : 错误表达式 （日志）]`

- 如果[boolean表达式]为true，则程序继续执行。
- 如果为false，则程序抛出java.lang.AssertionError，输出[错误信息]。

```java
int a = 1;
int b = 2;
assert a > b : "错误，a不大于b";
```



# Character.*isLetterOrDigit*
