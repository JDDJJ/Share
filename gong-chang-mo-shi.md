#工厂模式

##简述
- 当需要一个实例 而这个实例又依赖于其他实例 或者说这个实例的创建过程复杂 

- 当有许多类似的实例 而需要这中实例的地方创建这种实例 会造成高耦合
  以上这些情况 我们为了查得到需要的时候 可以引入工厂模式

  **注： 与依赖注入不同 他要可以生产各种类似实例的模块 而 注入的实例是将A 和B的依赖解耦 **

![简单工厂](http://pic002.cnblogs.com/img/guoshiandroid/201006/2010062300571970.png)

## 例子

```java
/*
 * 产品的抽象接口
 */
public interface Food {
    /*
     * 获得相应的食物
     */
    public void get();
}
```

```java
/*
 * 麦香鸡对抽象产品接口的实现
 */
public class McChicken implements Food{
    /*
     * 获取一份麦香鸡
     */
    public void get(){
        System.out.println("我要一份麦香鸡");
    }
}
```

```java
/*
 * 薯条对抽象产品接口的实现
 */
public class Chips implements Food{
    /*
     * 获取一份薯条
     */
    public void get(){
        System.out.println("我要一份薯条");
    }
}
```

```java
public class FoodFactory {
 
    public static Food getFood(String type) throws InstantiationException, IllegalAccessException, ClassNotFoundException {
        if(type.equalsIgnoreCase("mcchicken")) {
            return McChicken.class.newInstance();
 
        } else if(type.equalsIgnoreCase("chips")) {
            return Chips.class.newInstance();
        } else {
            System.out.println("哎呀！找不到相应的实例化类啦！");
            return null;
        }
 
 
    }
}
```

```java
 public static void main(String[] args) throws InstantiationException, IllegalAccessException, ClassNotFoundException {
 
        //实例化各种食物
        Food mcChicken = FoodFactory.getFood("McChicken");
        Food chips = FoodFactory.getFood("Chips");
        Food eggs = FoodFactory.getFood("Eggs");
 
        //获取食物
        if(mcChicken!=null){
            mcChicken.get();
        }
        if(chips!=null){
            chips.get();
        }
        if(eggs!=null){
            eggs.get();
        }
 
 
    }
```

