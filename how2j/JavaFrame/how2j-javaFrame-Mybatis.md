Mybatis
===
一款对SQL进行轻量级封装的高效率ORM框架，前身是（ibatis）

前言
---
> - 不适用框架的局限性
>   - 平时使用JDBC访问数据库，除了自己写SQL外，必须操作Connection,Statement,ResultSet等。这些其实只是手段的辅助类
>   - 另外，访问不同的表，还会写很多雷同的代码，显得繁琐和枯燥
> - 使用了Mybatis之后
>   - 只需要自己提供SQL语句，其他的工作，如建立连接，Statement,JDBC相关异常处理等等都交给Mybatis。
>   - 那些重复性的工作Mybatis也给做掉了，开发者只需关注在增删改查等操作层面上，而把技术细节都封装在了我们看不见的地方。

入门
---
#### 1.创建数据库等前期准备用于演示
1. 创建数据库how2java
  - `crate database how2java`
2. 创建表category_
```sql
use how2java;
create table category_(
  id int(11) not null auto_increment,
  name varchar(32) default null,
  primary key (id)
) engine=MyISAM auto_increment=1
default charset=utf8;
```
3. 导入数据
```sql
use how2java;
insert into category_ values (null, 'category1');
insert into category_ values (null, 'category2');
```

#### 2.创建项目
1. 创建项目、导入JAR包（）
2. 准备实体类Category,用于映射表category_
```java
package com.hwo2java.pojo;

public class Category {
  private int idl
  private String name;

  //getter和setter方法
}
```

#### 3.配置文件mybatis-config.xml

- 在src目录下创建mybatis的主配置文件 **mybatis-config.xml**
- 其作用主要是提供连接数据库用的驱动，数据库名称，编码方式，账号密码等
```xml
<property name="driver" value="com.mysql.jdbc.Driver" />
<property name="url" value="jdbc:mysql://localhost:3306/how2java?characterEncoding=UTF-8" />
<property name="username" value="root" />
<property name="password" value="admin" />
```

- 以及别名，自动扫描com.how2java.pojo下的类型，使得在后续 **配置文件Category.xml** 中使用resultType的时候，可以直接使用Category,而不必写全类名：com.how2java.pojo.Category
```xml
<typeAliases>
  <package name="com.how2java.pojo"/>
</typeAliases>
```

- 映射Category.xml
```xml
<mappers>
  <mapper resource="com/how2java/pojo/Category.xml"/>
</mappers>
```

- 完整mybatis-config.xml代码
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
      <package name="com.how2java.pojo"/>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/how2java?characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="admin"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/how2java/pojo/Category.xml"/>
    </mappers>
</configuration>
```

#### 4.配置文件Category.xml
在包com.how2java.pojo下，新建 **Category.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

    <mapper namespace="com.how2java.pojo">
        <select id="listCategory" resultType="Category">
            select * from   category_     
        </select>
    </mapper>
```
- `namespace="com.how2java.pojo"`
  - 表示命名空间是com.how2java.pojo,在后续调用sql语句的时候会用到它
- 里面定义了一条sql语句`select *from category_`
  - 这条sql语句用id： **listCategory** 进行标示以供后续代码调用。
  - **resultType="Category"** 表示返回的数据和Category关联起来
    - 此处本该使用的是全类名com.how2java.pojo.Category,但是因为上一步配置l别名，所以直接使用Category就可以了

#### 5.测试类TestMybatis

```java
package com.how2java;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import com.how2java.pojo.Category;

public class TestMybatis {

    public static void main(String[] args) throws IOException {
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession session=sqlSessionFactory.openSession();

        List<Category> cs=session.selectList("listCategory");
        for (Category c : cs) {
            System.out.println(c.getName());
        }         
    }
}
```
