# 注解

## 功能

- 生成文档。这是最常见的，也是java 最早提供的注解。常用的有@see @param @return 等
- 跟踪代码依赖性，实现替代配置文件功能。比较常见的是spring 2.5 开始的基于注解配置。作用就是减少配置。现在的框架基本都 使用了这种配置来减少配置文件的数量
- 在编译时进行格式检查。如@override 放在方法前，如果你这个方法并不是覆盖了超类方法，则编译时就能检查 出

## Java常用注解

- @Deprecated

  标记性注解 表示即将弃用 尽量不要用

- @ Override

  编译时检查 注释某一个方法重写了父类的方法 建议使用这个去修饰 因为某些情况假如你修改了父类的方法 子类会提示 不用@override 则不会提示

- @ SuppressWarnings

  当我们调用了一个被弃用的方法或者进行不安全的类型转换时，编译器会警告，可以用这个注解来抑制警告 可以修饰的元素为类，方法，方法参数，属性，局部变量 但需要采用就近原则 以避免其他警告未被发现

  ```java
   @SuppressWarnings(value={"deprecation"})
      public void waning(){
      }
  ```


## 修饰的注解类型 API

| 注解类型       | 含义                                      |
| ---------- | --------------------------------------- |
| Documented | 表示含有该注解类型的元素(带有注释的)会通过javadoc或类似工具进行文档化 |
| Inherited  | 表示注解类型能被自动继承                            |
| Retention  | 表示注解类型的存活时长                             |
| Target     | 表示注解类型所适用的程序元素的种类                       |

## Retention

@Retention：表示该注解类型的注解保留的时长。当注解类型声明中没有@Retention元注解，则默认保留策略为RetentionPolicy.CLASS。关于保留策略(RetentionPolicy)是枚举类型，共定义3种保留方式

| name    | exp                                      |
| ------- | ---------------------------------------- |
| SOURCE  | 仅存在Java源文件，经过编译器后便丢弃相应的注解                |
| CLASS   | 存在Java源文件，以及经编译器后生成的Class字节码文件，但在运行时VM不再保留注释 |
| RUNTIME | 存在源文件、编译生成的Class字节码文件，以及保留在运行时VM中，可通过反射性地读取注解 |

示例代码：

```
@Documented
@Inherited
@Retention(RetentionPolicy.RUNTIME)
public @interface Ety {
    @Deprecated
    String value();
    @Deprecated
    String name();
}
1234567891012345678910
```

例如，上面源码@Retention的定义中有一行@Retention(RetentionPolicy.RUNTIME)，意思是指当前注解的保留策略为RUNTIME，即存在Java源文件，也存在经过编译器编译后的生成的Class字节码文件，同时在运行时虚拟机(VM)中也保留该注解，可通过反射机制获取当前注解内容

## Target

表示该注解类型的所使用的程序元素类型。当注解类型声明中没有@Target元注解，则默认为可适用所有的程序元素。如果存在指定的@Target元注解，则编译器强制实施相应的使用限制。关于程序元素(ElementType)是枚举类型，共定义8种程序元素

| ElementType     | exp               |
| --------------- | ----------------- |
| TYPE            | 类、接口（包括注解类型）或枚举声明 |
| CONSTRUCTOR     | 构造方法声明            |
| PACKAGE         | 包声明               |
| LOCAL_VARIABLE  | 局部变量声明            |
| METHOD          | 方法声明              |
| ANNOTATION_TYPE | 注解类型声明            |
| PARAMETER       | 参数声明              |
| FIELD           | 字段声明（包括枚举常量）      |

------

### 代码如下

```
@Documented
@Inherited
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD})
public @interface Ety {
    @Deprecated
    String value();
    @Deprecated
    String name();
}
```

## Android注解

### 环境

在使用Android注解前 需要引入依赖包

```groovy
compile 'com.android.support:support-annotations:latest.integration'
```

注意：如果我们已经引入了appcompat则没有必要再次引用support-annotations,因为appcompat默认包含了对其引用

