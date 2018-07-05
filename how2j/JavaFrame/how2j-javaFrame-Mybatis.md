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

Mybatis基础
===

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

#### 2.多条件查询
结合前面的模糊查询，多一个id> 多少的条件

- 1. Category.xml
```xml
<select id="listCategoryByIdAndName" parameterType="map" resultType="Category">
  select * from category_ where id> #{id} and name like concat('%', #{name}, '%')
</select>
```

- 2. 测试代码
因为是多个参数，而selectList方法又只接受一个参数对象，所以需要把多个参数放在Map里，然后把这个Map对象作为参数传递进去
```java
import org.apache.ibatis.io.Resources;
public static void main(String[] args) {
  String resource = "mybatis-config.xml";
  InputStream inputStream = Resources.getResourceAsStream(resource);
  SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
  SqlSession session = sqlSessionFactory.openSession();

  Map<String, Object> params = new HashMap<>();
  params.put("id",3);
  params.put("name","cat");

  List<Category> cs = session.selectList("listCategoryByIdAndName",params);
  for (Category c : cs) {
    System.out.print(c.getName);
  }

  session.commit();
  session.close();
}
```
***
一对多
---
一个分类对应多个产品，基于Mybatis入门教程的知识点

#### 1.准备工作
1. 分类表不变化，新增加产品表
```sql
use how2java;
create table product_(
  id int not null auto_increment,
  name varchar(30) default null,
  price float default 0,
  cid int ,
  primary key (id)
)auto_increment=1 default charset=utf8;
```

2. 准备数据
- 清空category_和product_表
  - 新增2条分类数据，id分别是1,2
  - 新增6条产品数据，分别关联上述2条分类数据
```sql
use how2java;
delete from category_;
insert into category_ values (1, 'category1')
insert into category_ values (2, 'category2')
delete from product_;
insert into product_ values(1, 'producta', 88.88, 1);
insert into product_ values(2, 'productb', 88.88, 1);
insert into product_ values(3, 'productc', 88.88, 1);
insert into product_ values(4, 'productx', 88.88, 2);
insert into product_ values(5, 'producty', 88.88, 2);
insert into product_ values(6, 'productz', 88.88, 2);
```

3. Product实体类(java)
普通的一个pojo
```java
package com.how2java.pojo;

public class Product {
  private int id;
  private String name;
  private float price;
  // getter和setter方法
  public String toString() {}
}
```

4. 修改Category实体类
```java
public class category {
  private int id;
  private String name;
  List<Product> products;
  // getter和setter方法
  public String toString(){}
}
```
5. 暂时不需要Product.xml
  - 本例演示通过分类对产品的一对多，暂时无需Prouduct.xml

6. 修改Category.xml
- 通过left join关联查新，对Category和Product表进行关联查询。
- 这里不是使用resultType而是使用**resultMap**,通过resultMap把数据取出来放在**对应的**对象属性里
- **注**：Category的id字段和Product的id字段同名，Mybatis不知道谁是谁的，所以需要通过取别名cid,pid来区分。name字段同理

-Category.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org/dtd/mybatis-3-mapper.dtd"
  "http://mybatis.org/dtd/mybatis-3-mapper/dtd">
<mapper namespace="com.how2java.pojo">
  <resultMap type="Category" id="categoryBean">
    <id column="cid" property="id"/>
    <result column="cname" property="name"/>
    <!-- 一对多的关系 -->
    <!-- property：指的是集合属性的值，ofType:指的是集合中元素的类型 -->
    <collection property="products" ofType="Product">
      <id column="pid" property="id"/>
      <result column="pname" property="name"/>
      <result column="price" property="price"/>
    </collection>
  </resultMap>

  <!-- 关联查询分类和产品表 -->
  <select id="listCategory" resultMap="categoryBean">
    select c.*, p.*, c.id 'cid', p.id 'pid', c.name 'cname', p.name 'pname' from category_c left join product_p on c.id = p.cid
  </select>
