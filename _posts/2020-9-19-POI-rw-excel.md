---
layout: post
title: POI读写excel
tags: POI读写excel
categories: backends
---

依赖、读取文件、创建并写入文件

**依赖**

```xml
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi-ooxml</artifactId>
            <version>4.1.2</version>
        </dependency>
```

---

**读取文件**

```java

import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.xssf.usermodel.XSSFRow;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.junit.jupiter.api.Test;

import java.io.IOException;

public class Test1 {

    @Test
    public void test1() throws IOException {//第一种读取方式
        //获取到工作簿
        XSSFWorkbook xssfWorkbook = new XSSFWorkbook ("C:\\Users\\admin\\Desktop\\视频目录.xlsx");

        //读取第一张sheet
        XSSFSheet sheet=xssfWorkbook.getSheetAt (0);

        //遍历sheet的每一行，每一行的每一列
        for(Row row:sheet){
            for (Cell cell:row){
                System.out.println (cell.getStringCellValue ( ));//输出每个单元格的内容
            }
        }
    }

    @Test
    public void test2() throws IOException {//第二种读取方式
        //获取到表格
        XSSFWorkbook xssfWorkbook = new XSSFWorkbook ("C:\\Users\\admin\\Desktop\\视频目录.xlsx");

        //读取第一张sheet
        XSSFSheet sheet=xssfWorkbook.getSheetAt (0);

        int max_row_num=sheet.getLastRowNum ();//获取到最后一行

        for (int i = 0; i <max_row_num ; i++) {//遍历到最后一行
            XSSFRow row=sheet.getRow (i);//当前行

            if(row != null){
                int max_col_num=row.getLastCellNum ();//获取到最后一列

                for (int j = 0; j <max_col_num ; j++) {//每一行遍历到最后一列
                    Cell cell=row.getCell (j);

                    if(cell != null){
                        System.out.println (cell.getStringCellValue ( ));
                    }

                }
            }
        }
    }


}


```

> 注意
>
> java将表格读取后，会自动将表格内的数据转换成对应的类型。如11位以内的数字会别转换城数字类型，超过11位的会转换成字符串类型。手机号11位，会被识别城数字，应该在读取前通过`cell.setCellType (CellType.STRING)`将单元格设置位字符串类型，再进行读取。
>
> ---

**创建并写入文件**

```java
//省略部分代码
    @Test
    public void testwrite() throws IOException {
        //创建工作簿
        XSSFWorkbook workbook=new XSSFWorkbook ();
        //创建表格
        XSSFSheet sheet=workbook.createSheet ("sheet1");
        //创建第一行
        XSSFRow row1= (XSSFRow) sheet.createRow (0);//注意，从0开始
        
        //创建字体样式
        Font font=workbook.createFont();
        font.setColor (IndexedColors.BLUE.getIndex ());

        //设置字体样式
        XSSFCellStyle style=workbook.createCellStyle();
        style.setFont(font);
        
        //设置背景
        style.setFillForegroundColor(IndexedColors.RED.getIndex());
        style.setFillPattern(FillPatternType.SOLID_FOREGROUND);
        
        //第一行写入数据
        row1.createCell (0).setCellValue ("第一列");//注意，从0开始
        row1.createCell (1).setCellValue ("第二列");
        row1.getCell(1).setCellStyle(style);//给单元格设置样式

        //表格通过流写入到硬盘上
        FileOutputStream fileOutputStream=new FileOutputStream ("C:\\Users\\admin\\Desktop\\新建表格.xlsx");
        workbook.write (fileOutputStream);
        fileOutputStream.flush ();

        //释放资源
        workbook.close ();
        fileOutputStream.close ();
    }
```

