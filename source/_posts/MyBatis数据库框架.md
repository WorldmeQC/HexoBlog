---
title: MyBatis数据库框架
date: 2024-01-31 11:18:59
tags:
- Java
- JavaWeb
- 数据库
- MyBatis
categories: 后端开发
---

## MyBatis 框架初识

![MyBatis](/images/MyBatis_logo.png)

### 什么是 MyBatis ？

- MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。
- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。
- 中文文档：[MyBatis 中文网](https://mybatis.net.cn/index.html)
- GitHub：https://github.com/mybatis/mybatis-3

### 为什么需要使用 MyBatis

- 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件。易于学习，易于使用。通过文档和源代码，可以比较完全的掌握它的设计思路和实现。
- 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。 sql写在xml里，便于统一管理和优化。通过sql语句可以满足操作数据库的所有需求。
- 解除sql与程序代码的耦合：通过提供DAO层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。
- 提供映射标签，支持对象与数据库的ORM字段关系映射。
- 提供对象关系映射标签，支持对象关系组建维护。
- 提供xml标签，支持编写动态sql。

### 持久化

- 持久化是将程序数据在持久状态和瞬时状态间转换的机制
  - 就是把内存中的数据保存到可永久保存的存储设备中。主要应用就是将内存中的对象存储在数据库中。
  - JDBC、文件IO就是一种持久化的机制。
- 为什么需要持久化服务？
  - 内存断电后数据会丢失，但是我们不希望一些数据丢失，但是却无法保证内存永远不断电。
  - 内存成本比较高，就算数据不需要永久保存，也会因为内存的容量限制而不能一直放在内存中，需要持久化到外存。

### 持久层

- 持久层就是完成持久化工作的代码块，一般为DAO层（Data Access Object 数据访问对象）。
- 持久化的实现过程大多都是通过各种关系数据库来完成的。
- 总的来说，持久层就是用来操作数据库的。

## MyBatis 的使用

### 导入 MyBatis （Maven）

```
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.9</version>
</dependency>
<!-- 另外还需要一个MySQL的连接驱动包 -->
```

### 编写 MyBatis 核心配置文件

mybatis-config.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;serverTimezone=GMT%2B8&amp;characterEncoding=utf8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/worldmeqc/dao/userMapper.xml"/>
    </mappers>
</configuration>
```

### 编写 MyBatis 工具类

```
public class MyBatisUtil{
    private static SqlSessionFactory sqlSessionFactory;
    static {
        try {
            InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    //获取SqlSession连接
    public static SqlSession getSession(){
        return sqlSessionFactory.openSession();
    }
}
```

### 创建实体类

```
@Data //lombok
public class User {
    private int id;
    private String username;
    private String password;
}
```

### 编写Mapper接口

```
public interface UserMapper {
    List<User> queryAll();
}
```

### 编写UserMapper.xml配置文件

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace必须与Mapper接口类对应 -->
<mapper namespace="com.worldmeqc.mapper.UserMapper">
    <select id="queryAll" resultType="com.worldmeqc.pojo.User">
        select * from user
    </select>
</mapper>
```

### 测试类

junit测试

```
public class MyTest {
    @Test
    public void testQueryAll() {
        SqlSession session = MyBatisUtil.getSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        List<User> users = mapper.queryAll();
        for(User user : users) {
            System.out.println(user);
        }
        session.close();
    }
}
```

### 可能出现的问题

#### 找不到Mapper.xml文件

如果我们的UserMapper.xml文件是放在mapper包中，与Mapper接口类放在一起的话，Maven会将xml文件过滤掉，在生成的代码包中就找不到xml文件了，我们可以在pom.xml文件中添加以下代码解决问题

```
<build>
    <!-- 资源文件的位置 -->
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*</include>
            </includes>
        </resource>
        <resource>
            <directory>src/main/resources/</directory>
            <includes>
                <include>**/*</include>
            </includes>
        </resource>
    </resources>
</build>
```

## 几个顶级元素标签

### select、insert、update、delete标签

其中有很多属性可以详细配置每一条SQL语句

- id
  - 命名空间中唯一的标识符，并且与接口中的方法名对应
- parameterType
  - 传入SQL语句的参数类型，如int
- resultType
  - SQL语句返回值类型（完整的类名或者别名），如果返回集合应设置为集合中存放的元素类型。
- resultMap
  - 结果映射，可以解决实体属性名与列名不一致的问题
  - 可以在外部配置一个resultMap，然后在引用它的语句中设置resultMap属性就行
- flushCache
  - 设置为true时，只要语句被调用了，本地缓存和二级缓存会被清空
  - 默认值为false
- useCache
  - 设置为true时，本条语句的结果会被二级缓存储存
  - 默认值 对于select为true，其他为false
- useGeneratedKeys（适用于 insert 和 update ）
  - 设置为true时，会使用JDBC的getGeneratedKeys方法取出由数据库内部生成的主键
  - 默认值为false
- keyProperty（适用于 insert 和 update ）
  - 指定能够唯一识别对象的属性，Mybatis会使用getGeneratedKeys 的返回值或 insert 语句的 selectKey 子元素设置它的值
  - 默认值未设置，如果生成列不止一个，可以用逗号隔开

### sql标签

这个元素可以用来定义可以重用的SQL代码片段，方便在其他语句中使用。其中的参数可以在加载的时候确定下来，还可以在不同的使用中定义不同的参数值。

```
<sql id="userColumns">
    ${alias}.id,${alias}.username,${alias}.password
</sql>
```

上面的代码块可以在其他地方使用

```
<select id="selectUsers" resultType="map">
    select
    	<include refid="userColumns"><property name="alias" value="t1"/></include>,
    	<include refid="userColumns"><property name="alias" value="t2"/></include>
	from some_table t1
    cross join some_table t2
</select>
```

### resultMap标签

这个标签是MyBatis中最重要的最强大的元素标签：结果映射

示例：

一个简单的实体类

```
package com.someapp.model;
public class User {
    private int id;
    private String username;
    private String hashedPassword;

    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getUsername() {
        return username;
    }
    public void setUsername(String username) {
        this.username = username;
    }
    public String getHashedPassword() {
        return hashedPassword;
    }
    public void setHashedPassword(String hashedPassword) {
        this.hashedPassword = hashedPassword;
    }
}
```

基于JavaBean的规范，上面的类有三个属性，分别是id，username，hashedPassword

这样的一个JavaBean可以被映射到ResultSet

```
<select id="selectUsers" resultType="com.someapp.model.User">
    select id, username, hashedPassword
    from some_table
    where id = #{id}
</select>
```

我们可以使用resultMap来解决列名不匹配的问题，比如上述resultMap修改为

```
<resultMap id="userResultMap" type="User">
    <id property="id" column="user_id" />
    <result property="username" column="user_name"/>
    <result property="password" column="hashed_password"/>
</resultMap>
```

然后在引用它的语句中设置resultMap属性就好了（注意去掉resultType属性）

```
<select id="selectUsers" resultMap="userResultMap">
    select user_id, user_name, hashed_password
    from some_table
    where id = #{id}
</select>
```

下面是一些resultMap的元素标签

- id
  - 一个ID结果，标记出作为ID的结果可以帮助提高整体性能
- result
  - 注入到字段或JavaBean中属性的普通结果
- association
  - 一个复杂类型的关联，许多结果会包装成这种类型
  - 嵌套的结果映射：关联可以是resultMap元素或是对其他结果映射的引用
- collection
  - 一个复杂类型的集合
  - 嵌套的结果映射：关联可以是resultMap元素或是对其他结果映射的引用
- discriminator
  - case 基于某些值的结果映射
    - 嵌套的结果映射：case也是一个结果映射所以具有相同的结构和元素或是对其他结果映射的引用
- constructor
  - 用于在实例化类时，注入结果到构造方法中
  - idArg：ID参数，标记出作为ID的结果
  - arg：将被注入到构造方法的一个普通结果

### resultMap的元素标签的属性

- id & result
  - 这是结果映射的基础，会将一个列的值映射到一个简单数据类型的属性或字段
  - id对应的属性会被标记为对象的标识符，在比较对象实例时使用，可以提高性能
  - 属性
    - property：映射到列结果的字段或属性，优先寻找JavaBean中的属性
    - column：数据库中的列名或是列的别名
    - javaType：一个Java类的全限定名或是类型别名（内置）
    - jdbcType：JDBC类型，只需要在可能执行插入更新删除且允许空值的列上指定JDBC类型，这是JDBC的要求
- 内置的jdbcType

| **BIT**           | **FLOAT**   | **CHAR**        |
| ----------------- | ----------- | --------------- |
| **TIMESTAMP**     | **OTHER**   | **UNDEFINED**   |
| **TINYINT**       | **REAL**    | **VARCHAR**     |
| **BINARY**        | **BLOB**    | **NVARCHAR**    |
| **SMALLINT**      | **DOUBLE**  | **LONGVARCHAR** |
| **VARBINARY**     | **CLOB**    | **NCHAR**       |
| **INTEGER**       | **NUMERIC** | **DATE**        |
| **LONGVARBINARY** | **BOOLEAN** | **NVLOB**       |
| **BIGINT**        | **DECIMAL** | **TIME**        |
| **NULL**          | **CURSOR**  | **ARRAY**       |

- association
  - 关联元素主要处理一对一类型的关系。比如一篇博客有一个作者
