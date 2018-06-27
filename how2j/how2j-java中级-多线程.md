多线程
===
多线程就是在同一个时间，可以做多件事情
- 创建线程有3种方式
  - 继承线程类
  - 实现Runnable接口
  - 匿名类
### 线程概念
- #### 进程(Processor)
- #### 线程(Thread)

### 创建多线程-继承线程类

也就是说
  - 写个类继承**Thread**重写**run**方法
  - 调用start()方法的时候就能以线程的方式执行run方法

- 使用多线程，就可以走到盖伦在攻击提莫的**同时**，赏金猎人也在攻击盲僧
- 设计一个类KillThread**继承Thread,并重写run方法**
- 启动线程办法：实例化一个KillThread对象，并调用**start**方法

- KillThread.java & TestThread.java
```java
class KillThread extends Thread {
  private Hero h1;
  private Hero h2;

  public KillThread(Hero h1, Hero h2) {
    this.h1 = h1;
    this.h2 = h2;
  }

  public void run() {
    while(!h2.isDead()) {
      h1.attackHero(h2);
    }
  }
}

public class TestThread {
  public static void main(String[] args) {
    Hero gareen = new Hero();
    gareen.name = "盖伦";
    gareen.hp = 616;
    gareen.damage = 50;

    Hero teemo = new Hero();
    teemo.name = "提莫";
    teemo.hp = 300;
    teemo.damage = 30;

    Hero bh = new Hero();
    bh.name = "赏金猎人";
    bh.hp = 500;
    bh.damage = 65;

    Hero leesin = new Hero();
    lessin.name = "盲僧";
    lessin.hp = 455;
    lessin.damage = 80;

    KillThread killThread1 = new KillThread(gareen, teemo);
    killThread1.start();
    KillThread killThread2 = new KillThread(bh, lessin);
    killThread2.start();
  }
}
```

### 以Runnable接口方式实现多线程
- 创建类Battle,实现Runnable接口
- 启动的时候，首先创建一个Battle对象，然后再根据该battle对象创建一个线程对象，并启动
```java
Battle battle1 = new Battle(gareen, teemo);
new Thrad(battle1).start();
```
- battle1对象实现了Runnable接口，所有有run方法，但是直接调用run方法并不会启动一个新的线程
- 必须借助一个线程对象的start()方法，才会启动一个新的线程。
- 所以，在创建Thread对象的时候，把battle1作为构造方法的参数传递进去
  - 该线程启动的时候，就会去执行battle1.run()方法了。

- Battle.java
```java
public class battle implements Runnable {
  private Hero h1;
  private Hero h2;

  public Battle(Hero h1, Hero h2) {
    this.h1 = h1;
    this.h2 = h2;
  }

  public void run() {
    while(!h2.isDead()) {
      h1.attackHero(h2);
    }
  }
}
```
- TestThread.java
```java
public class TestThread {
  // 创建4个对象

  Battle battle1 = new Battle(gareen, teemo);
  new Thread(battle1).start();

  Battle battle2 = new Battle(bh, leesion);
  new Thread(battle).start();
}
```

### 匿名类方式创建多线程

```java
public class TestThread {
  public static void main(String[] args) {
    // 创建4个英雄类，略
    Thread t1 = new Thread(){
      public void run() {
        while(!leesin.isDead()) {
          bh.attackHero(leesion);
        }
      }
    };
    t2.start();
  }
}
```

- 小结：启动线程是start()方法，run()方法

常见线程方法
---
#### sleep
当前线程暂停
- Thread.sleep(1000);
  - 表示当前线程暂停1000毫秒，其他线程不受影响
  - 可能会抛InterruptedException中断异常
    - 因为当前线程sleep的时候有可能被停止。

```java
package multiplethread;

public class TestThread {
    public static void main(String[] args) {
        Thread t1= new Thread(){
            public void run(){
                int seconds =0;
                while(true){
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.printf("已经玩了LOL %d 秒%n", seconds++);
                }
            }
        };
        t1.start();
    }
}
```

