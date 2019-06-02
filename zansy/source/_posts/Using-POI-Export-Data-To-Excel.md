title: POI：特定数据导出为含格式的 Excel
author: zansy
tags:
  - 水
categories:
  - 唯有爱与工作不可辜负
date: 2019-01-22 08:23:00
---
配合其他方法使用，需求是将云平台中存储的特定格式表导出为Excel，其中特定表已处理为`List<Map<Object,Object>>`的数据，Map中 key 为列名，value 为其对应的列值。
<!--more-->

### 实现功能
1. 固定窗口冻结；
2. 标题背景颜色填充；
3. 不同字体格式设置；
4. 边框设置；
5. 根据不同标题设定不同对齐标准
6. 根据传入的格式参数，设定指定列名的列单元格式


### 随手测试代码
```
public class ExcelHelperTest {
    @Test
    public void test() {
        List<Map<Object,Object>> rows = new LinkedList<>();
        Map<Object,Object> content = new HashMap<>();
        content.put("性别","女");
        content.put("年龄","53");
        content.put("日期","2018-09-01");
        content.put("金额","20.65");
        rows.add(content);
        Map<Object,Object> content1 = new HashMap<>();
        content1.put("性别","男");
        content1.put("年龄","15");
        content1.put("日期","2018-10-01");
        content1.put("金额","240.05");
        rows.add(content1);
        Map<String,String> ftype = new HashMap<>();
        ftype.put("性别","");
        ftype.put("年龄","int");
        ftype.put("日期","date");
        ftype.put("金额","decimal");
        ExcelHelper.exportExcel("test.xls","title",rows,ftype);
        ExcelHelper.exportExcel("test.xlsx","title",rows,ftype);
    }
}
```
### 结果
![导出效果图](/images/pasted-10.png)

### Maven
```
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>3.16</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>3.14</version>
</dependency>
<dependency>
    <groupId>net.sourceforge.jexcelapi</groupId>
    <artifactId>jxl</artifactId>
    <version>2.6.10</version>
</dependency>
```

