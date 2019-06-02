title: 通过 Java 将 Excel 中信息转存入 Navicat
author: zansy
tags: []
categories:
  - 唯有爱与工作不可辜负
date: 2018-12-04 00:23:00
---
通过引入poi，解析 workbook 视图，获取 Excel 工作簿中信息；再通过简单的正则表达式等字符串处理 将其中信息存入 Navicat 中的表。
<!--more-->

### 原始数据
![Excel 中数据-目录](/images/pasted-13.png)
![Excel 中数据-具体表](/images/pasted-14.png)
![目标表“元数据表信息表”表设计](/images/pasted-16.png)
![目标表“元数据表字段信息表”表设计](/images/pasted-15.png)

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
public void InsertExcel(String path) {
        String fileType = path.substring(path.lastIndexOf(".") + 1);
        //读取excel文件
        InputStream is = null;
        MdTableInfo mdTableInfo = new MdTableInfo();
        try {
            is = new FileInputStream(path);
            //获取工作薄
            Workbook wb = null;
            if (fileType.equals("xls")) {
                wb = new HSSFWorkbook(is);
            } else if (fileType.equals("xlsx")) {
                wb = new XSSFWorkbook(is);
            } else {
                return ;
            }

            //读取第一个工作页sheet
            Sheet menuSheet = wb.getSheetAt(0);
            for (Row row : menuSheet) {
                String line = "";
                for (Cell cell : row) {
                    cell.setCellType(Cell.CELL_TYPE_STRING);
                    line +=cell.getStringCellValue()+",";//读取每一行
                }
                //分解读取的每一行并存储于表对象中
                String[] parts = line.split(",");
                mdTableInfo.setSeqnum(Integer.valueOf(parts[0].trim()));
                String Guid = UtilHelper.makeGUID();
                mdTableInfo.setId(Guid);
                mdTableInfo.setCid("AA");
                mdTableInfo.setEname(parts[1]);
                mdTableInfo.setCname(parts[2]);
                //目标是将元数据表信息表中的 ID 作为字段信息表中的 TID 进行联结，因此设定一个Map存储中文表名和ID
                Map<String,String> guidCname = new HashMap<>();
                guidCname.put(parts[1],Guid);
                mdTableInfo.setStatus(1);
                service.insert(mdTableInfo);//插入该对象
                //根据每一个对象的表名，读取 Excel 工作簿中以该表名为标题的工作表
                Sheet singleSheet = wb.getSheet(parts[1]);
                //Sheet singleSheet = wb.getSheet("CBEC_ORDER_CHECK_UPDATE");
                for(Row subRow : singleSheet){
                    if(subRow.getRowNum()>2){//跳过开头的信息行
                        String line2 = "";
                        MdTableFields mdTableFields = new MdTableFields();
                        for (Cell cell : subRow) {
                            cell.setCellType(Cell.CELL_TYPE_STRING);
                            line2 +=cell.getStringCellValue()+"~";
                        }
                        String[] parts2 = line2.split("~");
                        mdTableFields.setId(UtilHelper.makeGUID());
                        mdTableFields.setTid(guidCname.get(singleSheet.getSheetName()));
                        mdTableFields.setSeqnum(Integer.valueOf(parts2[0].trim()));
                        mdTableFields.setEname(parts2[1]);
                        //将VARCHAR2(64)等类型通过字符串处理 简单地存储于表对象的不同属性中
                        String[] typeLength = parts2[3].split("\\(");
                        mdTableFields.setType(typeLength[0]);
                        String[] lengthScale = typeLength[1].split(",");
                        mdTableFields.setLength(Long.valueOf(lengthScale[0].split("\\)")[0].trim()));
                        if(lengthScale.length>1){
                            mdTableFields.setScale(Integer.valueOf(lengthScale[1].split("\\)")[0].trim()));
                        }
                        //开始处理“唯一主键”等描述信息，分别存储于表对象的“字段中文名”和“备注”属性中
                        if(parts2.length>5){
                            //通过简单的正则表达式，提取出关键信息
                            Pattern pattern = Pattern.compile("^[a-zA-Z0-9\\u4E00-\\u9FA5]+");
                            Matcher matcher = pattern.matcher(parts2[6]);
                            if (matcher.find()) {
                                mdTableFields.setCname(matcher.group());
                                if(parts2[6].split("^[a-zA-Z0-9\\u4E00-\\u9FA5]+").length>1){
                                    String other = parts2[6].split("^[a-zA-Z0-9\\u4E00-\\u9FA5]+")[1];
                                    //去掉描述截取后的冗余字符
                                    if(other.charAt(0) == '。'||other.charAt(0) == '，'||other.charAt(0) == ',')
                                        mdTableFields.setRemarks(String.valueOf(other.subSequence(1,other.length())));
                                    else{
                                        if(other.charAt(0) == '('){
                                            int lastBracket = other.lastIndexOf(')');
                                            StringBuilder deleteFirst = new StringBuilder(String.valueOf(other.subSequence(1,other.length())));
                                            if(lastBracket != -1)
                                                mdTableFields.setRemarks(String.valueOf(deleteFirst.replace(lastBracket-1,lastBracket," ")));

                                            else
                                                mdTableFields.setRemarks(String.valueOf(deleteFirst));
                                        }
                                        else if(other.charAt(0) == '（'){
                                            int lastBracket = other.lastIndexOf('）');
                                            StringBuilder deleteFirst = new StringBuilder(String.valueOf(other.subSequence(1,other.length())));
                                            if(lastBracket != -1)
                                                mdTableFields.setRemarks(String.valueOf(deleteFirst.replace(lastBracket-1,lastBracket," ")));

                                            else
                                                mdTableFields.setRemarks(String.valueOf(deleteFirst));
                                        }
                                    }
                                }
                            }
                            else{
                                mdTableFields.setCname(parts2[6]);
                            }
                        }
                        mdTableFields.setStatus(1);
                        service.insert(mdTableFields);//插入 Navicat 数据库中
                    }
                }

            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (is != null) is.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

### 最终效果
![数据库中数据](/images/pasted-17.png)