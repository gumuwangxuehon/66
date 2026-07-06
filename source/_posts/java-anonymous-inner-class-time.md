---
title: Java匿名内部类与方法执行时间计算
date: 2026-07-07 00:00:00
categories:
  - Java作业
tags:
  - Java
  - 匿名内部类
  - 性能测试
---

# Java匿名内部类与方法执行时间计算

## 一、匿名内部类简介

匿名内部类是没有名字的内部类，通常用于创建一个只使用一次的类实例。它必须继承一个父类或实现一个接口。

### 匿名内部类的语法

```java
new 父类/接口() {
    // 类体
};
```

### 匿名内部类的使用场景

1. 事件处理（Swing、Android等）
2. 线程创建（Runnable接口）
3. 比较器（Comparator接口）
4. 策略模式（方法参数传递）
5. 模板方法模式（计算代码执行时间等）

## 二、计算方法执行时间（模板方法模式）

### 应用场景

在开发和性能优化中，我们经常需要计算一个方法执行了多少秒。使用匿名内部类可以实现一个通用的计时工具，无需为每个方法重复编写计时代码。

### 定义计算接口

```java
interface Calculator {
    void calculate();
}
```

### 实现计时工具类

```java
public class TimeCounter {

    public static long countTime(Calculator calc) {
        long startTime = System.currentTimeMillis();
        calc.calculate();
        long endTime = System.currentTimeMillis();
        return endTime - startTime;
    }

    public static void printTime(String taskName, Calculator calc) {
        long time = countTime(calc);
        System.out.println("[" + taskName + "] 执行耗时: " + time + " 毫秒 (" + (time / 1000.0) + " 秒)");
    }
}
```

### 使用示例

```java
public class AnonymousInnerClassDemo {

    public static void main(String[] args) {
        System.out.println("=== 匿名内部类使用场景：计算方法执行时间 ===");
        System.out.println();

        TimeCounter.printTime("冒泡排序10000个元素", new Calculator() {
            @Override
            public void calculate() {
                int[] arr = new int[10000];
                for (int i = 0; i < arr.length; i++) {
                    arr[i] = (int) (Math.random() * 10000);
                }
                bubbleSort(arr);
            }
        });

        TimeCounter.printTime("选择排序10000个元素", new Calculator() {
            @Override
            public void calculate() {
                int[] arr = new int[10000];
                for (int i = 0; i < arr.length; i++) {
                    arr[i] = (int) (Math.random() * 10000);
                }
                selectionSort(arr);
            }
        });

        TimeCounter.printTime("Arrays排序10000个元素", new Calculator() {
            @Override
            public void calculate() {
                int[] arr = new int[10000];
                for (int i = 0; i < arr.length; i++) {
                    arr[i] = (int) (Math.random() * 10000);
                }
                java.util.Arrays.sort(arr);
            }
        });

        System.out.println();
        System.out.println("=== 其他匿名内部类使用场景 ===");
        demonstrateRunnable();
        demonstrateComparator();
        demonstrateSwingEvent();
    }

    public static void bubbleSort(int[] arr) {
        int n = arr.length;
        for (int i = 0; i < n - 1; i++) {
            for (int j = 0; j < n - i - 1; j++) {
                if (arr[j] > arr[j + 1]) {
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                }
            }
        }
    }

    public static void selectionSort(int[] arr) {
        int n = arr.length;
        for (int i = 0; i < n - 1; i++) {
            int minIdx = i;
            for (int j = i + 1; j < n; j++) {
                if (arr[j] < arr[minIdx]) {
                    minIdx = j;
                }
            }
            int temp = arr[minIdx];
            arr[minIdx] = arr[i];
            arr[i] = temp;
        }
    }

    public static void demonstrateRunnable() {
        System.out.println("\n--- 场景1：创建线程（Runnable）---");
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("匿名内部类实现的线程正在运行");
            }
        });
        thread.start();
        try {
            thread.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void demonstrateComparator() {
        System.out.println("\n--- 场景2：比较器（Comparator）---");
        String[] names = {"Tom", "Alice", "Bob", "Charlie", "David"};

        java.util.Arrays.sort(names, new java.util.Comparator<String>() {
            @Override
            public int compare(String s1, String s2) {
                return s1.length() - s2.length();
            }
        });

        System.out.print("按长度排序: ");
        for (String name : names) {
            System.out.print(name + " ");
        }
        System.out.println();
    }

    public static void demonstrateSwingEvent() {
        System.out.println("\n--- 场景3：Swing事件处理 ---");
        System.out.println("按钮点击事件通常使用匿名内部类实现ActionListener");
        System.out.println("例如: button.addActionListener(new ActionListener() { ... });");
    }
}
```

## 三、运行结果示例

```
=== 匿名内部类使用场景：计算方法执行时间 ===

[冒泡排序10000个元素] 执行耗时: 125 毫秒 (0.125 秒)
[选择排序10000个元素] 执行耗时: 58 毫秒 (0.058 秒)
[Arrays排序10000个元素] 执行耗时: 3 毫秒 (0.003 秒)

=== 其他匿名内部类使用场景 ===

--- 场景1：创建线程（Runnable）---
匿名内部类实现的线程正在运行

--- 场景2：比较器（Comparator）---
按长度排序: Bob Tom Alice David Charlie

--- 场景3：Swing事件处理 ---
按钮点击事件通常使用匿名内部类实现ActionListener
例如: button.addActionListener(new ActionListener() { ... });
```

## 四、Java 8 Lambda表达式对比

Java 8之后，对于函数式接口（只有一个抽象方法的接口），可以使用Lambda表达式替代匿名内部类，代码更简洁：

```java
@FunctionalInterface
interface Calculator {
    void calculate();
}

public class LambdaDemo {
    public static void main(String[] args) {
        TimeCounter.printTime("Lambda方式测试", () -> {
            int sum = 0;
            for (int i = 0; i < 100000; i++) {
                sum += i;
            }
            System.out.println("sum = " + sum);
        });
    }
}
```

## 五、匿名内部类总结

| 特性 | 说明 |
|------|------|
| 语法 | `new 父类/接口() { 类体 }` |
| 适用场景 | 事件处理、线程、比较器、策略模式 |
| 访问外部变量 | 只能访问final或实际上final的局部变量 |
| Java 8替代 | 函数式接口可用Lambda表达式 |

匿名内部类是Java中非常实用的特性，特别是在事件处理和回调函数中应用广泛。通过模板方法模式配合匿名内部类，可以优雅地实现代码执行时间统计等通用功能。
