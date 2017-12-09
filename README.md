# MyBatis逆向工程

逆向工程字面意思就是反向生成工程, 和 Hibernate一样, MyBatis也有自己的逆向工程工具。详细信息请看[官方文档](http://www.mybatis.org/generator/index.html)

使用MyBatis逆向工程, 我们就不用再手动创建Model实体类以及对应的Mapper.xml文件和dao类；它可以直接连接数据库的表自动帮我们完成这些操作。是不是很方便？其实也只需要简单的几步就可以了。

首先我们从MyBatis的逆向工程[Github仓库](https://github.com/mybatis/generator/releases)下载最新的jar包,还有MyBatis和MySQL的jar包。

### 逆向工程的配置

####  1. 新建项目，导入Jar包

使用IDEA新建一个Java项目，导入我们刚刚下载的三个jar包。1、逆向工程核心包 2、MyBatis3.x jar包 3、MySQL连接jar包。



#### 2.新建逆向工程的配置文件 

在src包下创建逆向工程的配置文件generatorConfig.xml,直接拷贝官方介绍中的[XML配置](http://www.mybatis.org/generator/configreference/xmlconfig.html)即可.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <context id="testTables" targetRuntime="MyBatis3">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是  false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <!--数据库连接信息：驱动类、连接地址、用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"
                        userId="root" password="root">
        </jdbcConnection>

        <!-- 默认为false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为true时把JDBC DECIMAL 和
            NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- 生成Model类的位置 -->
        <javaModelGenerator targetPackage="cn.devtor.model" targetProject=".\src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- 生成mapper映射文件的位置 -->
        <sqlMapGenerator targetPackage="cn.devtor.mapper"
                         targetProject=".\src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
        <!-- 生成Dao接口的位置 -->
        <javaClientGenerator type="XMLMAPPER"  
                             targetPackage="cn.devtor.dao" 
                             targetProject=".\src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>
        <!-- 指定数据库的表 -->
        <table tableName="book"></table>

        <!-- 有些表的字段需要指定java类型
         <table schema="" tableName="">
            <columnOverride column="" javaType="" />
        </table> -->
    </context>
</generatorConfiguration>
```

XML内容都加了注释，我们只要修改几个地方就可以了

* 数据库连接信息 
* 生成Model、Mapper、Dao 类的位置
* 数据库的表 table



#### 3.执行代码

在src包下新建一个Main.java 文件, 内容也是从官方那里Copy过来的

```java

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.internal.DefaultShellCallback;

import java.io.File;
import java.util.ArrayList;
import java.util.List;

public class Main {
    public void generator() throws Exception{
        List<String> warnings = new ArrayList<String>();
        boolean overwrite = true;
        //指定逆向工程de配置文件
        File configFile = new File("src/generatorConfig.xml");
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,
                callback, warnings);
        myBatisGenerator.generate(null);
    }

    public static void main(String[] args) throws Exception{
	// write your code here
        try {
            Main generatorSqlmap = new Main();
            generatorSqlmap.generator();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

运行程序，等控制台打印出 `Process finished with exit code 0` 刷新一下文件目录就能看到自动生成的文件了。



### 快速使用

1. git clone https://github.com/ZengGUI/MyBatis-generator.git
2. 用你的IDEA打开即可。