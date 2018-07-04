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

- 在src目录下创建mybatis的**主配置**文件 **mybatis-config.xml**
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
- **如果resource**的路径没有填对，会报错，找不到文件
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
  - **resultType="Category"** 表示**返回的数据和Category关联起来**
    - 此处本该使用的是全类名com.how2java.pojo.Category,但是因为上一步配置了别名，所以直接使用Category就可以了

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

#### 6.基本原理
1. 应用程序找Mybatis要数据
2. mybatis从数据库中找来数据
  1. 通过mybatis-config.xml定位哪个数据库
  2. 通过Category.xml执行对应的select语句
  3. 基于Category.xml把返回的数据库记录封装在Category对象中
  4. 把多个Category对象装在一个Category集合中
3. 返回一个Category集合

***

CRUD
---
本建立在上一知识点Mybatis入门的基础上

#### 1.配置文件Category.xml
首先一次性修改配置文件Category.xml,提供CRUD对应的sql语句。
- Category.xml
-
```
<?xml version="1.0" encoding="UTF-8">
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

  <mapper namespace="com.how2java.pojo">

  </mapper>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

    <mapper namespace="com.how2java.pojo">
        <select id="listCategory" resultType="Category">
            select * from   category_
        </select>

        <insert id="addCategory" parameterType="Category">
          insert into category_ ( name ) values (#{name})
        </insert>

        <delete id="deleteCategory" parameterType="Category">
          delete from category_ where id= #{id}
        </delete>

        <select id="getCategory" parameterType="_int" resultType="Category">
          select * from category_ where id= #{id}
        </select>

        <update id="updateCategory" parameterType="Category">
          update category_ set name=#{name} where id= #{id}
        </update>
    </mapper>
```
- 每个SQL如何使用将在后续操作里一一讲解

#### 2.增加（进一步封装）
```java
import org.apache.ibatis.io.Resources;

public class TestMybatis {
  public static void main(String[] args) throws IOException {
    Strng resource = "mybatis-config.xml";
    InputSteam inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory =  new SqlSessionFactoryBuilder().build(inputStream);
    SqlSession session = sqlSessionFactory.openSession();

    Category c = new Category();
    c.setName("新增加的Category");
    session.insert("addCategory",c);

    listAll(session);

    session.commit();
    session.close();
  }

  private static void listAll(SqlSession session) {
    List<Category> cs = session.selectList("listCategory");
    for(Category c : cs) {
      System.out.print(c.getName());
    }
  }
}
```
 #### 3.删除
 删除id=6的对象
 ```java
 Category c =new Category();
 c.setId(6);
 session.delete("deleteCategory",c);
 ```

 deleteCategory对应删除的sql语句
 ```xml
 <delete id="deleteCategory" parameterType="Category">
  delete from category_ where id= #{id}
 </delete>
 ```

 #### 4.获取
 通过session.selectOne获取id=3的记录
 ```java
 Category c = session.selectOne("getCategory",3);
 ```

 getCategory对应的sql语句：
 ```xml
 <select id="getCategory" parameterType="_int" resultType="Category">
  select * from category_ where id= #{id}
 </select>
 ```

 #### 5.修改
 通过session.update进行修改
 ```java
 session.update("updateCategory",c);
 ```

 updateCategory对应的sql语句：
 ```xml
 <update id="updateCategory" parameterType="Category">
  update category_ set name= #{name} where id= #{id}
 </update>
 ```

#### 6.查询所有
session.selectList执行查询语句
```java
List<Category> cs = session.selectList("listCategory");
```

listCategory对应的sql语句
```xml
<select id="listCategory" resultType="Category">
  select * from category_
</select>
```

更多查询
---

#### 1.模糊查询
1. 修改Category.xml，提供listCategoryByName查询语句
  - `select *from category_ where name like concat('%', #{0}, '%')`
  - **concat('%',#{0},'%')** 这是mysql的写法
- 如果是oracle，写法是
  - `select * from category_ where name like '%'||#{0}||'%'`

2. 运行测试
```java
List<Category> cs = session.selectList("listCategoryByName","cat");
```
