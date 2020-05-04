---
title: 小议编码时for循环和while循环的选择
date: 2020-05-04 15:15:32
tags:
- Java
---

大家都知道for循环和while循环是等价的，我们平时写代码既可以用for循环也可以用while循环，正所谓条条大路通罗马。可是，既然java选择保留了这两种循环，至少意味着它们在使用场合上还是有一些侧重的。先上个例子：
** 猜数字游戏：系统先生成一个1-100之间的随机数(不让用户知道)，然后让用户来猜测输入的数字是几，如果用户输入正确则游戏结束，否则，给出大于、小于的提示，继续让用户输入。**
这个题目，如果用for循环来写，大概是这样的：
```java
    public static void guessNumberUsingFor() {
        System.out.println("-----猜数字游戏开始------");
        int givenNumber = new java.util.Random().nextInt(100) + 1;
        java.util.Scanner scanner = new java.util.Scanner(System.in);
        System.out.println("请猜一个数字：");
        for (int inputNumber = scanner.nextInt(); inputNumber != givenNumber; inputNumber = scanner.nextInt()) {
            System.out.println((inputNumber < givenNumber) ? "猜小了,再猜：" : "猜大了，再猜：");
        }
        System.out.println("啊哈，猜对了！");
        System.out.println("-----猜数字游戏结束------");
    }
```
用while循环的版本如下：
```java
    public static void guessNumberUsingWhile() {
        System.out.println("-----猜数字游戏开始------");
        int givenNumber = new java.util.Random().nextInt(100) + 1;
        java.util.Scanner scanner = new java.util.Scanner(System.in);
        System.out.println("请猜一个数字：");
        int inputNumber = scanner.nextInt();
        while (inputNumber != givenNumber) {
            System.out.println((inputNumber < givenNumber) ? "猜小了,再猜：" : "猜大了，再猜：");
            inputNumber = scanner.nextInt();
        }
        System.out.println("啊哈，猜对了！");
        System.out.println("-----猜数字游戏结束------");
    }
```
上面这个例子，for版本似乎没有while版本顺滑，症结在于for循环版本把一些比较**重**的赋值、更新语句（这里是`scanner.nextInt()`）揉进了for循环的变量初始化和更新值的过程中，显得有违常识，影响了代码的可读性。对于习惯了`for(int i=0; i < 10; i++) {}`这样的人来说，极有可能会忽视掉`inputNumber = scanner.nextInt()`这条最重要的业务处理语句。而while版本，判断逻辑非常清晰，读起来一气呵成，没有任何认知障碍。

再看第二个例子：
** 计算1到100的整数之和 **
for循环版本如下：
```java
    public static void sumUsingFor() {
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            sum += i;
        }
        System.out.println("sum = " + sum);
    }
```
while循环版本为：
```java
    public static void sumUsingWhile() {
        int sum = 0;
        int i = 1;
        while (i <= 100) {
            sum += i;
            i++;
        }
        System.out.println("sum = " + sum);
    }
```
这个例子中，for循环的优势就比较明显了。首先，单单从代码行数上来讲，for循环7行，while循环需要9行，少了两行代码。其次，while循环里的`i++;`语句，稍微不仔细的话就可能忘记写了，造成无限循环！而且这错误属于逻辑错误，编译器也帮不了你。

从上面两个例子可以看出，选择合适的循环语句是需要费点脑子的。那么，到底什么时候该用for，什么时候适合用while，有没有什么规矩呢？有的。
** 一般来说，while循环更适合于明确循环条件而不明确循环次数的场合，for循环则更适合于明确循环次数或范围的场合。 **