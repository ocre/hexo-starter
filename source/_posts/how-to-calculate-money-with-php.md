---
title: PHP计算货币金额的方法
date: 2020-04-24 17:59:08
tags:
- PHP
---

涉及到计算金额，PHP提供了两类方法。一类是使用`floor()`和`round()`进行四舍五入，还有一类是以`bc`打头的一系列函数。

### 使用floor() 和 round()计算金额
```php
//计算折扣
$value = '9.95';//折扣
$money = '39.555';//原始价格，当然到这一步的价格，一般都是小数点后两位的，此处保留三位，主要是为了对比
echo '原始价格：'. $money * ($value/10);
echo '<pre>';
echo '直接四舍五入：'.round($money * ($value / 10),2);
echo '<pre>';
echo '截取小数点后1位：'.floor($money * $value)/10;
echo '<pre>';
echo '截取小数点后2位'.floor($money * ($value / 10) * 100)/100;
die;
```

### 使用bc类函数计算金额
```php
/**
 * PHP精确计算  主要用于货币的计算用
 * @param string $n1 第一个数
 * @param string $symbol 计算符号 + - * / %
 * @param string $n2 第二个数
 * @param string $scale  精度 默认为小数点后两位
 * @return  string
 */
function price_calc($n1, $symbol, $n2, $scale = '2')
{
    $res = "";
    switch ($symbol) {
        case "+"://加法
            $res = bcadd($n1, $n2, $scale);
            break;
        case "-"://减法
            $res = bcsub($n1, $n2, $scale);
            break;
        case "*"://乘法
            $res = bcmul($n1, $n2, $scale);
            break;
        case "/"://除法
            $res = bcdiv($n1, $n2, $scale);
            break;
        case "%"://求余、取模
            $res = bcmod($n1, $n2, $scale);
            break;
        default:
            $res = "";
            break;
    }
    return $res;
}
```
