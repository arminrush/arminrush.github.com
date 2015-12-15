---
title: NullPointException 堆栈信息丢失
layout: post
category: java
splitor: <!--more-->
---

在生产环境JRE 运行在server 模式下， 从日志上看大量的NullPointException日志打印时，没有堆栈信息输出。查了一下，JIT编译会对某些异常如果大量的抛出时，会进行优化，删除堆栈信息。

<!--more-->

通过下面的代码可以重现问题

``` java

public class NullPointExceptionCountMain {
        public static void main(String[] args) {
            int i = 1;
            while (i <= 200000) {
                try {
                    Long l = null;
                    l.toString();
                } catch (Exception e) {
                    if (e.getStackTrace().length == 0) {
                        System.out.println("count is " + i);
                        break;
                    }
                }
                i++;
            }
        }
}

```

client 模式下运行，异常堆栈正常

```
java -classpath . NullPointExceptionCountMain
```

server 模式下运行，问题就重现出来。

```
java -server -classpath . NullPointExceptionCountMain
```


解决方案：

 * -XX:-OmitStackTraceInFastThrow 关闭异常堆栈优化

    ```
        java -XX:-OmitStackTraceInFastThrow -classpath . NullPointExceptionCountMain
    ```

 * -Xint 以解释模式执行

    ``` 
        java -Xint -classpath . NullPointExceptionCountMain
    ```

