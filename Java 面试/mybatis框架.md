# mybatis框架
## 概述
mybatis是基于java的持久层框架，内部封装了jdbc，使开发者只关注sql语句本身。
mybatis通过xml和注解的方式将各种statement配置起来，并通过Java对象和statement中的动态参数进行映射最终生成sql语句，并对结果映射为java对象并返回
mybatis采用ORM的思想方便操作等
ORM:
	Object Relational Mapping 对应关系映射
	简单的来说就是：
				将数据库中的表和实体类及实体类的属性对应起来
				让我们看可以操作实体类就可以实现操作数据库表
				

| 表 | 实体类 |
| :--: | :-------: |
|  user  | User |
|  id  |  user_id  |
| name | user_name |

## mybatis 入门（xml实现）
创建一个maven项目，直接创建
在pom.xml加入依赖

```xml-dtd
<dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
```
在resource文件夹中创建mybatis的 --主配置-- 文件命名为mysqlMapConfig.xml,
在主配置文件中配置我们的所有javabean对象的一些CRUD操作。
主配置文件主要功能：
	1.配置连接的数据库
	2.配置文件映射，如：对UserDao的一些操作的文件映射，需要使用Mapper标签
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPD configuration
        PUBLIC "-//mybatis.org.//DTD Config 3.0//DN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!-- mybatis 的 主配置文件-->

<configuration>
    <!-- 配置环境-->
    <environments default="mysql">
        <!--配置mysql-->
        <environment id="mysql">
            <!--配置事务类型-->
            <transactionManager type="JDBC"/>
            <!--配置数据源/连接池-->
            <dataSource type="POOLDD">
                <!--配置连接数据库的基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://xx.xxx.xxx.xxx:3306/xxx"/>
                <property name="username" value="xxxx"/>
                <property name="password" value="xxx"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
       <mapper resource="club/twzw/dao/UserDao.xml"/>
    </mappers>
	
</configuration>
```
在resources 文件夹下 创建UserDao.xml（与类名相一致,路径也要一致，映射的配置文件相一致）
主要包括一些CRUD的操作，延迟加载等等。
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPD mapper
        PUBLIC "-//mybatis.org.//DTD Config 3.0//DN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="club.twzw.dao.UserDao"> 		<<<<<------这里的namespace 必须和 接口进行绑定，也就是接口的全类名路径，且各个标签使用的id为接口中定义的名称，接口名为findAll，标签使用的id也为findAll，完成绑定。

    <select id="findAll" resultType="club.twzw.domain.User">
        select * from user
    </select>
</mapper>
```
在resources 文件夹下 创建log4j.properties文件，用于输入日志文件
```
# Global logging configuration
log4j.rootLogger=DDBUG, stdout
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```
创建dao处理层接口，用于一些CRUD操作的方法，
```
package club.twzw.dao;

import club.twzw.domain.User;

import java.util.List;

/**
 * 用户持久层接口
 */
public interface UserDao {

    /**
     * 查询所有操作
     */
    List<User> findAll();
}
```
创建bean对象，用于存储数据
```
package club.twzw.domain;

import java.io.Serializable;

public class User implements Serializable {
    private Integer id;
    private String name;
    private String born;
    private Integer sex;
    private String address;
	
    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", born='" + born + '\'' +
                ", sex=" + sex +
                ", address='" + address + '\'' +
                '}';
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getBorn() {
        return born;
    }

    public void setBorn(String born) {
        this.born = born;
    }

    public Integer getSex() {
        return sex;
    }

    public void setSex(Integer sex) {
        this.sex = sex;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}

```
创建测试函数,factory.openSession();为空需要手动提交，factory.openSession(true);为自动提交
```
package club.twzw.test;

import club.twzw.dao.UserDao;
import club.twzw.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import javax.annotation.Resource;
import java.io.IODxception;
import java.io.InputStream;
import java.util.List;

public class mybatisTest {
    public static void main(String[] args) throws IODxception {
        // 读取配置文件，为主配置文件
        InputStream in = Resources.getResourceAsStream("mysqlMapConfig.xml");
        // 创建SqlSessionFactory 工厂
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);
    	//使用工厂生产session
        SqlSession sqlSession = factory.openSession();
        //  使用sqlsession创建dao接口的代理对象，传入接口
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        // 使用代理对象执行方法
        List<User> users = userDao.findAll();
        for (User user:users){
            System.out.println(user);
        }
        //释放资源				
        in.close();
        sqlSession.close();
    }
}

```
最终实现效果控制台输出
```
User{id=1, name='comi', born='1998', sex=0, address='xxxx'}
User{id=2, name='siki', born='2002', sex=1, address=' xxxx'}
```
## mybatis 注意事项
	一、创建UserDao.xml和UserDao.java时名称是为了和我们之前的知识保持一致
	在mybatis中它把持久层的操作接口名称和映射文件也叫做  Mapper
	所以 UserDao 和 UserMapper 是一样的
	二、mybatis的映射配置文件位置必须与dao接口的包结构相同
	三、映射配置文件的mapper标签namespace属性的取值必须是dao接口的全限定类名
	四、映射配置文件的操作配置（select），id属性的取值必须是dao接口的方法名
	
	当我们遵循二、三、四点后，在开发中便~~~无须实现dao实现类~~~
	
	Sqlsession和connection都是非线程安全的，不能放在成员变量中，每一次使用都应该去获取新的对象
