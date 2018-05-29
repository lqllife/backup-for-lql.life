---
title: 国际坐标转换
date: 2018-04-28 10:25:44
tags: 原创
categories: PHP
---

工作中用到国际坐标转百度坐标的方法，根据百度地图提供的api，坐标的转换只能用官方提供的api来进行坐标转换，因数据量大，用接口请求数据会导致数据不能及时处理，所以在网上找了好久，再加上同事的帮助和自己的整理，终于整理出PHP可用的坐标转换。

<!--more-->


### 类文件 Position.php

```php

<?php

Class BaiduCoordinate{
    
    private $x_PI = 3.14159265358979324 * 3000.0 / 180.0;
    private $PI   = 3.1415926535897932384626;
    private $a    = 6378245.0;
    private $ee   = 0.00669342162296594323;
    
    
    public function gcj02tobd09($a,$b){
        $confir = $this -> wgs84togcj02($a,$b);
        [$a,$b] = $confir;
        $z      = sqrt($a * $a + $b * $b) + 0.00002 * sin($b * $this -> x_PI);
        $theta  = atan2($b,$a) + 0.000003 * cos($a * $this -> x_PI);
        $gb     = number_format($z * cos($theta) + 0.0065,6);
        $ga     = number_format($z * sin($theta) + 0.006,6);
        
        return [$gb,$ga];
    }
    
    public function wgs84togcj02($lng,$lat){
        
        $lng = $this -> latlng($lng);
        $lat = $this -> latlng($lat);
        
        if($this -> out_of_china($lng,$lat)){
            return [$lng,$lat];
        }else{
            $dlat      = $this -> transformlat($lng - 105.0,$lat - 35.0);
            $dlng      = $this -> transformlng($lng - 105.0,$lat - 35.0);
            $radlat    = $lat / 180.0 * $this -> PI;
            $magic     = sin($radlat);
            $magic     = 1 - $this -> ee * $magic * $magic;
            $sqrtmagic = sqrt($magic);
            $dlat      = ($dlat * 180.0) / (($this -> a * (1 - $this -> ee)) / ($magic * $sqrtmagic) * $this -> PI);
            $dlng      = ($dlng * 180.0) / ($this -> a / $sqrtmagic * cos($radlat) * $this -> PI);
            $mglat     = $lat + $dlat;
            $mglng     = $lng + $dlng;
            
            return [$mglng,$mglat];
        }
    }
    
    public function out_of_china($lng,$lat){
        // 纬度3.86~53.55,经度73.66~135.05
        return !($lng > 73.66 && $lng < 135.05 && $lat > 3.86 && $lat < 53.55);
    }
    
    public function transformlng($lng,$lat){
        $ret = 300.0 + $lng + 2.0 * $lat + 0.1 * $lng * $lng + 0.1 * $lng * $lat + 0.1 * sqrt(abs($lng));
        $ret += (20.0 * sin(6.0 * $lng * $this -> PI) + 20.0 * sin(2.0 * $lng * $this -> PI)) * 2.0 / 3.0;
        $ret += (20.0 * sin($lng * $this -> PI) + 40.0 * sin($lng / 3.0 * $this -> PI)) * 2.0 / 3.0;
        $ret += (150.0 * sin($lng / 12.0 * $this -> PI) + 300.0 * sin($lng / 30.0 * $this -> PI)) * 2.0 / 3.0;
        
        return $ret;
    }
    
    public function transformlat($lng,$lat){
        $ret = -100.0 + 2.0 * $lng + 3.0 * $lat + 0.2 * $lat * $lat + 0.1 * $lng * $lat + 0.2 * sqrt(abs($lng));
        $ret += (20.0 * sin(6.0 * $lng * $this -> PI) + 20.0 * sin(2.0 * $lng * $this -> PI)) * 2.0 / 3.0;
        $ret += (20.0 * sin($lat * $this -> PI) + 40.0 * sin($lat / 3.0 * $this -> PI)) * 2.0 / 3.0;
        $ret += (160.0 * sin($lat / 12.0 * $this -> PI) + 320 * sin($lat * $this -> PI / 30.0)) * 2.0 / 3.0;
        
        return $ret;
    }
    
    public function latlng($lat){
        $lat_val = floor($lat / 100);
        
        return (($lat / 100) - $lat_val) * 100 / 60 + $lat_val;
    }
    
}
```

### 封装

```php
/**
 * 离线百度坐标转换
 * @param $gpsx
 * @param $gpsy
 * @return string
 */
function gpsPosition($gpsx,$gpsy){
    require 'Position.php';
    $BaiduCoordinate = new BaiduCoordinate();
    $gpsArr = $BaiduCoordinate -> gcj02tobd09($gpsx,$gpsy);
    return implode(',',$gpsArr);
}
```

### 测试

```php
$gpsx   = '11712.7609';
$gpsy   = '3150.1322';
$result = gpsPosition($gpsx,$gpsy);
var_dump($result);
```

### 说明

1. [坐标系说明](http://lbsyun.baidu.com/index.php?title=coordinate)
2. [百度坐标转换API](http://lbsyun.baidu.com/index.php?title=webapi/guide/changeposition)