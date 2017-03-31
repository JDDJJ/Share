# 注解原理

> 注解 Annotation 又叫元数据



@Override @SuppressWarnings 是存在于源文件阶段 source 编译成.class时就不存在了 但是能让编译器提示 即在编源码阶段 编译器会提示

@Documented @Deprecated 是运行时 Runtime阶段 仍会存在的

而RetentionPolicy.Class 是在编译成.class文件时仍存在 但运行时不存在



## 自定义用法

### 定义

```java
@Documented//生成文档表示
@Retention(RetentionPolicy.RUNTIME)//存在于哪个阶段的表示
@Target(value={CONSTRUCTOR, FIELD, LOCAL_VARIABLE, METHOD, PACKAGE, PARAMETER, TYPE})//可标注的目标
public @interface Hi {//用@interface声明一个自定义注解 
    //String name() default "Str";//表示需要的参数 以 类型 参数名（）default 默认参数值声明
    //int value() default 0; //而value是默认参数名 即当用注解标注时 写入参数可以不以（name=“”）的形式可以直接（“”）写入值 
  String value() default "Str";
}
```

### 使用

```java
public class A {
    @Hi("test")
    String str;
   

    public static void main(String[] args) {
        A a=new A();
        System.out.println(a.str);
    }
}
```

**编译后的.class文件**

```java
public class A {
    @Hi("test")
    String str;

    public A() {
    }

    public static void main(String[] args) {
        A a = new A();
        System.out.println(a.str);
    }
}
```

**执行结果**

```java
null
```

**至此，只是给当前变量或者方法或者其他 注解 或者注释了这个参数 如果传入参数 则这个注解也有了参数 或者default参数 **

**但是，我们的结果是 null也就是说我们的变量是没有赋值的 我们的注解是有值的 这种情况下我们就要用到反射**

注：所有注释 // or ／**／在.class 文件中是不存在的

### 结合反射

- A.java

```java
public class A {
    @Hi("test")
    String str;

    public static void main(String[] args) {
        A a = new A();
        Class<A> aClass = (Class<A>) a.getClass();//得到了.class对象
        Field[] fields=aClass.getDeclaredFields();
        for (Field field : fields) {
            Hi hi = field.getAnnotation(Hi.class);//这里是运行时执行的代码 之前定义的Hi.java 经过编译也成为了Hi.class对象 得到的是**被Hi注解注释的变量的注解对象**
            if (hi != null) {
                field.setAccessible(true);//可更改
                try {
                    field.set(a,hi.value());//set(Object obj, Object value) 向obj对象的这个Field设置新值value
                } catch (IllegalAccessException e) {
                    e.printStackTrace();
                }
            }
        }
        System.out.println(a.str);
    }
}
```



- A.class

```java
public class A {
    @Hi("test")
    String str;

    public A() {
    }
    public static void main(String[] args) {
        A a = new A();
        Class aClass = a.getClass();
        Field[] fields = aClass.getDeclaredFields();
        Field[] var4 = fields;
        int var5 = fields.length;

        for(int var6 = 0; var6 < var5; ++var6) {
            Field field = var4[var6];
            Hi hi = (Hi)field.getAnnotation(Hi.class);
            if(hi != null) {
                field.setAccessible(true);

                try {
                    field.set(a, hi.value());
                } catch (IllegalAccessException var10) {
                    var10.printStackTrace();
                }
            }
        }

        System.out.println(a.str);
    }
}
```

#### 输出结果

```java
test
```

### 小结

这里就有了结果 通过反射将注解的值复制给了比变量 是在运行时 通过反射机制 “**或者说动态加载的 生成的对象**” 这句话理解有误 这里并没有newInstance 而是通过已有类的实例 通过反射机制 知道了他自己内部的Field 在动态改变值 从而实现了动态加载 而注释的那句话也有他存在的一些场景

>问题来了 那如果不单单A类要用Hi注解 动态加载值 还有其他类要用 那这里我们就该将注入值的那部分代码模块化

### 注解反射模块化（注入模块）

- 注入模块 注解通过反射动态加载模块

```java
public class InjectHi {
  
    public  static void inject(Object clz){
        Class clazz =  clz.getClass();//得到了.class对象
        Field[] fields=clazz.getDeclaredFields();
        for (Field field : fields) {
            Hi hi = field.getAnnotation(Hi.class);//这里是运行时执行的代码 之前定义的Hi.java 经过编译也成为了Hi.class对象 得到的是注解对象
            if (hi != null) {
                field.setAccessible(true);//可更改
                try {
                    field.set(clz,hi.value());//set(Object obj, Object value) 向obj对象的这个Field设置新值value obj 应该时类对象 instance 不是class对象
                } catch (IllegalAccessException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

- 使用

```java
public class A {
    @Hi("test")
    String str;

    public A() {
      InjectHi.inject(this);
    }

    public static void main(String[] args) {
        A a = new A();
        B b = new B();
        System.out.println(a.str);
        System.out.println(b.str);
    }
}

```

```java
public class B extends A {
  //这里由于B 继承了A 所以不用在调注入方法 注解利用反射机制 动态加载 
  //java中继承 先执行子类构造方法 在执行父类构造方法 如果子类的构造方法中没有显示的调用基类的构造方法，则		系统默认调用基类的无参数构造方法。 
  //子类可以在自己的构造方法中使用super(argument_list）调用基类的构造方法。

  //　2.1、使用this(argument_list)调用本类的另外构造方法。

 　//2.2、如果调用super，必须写在子类构造方法的第一行。
    @Hi("this is b.str")
    String str;
}
```

#### 输出结果

```java
test
this is b.str
```

