title: “SQL语句分块解析”程序方法报告
author: zansy
tags: []
categories:
  - 唯有爱与工作不可辜负
date: 2018-09-02 14:01:00
---
实现了从数据库中读取特定表的存放SQL语句特定字段，分解SQL语句，提取出所需信息形成字段，并与“元数据表信息”表、“元数据表字段信息”表进行关联，形成新的“表信息映射”表。新表的字段分别为“表ID”、“表中文名”、“表英文名”、“字段ID”、“字段中文名”、“字段英文名”、“字段来源”、“字段处理过程”。
<!--more-->
## 成果
目前实现了从数据库中读取特定表的存放SQL语句特定字段，分解SQL语句，提取出所需信息形成字段，并与“元数据表信息”表、“元数据表字段信息”表进行关联，形成新的“表信息映射”表。新表的字段分别为“表ID”、“表中文名”、“表英文名”、“字段ID”、“字段中文名”、“字段英文名”、“字段来源”、“字段处理过程”。

### 测试样例：
 ![SQL语句表](/images/pasted-18.png)

在status为0的SQL语句字段批量读取、分析、存入成功后，重点设立了STATUS为2的SQL语句字段进行检验整个程序。
该字段完整内容：
```
INSERT OVERWRITE TABLE std_busi_event_zfqlyg
SELECT /*+mapjoin(c1,c2,c3,c4,c5,c6)*/ uuid(), a.CASEINFO_ID, '一般案卷', a.CASENUM, NULL
    , NULL, NULL, a.TYPECODE, a.TYPE1
    , tocname(a.TYPE1, c1.TYPENAME), a.TYPE2
    , tocname(a.TYPE2, c2.TYPENAME), a.TYPE3
    , tocname(a.TYPE3, c3.TYPENAME), a.TYPENAME
    , a.CURRSTATECODE, NULL, a.SRC_ID, a.SRCNAME, a.SUBSRC1
    , a.SUBSRC2, a.SUBSRC3, a.DEPARTMENT, a.PARTY, a.ISSUETIME
    , a.ADDRESS, a.REGION
    , CASE a.REGION
        WHEN 1 THEN '背街小巷'
        WHEN 2 THEN '次要道路'
        WHEN 3 THEN '主要道路'
        ELSE NULL
    END, a.REGIONLEVEL, a.UNDERTAKEPEOPLE, NULL, a.VIOLATELAW
    , a.PUNISHLAW, a.PUNISHDESC, a.DESCRIPTION, NULL, NULL
    , a.RECORDTIME, a.ISNOPUNISH
    , CASE a.ISNOPUNISH
        WHEN 0 THEN '处罚'
        WHEN 1 THEN '不予处罚'
        ELSE NULL
    END, a.PENALTYOVERLIMIT
    , CASE a.PENALTYOVERLIMIT
        WHEN 0 THEN '否'
        WHEN 1 THEN '处罚金额低于自由裁量标准'
        WHEN 2 THEN '处罚金额高于自由裁量标准'
        WHEN 3 THEN '自由裁量计算值与处罚金额不一致'
        ELSE NULL
    END, a.ENTERCURRPROTIME, a.EXPIREDATE, a.QUICKCASE
    , CASE a.QUICKCASE
        WHEN 0 THEN '否'
        WHEN 1 THEN '是'
        ELSE NULL
    END, a.ISNATUREPERSON, a.RECORDPEOPLE, a.GISX, a.GISY
    , a.CASESERIAL, a.PARTYNAME, a.PARTIESID, a.ZJCODE, NULL
    , tocname(a.caseinfo_id, c4.department)
    , tocname(a.caseinfo_id, c5.excuteresult)
    , tocname(a.caseinfo_id, c6.APPROVETIME)
    , tocname(a.caseinfo_id, c6.EXCUTETIME)
    , tocname(a.caseinfo_id, c5.CLOSECASEREPORTTIME)
    , tocname(a.caseinfo_id, c5.CLOSETIME)
FROM INFO_ZFQLYG_CASEINFO a
LEFT OUTER JOIN INFO_ZFQLYG_CASETYPE c1
ON a.type1 = c1.typecode
LEFT OUTER JOIN INFO_ZFQLYG_CASETYPE c2
ON a.type2 = c2.typecode
LEFT OUTER JOIN INFO_ZFQLYG_CASETYPE c3
ON a.type3 = c3.typecode
LEFT OUTER JOIN INFO_ZFQLYG_CASEUNDERTAKER c4
ON a.caseinfo_id = c4.caseinfo_id
LEFT OUTER JOIN INFO_ZFQLYG_CLOSECASE c5
ON a.caseinfo_id = c5.caseinfo_id
LEFT OUTER JOIN INFO_ZFQLYG_CLOSECASEREPORT c6
ON a.caseinfo_id = c6.caseinfo_id;
insert into table STD_OBJ_OTHER_CURB_AND_GUTTER
SELECT uuid(), a.NAME, a.ROAD_NAME, a.ORIGIN_INSERTION, a.LENGTH
       , a.MATERIAL, a.COUNT_NUM, a.ID, a.STATUS, a.POTENTIAL
       , a.START_TIME, a.UPDATE_TIME, a.FUNDS_PROVID, a.UPDATE_LIST, a.DEPT
       , a.PROPERTY, a.ADMINITRATION, a.MANAGEMENT, a.MAINTENANCE_UNIT, a.MANAGEMENT_UNIT_1
       , a.TRUSTEESHIP, a.SUPERVISION, a.TELEPHONE, a.CONTACTS, a.MAINTENANCE_CYCLE
       , a.CLEANING_INTERVAL, a.NOTICE_DEPT, a.COORDINATION_FIRST, a.COORDINATION_SENDCOND, a.REGION_INFO
       , a.STREET, a.COMMUNITY, a.GRID, a.POSITION_DESC, CAST(getlon(a.shape) AS DOUBLE)
       , CAST(getlat(a.shape) AS DOUBLE), 'GIS服务中心', '萧山道路平侧石'
FROM INFO_GIS_CURB_AND_GUTTER a;
```
### 最终映射表成果：
![映射表](/images/pasted-19.png)
 
