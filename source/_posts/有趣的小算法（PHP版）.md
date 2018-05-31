---
title: 有趣的小算法（PHP版）
date: 2018-05-28 17:59:23
tags: 原创
categories: PHP
---

本文主要记录一些在工作、生活中遇到的有趣的算法，旨在用简单的代码给出过程和答案（基于目前编程水平的理解），不定期更新。

<!--more-->

### 打印函数封装

为打印数据方便，简单自定义一个打印函数。

```php
/**
 * format and output array,string, supporting objects
 *
 * @param   mixed   $print
 * @param   string  $show
 * @param   bool    $need
 */
function pp($print,$show = 'pp',$need = false){

    echo '<div style="border: 1px solid bisque;border-bottom-color:red;border-right-color:red;color:green;background-color: bisque;"><pre>';
    if($need && is_object($print)){
        $print = json_decode($print);
    }
    if($show === 'pp'){
        print_r($print);
    }
    if($show === 'var'){
        var_dump($print);
    }
    echo '</pre></div>';
    
}
```


### “约瑟夫手环”

#### 故事背景

一群人排成一圈，按1,2,…,30依次编号。然后从第1个开始数，数到第7个把他踢出圈，从他后面再开始数，再数到第7个，在把他踢出去，如此不停的进行下去，直到最后个剩下一个人为止，求出最后剩下的那个人是谁。


#### 代码

```php
$arr = range(1,30);
$num = 0;
while(count($arr) !== 1){
    foreach($arr as $k => $v){
        $num++;
        if($num % 7 === 0){
            unset($arr[$k]);
            $num = 0;
        }
    }
}

pp($arr);
```

#### 思路说明

1. 解题关键点是不能被编号为1到30的人所迷惑，最直接的办法就是如上：设置一个变量，让他从零自增，自增到7再重新赋值为0，每次这个变量的值为7的时候，便把数组中循环了7次时对应的键删掉，核心逻辑就这么多；
2. `unset($arr[$k]);`不能替换成`unset($v)`，`unset($v)`这只是把$v这个数字删除掉，但并没有把数组中这个值对应的键删掉。 

#### 封装

```php
function theLastOne($peopleCount,$remove){
    $arr = range(1,$peopleCount);
    $num = 0;
    while(count($arr) !== 1){
        foreach($arr as $k => $v){
            $num++;
            if($num % $remove === 0){
                unset($arr[$k]);
                $num = 0;
            }
        }
    }
    return $arr;
}

pp(theLastOne(30,7));
```


### 关于分钱的问题

#### 问题描述
将10000块钱分成5份，且每一份之间的相差的值$d不一样，并且相差$d得大于100。

#### 代码及调用
```php
// 判断数组内是否有差值相等
function check_diff($arr){
    if(empty($arr)){
        return false;
    }
    
    $arr = array_map('intval',$arr);
    sort($arr);
    
    $count    = count($arr);
    $diff_arr = array();
    for($i = $count - 1;$i >= 0;$i--){
        for($j = 0;$j < $count;$j++){
            if($arr[$i] <= $arr[$j]){
                continue 2;
            }
            $diff_arr[] = $arr[$i] - $arr[$j];
        }
    }
    
    return count($diff_arr) === count(array_unique($diff_arr));
}

// 主要处理逻辑
function process($sum = 10000,$diff = 100,$count = 5){
    
    $return = array();
    $i      = 1;
    while($i < $count){
        // 随机数不符合要求时的结果初始化
        if(isset($return[$i]) || isset($return[$i + 1])){
            unset($return[$i],$return[$i + 1]);
        }
        $remain_sum   = $sum - array_sum($return);      // 剩余数
        $remain_count = ($count - count($return));     // 剩余需要随机的个数
        $min_diff     = $remain_count * ($remain_count - 1) * (2 * ($remain_count - 1) + 4) / 12 + ($remain_count * ($remain_count - 1) / 2) * $diff;// 高斯求最小差值和，分两部分：S = n(n+1)(2n+4)/12 + $diff * ((n-1)(n-2)/2)
        $min          = $i === 1 ? 1 : $return[$i - 1] + $diff + 1; // 随机范围的最小值
        mt_srand((double)microtime() * 1000000);// 在范围内获取随机数
        $rand_max   = (int)(($remain_sum - $min_diff) / $remain_count); // 随机范围的最大值
        $min        = random_int($min,$rand_max);   // 产生随机数
        $return[$i] = $min;     // 放入返回的数组
        if($i === $count - 1){
            $return[$i + 1] = $remain_sum - $min;   // 最后一个随机数直接取总数减去前面的随机数
        }
        if(check_diff($return) === false){
            $i--;
            continue;
        }
        $i++;
    }
    
    return $return;
}

pp(process());
```

#### 思路说明
[问题链接](https://segmentfault.com/q/1010000015064381)

<div class="text-right" style="font-size:12px;color:gray">*更新时间：2018-05-31 15:30*</div>