## mybatis 引用外部文件配置数据库连接
新建一个dbconfig.properties文件，在主配置文件下 添加以下语句，并且修改配置文件的properties
```
    <properties resource="dbconfig.properties"/>
	
	<environments default="mysql">
        <!--配置mysql-->
        <environment id="mysql">
            <!--配置事务类型-->
            <transactionManager type="JDBC"/>
            <!--配置数据源/连接池-->
            <dataSource type="POOLDD">
                <!--配置连接数据库的基本信息-->
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
```
dbconfig.properties文件内容
```
jdbc.driver = com.mysql.jdbc.Driver
jdbc.url = jdbc:mysql://xx.xx.xx.xx:3306/blog
jdbc.username = blog
jdbc.password =  146325
```
## 主配置文件的常用标签
### setting
在这里，可以配置相关的全局设置，比如懒加载，驼峰命名等等设置。
### typeAliases 别名处理器
可以为我们常用的Java类起别名，让很长的名字变得短，别名不区分大小写。在主配置文件下就为全局配置，在单独的UserDao中就为局部别名。
package可以批量起别名，自动为package下的类自动起别名
```
    <settings>
        <!--懒加载-->
        <setting name="lazyLoadingDnabled" value="true"/>
        <setting name="aggressiceLazyLoading" value="false"/>
        <!--开启驼峰命名自动转换-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
	
	<!--如果不配置alias就会默认为类名的小写-->
	<typeAliases>
        <typeAlias type="club.twzw.dao.UserDao" alias="user"/>
    </typeAliases>
	
	<!-包下所有类自动起别名-->
	<package name="club.twzw.dao"/>
```
### mappers 将sql映射注册到全局配置中 mapper 注册一个sql映射
resource：引用类路径下的sql映射文件
url：引入网络路径或者磁盘路径下的sql映射文件
class:引入（注册）接口
	1.有sql文件，sql文件必须和接口同名且在同一目录
	2.注解实现
推荐比较重要的Dao接口可以用xml实现，不太重要的可以使用注解实现，提高开发速度

#### 批量注册
使用package标签，注册该包下所有的映射，如果是xml文件，同样，sql文件必须和接口同名且在同一目录，才能实现注册
```
<package name="club.twxw.dao.UserDao"/>
```
### databaseIdProvider  使用不同的数据库
且每一个标签都有各自的使用顺序，否者会报错。
```
    <databaseIdProvider type="DB_VDBDOR">
        <property name="MySQL" value="mysql"/>
        <property name="Oracle" value="oracle"/>
    </databaseIdProvider>
```

