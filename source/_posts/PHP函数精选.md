---
title: PHP方法精选
date: 2017-10-16 15:48:00
tags: 原创
categories: PHP
---

**这些都是工作中用到的比较常用或者有意思的PHP方法，持续更新中...**
<!--more-->

### 显示彩虹字符串
```php
function color_txt($str){
    $len        = mb_strlen($str);
    $colorTxt   = '';
    for($i=0; $i<$len; $i++) {
        $colorTxt .=  '<span style="color:'.rand_color().'">'.mb_substr($str,$i,1,'utf-8').'</span>';
    }
    return $colorTxt;
}
function rand_color(){
    return '#'.sprintf("%02X",mt_rand(0,255)).sprintf("%02X",mt_rand(0,255)).sprintf("%02X",mt_rand(0,255));
}

echo color_txt('asdfwerwtertsadfsadfwtwer');
```

### 对查询结果集进行排序
```php
/**
 +----------------------------------------------------------
 * 对查询结果集进行排序
 +----------------------------------------------------------
 * @param array $list 查询结果
 * @param string $field 排序的字段名
 * @param string $sortby 排序类型
 * asc正向排序 desc逆向排序 nat自然排序
 +----------------------------------------------------------
 * @return array
 +----------------------------------------------------------
 */
function list_sort_by($list,$field, $sortby='asc') {
   if(is_array($list)){
       $refer = $resultSet = array();
       foreach ($list as $i => $data)
           $refer[$i] = &$data[$field];
       switch ($sortby) {
           case 'asc': // 正向排序
                asort($refer);
                break;
           case 'desc':// 逆向排序
                arsort($refer);
                break;
           case 'nat': // 自然排序
                natcasesort($refer);
                break;
       }
       foreach ( $refer as $key=> $val)
           $resultSet[] = &$list[$key];
       return $resultSet;
   }
   return false;
}
```

### 二维数组按指定的键值排序 
```php
/* 
* 二维数组按指定的键值排序 
* $array 数组
* $key排序键值
* $type排序方式
*/
function array_sort($arr, $keys, $type = 'desc') {
    $keysvalue = $new_array = array();
    foreach ($arr as $k => $v) {
        $keysvalue[$k] = $v[$keys];
    }
    if ($type == 'asc') {
        asort($keysvalue);
    } else {
        arsort($keysvalue);
    }
    reset($keysvalue);
    foreach ($keysvalue as $k => $v) {
        $new_array[$k] = $arr[$k];
    }
    return $new_array;
}
```

### php判断手机移动设备访问，返回true是移动设备
```php
function is_mobile(){
	// 如果有HTTP_X_WAP_PROFILE则一定是移动设备
    if (isset ($_SERVER['HTTP_X_WAP_PROFILE']))
    {
        return true;
    }
	// 如果via信息含有wap则一定是移动设备,部分服务商会屏蔽该信息
    if (isset ($_SERVER['HTTP_VIA']))
    {
	// 找不到为flase,否则为true
        return stristr($_SERVER['HTTP_VIA'], "wap") ? true : false;
    }
	// 脑残法，判断手机发送的客户端标志,兼容性有待提高
    if (isset ($_SERVER['HTTP_USER_AGENT']))
    {
        $clientkeywords = array (
            'nokia',
            'oppo',
            'xiaomi',
            'miui',
            'huawei',
            'coolpad',
            'sony',
            'ericsson',
            'mot',
            'samsung',
            'htc',
            'sgh',
            'lg',
            'sharp',
            'sie-',
            'philips',
            'panasonic',
            'alcatel',
            'lenovo',
            'iphone',
            'ipod',
            'blackberry',
            'meizu',
            'android',
            'netfront',
            'symbian',
            'ucweb',
            'windowsce',
            'palm',
            'operamini',
            'operamobi',
            'openwave',
            'nexusone',
            'cldc',
            'midp',
            'wap',
            'mobile'
        );
    // 从HTTP_USER_AGENT中查找手机浏览器的关键字
        if (preg_match("/(" . implode('|', $clientkeywords) . ")/i", strtolower($_SERVER['HTTP_USER_AGENT'])))
        {
            return true;
        }
    }
    // 协议法，因为有可能不准确，放到最后判断
    if (isset ($_SERVER['HTTP_ACCEPT']))
    {
        // 如果只支持wml并且不支持html那一定是移动设备
        // 如果支持wml和html但是wml在html之前则是移动设备
        if ((strpos($_SERVER['HTTP_ACCEPT'], 'vnd.wap.wml') !== false) && (strpos($_SERVER['HTTP_ACCEPT'], 'text/html') === false || (strpos($_SERVER['HTTP_ACCEPT'], 'vnd.wap.wml') < strpos($_SERVER['HTTP_ACCEPT'], 'text/html'))))
        {
            return true;
        }
    }
    return false;
}
```

### 将数组形式的字符串转成数组
```php
function strToArray(){
	//数组形式的字符串
	$arr = "array('name'=>'张三','age'=>'16')";
    $str = [];
    eval("\$str = ".$arr.'; ');
    pp($str); //$str即为数组
    pp($str['name']);
}
```
[参考链接](http://blog.csdn.net/aiku_de_yu/article/details/51037178)

### 取去数组中某age键值小于15的其他数组

```php
$arr = array(
    array('id' => 1,'age' => 11),
    array('id' => 2,'age' => 18),
    array('id' => 3,'age' => 16),
    array('id' => 4,'age' => 19),
    array('id' => 5,'age' => 20),
    array('id' => 6,'age' => 14),
);
$arrs = array();
foreach($arr as $key => $value){
    if($value['age'] < 15){
        $arrs[] = $value;   //$arrs即是结果
    }
}
pp($arrs);
```