#### join
所有进程，至少会有一个线程即主线程、

在主线程中加入该线程。
主线程会等待该线程结束完毕才会往下运行。

```java
public class TestThread {
  public static void main(String[] args) {
    Hero gareen = new Hero("盖伦", 616, 50);
    Hero teemo = new Hero("提莫", 300, 30);
    Hero bh = new Hero("赏金猎人", 500, 65);
    Hero leesin = new Hero("盲僧", 455, 80);

    Thread t1 = new Thread() {
      public void  run() {
        while(!teemo.isDead()) {
          gareen.attackHero(teemo);
        }
      }
    };
    t1.start();
    // 代码执行到这里，一直是main线程在运行
    try {
      // t1线程加入到Main线程中，只有t1运行结束才会继续往下走
      t1.join();
    } catch(Exception e) {
      e.printStackTrace();
    }

    Thread t2 = new Thread() {
      public void run() {
        bh.attackHero(leesin);
      }
    };
    // 会观察到盖伦把提莫杀掉后，才运行t2线程
    t2.start();
  }
}
```

#### setPriority
当线程处于竞争关系的时候，优先级高的线程会有更大的几率获得CPU资源
```java
t1.setPriority(Thread.MAX_PRIORITY);
t2.setPriority(Thread.MIN_PRIORITY);
t1.start();
t2.start();
```
#### yield
当前线程，**临时暂停**，使得其他线程可以有更多的机会占用CPU资源
```java
Thread t2 = new Thread() {
  public void run() {
    while(!leesin.isDead()) {
      Thread.yield();
      bh.attackHero(leesin);
    }
  }
};
t1.setPriority(5);
t2.setPriority(5);
t1.start();
t2.start();
```

#### setDaemon
守护线程的概念：当一个进程里，所有的线程都是守护线程的时候，结束当前进程。

守护线程通常会被用来做日志，性能统计等工作。

同步
---
多线程的同步问题指的是多个线程同时修改一个数据的时候，可能导致的问题

多线程问题，又叫**Concurrency**问题

- 问题
<p>
假设盖伦有10000滴血，并且在基地里，同时又被对方多个英雄攻击
就是有多个线程在减少盖伦的hp
同时又有多个线程在恢复盖伦的hp
假设线程的数量是一样的，并且每次改变的值都是1，那么所有线程结束后，盖伦应该还是10000滴血。
但是。。。
</p>

- 分析
<p>
1. 假设增加线程先进入，得到的hp是10000
2. 进行增加运算
3. 正在做增加运算的时候，还没有来得及修改hp的值，减少线程来了
4. 减少线程得到的hp的值也是10000
5. 减少线程进行减少运算
6. 增加线程运算结束，得到值10001，并把这个值赋予hp
7. 减少线程也运算结束，得到值9999，并把这个值赋予hp
hp，最后的值就是9999
虽然经历了两个线程各自增减了一次，本来期望还是原值10000，但是却得到了一个9999
这个时候的值9999是一个错误的值，在业务上又叫做脏数据
</p>

- 解决思路
<p>
总体解决思路是： 在增加线程访问hp期间，其他线程不可以访问hp
1. 增加线程获取到hp的值，并进行运算
2. 在运算期间，减少线程试图来获取hp的值，但是不被允许
3. 增加线程运算结束，并成功修改hp的值为10001
4. 减少线程，在增加线程做完后，才能访问hp的值，即10001
5. 减少线程运算，并得到新的值10000
</p>

#### synchronized同步对象概念
**synchronized**关键字的意义
```java
Object someObject = new Object();
synchronized(someObject) {
  // 此处的代码只有占有了someObject后才可以执行
}
```

**synchronized表示当前线程，独占对象 someObject**
当前线程**独占**了对象someObject，如果有**其他线程试图占有对象**someObjective，**就会等待**，直到当前线程释放对someObject的占用。

someObject又叫同步对象，所有的对象，都可以作为同步对象

为了达到同步的效果，必须使用同一个同步对象

**释放同步对象**的方式：synchronized块自然结束，或者有异常抛出
