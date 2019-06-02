title: 通过接口读取生成存储 ODPS 上信息
author: zansy
tags: []
categories:
  - 唯有爱与工作不可辜负
date: 2018-08-09 04:03:00
---
该 Maven 程序使用阿里官方提供的 Java API，读取存储在 ODPS 上的数据表，通过字符串处理，自动生成它们的数据字典，并将数据字典存储到本地的 MySQL 数据库中。
<!--more-->
## 参考文档
[MaxCompute  Java SDK](https://help.aliyun.com/document_detail/34614.html)

[SDK Java Doc](http://repo.aliyun.com/java-sdk-doc/?spm=a2c4g.11186623.2.1.JrgQuV)

## 简述
在 ODPS 上大概存储了 200,000+ 万张表，需要拉取数据字典，即所有英文表名、表名中文注释、表中字段名、表中字段名中文注释。
![最终存入 MySQL 的数据字典表](https://wx4.sinaimg.cn/mw690/b11f7046gy1fu3i8oz7ejj20so07otb1.jpg)
```
//需要的数据组成一列，那么就把列作为对象
//col.java
import java.util.List;
public class col {
	private String tablename;
	public String getTablename() {
		return tablename;
	}
	public void setTablename(String tablename) {
		this.tablename = tablename;
	}
	public String getTablename_CN() {
		return tablename_CN;
	}
	public void setTablename_CN(String tablename_CN) {
		this.tablename_CN = tablename_CN;
	}
	public String getFieldname() {
		return fieldname;
	}
	public void setFieldname(String fieldname) {
		this.fieldname = fieldname;
	}
	public String getFieldname_CN() {
		return fieldname_CN;
	}
	public void setFieldname_CN(String fieldname_CN) {
		this.fieldname_CN = fieldname_CN;
	}
	private String tablename_CN;
	private String fieldname;
	private String fieldname_CN;
}
```
接下去是数据连接，通过配置 pom.xml 获得所需要的 Java 连接 MySQL 驱动包
```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.39</version>
</dependency>
```
通用的 DBConnection 数据库连接文件
```
import java.sql.*;
public class DBConnection {
	private static String driverName="com.mysql.jdbc.Driver";//在 MySQL 8.0 中合理
	private static String userName="root";
	private static String userPwd="123456";
	private static String dbName="*";//自己建的 database 名
	public static Connection getDBConnection() {
		String url1="jdbc:mysql://localhost/"+dbName;
		String url2="?user="+userName+"&password="+userPwd;
		String url3="&useUnicode=true&characterEncoding=utf-8";
		String url=url1+url2+url3;
		Connection con=null;
		try {
			Class.forName(driverName);
			con=DriverManager.getConnection(url);
		}catch(Exception e) {
			e.printStackTrace();
		}
		return con;
	}
	public static void closeDB(Connection con,PreparedStatement pstm,ResultSet rs) {
		if(rs!=null) try{
			rs.close();
		}catch(SQLException e) {
			e.printStackTrace();
		}
		if(pstm!=null) try{
			pstm.close();
		}catch(SQLException e) {
			e.printStackTrace();
		}
		if(con!=null) try{
			con.close();
		}catch(SQLException e) {
			e.printStackTrace();
		}
	}
}
```
简单的 MySQL 数据库 插入操作
```Java
import java.sql.ResultSet;
import com.mysql.jdbc.*;
import  odpstest.DBConnection;
public class colDao {
	public int saveCol(col c ) {
		Connection con = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		con = (Connection) DBConnection.getDBConnection();
		int row = 0;
		String sql="insert into data(tablename,tablename_CN,fieldname,fieldname_CN) values(?,?,?,?)";
		try {
			pstmt = (PreparedStatement) con.prepareStatement(sql);
			pstmt.setString(1, c.getTablename());
			pstmt.setString(2, c.getTablename_CN());
			pstmt.setString(3, c.getFieldname());
			pstmt.setString(4, c.getFieldname_CN());
			row = pstmt.executeUpdate();
		}catch(Exception e) {
			e.printStackTrace();
		}finally {
			DBConnection.closeDB(con, pstmt, rs);
		}
		return row;
	}
}
```
导出数据字典的步骤：

1. 执行 SQL 语句`show tables`，用 `like` 关键字找出含有需要字段的所有表名。在这个时候出来的结果如图，只有高亮的才是需要的表名：![含有需要字段的所有表名](https://wx1.sinaimg.cn/mw690/b11f7046gy1fu3i8nwptkj20ts07qgmb.jpg) 
2. 做字符串处理操作，取出表名。
3. 在程序中安排循环，对每一个表名执行`desc`语句操作，得到这个表的结构描述，具体到字段，每张表都不一样的。举例，结构描述如图：![表的结构描述](https://wx1.sinaimg.cn/mw690/b11f7046gy1fu3i8o36zuj20ts0izq43.jpg)
4. 进行字符串处理，取出表描述信息中的字段名、字段中文注释。
5. 最后将取出的四个字段组成一个列对象中的四个变量，存储这个列对象进入本地 MySQL 数据库中。

```
import java.util.ArrayList;
import java.sql.*;
import java.util.List;
import com.aliyun.odps.Instance;
import com.aliyun.odps.Odps;
import com.aliyun.odps.OdpsException;
import com.aliyun.odps.account.Account;
import com.aliyun.odps.account.AliyunAccount;
import com.aliyun.odps.data.Record;
import com.aliyun.odps.task.SQLTask;
public class Sql {
private static final String accessId = "*";
private static final String accessKey = "*";//以上两项在信息页上都能找到，就是Access Key 的 ID/Secret
private static final String endPoint = "http://service.*/api";//自己项目的api
private static final String project = "*";//项目名
private static final String sql = "show tables like '*_330801_*';";
public static void main(String[] args) {
  Account account = new AliyunAccount(accessId, accessKey);
   Odps odps = new Odps(account);
   odps.setEndpoint(endPoint);
   odps.setDefaultProject(project);
   Instance i;
   String tablename = null, tablename_CN = null;
  try {
     i = SQLTask.run(odps, sql);
     i.waitForSuccess();
     List<Record> tables = SQLTask.getResult(i); 
     int cout = 0;
     int table_num=0;
     String name ="";
     for(Record table:tables){//负责取table
    	 table_num++;
    	 String[] table_pattern = table.get(0).toString().split(":");
    	 tablename = table_pattern[1];
    	 System.out.println(tablename);
    	 String sql_describe="desc "+tablename+" ;";//该语句负责展示每个表的表结构
    	 Instance ins = SQLTask.run(odps, sql_describe);
    	 ins.waitForSuccess();
         List<Record> describes = null;
         try {
        	 describes = SQLTask.getResult(ins); 
	         String[] tablename_CN_pattern = describes.get(1).get(0).toString().split(":");
	         tablename_CN = tablename_CN_pattern[1].replace("|", "").trim();
	         if(tablename_CN.equals("")) {
	        	 tablename_CN="null";
			 }
	         System.out.println(tablename_CN);
	         int temp=0;
	         for(Record describe_column:describes) {
	        	 String columns=describe_column.get(0).toString();
	        	 //System.out.println(columns);
	        	 if(temp>12&&columns.indexOf("------")==-1) {//保证在---最终分隔符之前
	        		 String[] pattern = describe_column.get(0).toString().split("\\|");//注意正则，用“|”分割
	        		 String fieldname = pattern[1].trim();
	        		 String fieldname_CN = pattern[4].trim();
	        		 System.out.println("fieldname: "+fieldname);
	        		 if(fieldname_CN.equals("")) {
	        			 fieldname_CN="null";
	        		 }
	        		 System.out.println("fieldname_CN: "+fieldname_CN);
	        		 col column = new col();//创建新的一列对象
	        		 column.setTablename(tablename);
	        		 column.setTablename_CN(tablename_CN);
	        		 column.setFieldname(fieldname);
	        		 column.setFieldname_CN(fieldname_CN);
	        		 colDao colDao = new colDao();
	        		 int flag= colDao.saveCol(column);
	        		 if(flag==1) {
	        			 System.out.println("success!");
	        		 }
	        	 }
	        	 temp++;
	         } 
         } catch (Exception e) {
 			cout++;
 			name += tablename+" & ";
 		}
     }
     System.out.println(table_num+" tables, "+cout+" errors: "+name);
  } catch (OdpsException e) {
     e.printStackTrace();
  }
}
}
```

最后给出建表语句：
```
CREATE TABLE `zjsjt`.`data` (
  `iddata` INT NOT NULL AUTO_INCREMENT,
  `tablename` VARCHAR(500) CHARACTER SET 'utf8' COLLATE 'utf8_unicode_ci' NULL,
  `tablename_CN` VARCHAR(500) CHARACTER SET 'utf8' COLLATE 'utf8_unicode_ci' NULL,
  `fieldname` VARCHAR(500) CHARACTER SET 'utf8' COLLATE 'utf8_unicode_ci' NULL,
  `fieldname_CN` VARCHAR(500) CHARACTER SET 'utf8' COLLATE 'utf8_unicode_ci' NULL,
  PRIMARY KEY (`iddata`))
ENGINE = InnoDB
DEFAULT CHARACTER SET = utf8
COLLATE = utf8_unicode_ci;
```
## 补充
如果是单纯只需要表名列名或者注释的，在 Table 里面有个 Schema 对象，可以顺着取列对象，具体参见接口文档。这个字符串处理方法针对只能靠 `desc 表名` 方法提取基本结构信息的要求。 