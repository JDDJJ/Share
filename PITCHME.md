## QuickAdapter

1. 创建Holder 继承于BaseHolder<T> 并且设置BaseHolder的类型 T 创建的Holder不要用泛型修饰

2. 创建 Adapter 继承于QiuckAdapter<T> 同样是QuickAdapter设置参数类型 T

   比如 QuickAdapter<User> 或者QuickAdapter<String>

3. 在新建的Adapter中重写和实现QuickAdapter中的一些方法 

   1. 构造方法

   2. ```java
      public BaseHolder<String> getHolder(View v)//返回第一步创建的Holder
      ```

   3. ```java
      public int getLayoutId() {return R.layout.text_blank;}//设置viewholder的对应的布局文件
      ```

#### 使用

- ```java
  mRecyclerView.setAdapter(new StringAdapter(data));//其中data就是为Holder和Adapter设置的泛型T类型的List
  ```



## MultiItemAdapter

1. 创建Holder的过程是一样的 但是要创建多个对应的Holder 因为要的是混合布局

2. 创建Adapter继承于MultiItemAdapter<T>并为其设置泛型  一般情况下 传入的数据Bean是一样的 只是由于一些status不同 从而改变相应布局 比如MultiItemAdapter<Product> 其中Product有进行中，已完成，和未开始等

3. 重写和实现MultiItemAdapter中的一些方法

   1. 构造方法 

      ```java
      public MultiAdapter(List<Map> data) {
          super(data);
         addItemType(1,R.layout.text_blank);
         addItemType(2,R.layout.text_blank);
         addItemType(3,R.layout.text_blank);
      }
      ```

      要在构造方法中，执行 addItemType方法 将类型值以及对应的layoutId传入 

      ```java
      protected  void addItemType(int viewType, int layoutResId)
      ```

   2. 实例化对应Holder

      ```java
      public BaseHolder<Map> getHolder(int viewType, View v) {
          BaseHolder holder = null;
          switch (viewType) {

              case 1:
                  holder = new StringHolder(v);
                  break;
              case 2:
                  holder = new StringHolder2(v);
                  break;
              case 3:
                  holder = new StringHolder3(v);
                  break;
          }
          return holder;
      }
      ```

      根据对应viewType，返回对应实例化ViewHolder

      **PS：viewType一般会设置为public static final的静态常量**

   3. 设置相应recyclerviewItem的类型

      ```java
      @Override
          public int getItemViewType(int position) {
              String value = mData.get(position).get("key").toString();

              return Integer.valueOf(value);
          }
      ```

      这里可以通过全局的**mData** 去和viewType去对比 判断从而设置viewType 这个mData是父类 MultiItemAdapter持有的相关类型列表的对象

### 坑

1. 注意 不论是混合布局 还是Single布局 Adapter与 Holder的类型<T>要一致 否则会在类型不匹配
2. 空指针 getLayoutId 报空指针 是 add viewType和getviewType不匹配  查看一下设置viewType和addviewType的类型一致否
