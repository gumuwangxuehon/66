---
title: Java文件复制竞赛题（字符缓冲流与字节缓冲流）
date: 2026-07-07 00:00:00
categories:
  - Java作业
tags:
  - Java
  - IO流
  - 文件操作
---

# Java文件复制竞赛题（字符缓冲流与字节缓冲流）

## 一、IO流简介

Java IO流是处理输入输出的核心机制。根据数据流向分为输入流和输出流；根据数据单位分为字节流和字符流。

### 流的分类

| 分类方式 | 类型 | 说明 |
|---------|------|------|
| 按数据流向 | 输入流 | 从文件/网络读取数据到内存 |
| | 输出流 | 从内存写入数据到文件/网络 |
| 按数据单位 | 字节流 | 以字节为单位，可处理所有文件 |
| | 字符流 | 以字符为单位，适合处理文本文件 |
| 按功能 | 节点流 | 直接与数据源连接 |
| | 缓冲流 | 对节点流进行包装，提高效率 |

## 二、文本文件复制（字符缓冲流）

字符缓冲流（BufferedReader / BufferedWriter）是处理文本文件最常用的方式，效率高且支持按行读写。

### 方式一：使用字符缓冲流（逐字符读写）

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class TextFileCopyCharByChar {
    public static void copyFile(String srcPath, String destPath) {
        BufferedReader br = null;
        BufferedWriter bw = null;
        try {
            br = new BufferedReader(new FileReader(srcPath));
            bw = new BufferedWriter(new FileWriter(destPath));

            int ch;
            while ((ch = br.read()) != -1) {
                bw.write(ch);
            }
            System.out.println("文件复制成功！");
        } catch (IOException e) {
            System.out.println("文件复制失败：" + e.getMessage());
        } finally {
            try {
                if (bw != null) bw.close();
                if (br != null) br.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        copyFile("source.txt", "dest_char.txt");
    }
}
```

### 方式二：使用字符缓冲流（逐行读写，最常用）

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class TextFileCopyLineByLine {
    public static void copyFile(String srcPath, String destPath) {
        try (BufferedReader br = new BufferedReader(new FileReader(srcPath));
             BufferedWriter bw = new BufferedWriter(new FileWriter(destPath))) {

            String line;
            while ((line = br.readLine()) != null) {
                bw.write(line);
                bw.newLine();
            }
            System.out.println("文件复制成功！");
        } catch (IOException e) {
            System.out.println("文件复制失败：" + e.getMessage());
        }
    }

    public static void main(String[] args) {
        copyFile("source.txt", "dest_line.txt");
    }
}
```

### 方式三：使用字符数组缓冲

```java
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class TextFileCopyCharArray {
    public static void copyFile(String srcPath, String destPath) {
        try (FileReader fr = new FileReader(srcPath);
             FileWriter fw = new FileWriter(destPath)) {

            char[] buffer = new char[1024];
            int len;
            while ((len = fr.read(buffer)) != -1) {
                fw.write(buffer, 0, len);
            }
            System.out.println("文件复制成功！");
        } catch (IOException e) {
            System.out.println("文件复制失败：" + e.getMessage());
        }
    }

    public static void main(String[] args) {
        copyFile("source.txt", "dest_chararray.txt");
    }
}
```

## 三、任意文件复制（字节缓冲流）

字节缓冲流（BufferedInputStream / BufferedOutputStream）可以处理所有类型的文件（文本、图片、音频、视频等），是万能复制方式。

### 方式一：使用字节缓冲流（逐字节读写）

```java
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class FileCopyByteByByte {
    public static void copyFile(String srcPath, String destPath) {
        try (BufferedInputStream bis = new BufferedInputStream(new FileInputStream(srcPath));
             BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(destPath))) {

            int b;
            while ((b = bis.read()) != -1) {
                bos.write(b);
            }
            System.out.println("文件复制成功！");
        } catch (IOException e) {
            System.out.println("文件复制失败：" + e.getMessage());
        }
    }

    public static void main(String[] args) {
        copyFile("image.jpg", "dest_byte.jpg");
    }
}
```

### 方式二：使用字节数组缓冲（万能复制，效率最高）

```java
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class FileCopyByteArray {
    public static void copyFile(String srcPath, String destPath) {
        try (BufferedInputStream bis = new BufferedInputStream(new FileInputStream(srcPath));
             BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(destPath))) {

            byte[] buffer = new byte[8192];
            int len;
            while ((len = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, len);
            }
            System.out.println("文件复制成功！");
        } catch (IOException e) {
            System.out.println("文件复制失败：" + e.getMessage());
        }
    }

    public static void main(String[] args) {
        copyFile("image.jpg", "dest_bytearray.jpg");
    }
}
```

### 方式三：基本字节流（无缓冲，仅作对比）

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class FileCopyBasic {
    public static void copyFile(String srcPath, String destPath) {
        try (FileInputStream fis = new FileInputStream(srcPath);
             FileOutputStream fos = new FileOutputStream(destPath)) {

            byte[] buffer = new byte[8192];
            int len;
            while ((len = fis.read(buffer)) != -1) {
                fos.write(buffer, 0, len);
            }
            System.out.println("文件复制成功！");
        } catch (IOException e) {
            System.out.println("文件复制失败：" + e.getMessage());
        }
    }

    public static void main(String[] args) {
        copyFile("image.jpg", "dest_basic.jpg");
    }
}
```

## 四、完整竞赛题：文件复制工具类

```java
import java.io.*;

public class FileCopyUtil {

    public static void copyTextFile(String srcPath, String destPath) {
        long startTime = System.currentTimeMillis();
        try (BufferedReader br = new BufferedReader(new FileReader(srcPath));
             BufferedWriter bw = new BufferedWriter(new FileWriter(destPath))) {

            String line;
            while ((line = br.readLine()) != null) {
                bw.write(line);
                bw.newLine();
            }
            long endTime = System.currentTimeMillis();
            System.out.println("文本文件复制成功！耗时: " + (endTime - startTime) + "ms");
        } catch (IOException e) {
            System.out.println("文本文件复制失败：" + e.getMessage());
        }
    }

    public static void copyBinaryFile(String srcPath, String destPath) {
        long startTime = System.currentTimeMillis();
        try (BufferedInputStream bis = new BufferedInputStream(new FileInputStream(srcPath));
             BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(destPath))) {

            byte[] buffer = new byte[8192];
            int len;
            while ((len = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, len);
            }
            long endTime = System.currentTimeMillis();
            System.out.println("二进制文件复制成功！耗时: " + (endTime - startTime) + "ms");
        } catch (IOException e) {
            System.out.println("二进制文件复制失败：" + e.getMessage());
        }
    }

    public static long getFileSize(String filePath) {
        File file = new File(filePath);
        return file.length();
    }

    public static void main(String[] args) {
        System.out.println("=== 文件复制竞赛题 ===");
        System.out.println();

        String textSrc = "source.txt";
        String textDest = "dest_text.txt";
        File textFile = new File(textSrc);
        if (textFile.exists()) {
            System.out.println("源文件大小: " + getFileSize(textSrc) + " 字节");
            copyTextFile(textSrc, textDest);
        } else {
            System.out.println("文本源文件不存在，正在创建测试文件...");
            createTestTextFile(textSrc, 10000);
            System.out.println("源文件大小: " + getFileSize(textSrc) + " 字节");
            copyTextFile(textSrc, textDest);
        }

        System.out.println();

        String binarySrc = "test.bin";
        String binaryDest = "dest_binary.bin";
        File binFile = new File(binarySrc);
        if (binFile.exists()) {
            System.out.println("源文件大小: " + getFileSize(binarySrc) + " 字节");
            copyBinaryFile(binarySrc, binaryDest);
        } else {
            System.out.println("二进制源文件不存在，正在创建测试文件...");
            createTestBinaryFile(binarySrc, 5 * 1024 * 1024);
            System.out.println("源文件大小: " + getFileSize(binarySrc) + " 字节 ("
                    + String.format("%.2f", getFileSize(binarySrc) / 1024.0 / 1024.0) + " MB)");
            copyBinaryFile(binarySrc, binaryDest);
        }

        System.out.println();
        System.out.println("=== 各方式效率对比（使用5MB测试文件）===");
        compareEfficiency(binarySrc);
    }

    public static void createTestTextFile(String path, int lines) {
        try (BufferedWriter bw = new BufferedWriter(new FileWriter(path))) {
            for (int i = 1; i <= lines; i++) {
                bw.write("这是第 " + i + " 行测试数据：Hello World! Java IO流练习。");
                bw.newLine();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void createTestBinaryFile(String path, int size) {
        try (FileOutputStream fos = new FileOutputStream(path)) {
            byte[] data = new byte[8192];
            int written = 0;
            while (written < size) {
                int toWrite = Math.min(8192, size - written);
                fos.write(data, 0, toWrite);
                written += toWrite;
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void compareEfficiency(String srcPath) {
        String dest1 = "compare1.tmp";
        String dest2 = "compare2.tmp";
        String dest3 = "compare3.tmp";

        long start, end;

        start = System.currentTimeMillis();
        try (FileInputStream fis = new FileInputStream(srcPath);
             FileOutputStream fos = new FileOutputStream(dest1)) {
            int b;
            while ((b = fis.read()) != -1) {
                fos.write(b);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        end = System.currentTimeMillis();
        System.out.println("基本字节流逐字节: " + (end - start) + "ms");

        start = System.currentTimeMillis();
        try (FileInputStream fis = new FileInputStream(srcPath);
             FileOutputStream fos = new FileOutputStream(dest2)) {
            byte[] buffer = new byte[8192];
            int len;
            while ((len = fis.read(buffer)) != -1) {
                fos.write(buffer, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        end = System.currentTimeMillis();
        System.out.println("基本字节流+数组: " + (end - start) + "ms");

        start = System.currentTimeMillis();
        try (BufferedInputStream bis = new BufferedInputStream(new FileInputStream(srcPath));
             BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(dest3))) {
            byte[] buffer = new byte[8192];
            int len;
            while ((len = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        end = System.currentTimeMillis();
        System.out.println("缓冲流+数组（推荐）: " + (end - start) + "ms");

        new File(dest1).delete();
        new File(dest2).delete();
        new File(dest3).delete();
    }
}
```

## 五、运行结果示例

```
=== 文件复制竞赛题 ===

文本源文件不存在，正在创建测试文件...
源文件大小: 618890 字节
文本文件复制成功！耗时: 18ms

二进制源文件不存在，正在创建测试文件...
源文件大小: 5242880 字节 (5.00 MB)
二进制文件复制成功！耗时: 5ms

=== 各方式效率对比（使用5MB测试文件）===
基本字节流逐字节: 15234ms
基本字节流+数组: 12ms
缓冲流+数组（推荐）: 4ms
```

## 六、总结

| 文件类型 | 推荐方式 | 核心类 | 效率 |
|---------|---------|--------|------|
| 文本文件 | 字符缓冲流逐行读写 | BufferedReader / BufferedWriter | 高 |
| 任意文件 | 字节缓冲流+字节数组 | BufferedInputStream / BufferedOutputStream | 最高 |

### 使用建议

1. **文本文件**：使用 BufferedReader + BufferedWriter 逐行读写，最常用
2. **二进制文件**：使用 BufferedInputStream + BufferedOutputStream 配合 byte[] 数组，万能复制
3. **缓冲流作用**：内部维护缓冲区，减少IO次数，大幅提升性能
4. **try-with-resources**：Java 7+推荐使用，自动关闭流，避免资源泄漏
5. **数组大小**：通常使用 1024~8192 字节的缓冲数组，8192 是比较常用的大小

文件复制是Java IO流的经典练习题，掌握字符缓冲流和字节缓冲流的使用，是Java开发者必备的基础技能。