### 代码和简单注释
```
package com.itcn.utils;

import org.apache.poi.hssf.usermodel.HSSFFont;
import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFFont;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

import java.io.FileOutputStream;
import java.util.List;
import java.util.Map;

public class ExcelHelper {
    public static boolean exportExcel(String fileName, String title,
                                      List<Map<Object,Object>> dataset,Map<String,String> ftype)
    {
        boolean flag = false;
        Workbook workbook = null;
        Font titleFont = null;
        DataFormat dataFormat = null;
        if (fileName.endsWith("xlsx"))
        {
            workbook = new XSSFWorkbook();
            titleFont = (XSSFFont) workbook.createFont();//创建字体对象

        } else if (fileName.endsWith("xls"))
        {
            workbook = new HSSFWorkbook();
            titleFont = (HSSFFont) workbook.createFont();//创建字体对象
        } else
        {
            LogHelper.writeInfo("必须是xls或者xlsx结尾的文件.");
        }

        Sheet sheet = workbook.createSheet(title);
        sheet.createFreezePane( 0, 1, 0, 1 );//设置单元格冻结
        dataFormat = workbook.createDataFormat();
        CellStyle titleStyle = workbook.createCellStyle();//新建样式对象

        //列名
        Row row = sheet.createRow(0);
        row.createCell(0).setCellValue("序号");
        row.getCell(0).setCellStyle(ExcelHelper.getTitleStyle(titleStyle,titleFont));
        int i= 1;
        for(Object headers : dataset.get(0).keySet()){
            Cell cell = row.createCell(i);
            sheet.setColumnWidth(i, 5000);
            titleStyle = ExcelHelper.getTitleStyle(titleStyle,titleFont);
            cell.setCellStyle(titleStyle);
            cell.setCellValue(StringHelper.obj2String(headers));
            i++;
        }

        //内容
        for(i=1;i<=dataset.size();i++) {
            row = sheet.createRow(i);//第i行
            int j=1;//第i行的第j个单元格
            row.createCell(0).setCellValue(i);//第一个单元格添加行号
            row.getCell(0).setCellStyle(ExcelHelper.getContentStyle(workbook,"order"));

            for(Object key : dataset.get(i-1).keySet()){
                Cell cell = row.createCell(j);
                String keyString = key.toString();
                String valueString = dataset.get(i-1).get(key).toString();
                //设定单元格样式
                CellStyle contentStyle = ExcelHelper.getContentStyle(workbook,ftype.get(keyString));

                //判定列格式
                if(ftype.get(keyString).equals("")||ftype.get(keyString).equals("string")){

                    cell.setCellValue(valueString);//根据不同的值写入单元格
                    contentStyle.setDataFormat(dataFormat.getFormat("General"));
                    cell.setCellStyle(contentStyle);

                }else if(ftype.get(keyString).equals("int")){

                    cell.setCellValue(Integer.valueOf(valueString));
                    contentStyle.setDataFormat(dataFormat.getFormat("0"));//设定单元格格式
                    cell.setCellStyle(contentStyle);

                }else if(ftype.get(keyString).equals("decimal")){

                    cell.setCellValue(Double.valueOf(valueString));
                    contentStyle.setDataFormat(dataFormat.getFormat("0.00"));
                    cell.setCellStyle(contentStyle);

                }else if(ftype.get(keyString).equals("date")){

                    cell.setCellValue(valueString);
                    contentStyle.setDataFormat(dataFormat.getFormat("General"));
                    cell.setCellStyle(contentStyle);

                }
                j++;
            }
        }

        //开始一次性写入
        FileOutputStream fos;
        try
        {
            fos = new FileOutputStream(fileName);
            workbook.write(fos);
            fos.close();
            flag = true;
        } catch (Exception e)
        {
            LogHelper.writeErr(e);
            flag = false;
        }
        return flag;
    }

    //设定内容格式
    public static CellStyle getContentStyle(Workbook workbook,String dataType){
        Font contentFont = workbook.createFont();
        CellStyle contentStyle = workbook.createCellStyle();//新建样式对象

        contentFont.setBold(false);
        contentFont.setFontName("微软雅黑");
        contentStyle.setFont(contentFont);

        //设置边框
        contentStyle.setBorderTop(BorderStyle.THIN);
        contentStyle.setBorderBottom(BorderStyle.THIN);
        contentStyle.setBorderLeft(BorderStyle.THIN);
        contentStyle.setBorderRight(BorderStyle.THIN);

        contentStyle.setVerticalAlignment(VerticalAlignment.CENTER);

        switch (dataType){
            case "order":
                contentStyle.setAlignment(HorizontalAlignment.CENTER);
                break;
            case "string":
                contentStyle.setAlignment(HorizontalAlignment.LEFT);
                break;
            case "date":
                contentStyle.setAlignment(HorizontalAlignment.CENTER);
                break;
            case "int":
                contentStyle.setAlignment(HorizontalAlignment.RIGHT);
                break;
            case "decimal":
                contentStyle.setAlignment(HorizontalAlignment.RIGHT);
                break;
        }
        return contentStyle;
    }

    //设定标题样式
    public static CellStyle getTitleStyle(CellStyle titleStyle,Font titleFont){
        titleFont.setFontName("微软雅黑");
        titleFont.setBold(true);
        titleStyle.setFont(titleFont);
        //设置居中
        titleStyle.setVerticalAlignment(VerticalAlignment.CENTER);
        titleStyle.setAlignment(HorizontalAlignment.CENTER);

        //设置边框
        titleStyle.setBorderTop(BorderStyle.THIN);
        titleStyle.setBorderBottom(BorderStyle.THIN);
        titleStyle.setBorderLeft(BorderStyle.THIN);
        titleStyle.setBorderRight(BorderStyle.THIN);

        //设置单元格背景色
        titleStyle.setFillForegroundColor(IndexedColors.GREY_25_PERCENT.getIndex());
        titleStyle.setFillPattern(CellStyle.SOLID_FOREGROUND);

        return titleStyle;
    }
}

```

### 备注
还需要完成超链接功能，即点击 Excel 单元格中表名，可跳转到指定表名的 sheet 页中。
以及指定列名中，相同值的单元格合并操作。