##  mybatis(注解实现映射)
	1.无需在配置UserDao.xml文件
	2.将mysqlMapConfig.xml的mapper属性的resource改为class,并配置全路径类名
```
<mappers>  
  	<mapper class="club.twzw.dao.UserDao"/>  
</mappers>
```
3.将UserDao的findAll方法上加入注解@Select()

```
	@Select("select * from user")  
	List<User> findAll();
```
## 增加代码的复用性
由于每一次使用dao都会有一些重复的过程，我们可以把这些重复的额过程提取出来，不但方便使用，而且代码简洁。
比如说：我们可以在代码运行之前，销毁之前运行一些代码
```
    @Before
    public void init() throws IODxception {
        // 读取配置文件
        in = Resources.getResourceAsStream("mysqlMapConfig.xml");
        // 创建SqlSessionFactory 工厂
        builder = new SqlSessionFactoryBuilder();
        factory = builder.build(in);
        //使用工厂生产session
        sqlSession = factory.openSession();
        //  使用sqlsession创建dao接口的代理对象
        userDao = sqlSession.getMapper(UserDao.class);
    }

    @After
    public void destory() throws Dxception{
		sqlSession.commit();
        //释放资源
        in.close();
        sqlSession.close();
    }
```
这样我们就可以简化代码，彻底专注于核心业务dao层
```
  @Test
    public void testSave() throws IODxception {
        User user = new User();
        user.setAddress("japan");
        user.setBorn("1998");
        user.setName("gimi");
        user.setSex(1);
        user.setId(3);
        // 使用代理对象执行方法
        userDao.addUser(user); 		<<<-----
		
		//sqlSession.commit();     <<<-----必须要提交事务，否则无法实现CRUD操作，同样，也可以把这一句放在销毁之前，但是这一句便不能再出现
    }
```
当然，还需要再UserDao.xml 中 加入相应的id addUser
```
    <insert id="addUser" parameterType="club.twzw.domain.User">
        insert into user (id,name,born,sex,address)values(#{id},#{name},#{born},#{sex},#{address})
    </insert>
```
## 完善CRUD相关代码及模糊查询
```
	//findById
    @Test
    public void testDelete(){
		User user = userDao.findById(1);
		System.out.println(user);
    }
	
	//add
    @Test
    public void testSave() throws IODxception {
        User user = new User();
        user.setAddress("japan");
        user.setBorn("1998");
        user.setName("gimi");
        user.setSex(1);
        user.setId(3);
        // 使用代理对象执行方法
        userDao.addUser(user);
    }
	//update
    @Test
    public void testUpdate(){
        User user = new User();
        user.setId(3);
        user.setSex(1);
        user.setName("???");
        user.setAddress("djkajdjwa");
        user.setBorn("20000000000");
        userDao.UpdateUser(user);
    }
	//delete
    @Test
    public void testDelete(){
        userDao.delete(3);
    }
	//	模糊查询
	@Test
    public void findByName() {
        List<User> users = userDao.findByName("%comii%");
        for (User user : users) {
            System.out.println(user);
        }
    }
	
```
配置相关xml，namespace 需和类路径相同,与接口进行绑定，且#{xxx}中的名称为JavaBean中的属性名称相同，User中有name，sex,address,那么xml文件中的#{xxx} 也为#{name},#{sex},#{address}
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPD mapper
        PUBLIC "-//mybatis.org.//DTD Config 3.0//DN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="club.twzw.dao.UserDao">
    <!--    // 有时数据库和java bean 中的命名不一致，为了方便起见，我们可以配置resultMap（结果集）-->
    <!--    // 配置查询结果的列名和实体类的属性名对应的关系（如果命名不一致，一致无需创建）-->
	
    <resultMap id="userMap" type="club.twzw.domain.User">
        <!--        主键对应字段-->
        <id property="id" column="id"></id>
        <!--        非主键对应字段-->
        <result property="name" column="name"></result>
        <result property="born" column="born"></result>
        <result property="sex" column="sex"></result>
    </resultMap>
	
    <!--使用resultMap-->
    <!--查询所有-->
    <select id="findAll" resultMap="userMap">
        select * from user
    </select>
	
    <!--不使用resultMap-->
    <!--查询所有-->
    <select id="findAll" resultType="club.twzw.domain.User">
        select * from user
    </select>
	
    <!--查询单个-->
    <select id="findById" parameterType="int" resultType="club.twzw.domain.User">
        select * from user where id = #{id}
    </select>
	
    <!--添加用户-->
    <insert id="addUser" parameterType="club.twzw.domain.User">
        insert into user (id,name,born,sex,address)values(#{id},#{name},#{born},#{sex},#{address})
    </insert>
	
    <!--更新用户数据-->
    <update id="UpdateUser" parameterType="club.twzw.domain.User">
        update user set name=#{name},address=#{address},sex=#{sex},born=#{born} where id=#{id}
    </update>
	
    <!--删除用户数据-->
    <delete id="delete" parameterType="Integer">
        delete from user where id=#{id}
    </delete>
	
    <!--根据name模糊查询用户-->
    <select id="findByName" parameterType="string" resultType="club.twzw.domain.User">
        select * from user where name like #{name}
    </select>
</mapper>
```
## 在插入完成之后获取ID的值
1.通过selectKey标签 设置resultType，keyProperty，keyColumn，order 相关设置，自动返回数据。（不支持自增的数据库使用的方法，Oracle）,order="BDFORD" 在执行插入之前
2.使用useGeneratedKeys="true" keyProperty="id",这里的id 为JavaBean的id（支持自增的数据库，mysql）
```
	    <!--方法一-->
    <insert id="addUser" parameterType="club.twzw.domain.User">
        <selectKey resultType="int" keyProperty="int" keyColumn="id" order="BDFORD">
            <!--查询主键id，得到后便可以使id有值-->
			select last_intsert_id();
        </selectKey>
        insert into user (id,name,born,sex,address)values(#{id},#{name},#{born},#{sex},#{address})
    </insert>

	<!--方法二-->
	<insert id="addUser" parameterType="user" useGeneratedKeys="true" keyProperty="id">
         insert into user (name,born,sex,address)values(#{name},#{born},#{sex},#{address})
    </insert>
```
## OGNL表达式（object graphic navigation language）
	通过对象的取值方法 来获取数据，在写法上省略了get（省略相关配置）
	eg：
		类：	User.GetName();
		ONNL：	User.name
## 使用alias 配置别名
```
    <!--    使用alias配置别名-->
	<!--    <typeAliases>-->
	<!--        <typeAlias type="club.twzw.domain.User" alias="user"></typeAlias>-->
	<!--    </typeAliases>-->
    <!--    那么所有的club.twzw.domain.User 便可以用 user 替代-->
    <!--    <select id="findById" parameterType="int" resultType="user">-->
    <!--        select * from user where id = #{id}-->
    <!--    </select>-->
```
## mybatics 中的动态sql语句查询 -if
UserDao.xml 中添加
```
    <!--    根据条件查询-->
    <select id="findByCondition" resultType="club.twzw.domain.User" parameterType="club.twzw.domain.User">
        select * from user where 1 = 1		<<<<<----------
        <if test="name != null">
            and name = #{name}
        </if>
    </select>
```
UserDao.java 中添加
```
    boolean findByCondition(User user);
```
测试代码
```
    @Test
    public void findByCondition() {
        User u = new User();
        u.setName("comi");
        User u = userDao.findByCondition(u);
        System.out.println(u);
    }
```
## mybatics 中的动态sql语句查询 -where
```
    <!--使用where 标签-->
    <select id="findByCondition" resultType="club.twzw.domain.User" parameterType="club.twzw.domain.User">
        select * from user			<<<<<----------
        <where>						<<<<<----------
            <if test="name != null">
                and name = #{name}
            </if>
            <if test="sex != null">
                and sex = #{sex}
            </if>
        </where>
    </select>
```
## 将重复使用的 sql 语句，抽取出来，重复使用
```
<!--由于我们经常使用 select * from user 这条语句，所以我们可以把这条语句抽取出来-->
<sql id="selectUserAll">			<<<<<----------
    select * from user;
</sql>

<!-- 使用 sql-->
<select id="findAll" resultType="club.twzw.damain.User">
   <include refid="selectUserAll"></include> 			<<<<<----------
</select>
```
## mybatics 中的动态sql语句--批量查询，-foreach
sql 语句 select * from user where id in (41,42,43);
mybatis 实现
```
    <!--    查询多个指定id的数据-->
    <select id="findByUsersIds" resultType="club.twzw.damain.User" parameterType="club.twzw.damain.User">
        select * from user
        <where>
            <if test="ids != null and ids.size()>0">
                <foreach collection="ids" open=" and ids in (" close=")" item="id" separator=",">
                    #{id}
                </foreach>
            </if>
        </where>
    </select>
```
## mybatics 中的动态sql语句--批量保存，-foreach
接口定义
```
void addUsers(@Param("users")List<User> users);
```
xml语句
```
    <insert id="saveUsers" parameterType="domain.User">
        insert INTO user(name,gender,email)values
        <foreach collection="users" item="user" separator=",">
            (#{user.name},#{user.gender},#{user.email})
        </foreach>
    </insert>
```
## mybatics 中字符截取，-trim
如果gender为空，那么会在结尾多出来一个and，而suffixOverrides就是用来截取and的功能
```
    <select id="findByCondition" resultType="domain.User" parameterType="string">
        select * from user
        <trim suffixOverrides="and">
            <where>
                <if test="name !=''">
                    and name like #{name} and
                </if>
                <if test="gender !=''">
                    gender = #{gender}
                </if>
            </where>
        </trim>    
    </select>
```
## mybatics 中字符截取，-choose，类似于switch case选择分支语句
```
     <select id="findByCondition" parameterType="string" resultType="domain.User">
        select *
        from user
        <where>
            <choose>
                <when test="id != ''">
                    id=#{id}
                </when>
                <when test="gender != ''">
                    gender=#{gender}
                </when>
                <otherwise>
                    email=#{email}
                </otherwise>
            </choose>
        </where>
    </select>
```
## mybatics 中数据更新,-set
```
    <update id="updateByCondition" parameterType="string">
        update user
        <set>
            <if test="name !=''">
                name=#{name},
            </if>
            <if test="gender !=''">
                gender=#{gender}
            </if>
        </set>
        where id=#{id}
    </update>
```
## 单个参数和多个参数
如果是单个参数或者javaBean对象，mybatis会自动识别，无需特别处理，但是如果是多个参数，则需要特殊处理
### 使用@Param注解，在接口中使用
```
    boolean addUser(@Param("id")Interger id,@Param("name")String name);
```
在xml中直接使用key即可
```
    <select id="findByCondition" resultMap="userMap">
        select * from user where id = #{id} and name =#{name}
    </select>
```
### 将不相关的数据进行封装成一个参数
java代码中
```
        Map<String,Object> map = new HashMap<>();
        map.put("id",1);
        map.put("name","comi");
        User u = userDao.findByCondition(mao);
```
xml就可以直接使用#{id},#{name}
```
    <select id="findByCondition" resultMap="userMap">
        select * from user where id = #{id} and name =#{name}
    </select>
```
## select 标签
1.使用select 封装 List，如果返回数据为多个，mybatis会自动封装返回的数据为list
2.使用select 封装 Map,resultMap 设置为Map（单个数据）
3.使用select 封装 Map,在方法上添加    @MapKey("id") 指定 key，就可以自动封装
```
@MapKey("id")
public Map<Interger,User> map getUserByCondition(String name);
```
## resultMap 自定义结果集（如果数据库和JavaBean数据名称对应不一致）
```
    <resultMap id="userMap" type="club.twzw.domain.User">
        <!--        主键对应字段-->
        <id property="id" column="Id"></id>
        <!--        非主键对应字段-->
        <result property="name" column="Name"></result>
        <result property="born" column="Born"></result>
        <result property="sex" column="Sex"></result>
    </resultMap>
```
## resultMap中返回的结果中一个类包含另一个类的情况(联合查询)
有两个JavaBean对象，一个是User，一个是account,在查询user时需要查询account。
user
&emsp;id 
&emsp;name
&emsp;gender
&emsp;acc
account
&emsp;id
&emsp;money
进行设置结果集
```
	    <!--方法一-->
    <resultMap id="userMap" type="club.twzw.domain.User">
        <!--        主键对应字段-->
        <id property="id" column="Id"></id>
        <!--        非主键对应字段-->
        <result property="name" column="Name"></result>
        <result property="born" column="Born"></result>
        <result property="gender" column="Gender"></result>
		<result property="aid" column="account.id"></result>  		 <<<<---------
		<result property="amonty" column="account.money"></result>   <<<<---------
    </resultMap>
	
	<!--方法二-->
	<!--property 指的是 要封装的JavaBean中的属性是哪一个-->
	<!--javaType 这个属性对象的类型，即account-->
	<association property="acc" column="id" javaType="club.twzw.domain.account">
		<result property="aid" column="id"></result>  		 <<<<---------
		<result property="amonty" column="money"></result>   <<<<---------
    </association>
```
## mybatis 延迟加载和立即加载
### 什么是延迟加载？
在真正使用数据时，才发起查询，不用的时候不查询，按需加载（懒加载）
### 什么是立即加载？
不管用不用，只要一调用方法，马上发起查询。
在对应的四种表关系中：一对一，多对一(mytatis 没有这个概念)，一对多，多对多。不同情况使用不同的加载策略。
	一对多，多对多：采用延迟加载
	一对一，多对一：采用立即加载
### 配置延迟加载
全局配置文件中加入（如果不加入这两句，就会变为分步查询）
```
    <!--在这里配置相关懒加载参数-->
	<!--这样 懒加载的配置就配置好了-->
    <settings>
        <setting name="lazyLoadingDnabled" value="true"/>
        <setting name="aggressiceLazyLoading" value="false"/>
    </settings>
```
### mybatis 返回数据中子属性为单个JavaBean
在UserDao.xml 中配置 resultMap的association标签属性，重点是select 属性,必须是namespace加方法名，唯一标识用户,通过此方法去查出信息
```
    <!--配置延迟加载-->
    <resultMap id="userMap" type="club.twzw.domain.User">
        <!--主键对应字段-->
        <id property="id" column="id"></id>
        <!--非主键对应字段-->
        <result property="name" column="name"></result>
        <result property="born" column="born"></result>
        <result property="sex" column="sex"></result>
		
        <!--使用association 标签-->
		<!--select 属性指定的内容是查询用户的唯一标识-->
        <!--同样 column 也是必须的，否则无法运行，会报错-->
        <!--这样我们就可以配置相关连的对象了，比如说用户的账户信息等！-->
        <association property="user" column="id" javaType="user" select="club.twzw.dao.UserDao.findById"> 		<<<<<---------
        </association>
    </resultMap>
```
### mybatis 返回数据中子属性为集合
使用标签collection
&emsp;&emsp;collection定义关联集合的属性的封装规则
&emsp;&emsp;ofType指定集合中的元素类型
```
    <resultMap id="userMap" type="club.twzw.domain.User">
        <!--主键对应字段-->
        <id property="id" column="id"></id>
        <!--非主键对应字段-->
        <result property="name" column="name"></result>
        <result property="born" column="born"/>
        <result property="sex" column="sex"></result>
		
        <collection property="account" ofType="club.twzw.domain.account">
            <!--主键对应字段-->
            <id property="aid" column="id"></id>
            <!--非主键对应字段-->
            <result property="amoney" column="money"></result>
        </collection>
    </resultMap>
```
同样，按需加载也是需要设定select属性并书写相应的xml查询语句
### resultMap 鉴别器 discriminator （类似于switch）
加入我们需要根据不同的部门来查询封装信息，则需要使用discriminator标签
javaType：传入值类型
column：哪一列需要作为判断
```
    <resultMap id="userMap" type="club.twzw.domain.User">
        <!--主键对应字段-->
        <id property="id" column="id"></id>
        <!--非主键对应字段-->
        <result property="name" column="name"></result>
        <result property="born" column="born"/>
        <result property="sex" column="sex"></result>

        <discriminator javaType="String" column="department">
            <case value="研发部">
                <!--相关操作-->
            </case>
            <case value="设计部">
                <!--相关操作-->
            </case>
        </discriminator>
    </resultMap>
```
## mybatis 缓存机制
二级缓存（全局缓存）
### 一级缓存（本地缓存）
&emsp;与数据库同一次会话期间查询到的数据会放在本地缓存中，以后有相同数据，直接从缓存中拿，没必要查询数据库,
&emsp;在同一次会话中，相同的sql语句只会查询一次，查询结果被缓存在sqlsseesion中，一级缓存是一直开启的。
#### 一级缓存失效情况
1.sqlsession不同
2.sqlsession相同，查询条件不同（当一级缓存还没有这条数据）
3.sqlsession相同，两次查询之间执行了增删改操作（这次修改可能对之前数据有影响）
4.手动清除cache缓存
```
 @Test
    void testFirstLevel() throws IODxception {
        InputStream in = Resources.getResourceAsStream("mysqlConfig.xml");
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);
        SqlSession session = factory.openSession(true);
        try {
            UserDao dao = session.getMapper(UserDao.class);
            Map<Integer, User> users = dao.getUser("comi");
            System.out.println(users);
        } finally {
            session.close();
        }
    }
```
### 二级缓存
基于namespace级别的缓存，一个namespace对应一个二级缓存
#### 工作机制：
1.一个会话，查询一条数据，这个数据就会放在当前会话的一级缓存中。
2.如果会话关闭，一级缓存中的数据会保存到二级缓存中，新的会话查询信息，就可以参照二级缓存
3.SQL session
&emsp;&emsp;userMapper==>user
&emsp;&emsp;accountMapper==>account
#### 使用步骤
&emsp;&emsp;在全局配置文件中开启全局二级缓存（默认开启）
```
    <settings>
        <setting name="cacheDnabled" value="true"/>
    </settings>
```
&emsp;&emsp;在单独的xml文件中配置二级缓存
```
    <!--    开启缓存，设置刷新缓存间隔 60 秒 刷新规则：先进先出 且只读-->
    <cache readOnly="true" flushInterval="60000" eviction="FIFO"></cache>
```
&emsp;&emsp;JavaBean实现序列化接口
测试代码：不同的sqlsession请求同一个数据，开启二级缓存之后，还是只会请求一次
```
    @Test
    void testSecondCache() throws IODxception {
        InputStream in = Resources.getResourceAsStream("mysqlConfig.xml");
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);
        SqlSession session = factory.openSession(true);
        SqlSession session1 = factory.openSession(true);
        try {
            UserDao dao = session.getMapper(UserDao.class);
            UserDao mapper = session1.getMapper(UserDao.class);
            Map<Integer, User> comi = dao.getUser("comi");
            System.out.println(comi);
            session.close();
            Map<Integer, User> user = mapper.getUser("comi");
            System.out.println(user);
        } finally {
            session1.close();
        }
    }
```