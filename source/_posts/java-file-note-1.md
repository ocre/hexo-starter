---
title: Java File类笔记
date: 2020-06-12 12:32:03
tags:
- Java
---

java.io.File类是对文件极其常用操作的抽象。

#### File类的常用方法

直接上代码：
```java
public class FileTest {
    public static void main(String[] args) throws IOException {
        // 1. 构造函数
        String filename = "./README.md";
        System.out.println("File(" + filename + ")");
        File f1 = new File("./README.md");
        if (f1.exists()) {
            System.out.println("getName(): " + f1.getName());
            System.out.println("getPath(): " + f1.getPath());
            System.out.println("getAbsolutePath(): " + f1.getAbsolutePath());
            System.out.println("getCanonicalPath(): " + f1.getCanonicalPath());
            System.out.println("lastModified(): " + f1.lastModified());
            System.out.println("length(): " + f1.length());
            System.out.println("isFile(): " + f1.isFile());
        }
    }
}
```
#### 区分getName()、getPath()、getAbsolutePath()、getCanonicalPath()
![区分path、absolutePath、canonicalPath](https://cdn132.vieek.com/2020-06-11-%E6%88%AA%E5%B1%8F2020-06-11%20%E4%B8%8B%E5%8D%8812.31.50.png)

#### 文件的创建和删除
示例代码：
```java
      String filename = "./test-file.md";
      File f1 = new File(filename);
      if (f1.exists()) {
          System.out.println(f1.delete() ? "文件删除成功！" : "文件删除失败！");
      } else {
          System.out.println(f1.createNewFile() ? "文件创建成功！" : "文件创建失败！");
      }
```

#### 目录的创建和删除
示例代码：
```java
				// 2. 目录创建、删除
        File f2 = new File("./test-dir/test-subdir");
        if (f2.exists()) {
            System.out.println("getName(): " + f2.getName());
            // 这里只能删除最内层的非空目录
            System.out.println(f2.delete()? "目录删除成功！" : "目录删除失败！");
        } else {
            System.out.println(f2.mkdirs() ? "目录创建成功！" : "目录创建失败！");
        }
```

#### listFiles()指定文件过滤FileFilter
示例代码：
```java
				File f4 = new File("./.idea");
        FileFilter filter = (pathname) -> { return pathname.getName().endsWith(".xml");};
        File[] fileList2 = f4.listFiles(filter);
        for (File f: fileList2) {
            System.out.println(f.getName());
        }
```

#### 目录及子目录的递归遍历
示例代码如下：
```java
		/**
     * 递归打印目录和子目录下所有文件
     * @param file
     * @param filter
     */
    public static void show_dirs(File file, FileFilter filter) {
        File[] files = file.listFiles(filter);
        for (File f: files) {
            if (f.isFile()) {
                System.out.println(f.getName());
            } else if (f.isDirectory()) {
                System.out.println("Dir[" + f.getName() + "]");
                show_dirs(f, filter);
            }
        }
    }
    
    public static void main(String[] args) {
        FileFilter filter2 = (pathname) -> { return pathname.isDirectory() || pathname.getName().endsWith(".class"); };
        show_dirs(new File("./out"), filter2);
    }
```