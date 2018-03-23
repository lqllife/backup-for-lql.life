---
title: ThinkPHP用PHPExcle导出Excel文件
date: 2017-06-10 09:49:18
tags: [ThinkPHP,PHP,PHPExcel]
categories: 原创
---

** 本篇博客主要讲述，如何在thinkPHP中使用PHPExcel中导出Excel文件，是博主在实践了好多篇别人的帖子之后，总结归纳出来的文章，亲测可用。**

<!--more-->

## 准备工作

### 下载、配置PHPExcel扩展插件

1. 去[PHPExcel的官网](http://phpexcel.codeplex.com/)或者[github](https://github.com/PHPOffice/PHPExcel)去下载最新PHPExcel放到`ThinkPHP/Library/Vendor`目录下，`Vendor`目录是放置PHP扩展包的默认文件夹，在`Vendor`目录下建立`PHPExcel`文件夹，将下载的文件，解压到此处。

2. 将`PHPExcel.php`文件，重命名成`PHPExcel.class.php`。

   至此，插件安装已经做好，目录结构如下图，接下来做功能部分。

   ![Markdown](http://i4.piimg.com/585619/349788884a91c6b5.png)

## 创建Excel方法

### 生成Excel文件方法

``` php
/**
     * 数据导出为.xls格式
     * @param string $fileName 导出的文件名
     * @param $expCellName     array -> 数据库字段以及字段的注释
     * @param $expTableData    Model -> 连接的数据库
     */
    public function exportExcel($fileName='table',$expCellName,$expTableData){
        $xlsTitle = iconv('utf-8', 'gb2312', $fileName);//文件名称
        $xlsName = $fileName.date("_Y.m.d_H.i.s"); //or $xlsTitle 文件名称可根据自己情况设定
        $cellNum = count($expCellName);
        $dataNum = count($expTableData);

        import("Vendor.PHPExcel.PHPExcel");
        import("Vendor.PHPExcel.Writer.Excel5");
        import("Vendor.PHPExcel.IOFactory.php");

        $objPHPExcel = new \PHPExcel();
        $cellName = array('A','B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z','AA','AB','AC','AD','AE','AF','AG','AH','AI','AJ','AK','AL','AM','AN','AO','AP','AQ','AR','AS','AT','AU','AV','AW','AX','AY','AZ');

        $objPHPExcel->getActiveSheet(0)->mergeCells('A1:'.$cellName[$cellNum-1].'1');//合并单元格
        $objPHPExcel->setActiveSheetIndex(0)->setCellValue('A1', $fileName.'  Export time:'.date('Y-m-d H:i:s'));
        for($i=0;$i<$cellNum;$i++){
            $objPHPExcel->setActiveSheetIndex(0)->setCellValue($cellName[$i].'2', $expCellName[$i][1]);
        }
        // Miscellaneous glyphs, UTF-8
        for($i=0;$i<$dataNum;$i++){
            for($j=0;$j<$cellNum;$j++){
                $objPHPExcel->getActiveSheet(0)->setCellValue($cellName[$j].($i+3), $expTableData[$i][$expCellName[$j][0]]);
            }
        }

        header('pragma:public');
        header('Content-type:application/vnd.ms-excel;charset=utf-8;name="'.$xlsTitle.'.xls"');
        header("Content-Disposition:attachment;filename=$xlsName.xls");//attachment新窗口打印inline本窗口打印
        $objWriter = \PHPExcel_IOFactory::createWriter($objPHPExcel, 'Excel5');
        $objWriter->save('php://output');
        exit;
    }
```

### 方法生成说明

1. 第9行的

   `$xlsName = $fileName.date("_Y.m.d_H.i.s"); //or $xlsTitle 文件名称可根据自己情况设定`

   是设置文件名，上面所写是`文件名_Y.m.d_H.i.s`，`_Y.m.d_H.i.s`即是导出的时间，因为windows下，文件名中不能含有`:`，所以即便写成`_Y.m.d_H:i:s`，导出的文件也是`_Y.m.d_H.i.s`这种样式。

2. 第13行~15行的

   ```php
   import("Vendor.PHPExcel.PHPExcel");
   import("Vendor.PHPExcel.Writer.Excel5");
   import("Vendor.PHPExcel.IOFactory.php");
   ```

   这三行即是引入PHPExcel，因为当时的文件目录结构是`ThinkPHP/Library/Vendor/PHPExcel`，所以`import("Vendor.PHPExcel.PHPExcel");，注意目录结构，只要引入的位置和放置PHPExcel的目录相同即可；`

3. 第20、21行的

   ```php
   $objPHPExcel->getActiveSheet(0)->mergeCells('A1:'.$cellName[$cellNum-1].'1');//合并单元格
   $objPHPExcel->setActiveSheetIndex(0)->setCellValue('A1', $fileName.'  Export time:'.date('Y-m-d H:i:s'));
   ```

   此作用是，在Excel文件的第一行合并一行单元格，用作表格的标题或简介。如下图所示：

   ![Markdown](//upload-images.jianshu.io/upload_images/4950628-bb944595406f3826.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

   `setCellValue('A1', $fileName.'  Export time:'.date('Y-m-d H:i:s'));`值可以根据自己需求修改;

4. 第34行的
   `header("Content-Disposition:attachment;filename=$xlsName.xls");//attachment新窗口打印inline本窗口打印`

   可以设置输出的文件格式，此处写的是`.xls`，可修改成`.xlsx`。

5. 此方法调用时需要传入三个参数（在方法开头的注释中已经说了），这里再具体说明一下：
```
    $fileName     导出的文件的文件名
    $expCellName  数据库字段以及字段的注释（数组） 
    $expTableData 连接的数据库
```


## 方法调用
### 调用方法

```php
 /**导出**/
    public function msgOut(){
        $excel = A('Excel');
        $xlsCell = array(
            array('id', 'ID'),
            array('staff_name', '昵称'),
            array('staff_real', '真实姓名'),
            array('mobile', '手机号'),
            array('card_id', '身份证号'),
            array('referee', '推荐人'),
            array('game_id', '游戏ID'),
            array('money', '余额'),
            array('consume_coin', '消费币'),
            array('create_time', '注册时间'),
            array('status', '状态'),
        );
        $xlsName = 'Staff表搜索结果导出';
        $field = null;
        foreach ($xlsCell as $key => $value) {
            if($key == 0){
                $field = $value[0];
            }else{
                $field .= "," . $value[0];
            }
        }
        $xlsModel = M('Staff');
        if (IS_POST) {
            $map = $this -> _queryTime();
            $staff_name = I('staff_name');
            if($staff_name) {
                if (is_numeric($staff_name)) {
                    $map["id|staff_name"] = array(intval($staff_name), array('like', '%' . $staff_name . '%'), '_multi' => true);
                } else {
                    $map['staff_name'] = array('like', '%' . (string)$staff_name . '%');
                }
            }
            $xlsData = $xlsModel->Field($field)->where($map)->order('id DESC')->select();
        }
        foreach ($xlsData as $k => $v) {
            $xlsData[$k]['create_time'] = $v['create_time'] == null ? '-' : date("Y-m-d H:i",$v['create_time']);
            $xlsData[$k]['status'] = $v['status'] == 1 ? '正常' : '禁用';
        }
        $excel->exportExcel($xlsName,$xlsCell,$xlsData);
    }
```

### 调用说明

1. 第3行
  `$excel = A('Excel');`
  这个方法我写在了`Application/Admin/Controller/ExcelController.class.php`中，当前用的控制器是`MainController.class.php`，调用其他控制器中的方法，用TP中自带的`A()`，所以第51行，用的是`$excel->exportExcel($xlsName,$xlsCell,$xlsData);`，如果你的`exportExcel()`方法和当前调用的方法在同一个控制器内，那么第三行可以不写，第51行改成`$this->exportExcel($xlsName,$xlsCell,$xlsData);`。

2. 第4行
  `$xlsCell`是导出这张表中所有的字段，以及字段的注释，导出后，不能把字段作为表格的列名，不知道数据库字段是什么意思的用户，看了这张表也不知所以然。

3. 第17~24行
  是根据`$xlsCell`数组中，提取出来字段名，作为一个字符串，在查询时使用。当然也可以把这个字段名的字符串手写出来，我这里用的是foreach处理。

4. 第27~38行
  是查询条件和查询结果，如果没有查询条件，想把整张表导出的话，直接用` $xlsData = $xlsModel->Field($field)->order('id DESC')->select();`即可。

5. 第47~50行
  是对`$xlsData`结果进行处理，我写的两个意思是：
  1、当`create_time`字段为空的时候，值为 `-` ，有值的时候，就用`date()`函数把时间戳处理成日期；
  2、当`status`字段的值为1的时候，显示为`正常`,当值为0的时候`禁用`。

*有点罗嗦了，见谅。*

## 模板文件

模板文件很简单，就一个提交。

```html
<form action="{:U('Main/msgOut')}" method="post" class="form-horizontal">
    <div class="search-form fr cf">
        <div class="sleft">
            <input type="text" class="search-input" onClick="WdatePicker()" name="start_time" style="cursor: pointer;" value="{:I('start_time')}" id="start" placeholder="开始日期" />
            <input type="text" class="search-input" onClick="WdatePicker()" name="end_time" style="cursor: pointer;" value="{:I('end_time')}" id="end" placeholder="结束日期" />
            <input type="text" name="staff_name" class="search-input" value="{:I('staff_name')}" id="name" placeholder="输入推广专员的昵称或者ID">
            <button type="submit" class="btn" id="out">导出</button>
        </div>
    </div>
</form>
```

`form`表单中三个`input`，是搜索条件，如果没有的话，直接写`button`即可。

## 文章结束，欢迎转载。