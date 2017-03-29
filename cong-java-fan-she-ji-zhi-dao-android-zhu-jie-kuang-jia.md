##结合注解框架 
- field.getAnnotation
- 自定义注解 可以利用反射找到 该注解 解析出注解
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






