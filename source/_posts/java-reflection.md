---
title: Java反射机制详解
date: 2026-07-07 00:00:00
categories:
  - Java作业
tags:
  - Java
  - 反射
  - 高级特性
---

# Java反射机制详解

## 一、什么是反射

Java反射（Reflection）是Java语言的一个重要特性，它允许程序在运行时获取类的信息，并且可以操作类的属性、方法和构造函数。简单来说，反射就是在运行时才知道要操作的类是什么，并且可以在运行时获取类的完整构造，以及调用对应的方法。

### 反射的核心思想

正常情况下，我们使用一个类是这样的：
1. 导入类 → 2. new实例化 → 3. 调用方法/访问属性

反射机制下：
1. 获取类的Class对象 → 2. 通过Class对象获取构造方法 → 3. 创建实例 → 4. 调用方法/访问属性

## 二、获取Class对象的三种方式

### 方式一：类名.class

```java
Class<?> clazz1 = String.class;
```

### 方式二：对象.getClass()

```java
String str = "hello";
Class<?> clazz2 = str.getClass();
```

### 方式三：Class.forName()（最常用）

```java
Class<?> clazz3 = Class.forName("java.lang.String");
```

### 示例代码

```java
public class ReflectionDemo1 {
    public static void main(String[] args) throws ClassNotFoundException {
        Class<?> clazz1 = String.class;
        System.out.println("方式一: " + clazz1.getName());

        String str = "hello";
        Class<?> clazz2 = str.getClass();
        System.out.println("方式二: " + clazz2.getName());

        Class<?> clazz3 = Class.forName("java.lang.String");
        System.out.println("方式三: " + clazz3.getName());

        System.out.println("三个Class对象是否相同: " + (clazz1 == clazz2 && clazz2 == clazz3));
    }
}
```

## 三、反射操作构造方法

### 示例类

```java
class Person {
    private String name;
    private int age;

    public Person() {
        System.out.println("无参构造方法被调用");
    }

    public Person(String name) {
        this.name = name;
        System.out.println("有参构造方法被调用，name=" + name);
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
        System.out.println("全参构造方法被调用，name=" + name + ", age=" + age);
    }

    private Person(int age) {
        this.age = age;
        System.out.println("私有构造方法被调用，age=" + age);
    }

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
}
```

### 获取并使用构造方法

```java
import java.lang.reflect.Constructor;

public class ReflectionConstructorDemo {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Person.class;

        System.out.println("=== 获取所有public构造方法 ===");
        Constructor<?>[] constructors = clazz.getConstructors();
        for (Constructor<?> c : constructors) {
            System.out.println(c);
        }

        System.out.println("\n=== 获取所有构造方法（包括私有）===");
        Constructor<?>[] declaredConstructors = clazz.getDeclaredConstructors();
        for (Constructor<?> c : declaredConstructors) {
            System.out.println(c);
        }

        System.out.println("\n=== 使用无参构造方法创建对象 ===");
        Constructor<?> noArgConstructor = clazz.getConstructor();
        Object obj1 = noArgConstructor.newInstance();
        System.out.println(obj1);

        System.out.println("\n=== 使用有参构造方法创建对象 ===");
        Constructor<?> oneArgConstructor = clazz.getConstructor(String.class);
        Object obj2 = oneArgConstructor.newInstance("张三");
        System.out.println(obj2);

        System.out.println("\n=== 使用全参构造方法创建对象 ===");
        Constructor<?> allArgConstructor = clazz.getConstructor(String.class, int.class);
        Object obj3 = allArgConstructor.newInstance("李四", 20);
        System.out.println(obj3);

        System.out.println("\n=== 使用私有构造方法创建对象 ===");
        Constructor<?> privateConstructor = clazz.getDeclaredConstructor(int.class);
        privateConstructor.setAccessible(true);
        Object obj4 = privateConstructor.newInstance(25);
        System.out.println(obj4);
    }
}
```

## 四、反射操作成员变量

```java
import java.lang.reflect.Field;

public class ReflectionFieldDemo {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Person.class;
        Constructor<?> constructor = clazz.getConstructor(String.class, int.class);
        Object person = constructor.newInstance("王五", 30);

        System.out.println("=== 获取所有public字段 ===");
        Field[] fields = clazz.getFields();
        for (Field f : fields) {
            System.out.println(f);
        }

        System.out.println("\n=== 获取所有字段（包括私有）===");
        Field[] declaredFields = clazz.getDeclaredFields();
        for (Field f : declaredFields) {
            System.out.println(f);
        }

        System.out.println("\n=== 获取并修改私有字段 ===");
        Field nameField = clazz.getDeclaredField("name");
        nameField.setAccessible(true);
        System.out.println("修改前 name = " + nameField.get(person));
        nameField.set(person, "赵六");
        System.out.println("修改后 name = " + nameField.get(person));

        Field ageField = clazz.getDeclaredField("age");
        ageField.setAccessible(true);
        System.out.println("修改前 age = " + ageField.get(person));
        ageField.set(person, 35);
        System.out.println("修改后 age = " + ageField.get(person));

        System.out.println("\n最终对象: " + person);
    }
}
```

## 五、反射操作成员方法

