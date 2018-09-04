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
        $clientkeywords = array ( 'nokia', 'oppo', 'xiaomi', 'miui', 'huawei', 'coolpad', 'sony', 'ericsson', 'mot', 'samsung', 'htc', 'sgh', 'lg', 'sharp', 'sie-', 'philips', 'panasonic', 'alcatel', 'lenovo', 'iphone', 'ipod', 'blackberry', 'meizu', 'android', 'netfront', 'symbian', 'ucweb', 'windowsce', 'palm', 'operamini', 'operamobi', 'openwave', 'nexusone', 'cldc', 'midp', 'wap', 'mobile' );
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

### 二维数组排序

```php
/**
 * 二维数组排序
 *
 * @param   array   $dataArr    要排序的数组
 * @param   string  $type       排序规则
 * @param   string  $field      根据某个键进行排序
 * @return  array
 */
function arraySort($dataArr,$type='SORT_ASC',$field){
    $sort = array(
        'direction' => $type,   //排序顺序标志 SORT_DESC 降序；SORT_ASC 升序
        'field'     => $field,  //排序字段
    );
    $arrSort = array();
    foreach($dataArr AS $uniqid => $row){
        foreach($row AS $key => $value){
            $arrSort[$key][$uniqid] = $value;
        }
    }
    if($sort['direction']){
        array_multisort($arrSort[$sort['field']],constant($sort['direction']),$dataArr);
    }
    return $dataArr;
}
```

### 二维数组去重

```php
/**
 * 二维数组去重
 *
 * @param   array   $arr    要去重的数组
 * @return array
 */
function more_array_unique($arr){
    $arr_inner_key = $temp = $arr_after = [];
    
    foreach($arr[0] as $k => $v){
        $arr_inner_key[] = $k;   //先把二维数组中的内层数组的键值记录在在一维数组中
    }
    
    foreach($arr as $k => $v){
        $v        = implode(',',$v);    //降维 用join()也行
        $temp[$k] = $v;      //保留原来的键值 $temp[]即为不保留原来键值
    }
    
    $temp = array_unique($temp);    //去重：去掉重复的字符串
    foreach($temp as $k => $v){
        $a             = explode(',',$v);   //拆分后的重组 如：Array( [0] => james [1] => 30 )
        $arr_after[$k] = array_combine($arr_inner_key,$a);  //将原来的键与值重新合并
    }
    
    //ksort($arr_after);//排序如需要：ksort对数组进行排序(保留原键值key) ,sort为不保留key值
    return array_values($arr_after);
}
```

### 过去某一天距今天多少年月日

```php
/**
 * 过去某一天距今天多少年月日
 *
 * @param   string  $date1
 * @param   string  $date2
 * @return array
 */
function diffDate($date1,$date2){
    if(strtotime($date1) > strtotime($date2)){
        $tmp   = $date2;
        $date2 = $date1;
        $date1 = $tmp;
    }
    list($Y1,$m1,$d1) = explode('-',$date1);
    list($Y2,$m2,$d2) = explode('-',$date2);
    $Y = $Y2 - $Y1;
    $m = $m2 - $m1;
    $d = $d2 - $d1;
    if($d < 0){
        $d += (int)date('t',strtotime("-1 month $date2"));
        $m--;
    }
    if($m < 0){
        $m += 12;
        $Y--;
    }
    return array('year' => $Y,'month' => $m,'day' => $d);
}
```


### 给字符串中每个元素加单引号

```php
/**
 * 给字符串中每个元素加单引号，用于数据库写入
 *
 * @example $str = 'a,b,1,2,3,45,g';  处理后 $str = "'a','b','1','2','3','45','g'";
 * @param   string  $str        需要处理的字符串
 * @param   string  $delimiter  分隔符
 * @return string
 */
function stringArray($str,$delimiter = ','){
    if($str !== ''){
        $arr = explode($delimiter,$str);
        $new = [];
        foreach($arr as $item){
            $new[] = "'" . $item . "'";
        }
        return implode($new,$delimiter);
    }else{
        return $str;
    }
}
```


### post请求实例

```php
/**
 * 卡斯达特指令下达
 * @param $ip    string  IP
 * @param $port  string  端口号
 * @param $card  string  车牌号
 * @param $devid string  设备ID（全小写）
 * @param $pwd   string  密码
 * @param $cmd   string  指令
 * @return array
 */
function carstart_send_order($ip,$port,$card,$devid,$pwd,$cmd){
    $url  = 'http://www.baidu.com';
    $data = array(
        'ip'        => $ip,
        'port'      => $port,
        'carNumber' => $card,
        'devideId'  => $devid,
        'password'  => $pwd,
        'cmd'       => $cmd,
    );
    $ch   = curl_init();
    curl_setopt($ch,CURLOPT_URL,$url);
    curl_setopt($ch,CURLOPT_RETURNTRANSFER,1);
    curl_setopt($ch,CURLOPT_CONNECTTIMEOUT,10);
    curl_setopt($ch,CURLOPT_POST,1);
    curl_setopt($ch,CURLOPT_POSTFIELDS,$data);
    $file_contents = curl_exec($ch);
    curl_close($ch);
    
    return $file_contents;
}
```

### 编码类函数

#### 检测字符编码类型

```php
function codeType($str){
    return mb_detect_encoding($str,array("ASCII",'UTF-8',"GB2312","GBK",'BIG5'));
}
```

#### 编码转换

```php
//gb2312转utf8
function gbkToUtf8($str){
    return iconv("GB2312","UTF-8",$str);
}

//utf8转gb2312
function utf8ToGbk($str){
    return iconv("UTF-8","GB2312",$str);
}

//mb_convert_encoding转编码
function mbGbkToUtf8($str,$turn = 1){
    switch($turn){
        case 0:
            $newStr = mb_convert_encoding($str,"UTF-8","GB2312");
            break;
        case 1:
            $newStr = mb_convert_encoding($str,"GB2312","UTF-8");
            break;
        default:
            $newStr = '参数错误';
    }
    return $newStr;
}
```

### 判断一个数组是否全部为空

```php

$arr = [
    ['a' => 1],
    [],
    [],
    ['b' => 2],
    [],
];
$felter = array_filter($arr);
echo count($felter) === 0 ? '全部为空' : '部分为空';

```
