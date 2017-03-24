> 静态和单例的区别
从面向对象的角度上来说，在抉择使用实例化方法或静态方法时，应该根据是否该方法和实例化对象具有逻辑上的相关性，如果是就应该使用实例化对象 反之使用静态方法。这只是从面向对象角度上来说的
##静态
- 静态方法不能访问外部非静态成员
##单例

##单例解析

```
 public static RetrofitUtil getInstance(){
        if (mInstance == null){
            synchronized (RetrofitUtil.class){
                mInstance = new RetrofitUtil();//这里只是将创建retrofit的方法锁住 以防创建多个retrofit 但是使用mInstance去进行各种
                                               //网络请求 是异步的 可以存在多个的 并没有将retrofit请求网络的方法锁住
            }
        }
        return mInstance;
    }
```