</mapper>
```
- 在上述配置中
  - `<mapper>`用来配置类和sql语句对应关系，所谓ORM
    - `<resultMap>`用来配置类属性和sql字段的对应关系
      - `<id column="cid" property="id">`
        - cid（sql语句中category_的id字段别名）对应父级`<resultMap type="Category" ... >`Category的id属性；需要一致。
    - `<collection property="products" ofType="Product">`
      - 指名为products的属性的类型是Product类
      - `<id column="pid" property="id">`
        - pid对应sql语句里Product_ id字段的别名，需要一致
      - `<result column="pname" property="name">`
        - pname对应sqil语句里Product_ name字段的别名，需要一致

#### 2.测试代码
```java
public class TestMybatis {
  public static void main(String[] args) {
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionoFactoryBuilder().build(inputStream);
    SqlSession session = sqlSessionFactory.openSession();

    List<Category> cs = session.selectList("listCategory");
    for(Category c : cs) {
      System.out.print(c);
      List<Product> ps = c.getProducts();
      for(Product p : ps) {
        System.out.print("\t" + p);
      }
    }

    session.commit();
    session.close();
  }
}
```

***

多对一
---

#### 1.准备工作
1. 为Product增加category属性
2. 提供Product.xml
  - 通过listProduct配置关联查询的sql语句。
  - 然后通过resultMap,进行字段和属性的对应。
  - 使用association进行多对一关系关联，指定表字段名称与对象属性名称的一一对应关系
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.how2java.pojo">
      <resutlMap type="Product" id="productBean">
        <id column="pid" property="id"/>
        <result column="pname" property="name"/>
        <result column="price" property="price"/>
      <!-- 多对一的关系
      property指的是属性名称，javaType指的是属性的类型 -->
        <association property="category" javaType="Category">
          <id column="cid" property="id"/>
          <result column="cname" property="name"/>
        </association>
      </resultMap>

      <select id="listProduct" resultMap="productBean">
        select c.*, p.*, c.id 'cid', p.id 'pid', c.name 'cname', p.name 'pname' form category_ c left join product_ p on c.id = p.cid
      </select>
    </mappper>
```

3. 在mybatis-config.xml中增加对于Product.xml的映射
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
        <mapper resource="com/how2java/pojo/Product.xml"/>
    </mappers>
</configuration>
```

#### 测试
```java
package com.how2java;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import com.how2java.pojo.Product;

public class TestMybatis {

    public static void main(String[] args) throws IOException {
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession session = sqlSessionFactory.openSession();

        List<Product> ps = session.selectList("listProduct");
        for (Product p : ps) {
            System.out.println(p+" 对应的分类是 \t "+ p.getCategory());
        }

        session.commit();
        session.close();
    }
}
```
***

多对多
---

***

动态SQL
===

if
---

where
---

choose
---

foreach
---

bind
---

***

注解
===
CRUD
---
本例把**XML方式的CRUD**修改为注解方式

#### 1.Mapper接口
- 新增加接口CategoryMapper，并在接口中声明的方法上，加上注解
- 对比**配置文件Category.xml**，其实就是把SQL语句从XML挪到了注解上来

```java
package com.how2java.mapper;
import java.util.List;
import org.apache.ibatis.annotations.Delete;
import ……Insert ……Select ……Update;
import com.how2java.pojo.Category;

public interface CategoryMapper {
  @Insert("insert into category_ (name) values (#{name}) ")
  public int add(Category category);

  @Delete("delete form category_ where id = #{id}")
  public void delete(int id);

  @Select("select * from category_ where id= #{id}")
  public Category get(int id);

  @Update("update category_ set name=#{name} where id=#{id}")
  public int update(Category category);

  @Select("select * from category_")
  public List<Category> list();
}
```

#### 2.修改mybatis-config.xml
在第22行增加对CategoryMapper映射，原来的Category.xml是否保留随意
`<mapper calss="com.how2java.mapper.CategoryMapper"/>`
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
        <mapper class="com.how2java.mapper.CategoryMapper"/>
    </mappers>
</configuration>
```

#### 3. 测试类
```java
package com.how2java;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import com.how2java.mapper.CategoryMapper;
import com.how2java.pojo.Category;

public class TestMybatis {

    public static void main(String[] args) throws IOException {
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession session = sqlSessionFactory.openSession();
        CategoryMapper mapper = session.getMapper(CategoryMapper.class);

//        add(mapper);
//        delete(mapper);
//        get(mapper);
//        update(mapper);
        listAll(mapper);

        session.commit();
        session.close();

    }

    private static void update(CategoryMapper mapper) {
        Category c= mapper.get(8);
        c.setName("修改了的Category名稱");
        mapper.update(c);
        listAll(mapper);
    }

    private static void get(CategoryMapper mapper) {
        Category c= mapper.get(8);
        System.out.println(c.getName());
    }

    private static void delete(CategoryMapper mapper) {
        mapper.delete(2);
        listAll(mapper);
    }

    private static void add(CategoryMapper mapper) {
        Category c = new Category();
        c.setName("新增加的Category");
        mapper.add(c);
        listAll(mapper);
    }

    private static void listAll(CategoryMapper mapper) {
        List<Category> cs = mapper.list();
        for (Category c : cs) {
            System.out.println(c.getName());
        }
    }
}
```

一对多
----

多对一
---

多对多
---

动态SQL语句
---
