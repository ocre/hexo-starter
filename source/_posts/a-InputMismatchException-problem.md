---
title: Java捕获InputMismatchException陷入死循环的bug处理
date: 2020-09-12 17:13:23
tags:
- Java
---

#### while(true) + scanner.nextInt() 导致InputMismatchException 死循环问题
今天做一个练手项目时，需要用户输入数字来选择菜单。示例代码如下：
```java
protected void processOption() {
    System.out.println("\n\n\t\t在线考试系统");
    System.out.println("---------------------------------");
    System.out.println("\t[1] 学员登录\t\t[2] 管理员登录");
    System.out.println("\t[0] 退出系统");
    System.out.print("请选择操作：");
    int choice = scanner.nextInt();
    System.out.println("您的选择是: " + choice);
    // 后续处理
    System.out.println("后续处理逻辑。。。");
}
```
显然，上面这段代码，用户只有一次输入机会。为提高程序可用性，如果用户输入的不是数字，需要重新输入直到输入数字为止。因此考虑加上循环，修改后的代码如下：

```java
protected void processOption() {
    while(true) {
        System.out.print("请选择操作：");
        int choice = scanner.nextInt();
        System.out.println("您的选择是: " + choice);
        if (choice >=0 && choice <=2) {
           // 输入的选项合法，跳出循环
           break;
        }
    }
    // 后续处理
    System.out.println("后续处理逻辑。。。");
}
```
如果用户正常输入数字，则没有问题。否则，若输入字母、其他字符等，则直接抛出运行时异常`InputMismatchException`，导致程序异常终止。
```
请选择操作：试试水
客户端关闭！
Exception in thread "main" java.util.InputMismatchException
	at java.base/java.util.Scanner.throwFor(Scanner.java:939)
	at java.base/java.util.Scanner.next(Scanner.java:1594)
	at java.base/java.util.Scanner.nextInt(Scanner.java:2258)
	at java.base/java.util.Scanner.nextInt(Scanner.java:2212)
	at homework1.client.ClientView.processOption(ClientView.java:39)
	at homework1.client.ClientBaseView.mainPage(ClientBaseView.java:49)
	at homework1.test.ClientTest.main(ClientTest.java:23)

Process finished with exit code 1
```
为避免因为这么一个小小错误引起程序终止，增加`try-catch`异常捕获：
```java
protected void processOption() {
    while(true) {
        try {
            System.out.print("请选择操作：");
            int choice = scanner.nextInt();
            System.out.println("您的选择是: " + choice);
            if (choice >=0 && choice <=2) {
                // 输入的选项合法，跳出循环
                break;
            }
        } catch (InputMismatchException e) {
            System.out.println("输入错误！请输入数字选项。");
        }
    }
    // 后续处理
    System.out.println("后续处理逻辑。。。");
}
```
输入字符串进行测试，发现程序并未如预期般循环等待输入，而是直接陷入死循环：
```
请选择操作：输入错误！请输入数字选项。
请选择操作：输入错误！请输入数字选项。
请选择操作：输入错误！请输入数字选项。
请选择操作：输入错误！请输入数字选项。
请选择操作：输入错误！请输入数字选项。
请选择操作：输入错误！请输入数字选项。
...
```
#### 问题原因分析
为什么呢？翻一下`Scanner`的源码，发现`nextInt()`方法在识别到非数字字符串时，会抛出这个`Input`异常，并且在抛出异常前刻意把缓冲区指针设置到本字符串的开头位置。
```java
// java.util.Scanner.java 
    public int nextInt(int radix) {
        // Check cached result
        if ((typeCache != null) && (typeCache instanceof Integer)
            && this.radix == radix) {
            int val = ((Integer)typeCache).intValue();
            useTypeCache();
            return val;
        }
        setRadix(radix);
        clearCaches();
        // Search for next int
        try {
            String s = next(integerPattern());
            if (matcher.group(SIMPLE_GROUP_INDEX) == null)
                s = processIntegerToken(s);
            return Integer.parseInt(s, radix);
        } catch (NumberFormatException nfe) {
            position = matcher.start(); // don't skip bad token
            throw new InputMismatchException(nfe.getMessage());
        }
    }
```
重点看最后三行：
```
} catch (NumberFormatException nfe) {
    position = matcher.start(); // don't skip bad token
    throw new InputMismatchException(nfe.getMessage());
}
```
也就是说，在我们的程序中，如果用`nextInt()`接收一个非数字字符串输入，字符串并不会被**消费**掉。下次再接收时，仍然会读取到跟上次一样的字符串。这样程序就陷入了死循环。

#### 处理方案一：把错误字符串消费掉
`Scanner`没有帮我们做的事，我们可以自己做。在`Catch`代码块里调用一下`next()`方法，把错误字符串消费掉即可。代码如下：
```java
protected void processOption() {
    while(true) {
        try {
            System.out.print("请选择操作：");
            int choice = scanner.nextInt();
            System.out.println("您的选择是: " + choice);
            if (choice >=0 && choice <=2) {
                // 输入的选项合法，跳出循环
                break;
            }
        } catch (InputMismatchException e) {
            System.out.println("输入错误！请输入数字选项。");
            scanner.next(); // 增加这一句，消费掉输入错误的字符串
        }
    }
    // 后续处理
    System.out.println("后续处理逻辑。。。");
}
```
执行效果如下：
```
请选择操作：xasx
输入错误！请输入数字选项。
请选择操作：afdsfas
输入错误！请输入数字选项。
请选择操作：1
您的选择是: 1
后续处理逻辑。。。
```
解决！

#### 处理方案二：nextInt()换成next()
既然`nextInt()`方法有这些毛病，我们也可以放弃它，改用`next()`方法接收到字符串，然后自己针对字符串做处理。 代码如下：
```java
protected void processOption() {
    while(true) {
        System.out.print("请选择操作：");
        String temp = scanner.next();
        int choice = -1;
        boolean match = temp.matches("[0-9]+");
        if (match) {
            int choice = Integer.parseInt(temp);
            System.out.println("您的选择是: " + choice);
            if (choice >=0 && choice <=2) {
                // 输入的选项合法，跳出循环
                break;
            } else {
                continue;
            }
        } else {
            System.out.println("输入错误！请输入数字选项。");
            continue;
        }
    }
    // 后续处理
    System.out.println("后续处理逻辑。。。");
}
```
#### 小结
两种方案都可以处理掉`while(true) + nextInt()`导致的`InputMismatchException`死循环问题，相对来说，第一种方案代码更简洁一些。