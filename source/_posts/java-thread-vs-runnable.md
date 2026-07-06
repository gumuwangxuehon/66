---
title: Java创建线程的两种方式：继承Thread类vs实现Runnable接口
date: 2026-07-07 00:00:00
categories:
  - Java作业
tags:
  - Java
  - 多线程
  - Thread
  - Runnable
---

# Java创建线程的两种方式：继承Thread类vs实现Runnable接口

## 一、线程简介

线程（Thread）是程序执行的最小单元，是进程中的一个执行路径。Java提供了两种主要的创建线程的方式：
1. 继承 `Thread` 类
2. 实现 `Runnable` 接口

## 二、方式一：继承Thread类

### 实现步骤

1. 创建一个类继承 `Thread` 类
2. 重写 `run()` 方法，编写线程执行体
3. 创建线程对象，调用 `start()` 方法启动线程

### 示例代码

```java
class MyThread extends Thread {

    private String name;

    public MyThread(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println(name + " 正在执行，第 " + i + " 次");
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println(name + " 执行完毕");
    }
}

public class ThreadDemo {
    public static void main(String[] args) {
        System.out.println("主线程开始");

        MyThread t1 = new MyThread("线程A");
        MyThread t2 = new MyThread("线程B");

        t1.start();
        t2.start();

        System.out.println("主线程结束");
    }
}
```

### 运行结果示例

```
主线程开始
主线程结束
线程A 正在执行，第 1 次
线程B 正在执行，第 1 次
线程A 正在执行，第 2 次
线程B 正在执行，第 2 次
线程A 正在执行，第 3 次
线程B 正在执行，第 3 次
线程A 正在执行，第 4 次
线程B 正在执行，第 4 次
线程A 正在执行，第 5 次
线程B 正在执行，第 5 次
线程A 执行完毕
线程B 执行完毕
```

## 三、方式二：实现Runnable接口

### 实现步骤

1. 创建一个类实现 `Runnable` 接口
2. 实现 `run()` 方法，编写线程执行体
3. 创建 `Thread` 对象，将 `Runnable` 实现类作为参数传入
4. 调用 `start()` 方法启动线程

### 示例代码

```java
class MyRunnable implements Runnable {

    private String name;

    public MyRunnable(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println(name + " 正在执行，第 " + i + " 次");
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println(name + " 执行完毕");
    }
}

public class RunnableDemo {
    public static void main(String[] args) {
        System.out.println("主线程开始");

        Thread t1 = new Thread(new MyRunnable("线程A"));
        Thread t2 = new Thread(new MyRunnable("线程B"));

        t1.start();
        t2.start();

        System.out.println("主线程结束");
    }
}
```

### 匿名内部类方式

```java
public class AnonymousRunnableDemo {
    public static void main(String[] args) {
        System.out.println("主线程开始");

        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 1; i <= 5; i++) {
                    System.out.println("线程A 正在执行，第 " + i + " 次");
                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        });

        t1.start();
        System.out.println("主线程结束");
    }
}
```

### Lambda表达式方式（Java 8+）

```java
public class LambdaDemo {
    public static void main(String[] args) {
        System.out.println("主线程开始");

        Thread t1 = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("线程A 正在执行，第 " + i + " 次");
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        t1.start();
        System.out.println("主线程结束");
    }
}
```

## 四、两种方式的区别

| 对比项 | 继承Thread类 | 实现Runnable接口 |
|--------|-------------|-----------------|
| 继承限制 | 只能继承一个类，无法再继承其他类 | 可以实现多个接口，不影响继承体系 |
| 资源共享 | 多个线程不能共享同一个任务对象的资源 | 多个线程可以共享同一个Runnable对象 |
| 代码耦合 | 线程对象和任务逻辑耦合在一起 | 线程对象和任务逻辑分离，解耦 |
| 灵活性 | 较差，每个线程都有独立的对象 | 较好，同一个Runnable可以被多个线程执行 |
| 扩展性 | 受单继承限制 | 可以同时继承其他类、实现其他接口 |

### 1. 单继承限制

Java只支持单继承，继承了Thread类就不能再继承其他类。而实现Runnable接口的方式，还可以继承其他类或实现其他接口。

### 2. 资源共享能力

**使用继承Thread类：多个线程无法共享同一份资源**

```java
class TicketThread extends Thread {
    private int ticket = 5;

    @Override
    public void run() {
        while (ticket > 0) {
            System.out.println(Thread.currentThread().getName() + " 卖出第 " + ticket + " 张票");
            ticket--;
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

public class ThreadShareDemo {
    public static void main(String[] args) {
        TicketThread t1 = new TicketThread();
        TicketThread t2 = new TicketThread();
        TicketThread t3 = new TicketThread();

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();
    }
}
```

运行结果：每个窗口都卖了5张票，总共15张（各自独立的ticket变量）。

**使用实现Runnable接口：多个线程可以共享同一份资源**

```java
class TicketRunnable implements Runnable {
    private int ticket = 5;

    @Override
    public synchronized void run() {
        while (ticket > 0) {
            System.out.println(Thread.currentThread().getName() + " 卖出第 " + ticket + " 张票");
            ticket--;
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

public class RunnableShareDemo {
    public static void main(String[] args) {
        TicketRunnable ticket = new TicketRunnable();

        Thread t1 = new Thread(ticket, "窗口1");
        Thread t2 = new Thread(ticket, "窗口2");
        Thread t3 = new Thread(ticket, "窗口3");

        t1.start();
        t2.start();
        t3.start();
    }
}
```

运行结果：三个窗口共同卖5张票，总共只有5张（共享同一个ticket变量）。

### 3. 解耦性

- **继承Thread类**：线程对象和任务逻辑绑定在一起，不利于代码复用
- **实现Runnable接口**：任务逻辑和线程对象分离，同一个任务可以被不同的线程执行，也可以被线程池等复用

## 五、第三种方式：Callable和Future（带返回值）

除了上述两种方式，Java还提供了 `Callable` 接口，它可以返回执行结果：

```java
import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

class MyCallable implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            sum += i;
            Thread.sleep(10);
        }
        return sum;
    }
}

public class CallableDemo {
    public static void main(String[] args) throws Exception {
        MyCallable callable = new MyCallable();
        FutureTask<Integer> futureTask = new FutureTask<>(callable);

        Thread thread = new Thread(futureTask);
        thread.start();

        System.out.println("主线程继续执行...");
        Integer result = futureTask.get();
        System.out.println("计算结果: " + result);
    }
}
```

## 六、总结

### 推荐使用实现Runnable接口的方式

1. **避免单继承限制**：Java类只能单继承，实现接口不受限制
2. **便于资源共享**：多个线程可以共享同一个Runnable对象的资源
3. **解耦设计**：任务逻辑和线程对象分离，代码更清晰
4. **易于扩展**：可以结合线程池使用，提高性能

### 什么时候用继承Thread类？

虽然推荐使用Runnable方式，但继承Thread类也有适用场景：
- 当需要重写Thread类的其他方法时（不仅是run方法）
- 简单的测试或Demo代码，追求编写简洁
- 不需要资源共享的场景

### 一句话总结

> **优先使用实现Runnable接口的方式创建线程，它更灵活、更易扩展、更符合面向对象设计原则。**
