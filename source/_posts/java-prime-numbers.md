---
title: Java素数判断与求解
date: 2026-07-07 00:00:00
categories:
  - Java作业
tags:
  - Java
  - 算法
  - 素数
---

# Java素数判断与求解

## 一、素数的定义

素数（质数）是指在大于1的自然数中，除了1和它本身以外不再有其他因数的自然数。

## 二、素数判断方法

### 方法一：暴力枚举法

最基础的方法，从2遍历到n-1，判断是否有因数。

```java
public static boolean isPrime(int n) {
    if (n <= 1) {
        return false;
    }
    for (int i = 2; i < n; i++) {
        if (n % i == 0) {
            return false;
        }
    }
    return true;
}
```

**时间复杂度**：O(n)

### 方法二：优化枚举法（平方根优化）

如果n有因数a，那么必然存在另一个因数b = n/a，其中一个小于等于√n，另一个大于等于√n。因此只需要遍历到√n即可。

```java
public static boolean isPrime(int n) {
    if (n <= 1) {
        return false;
    }
    if (n == 2) {
        return true;
    }
    if (n % 2 == 0) {
        return false;
    }
    for (int i = 3; i * i <= n; i += 2) {
        if (n % i == 0) {
            return false;
        }
    }
    return true;
}
```

**时间复杂度**：O(√n)

### 方法三：埃拉托斯特尼筛法（埃氏筛）

用于批量求解一定范围内的所有素数，效率极高。

```java
public static boolean[] sieveOfEratosthenes(int n) {
    boolean[] isPrime = new boolean[n + 1];
    for (int i = 2; i <= n; i++) {
        isPrime[i] = true;
    }
    for (int i = 2; i * i <= n; i++) {
        if (isPrime[i]) {
            for (int j = i * i; j <= n; j += i) {
                isPrime[j] = false;
            }
        }
    }
    return isPrime;
}
```

**时间复杂度**：O(n log log n)

## 三、完整示例程序

```java
import java.util.Scanner;

public class PrimeNumbers {

    public static boolean isPrime(int n) {
        if (n <= 1) {
            return false;
        }
        if (n == 2) {
            return true;
        }
        if (n % 2 == 0) {
            return false;
        }
        for (int i = 3; i * i <= n; i += 2) {
            if (n % i == 0) {
                return false;
            }
        }
        return true;
    }

    public static boolean[] sieveOfEratosthenes(int n) {
        boolean[] isPrime = new boolean[n + 1];
        for (int i = 2; i <= n; i++) {
            isPrime[i] = true;
        }
        for (int i = 2; i * i <= n; i++) {
            if (isPrime[i]) {
                for (int j = i * i; j <= n; j += i) {
                    isPrime[j] = false;
                }
            }
        }
        return isPrime;
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("请输入一个整数：");
        int num = scanner.nextInt();

        if (isPrime(num)) {
            System.out.println(num + " 是素数");
        } else {
            System.out.println(num + " 不是素数");
        }

        System.out.println("\n--- 输出100以内的所有素数（埃氏筛法）---");
        boolean[] primes = sieveOfEratosthenes(100);
        int count = 0;
        for (int i = 2; i <= 100; i++) {
            if (primes[i]) {
                System.out.printf("%-4d", i);
                count++;
                if (count % 10 == 0) {
                    System.out.println();
                }
            }
        }
        System.out.println("\n100以内共有 " + count + " 个素数");

        scanner.close();
    }
}
```

## 四、运行结果示例

```
请输入一个整数：17
17 是素数

--- 输出100以内的所有素数（埃氏筛法）---
2   3   5   7   11  13  17  19  23  29
31  37  41  43  47  53  59  61  67  71
73  79  83  89  97
100以内共有 25 个素数
```

## 五、总结

| 方法 | 时间复杂度 | 适用场景 |
|------|-----------|----------|
| 暴力枚举法 | O(n) | 单个小数字判断 |
| 优化枚举法 | O(√n) | 单个数字判断（推荐） |
| 埃氏筛法 | O(n log log n) | 批量求解范围内素数 |

素数判断是算法入门的经典题目，掌握不同的优化方法对于理解算法复杂度分析很有帮助。
