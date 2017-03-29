> Enums often require more than twice as much memory as static constants. You should strictly avoid using enums on Android

但是在最新Android VM官博中，已经指出 enum已经优化 虽然还是比静态要耗费性能 enum 所带来的内存增加已经可以忽略。

**作用：用来存放一组固定的常量**                

它是用来解决用户进行多次if /switch重复简单的判断 是值类型

```java
public enum 枚举名
 {
     
 }
```
## 好处

1. 便于程序员回忆以前的资料
2. 便于少输入代码，使程序更容易编写和维护
3. 防止乱输入
## 应用场景

1. 有一个`任务`(Task)，服务端定义了一个`int status`来标记任务的状态，包括 `未开始`、`进行中`、`已完成` 这三个状态，分别用 0、1、2 来标记。我们拿到这个`status`之后，需要根据状态的不同，显示不同的文本。

   [例子](http://blog.csdn.net/my_truelove/article/details/52074493)

2. [换肤例子](http://www.cnblogs.com/tonney/archive/2011/04/28/2032270.html)

> public static int 的区别

1. 只可以是一种类型 不可关联多种类型
2. 同样会提示 并且多出了可以自定义 比如setColor（Color.BLACK）除了已经包含的自定义的Color 还可以输入 setColor(Color.parseColor("#FFFFFFF"))

**总结**

所以要取舍 如果需要关联多种 并且尽量同一类型更清晰 而且 只有确定的一组数据 避免逻辑泄漏 输入其他的数据则用枚举 否则 用static final即可 

## 优化方案

- 在 Android Proguard 中，可以在 proguard.cfg 中加入参数 `-Doptimization class/unboxing/enum` ，从而自动将 enum 替换为 static final int。这样，也就无需担心多余的内存问题了。
- 如果只是单纯的int或者String 使用 IntDef 注解替代 int或者StringDef