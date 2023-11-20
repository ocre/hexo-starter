---
title: Java序列化多个对象到文件并正确读写的技巧
date: 2020-08-19 21:45:03
tags:
- Java
---

我们一般使用`ObjectInputStream`的`Object readObject()`方法来从输入流中读出一个对象，这个方法有个缺陷是，无法通过返回值来判断是否读到了文件末尾。因此，我们要序列化多个对象时，需要额外一些小技巧来处理。大体来说，有四种方法能够正确序列化反序列化多个对象：
* 把对象装入集合中，对整个集合进行序列化和反序列化
* 把对象放入对象数组中，对对象数组进行序列化和反序列化
* 依次序列化写入多个对象，并追加一个null对象，反序列化读取时若读到null就停止
* 依次序列化写入多个对象，读取时以FileInputStream的`int available()`返回值判断是否终止
下面举例说明。

### 数据准备&测试程序准备
首先准备一个要序列化的类：
```java
public class User implements Serializable {
    private static final long serialVersionUID = 1105545465577482303L;
    private String name;
    private int age;
    private transient String phone; // transient 关键字表示该成员不参与序列化

    public User() {
    }

    public User(String name, int age, String phone) {
        this.name = name;
        this.age = age;
        this.phone = phone;
    }
    
    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", phone='" + phone + '\'' +
                '}';
    }
```
然后准备测试程序，主体代码如下：
```java
public class SerializableTest {
    public static void main(String[] args) {
        // 1. 准备要序列化的数据
        User[] users = {
                new User("张三",33, "185000011133"),
                new User("李四",44, "185000011144"),
                new User("王五",55, "185000011155"),
                new User("赵六",66, "185000011166"),
        };
        ObjectOutputStream oos = null;
        ObjectInputStream ois = null;
        try {
            // 2. 准备输出流和输入流
            String fileName = "./out/oos_1.txt";
            oos = new ObjectOutputStream(new FileOutputStream(fileName));
            FileInputStream fis = new FileInputStream(fileName);
            ois = new ObjectInputStream(fis);
            // 3. 序列化到文件,并反序列化读取并输出
//            testSerialize1(users, oos, ois);
//            testSerialize2(users, oos, ois);
            testSerialize3(users, oos, ois);
//            testSerialize4(users, oos, ois, fis);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            // 4. 关闭流
            if (null != ois) {
                try {
                    ois.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (null != oos) {
                try {
                    oos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

```

### 实现多个对象的序列化和反序列
下面分别用4种方式实现多个对象的序列化和反序列化。

#### 1. 把对象装入集合中，对整个集合进行序列化和反序列化（推荐）
把下面这个方法加入到上面的`SerializableTest`类中。
```java
/**
     * 方法1： 使用集合保存多个对象，对集合进行序列化反序列化
     * @param users
     * @param oos
     * @param ois
     */
    private static void testSerialize1(User[] users, ObjectOutputStream oos, ObjectInputStream ois) throws IOException, ClassNotFoundException {
        ArrayList<User> list1 = new ArrayList<>();
        for (User user : users) {
            list1.add(user);
        }
        oos.writeObject(list1);
        oos.flush();
        System.out.println("方法1写入完毕！");
        Object obj = ois.readObject();
        System.out.println("方法1读取数据为：" + obj);
        ArrayList<User> list2 = (ArrayList<User>)obj;
        for (User user: list2) {
            System.out.println(user);
        }
    }
```
运行测试程序，输出为：
```shell
方法1写入完毕！
方法1读取数据为：[User{name='张三', age=33, phone='null'}, User{name='李四', age=44, phone='null'}, User{name='王五', age=55, phone='null'}, User{name='赵六', age=66, phone='null'}]
User{name='张三', age=33, phone='null'}
User{name='李四', age=44, phone='null'}
User{name='王五', age=55, phone='null'}
User{name='赵六', age=66, phone='null'}
```
#### 2. 把对象放入对象数组中，对对象数组进行序列化和反序列化
把下面这个方法加入到上面的`SerializableTest`类中。
```java
/**
     * 方法2： 使用Object数组保存多个对象，对整个数组进行序列化反序列化
     * @param users
     * @param oos
     * @param ois
     */
    private static void testSerialize2(User[] users, ObjectOutputStream oos, ObjectInputStream ois) throws IOException, ClassNotFoundException {
        oos.writeObject(users);
        oos.flush();
        System.out.println("方法2写入完毕！");
        Object[] objs = (Object[])ois.readObject();
        System.out.println("方法2读取数据为：");
        for (Object obj: objs) {
            System.out.println(obj);
        }
    }
```
输出跟上面类似。

#### 3. 依次序列化写入多个对象，并追加一个null对象，反序列化读取时若读到null就停止
把下面这个方法加入到上面的`SerializableTest`类中。
```java
/**
     * 方法3： 依次写入多个对象,再追加一个null对象表示结束，读取时判断读到的对象是否为null
     * @param users
     * @param oos
     * @param ois
     */
    private static void testSerialize3(User[] users, ObjectOutputStream oos, ObjectInputStream ois) throws IOException, ClassNotFoundException {
        for (User user: users) {
            oos.writeObject(user);
        }
        oos.writeObject(null);
        oos.flush();
        System.out.println("方法3写入完毕！");

        System.out.println("方法3读取数据为：");
        Object obj = null;
        while ((obj = ois.readObject()) != null) {
            System.out.println(obj);
        }
    }
```

输出同方法2。
#### 4. 依次序列化写入多个对象，读取时以FileInputStream的`int available()`返回值判断是否终止
把下面这个方法加入到上面的`SerializableTest`类中。
```java
/**
     * 方法4： 依次写入多个对象，读取时使用fis.available()方法判断是否读取到末尾
     * @param users
     * @param oos
     * @param ois
     * @param fis
     */
    private static void testSerialize4(User[] users, ObjectOutputStream oos, ObjectInputStream ois, FileInputStream fis) throws IOException, ClassNotFoundException {
        for (User user: users) {
            oos.writeObject(user);
        }
        oos.flush();
        System.out.println("方法4写入完毕！");

        System.out.println("方法4读取数据为：");
        while (fis.available() > 0) {
            Object obj = ois.readObject();
            System.out.println(obj);
        }
    }
```

输出同方法2。