## 存在问题
1.	测试样本量不多。
2.	有大量函数，但目前没有拿到函数清单，在代码中只检测设计了一个简单的TOCNAME的函数。在对函数的处理上存在不足。

## 代码思路：
### 数据库读取函数
因为一个SQL字段中可能有多个以“;”分割的SQL语句，因此设定了一个读取方法边分解边读：
```
public List<String> getSQLs(){
    List<TaskSqlInfo> all = service.getObjects(TaskSqlInfo.class,"status = '2'",null,null);//0
    List<String> SQLs = new LinkedList<>();
    for(TaskSqlInfo s : all){
        //开始拆分一个SQL行中的十多个以;分割的SQL语句
        String part[] = s.getSqls().trim().split(";");
        if(part.length>1){
            for(String string:part){
                SQLs.add(string+";");
            }
        }
        else{
            SQLs.add(s.getSqls());
        }
    }
    return SQLs;
}
```

### 主要的分析处理函数
事实上就是对SQL语句进行字符串处理，截取出insert部分、select部分，from部分。具体写在注释中。
```
public List<MdTableMapping> SQLToExcel(String sql) {
    List<MdTableMapping> mdTableMappingsList = new LinkedList<>();
    try {
        int begin = 0,end = 0;
        //去除SQL语句中的所有注释部分
        begin = sql.indexOf("/*+");
        if(begin!=-1){
            end = sql.indexOf("*/");
            sql = sql.replace(sql.substring(begin,end+2),"");
        }
        String sqlToUpper = sql.toUpperCase();
        String sqlWithoutSpaceAndToUpper = sql.replaceAll("\\s*", "").toUpperCase();//全部转为大写，并去除空格
        //开始提取需要插入的主表表名，即 insert 语句
        begin = sqlToUpper.indexOf("INSERT OVERWRITE TABLE ");
        String mainTable;
        if(begin != -1){
            end = sqlToUpper.indexOf("SELECT");
            mainTable = sqlToUpper.substring(begin + 23, end).trim();
        }else{
            begin = sqlToUpper.indexOf("INSERT INTO TABLE ");
            end = sqlToUpper.indexOf("SELECT");
            mainTable = sqlToUpper.substring(begin + 18, end).trim();
        }
        //获得该insert表中全部所需数据（表ID 表名中英文 字段ID 字段中英文等）
        List<Map<Object, Object>> sqlResults = service.getObjectsBySql("SELECT a.ID,a.CNAME," +
                        "a.ENAME,b.ID as fid,b.CNAME as fcname,b.ENAME as fename from md_table_info a " +
                        "left outer join md_table_fields b on a.ID = b.TID where a.ENAME = ? ORDER BY b.SEQNUM",
                new Object[]{mainTable});
        //形成一个map。key为123等，指代等待insert表的第1234个标准字段；value为一个MdTableMapping对象，其中包含了这个字段的表ID 表名中英文 字段ID 字段中英文等
        int fieldsNum = 0;Map<Integer,MdTableMapping> mapMdTableMapping = new HashMap<>();
        for(Map<Object, Object> m:sqlResults){
            fieldsNum++; MdTableMapping mdTableMapping = new MdTableMapping();
            mdTableMapping.setTename(mainTable);
            String s = "ID";Object b = s;
            mdTableMapping.setTid(m.get(b).toString());
            s = "CNAME";b = s;
            mdTableMapping.setTcname(m.get(b).toString());
            s = "ENAME"; b =s;
            mdTableMapping.setTename(m.get(b).toString());
            s = "fid"; b =s;
            mdTableMapping.setFid(m.get(b).toString());
            s = "fename"; b =s;
            mdTableMapping.setFename(m.get(b).toString());
            s = "fcname"; b =s;
            mdTableMapping.setFcname(m.get(b).toString());
            mapMdTableMapping.put(fieldsNum,mdTableMapping);
        }
        // 提取select和from部分
        begin = sqlWithoutSpaceAndToUpper.indexOf("SELECT");
        end = sqlWithoutSpaceAndToUpper.indexOf("FROM");
        String selectToFrom = sqlWithoutSpaceAndToUpper.substring(begin + 6,end);
        //提取left outer join 部分
        begin = sqlToUpper.indexOf("FROM");
        end = sqlToUpper.indexOf(";");
        String fromToEnd = sqlToUpper.substring(begin + 4);
        String[] fromToEndArray = fromToEnd.split("LEFT OUTER JOIN");//INFO_ZFQLYG_CASEINFO a,INFO_ZFQLYG_CASETYPE c1 ON a.type1 = c1.typecode
        //从left outer join 中提取关联表以及关联表的别名，以此生成一个表和别名对照的Map
        Map<String, String> tableAndAlias = new HashMap<>();
        if(fromToEndArray.length>1) {
            for (String s : fromToEndArray) {
                String[] tableNames = s.split("ON");//INFO_ZFQLYG_CASEINFO a,INFO_ZFQLYG_CASETYPE c1 ,a.type1 = c1.typecode
                String[] tableAndAliasArray = tableNames[0].split(" ");
                if (!tableAndAliasArray[0].equals("")) {
                    tableAndAlias.put(tableAndAliasArray[1].trim().toUpperCase(), tableAndAliasArray[0].toUpperCase());//a作为key，方便后续取
                } else {
                    tableAndAlias.put(tableAndAliasArray[2].trim().toUpperCase(), tableAndAliasArray[1].toUpperCase());
                }
            }
        }else if(fromToEnd.split(";")[0].trim().split(" ").length>1){
            tableAndAlias.put(fromToEnd.split(";")[0].trim().split(" ")[1],fromToEnd.trim().split(" ")[0]);
        }
        //同样，读出它们的关联键，存入名为conditions的map中
        Map<String,String> conditions = new HashMap<>();
        if(fromToEndArray.length>1) {
            for (String s : fromToEndArray) {
                String[] tableNames = s.split("ON");
                if(tableNames.length > 1){
                    String[] on = tableNames[1].replace(" ","").split("=");
                    String table1Alia = on[0].split("\\.")[0];
                    String table2Alia = on[1].split("\\.")[0];
                    String conditionsKey = table1Alia+"_"+table2Alia;
                    String conditionsValue = tableNames[1];
                    conditions.put(conditionsKey,conditionsValue);
                }
            }
        }
        //将select部分字符串以逗号分割
        String[] fields = selectToFrom.split(",");
        //对每一列进行处理
        List<MdTableMapping> tempMdTableMappingSet = new LinkedList<>();
        for (String s : fields) {
            MdTableMapping mdTableMapping = new MdTableMapping();
            if (!s.contains("(") && !s.contains(")") && s.contains(".") && !s.startsWith("CASE")) {
                String[] names = s.split("\\.");
                mdTableMapping.setSource(tableAndAlias.get(names[0])+"."+names[1]);
                mdTableMappingsList.add(mdTableMapping);
            } else if (s.indexOf("CASE") != -1 && s.indexOf("WHEN") != -1) {
                int begintemp = s.indexOf("CASE");
                int endtemp = s.indexOf("WHEN");
                String tablenameAndAlias = s.substring(begintemp + 4, endtemp);
                String[] names = tablenameAndAlias.split("\\.");
                begintemp = s.indexOf("WHEN");
                endtemp = s.indexOf("END");
                String procession = s.substring(begintemp, endtemp);
                mdTableMapping.setSource(tableAndAlias.get(names[0])+"."+names[1]);
                mdTableMapping.setProcess(procession);
                mdTableMappingsList.add(mdTableMapping);
            } else if (s.contains("(") && !s.contains(")") && s.indexOf("TOCNAME(")!=-1) {//TOCNAME(A.TYPE3
                String[] tablenames = s.split("\\(");// TOCNAME A.TYPE3
                String[] names = tablenames[1].split("\\.");//A TYPE3
                mdTableMapping.setSource(tablenames[1]);
                mdTableMapping.setProcess(tablenames[0]);
                tempMdTableMappingSet.add(mdTableMapping);
            } else if (s.contains(")") && !s.contains("(")) {//C2.TYPENAME)
                s = s.replace(")","");//C2.TYPENAME
                String names[] = s.split("\\.");//C2 TYPENAME)
                MdTableMapping last = tempMdTableMappingSet.get(tempMdTableMappingSet.size()-1);
                if(conditions.keySet().contains(last.getSource().split("\\.")[0] + "_" + names[0])){
                    String[] conditionhalf = conditions.get(last.getSource().split("\\.")[0] + "_" + names[0]).split("=");
                    String tablename1 = tableAndAlias.get(conditionhalf[0].split("\\.")[0].toUpperCase().trim());
                    String tablename2 = tableAndAlias.get(conditionhalf[1].split("\\.")[0].toUpperCase().trim());
                    String translatedCondition = tablename1+"."+conditionhalf[0].split("\\.")[1]+" 条件："+tablename2+"."+conditionhalf[1].split("\\.")[1]+ " 关联 " +tablename2+"."+conditionhalf[1].split("\\.")[1];
                    mdTableMapping.setSource(tableAndAlias.get(last.getSource().split("\\.")[0])+"."+last.getSource().split("\\.")[1] +"  "+ translatedCondition);
                    mdTableMapping.setProcess(last.getProcess());
                    mdTableMappingsList.add(mdTableMapping);
                }

            } else {
                begin = sqlToUpper.indexOf("FROM");
                end = sqlToUpper.indexOf("LEFT OUTER JOIN");
                if(end == -1){//只有 from 部分
                    String fromTableAndAlias = sqlToUpper.substring(begin+4,sql.indexOf(";")).trim();
                    String[] fromTableAndAliasSplit = fromTableAndAlias.split(" ");
                    if(fromTableAndAliasSplit.length<2){//TableName a不存在
                        mdTableMapping.setSource(fromTableAndAlias+"."+s);
                        mdTableMappingsList.add(mdTableMapping);
                    }else {//ableName a存在
                        mdTableMapping.setSource(tableAndAlias.get(fromTableAndAliasSplit[1]).trim()+"."+s);
                        mdTableMappingsList.add(mdTableMapping);
                    }
                }else {
                    mdTableMapping.setSource(s);
                    mdTableMappingsList.add(mdTableMapping);
                }
            }
        }
        //将之前获得该insert表中全部所需数据的 map 对象集合中的信息 传递给最终需要返回插入的 MdTableMapping 对象集合中
        int i=1;
        for(MdTableMapping m:mdTableMappingsList){
            m.setTid(mapMdTableMapping.get(i).getTid());
            m.setTename(mapMdTableMapping.get(i).getTename());
            m.setTcname(mapMdTableMapping.get(i).getTcname());
            m.setFcname(mapMdTableMapping.get(i).getFcname());
            m.setFename(mapMdTableMapping.get(i).getFename());
            m.setFid(mapMdTableMapping.get(i).getFid());
            i++;
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
    return mdTableMappingsList;
}
```

### 数据库插入函数
```
public void AnalyseSQL(){
    List<MdTableMapping> result = new LinkedList<>();
    for(String s:getSQLs()){
        //System.out.println(s);
        List<MdTableMapping> all =  SQLToExcel(s);
        for(MdTableMapping r:all){
            result.add(r);
        }
    }
    for(MdTableMapping r:result){
        service.insert(r);
    }
}
```