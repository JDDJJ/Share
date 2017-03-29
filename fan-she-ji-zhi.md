> 反射是什么

> 静态编译：在编译时确定类型，绑定对象,即通过。 
> 动态编译：运行时确定类型，绑定对象。动态编译最大限度发挥了java的灵活性，体现了多态的应用，有以降低类之间的藕合性。 为什么要有反射

```java
Class c = Class.forName("java.lang.String");
Method m[] = c.getDeclaredMethods();
System.out.println(m[0].toString());
```

## 功能

- 在运行时判断任意一个对象所属的类；
- 在运行时构造任意一个类的对象；
- 在运行时判断任意一个类所具有的成员变量和方法；
- 在运行时调用任意一个对象的方法。

当一个类被加载以后，Java 虚拟机就会在内存中自动产生一个 Class 对象。Java中，无论生成某个类的多少个对象，这些对象都会对应于同一个Class对象，这个Class对象是由JVM生成的，通过它能够获悉整个类的结构。我们通过 new 构造函数的形式创建对象实际上也是通过这些 Class 来创建。那么，我们在代码中如何获得Class对象呢？通常有三个方法，如下所示：



```
/** 
 * 获取Class对象的三种方式 
 */  
public static Class<?> getClassObj() {  
    // 根据类名获取Class对象  
    Class<?> clazz1 = People.class;  
  
    // 根据对象获取Class对象  
    People people = new People();  
    Class<?> clazz2 = people.getClass();  
  
    // 根据完整类名获取Class对象  
    try {  
        Class<?> clazz3 = Class.forName("com.yuyh.reflection.java.People");  
    } catch (ClassNotFoundException e) {  
        Log.e(TAG, e.toString());  
    }  
  
    Log.i(TAG, "clazz1 = " + clazz1);  
  
    return clazz1; // clazz2 clazz3  
}  
```



##原理
- 静态加载类 是在编码阶段就知道要加载什么样的类 什么样的对象
![](/assets/43918004_1)

- 动态加载类 根据运行时的状态 动态的生成类的对象
![](/assets/43918004_2)

##应用场景
- 结合注解框架 eventbus retrofit butterknife 
- 动态代理
- spring hibernate等框架 
**举例：**


```
@Documented  
@Retention(RetentionPolicy.RUNTIME)  
@Target(ElementType.FIELD)  
public @interface InjectView {  
  
    int value() default 0;  
}  
```


```

package com.yuyh.reflection.annotation;  
  
import android.app.Activity;  
import android.util.Log;  
  
import java.lang.reflect.Field;  
  
/** 
 * @author yuyh. 
 * @date 2016/6/13. 
 */  
public class Inject {  
    public static final String TAG = "Reflection";  
  
    public static void inject(Activity activity) {  
        getAnnotationInfos(activity);  
    }  
  
    private static void getAnnotationInfos(Activity activity) {  
        Class clazz = activity.getClass();  
        Log.i(TAG, clazz.getName());  
  
        Field[] fields = clazz.getFields();  
        for (Field field : fields) {  
            InjectView injectView = field.getAnnotation(InjectView.class);  
            if (injectView != null) {  
                int id = injectView.value();  
                try {  
                    field.setAccessible(true);  
                    field.set(activity, activity.findViewById(id));  
                } catch (IllegalAccessException e) {  
                    Log.e(TAG, "IllegalAccessException = " + e.toString());  
                }  
            }  
        }  
    }  
}  

```




```
public class MainActivity extends AppCompatActivity {  
  
    @InjectView(R.id.hello)  
    TextView tvHello;  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        Inject.inject(this); // 初始化注解  
  
        tvHello.setText("hahaha");  
    }  
}  
```






> 反射为什么慢

反射是一种强大的工具，但也存在一些不足。一个主要的缺点是对性能有影响。使用反射基本上是一种解释操作，我们可以告诉JVM，我们希望做什么并且它满足我们的要求。这类操作总是慢于只直接执行相同的操作

> OC Python等面向对象语言中同样存在反射

- OC中运行时编程 runtime programming   也可以叫反射 [Objective-C](http://lib.csdn.net/base/objective-c)能动态的给class添加类和方法，Java 则不行

  ​
![](/assets/43918004_1)

![](/assets/43918004_2)