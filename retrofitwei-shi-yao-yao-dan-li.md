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