```java
import java.lang.reflect.Method;

class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    private int multiply(int a, int b) {
        return a * b;
    }

    public static void showInfo() {
        System.out.println("这是一个静态方法");
    }

    public void printMessage(String msg) {
        System.out.println("消息: " + msg);
    }
}

public class ReflectionMethodDemo {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Calculator.class;
        Object calculator = clazz.getConstructor().newInstance();

        System.out.println("=== 获取所有public方法 ===");
        Method[] methods = clazz.getMethods();
        for (Method m : methods) {
            if (m.getDeclaringClass() == clazz) {
                System.out.println(m);
            }
        }

        System.out.println("\n=== 获取所有方法（包括私有）===");
        Method[] declaredMethods = clazz.getDeclaredMethods();
        for (Method m : declaredMethods) {
            System.out.println(m);
        }

        System.out.println("\n=== 调用public方法 ===");
        Method addMethod = clazz.getMethod("add", int.class, int.class);
        int result = (int) addMethod.invoke(calculator, 10, 5);
        System.out.println("add(10, 5) = " + result);

        Method subtractMethod = clazz.getMethod("subtract", int.class, int.class);
        result = (int) subtractMethod.invoke(calculator, 10, 5);
        System.out.println("subtract(10, 5) = " + result);

        System.out.println("\n=== 调用私有方法 ===");
        Method multiplyMethod = clazz.getDeclaredMethod("multiply", int.class, int.class);
        multiplyMethod.setAccessible(true);
        result = (int) multiplyMethod.invoke(calculator, 10, 5);
        System.out.println("multiply(10, 5) = " + result);

        System.out.println("\n=== 调用静态方法 ===");
        Method showInfoMethod = clazz.getMethod("showInfo");
        showInfoMethod.invoke(null);

        System.out.println("\n=== 调用无返回值方法 ===");
        Method printMethod = clazz.getMethod("printMessage", String.class);
        printMethod.invoke(calculator, "Hello Reflection!");
    }
}
```

## 六、反射的应用场景

### 场景一：简单工厂模式

```java
interface Animal {
    void speak();
}

class Dog implements Animal {
    @Override
    public void speak() {
        System.out.println("汪汪汪");
    }
}

class Cat implements Animal {
    @Override
    public void speak() {
        System.out.println("喵喵喵");
    }
}

class AnimalFactory {
    public static Animal createAnimal(String className) throws Exception {
        Class<?> clazz = Class.forName(className);
        return (Animal) clazz.getConstructor().newInstance();
    }
}

public class FactoryDemo {
    public static void main(String[] args) throws Exception {
        Animal dog = AnimalFactory.createAnimal("Dog");
        dog.speak();

        Animal cat = AnimalFactory.createAnimal("Cat");
        cat.speak();
    }
}
```

### 场景二：配置文件+反射（解耦）

```java
import java.io.FileInputStream;
import java.util.Properties;

public class ConfigReflectionDemo {
    public static void main(String[] args) throws Exception {
        Properties prop = new Properties();
        prop.load(new FileInputStream("config.properties"));

        String className = prop.getProperty("className");
        String methodName = prop.getProperty("methodName");

        Class<?> clazz = Class.forName(className);
        Object obj = clazz.getConstructor().newInstance();
        Method method = clazz.getMethod(methodName);
        method.invoke(obj);
    }
}
```

config.properties文件：
```
className=Dog
methodName=speak
```

### 场景三：通用工具类（例如复制Bean属性）

```java
import java.lang.reflect.Field;

public class BeanUtils {
    public static void copyProperties(Object source, Object target) throws Exception {
        Class<?> sourceClazz = source.getClass();
        Class<?> targetClazz = target.getClass();

        Field[] sourceFields = sourceClazz.getDeclaredFields();
        for (Field sourceField : sourceFields) {
            sourceField.setAccessible(true);
            String fieldName = sourceField.getName();
            Object value = sourceField.get(source);

            try {
                Field targetField = targetClazz.getDeclaredField(fieldName);
                targetField.setAccessible(true);
                targetField.set(target, value);
            } catch (NoSuchFieldException e) {
            }
        }
    }

    public static void main(String[] args) throws Exception {
        Person p1 = new Person("张三", 20);
        Person p2 = new Person();
        copyProperties(p1, p2);
        System.out.println("p2 = " + p2);
    }
}
```

## 七、反射的优缺点

| 优点 | 缺点 |
|------|------|
| 提高了程序的灵活性和扩展性 | 性能开销大，比直接调用慢 |
| 可以解耦，提高代码的复用率 | 破坏了封装性（可以访问私有成员） |
| 是很多框架（Spring、MyBatis等）的基础 | 代码可读性和可维护性较差 |
| 可以在运行时获取类的信息 | 编译期无法检查错误，容易出运行时异常 |

## 八、总结

Java反射机制是一个强大的工具，它允许程序在运行时动态地获取类的信息并操作类的成员。虽然反射有一定的性能开销，并且会破坏封装性，但它是很多Java框架的基础，在开发中具有不可替代的作用。

掌握反射的核心API：
- **Class**：表示类的对象
- **Constructor**：表示构造方法
- **Field**：表示成员变量
- **Method**：表示成员方法

常用方法：
- `getXxx()` / `getDeclaredXxx()` - 获取public / 所有（包括私有）
- `setAccessible(true)` - 打破封装，访问私有成员
- `newInstance()` / `invoke()` / `get()` / `set()` - 创建实例、调用方法、访问属性
