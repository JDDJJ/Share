#依赖注入
##设计模式六大原则设计

- 模式六大原则（1）：单一职责原则

- 设计模式六大原则（2）：里氏替换原则

- 设计模式六大原则（3）：依赖倒置原则

- 设计模式六大原则（4）：接口隔离原则

- 设计模式六大原则（5）：迪米特法则

- 设计模式六大原则（6）：开闭原则

** 今天只分享依赖注入 依赖倒置 或者叫控制反转（Inversion of Control，英文缩写为IoC 中的控制反转（Inversion of Control， 控制反转一般分为两种类型，依赖注入（Dependency Injection，简称DI）和依赖查找（Dependency Lookup）。依赖注入应用比较广泛**

IoC容器有：Pico Container、Avalon 、Spring、JBoss、HiveMind、EJB dagger2
JS:require 
```
//依赖是什么
A中用到了B实例 A依赖B
class A{
  public A(){
    B b=new B();
  }
}

class B{
  
}
```

> 为什么要依赖注入 依赖注入的3种模式 构造器注入 setter 注入 （懒注入）接口注入

上述会造成高耦合 

- 假如有一天B的构造方法变了 所有依赖于它的地方都会改变

- 还有另一种需求 假如A是角色 B是武器 而武器应该是可以更换的 

  ```
  class A{
    public A(){
      B b=new B();
    }
  }
  interface Weapon{
    void attack（）
  }
  class Weapon1 implement Weapon{
    void attack(){
      print("Weapon1 Attack")
    }
    
  }
  //
  class A{
    public A(Weapon w){
      w.attack();//这里传入的不是接口 是实现了该接口的对象
    }
  }

public static void main(String[] args){
		A a = new A(new Weapon1());
	        A a2=new A(new Weapon2())//而工厂模式 可是把生产Weapon的过程再接偶 相辅相成
	
	      
	}
        public static void main(String[] args){
		A a = new A(WeaponFactory.getWeapon("Weapon1"));
	        A a2 = new A(WeaponFactory.getWeapon("Weapon2"));
//而工厂模式 可是把生产Weapon的过程再接偶 相辅相成
	
	      
	}

  ```

  ​

  ​