### 使用

## IntDef和StringDef注解

> 代替枚举 为什么需要枚举 为什么枚举耗内存 逻辑泄漏是什么  

**枚举的作用就是能表示一组类型相同的常量，是值类型**

这里我们采用假设一个问题然后一步步解决学习：假设有一个User对象，我们需要记录user类型的变量，如何实现呢？

#### 方案一

```java
public class UserI {
    public static  int childe=0x1;
    public static  int man=0x2;
    public static  int girl=0x3;


    private int userType;

    public int getUserType() {
        return userType;
    }

    public void setUserType(int userType) {
        this.userType = userType;
    }
}
```

估计大家常用的就是这样的方式去解决这样的需求，但是上述实现存在一个问题：`setUserType`设置的是一个`int`类型的变量，既然如此我们可以如此调用：

```java
UserI userI=new UserI();
    /*正确调用*/
    userI.setUserType(userI.childe);

    /*错误调用*/
    userI.setUserType(100);
```

错误方式下的调用也不会抛出异常，所以这样的实现方式存在逻辑泄漏的危险！



#### 方案二

```java
public class UserE {

    private UserEmun userType;

    public UserEmun getUserType() {
        return userType;
    }

    public void setUserType(UserEmun userType) {
        this.userType = userType;
    }

    public static enum UserEmun {
        childe,
        man,
        girl
    }
}
```

调用：

```java
UserE userE=new UserE();
userE.setUserType(UserE.UserEmun.childe);
```

#### 注解方案

```java
public class UserInter {
    public static final int childe = 0x1;
    public static final int man = 0x2;
    public static final int girl = 0x3;
    public static final int other = 0x4;

    @IntDef({childe, man, girl})
    @Retention(RetentionPolicy.SOURCE)//表示长时间存活
    public @interface UserInters{}//@interface 是自定义注解

    private int userType;

    @UserInters
    public int getUserType() {
        return userType;
    }

    public void setUserType(@UserInters int userType) {
        this.userType = userType;
    }
}
```

结果：

```java
  UserInter userInter=new UserInter();
  userInter.setUserType(UserInter.childe);

  userInter.setUserType(UserInter.other);//这里会报错
```



## 资源类型注解

常用的资源注解：

| name            | exp            |
| --------------- | -------------- |
| AnimRes         | 动画             |
| AnimatorRes     | animator资源类型   |
| AnyRes          | 任何资源类型         |
| ArrayRes        | 数组资源类型         |
| AttrRes         | 属性资源类型         |
| BoolRes         | bool类型资源类型     |
| ColorRes        | 颜色资源类型         |
| DimenRes        | 长度资源类型         |
| DrawableRes     | 图片资源类型         |
| IdRes           | 资源id           |
| InterpolatorRes | 动画插值器          |
| LayoutRes       | layout资源       |
| MenuRes         | menu资源         |
| RawRes          | raw资源          |
| StringRes       | 字符串资源          |
| StyleRes        | style资源        |
| StyleableRes    | Styleable资源类型  |
| TransitionRes   | transition资源类型 |
| XmlRes          | xml资源          |

资源注解是为了防止我们在使用程序资源的时候，错误传递资源类型给函数，导致程序错误！

## Threading 注解

thread注解是帮助我们指定方法在特定线程中执行处理，如果和指定的线程不一致，抛出异常；Threading 注解类型：

- @UiThread： UI线程
- @MainThread ：主线程
- @WorkerThread： 子线程
- @BinderThread ： 绑定线程

## 其他实用注解

- @CallSuper 子类覆盖被此注解修饰的方法时 必须调用父类的super.该方法
- @CheckResult 一个方法有返回值 而当该方法被使用时，若该方法被此注解修饰 则必须要定义一个变量接收此返回值
- @Size @Size(min = 1,max = 2)String s 定义长度
- @IntRange 如@IntRange(from = 1,to = 100)int 定义区间
- @FloatRange同上