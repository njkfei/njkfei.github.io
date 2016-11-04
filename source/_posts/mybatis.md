title: mybatis分布式事务
date: 2016.01.17
categories: java #分类
tags: [java, mybatis, jta]
toc: true
---
  __mybatis__提供了数据访问框架.相对hibernate，它更轻量化，相对jdbc，它更人性化．是java系访问数据库的最佳框架．mybatis的最佳实践，是与spring协同工作．

 ## 依赖项目
 ``` bash
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.3.0</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.2.2</version>
		</dependency>

		<!-- alibaba -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>1.0.2</version>
		</dependency>

		<!-- mysql 驱动 -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.29</version>
		</dependency>
```
## 使用方法
	mybatis，有两种使用方法．
* xml配置
* 注解驱动
　xml配置功能强大，但配置麻烦，看着比较繁琐．
　注解配置功能稍弱，但可以满足大部分需求，看着比较清爽．
　个人建议，xml和注解配合使用．
  xml完成动态sql功能．
  注解完成常规功能和事务处理．
## xml配置
  xml配置，只要有：
* 驱动源配置
* 动态sql配置．
* 自动扫包配置
* 事务驱动配置

### 驱动源配置
jdbc.properties
``` bash
jdbc_driverClassName=com.mysql.jdbc.Driver
jdbc_url=jdbc:mysql://localhost:3306/hollysys?useUnicode=true&amp;characterEncoding=utf-8
jdbc_username=user
jdbc_password=password
```
数据源
``` bash
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
		init-method="init" destroy-method="close">
		<property name="driverClassName">
			<value>${jdbc_driverClassName}</value>
		</property>
		<property name="url">
			<value>${jdbc_url}</value>
		</property>
		<property name="username">
			<value>${jdbc_username}</value>
		</property>
		<property name="password">
			<value>${jdbc_password}</value>
		</property>
		<!-- 连接池最大使用连接数 -->
		<property name="maxActive">
			<value>20</value>
		</property>
		<!-- 初始化连接大小 -->
		<property name="initialSize">
			<value>1</value>
		</property>
		<!-- 获取连接最大等待时间 -->
		<property name="maxWait">
			<value>60000</value>
		</property>
		<!-- 连接池最大空闲 -->
		<property name="maxIdle">
			<value>20</value>
		</property>
		<!-- 连接池最小空闲 -->
		<property name="minIdle">
			<value>3</value>
		</property>
		<!-- 自动清除无用连接 -->
		<property name="removeAbandoned">
			<value>true</value>
		</property>
		<!-- 清除无用连接的等待时间 -->
		<property name="removeAbandonedTimeout">
			<value>180</value>
		</property>
		<!-- 连接属性 -->
		<property name="connectionProperties">
			<value>clientEncoding=UTF-8</value>
		</property>
	</bean>
```
自动扫描配置
``` bash
	<!-- configLocation为mybatis属性 mapperLocations为所有mapper -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean"
		p:dataSource-ref="dataSource" p:configLocation="classpath:conf/mybatis-config.xml"
		p:mapperLocations="classpath:mapper/*.xml" />
```

通过spring注入
``` bash
	<!-- spring与mybatis整合配置，扫描所有dao -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer"
		p:basePackage="xyz.hollysys.api.dao"
		p:sqlSessionFactoryBeanName="sqlSessionFactory" />
```

### 动态sql配置
	动态sql配置，是一个非常常用的高级功能，对于动态sql,使用xml还是比较方便的．
``` bash
	<!-- 示例中的UserDetail是POJO决不能类 -->
	<update id="updateUserDetail" parameterType="UserDetail">
		update hls_user_detail set user_id=#{user_id} 
		<if test="user_idcard != null and user_idcard != '' ">
			,user_idcard=#{user_idcard}
		</if>
		<if test="user_idcardtype != null and user_idcardtype != '' ">
			,user_idcardtype=#{user_idcardtype}  
		</if>
		<if test=" user_head != null and user_head != '' ">
			,user_head=#{user_head}  
		</if>
		<if test=" user_nickname != null and user_nickname != '' ">
			,user_nickname=#{user_nickname}  
		</if>
		<if test=" user_sex != null and user_sex != '' ">
			,user_sex=#{user_sex}  
		</if>
		<if test=" user_birth_year != 0 ">
			,user_birth_year=#{user_birth_year}  
		</if>
		<if test=" user_birth_month != 0 ">
			,user_birth_month=#{user_birth_month}  
		</if>
		<if test=" user_birth_day != 0 ">
			,user_birth_day=#{user_birth_day}  
		</if>
		<if test=" user_default_address != null and user_default_address != '' ">
			,user_default_address=#{user_default_address}  
		</if>
		<if test=" user_default_mobile != null and user_default_mobile != '' ">
			,user_default_mobile=#{user_default_mobile}  
		</if>
		where user_id = #{user_id}
	</update>
```
	当然，动态sql也可以使用注解来驱动．动态sql的注解有：
* @SelectProvider
* @InsertProvider
* @UpdateProvider
* @DeleteProvider

### list foreach配置
**select**有时需要进行范围查询(in),通过xml也比较方便．
``` bash
	 <select id="order_validAllTT" >
		 select sum(tt_num) as all_tt 
		 from order_ct where teacher_id = #{teacher_id} 
		 and oct_status in 
		 <foreach item="item" index="index" collection="list" open="(" separator="," close=")">
		  	#{oct_status_list}
		 </foreach>
	 </select>
```

### 事务驱动配置
对数据源进行事务管理
``` bash
	<!-- 对数据源进行事务管理 -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager"
		p:dataSource-ref="dataSource" />
```

管理事务注解，@Transational注解的方法，具有事务的特性
``` bash
	<!-- 事务注解驱动，标注@Transactional的类和方法将具有事务性 -->
	<tx:annotation-driven transaction-manager="transactionManager" />
```

 ## 注解配置
 常用的注解有：
 * @Select
 * @Update
 * @Insert
 * @Delete
 * @Transactional

 ### @Select
 查询操作，获取单个记录，简单如下：
 ``` bash
 	@Select("SELECT * FROM course where course_id=#{course_id} limit 1")
	Course getCourseInfo(@Param("course_id")int course_id);
```
获取多条记录，如下：
``` bash
	@Select("SELECT `coupon_id` FROM `coupon` WHERE order_id=#{order_id}")
	List<Integer> getCouponIds(@Param("order_id") int order_id);
```

如果实体类变量名与数据库field字段不一致，则需要配置映射关系．如下所示：
``` bash
	 @Results(value = {  
	   @Result(property="teacher_id",column="teacher_id",javaType=Integer.class,jdbcType=JdbcType.INTEGER),  
	   @Result(property="tt_time", column="tt_time",javaType=Integer.class,jdbcType=JdbcType.INTEGER),
	   @Result(property="free_num", column="free_num",javaType=Short.class,jdbcType=JdbcType.TINYINT),
	   @Result(property="timemap1", column="timemap1",javaType=Integer.class,jdbcType=JdbcType.INTEGER),
	   @Result(property="timemap2", column="timemap2",javaType=Integer.class,jdbcType=JdbcType.INTEGER),
	   @Result(property="timemap3",column="timemap3",javaType=Integer.class,jdbcType=JdbcType.INTEGER)
	 }) 
	@Update("update teacher_time set free_num=#{free_num},timemap1=#{timemap1},timemap2=#{timemap2},timemap3=#{timemap3} where teacher_id=#{teacher_id} and tt_time=#{tt_time}")
	int updateTtByTt(TeacherTime tt);
```

### @Update
更新操作，简单如下：
``` bash
	@Update("update `coupon` set `coupon_status` = #{coupon_status} where `order_id` =  #{order_id}")
	int updateCouponStatus(@Param("order_id")int order_id,@Param("coupon_status")short coupon_status);
```
如果实体类变量名与数据库field字段不一致，则需要配置映射关系．同上．

### @Insert
新增记录．简单如下：
``` bash
	@Insert("insert into teacher_tag(teacher_id,tag_id) values(#{teacher_id},#{tag_id})")
	int insertTt(@Param("teacher_id")int teacher_id,@Param("tag_id")int tag_id);
```
如果实体类变量名与数据库field字段不一致，则需要配置映射关系．同上．

### @Delete
删除记录，如下：
``` bash
	@Delete("delete from ysyy_teacher_tag where teacher_id=#{teacher_id}")
	int deleteTt(@Param("teacher_id")int teacher_id);
```

### @Transactional
用于注解事务，没有事务的业务系统不是真的业务系统，所以这个必须会．其实很简单，在需要事务操作的方法前加上注解即可．
``` bash
	// 用户注册
	@Transactional
	public ApiResult register(Map<String, String> params, String body) {}
```

### 自动生成主建
``` bash
@Insert("INSERT INTO STUDENTS(NAME,EMAIL,ADDR_ID, PHONE)
        VALUES(#{name},#{email},#{address.addrId},#{phone})")
@Options(useGeneratedKeys = true, keyProperty = "studId")
int insertStudent(Student student);
```
这里STUD_ID列值将会通过MySQL数据库自动生成。并且生成的值将会被设置到student对象的studId属性中。

### 结果映射
``` bash
	@Select("select * from app_user where sso_id=#{sso_id}")
	@Results(value = {
			@Result(property = "ssoId", column = "sso_id", javaType = String.class, jdbcType = JdbcType.VARCHAR),
			@Result(property = "firstName", column = "first_name", javaType = String.class, jdbcType = JdbcType.VARCHAR),
			@Result(property = "lastName", column = "last_name", javaType = String.class, jdbcType = JdbcType.VARCHAR) })
	User findBySSO(@Param("sso_id") String sso_id);
```

## 关系映射
　关系映射这种功能，在关系数据库表中相当常见．关系映射有：
* 一对一映射
* 一对多映射
* 多对多映射

### 一对一映射
MyBatis提供了@One注解来使用嵌套select语句（Nested-Select）加载一对一关联查询数据。

### 一对多映射
MyBatis提供了@Many注解，用来使用嵌套Select语句加载一对多关联查询。
``` bash
	@Select("select * from app_user where id=#{id}")
	@Results(value = {
			 @Result(id = true, column = "id", property = "id"),  
            @Result(property="userProfiles", javaType=List.class, column="id",
                   many=@Many(select="xyz.hollysys.spring.mybatis_super.dao.UserProfileDao.getUserProfiles"))
            })
	User findById(@Param("id") int id);
```

__getUserProfiles__的实现在这里
``` bash
	@Select("select uu.user_id as id, p.type as type from app_user_user_profile uu,user_profile p,app_user au where uu.user_profile_id=p.id and uu.user_id=#{user_id} and au.state='Active' and au.id=uu.user_id")
	List<UserProfile> getUserProfiles(@Param("user_id")int user_id);
```


### 多对多映射

## 动态sql(使用注解)

### sql语句来源于注解
``` bash
	@SelectProvider(type = SqlProvider.class, method = "updateUser")
	void update(User user);
```

### 动态sql注解
``` bash
import org.apache.ibatis.jdbc.SQL;
import org.apache.log4j.Logger;
import xyz.hollysys.spring.mybatis_super.model.User;

public class SqlProvider {
	static Logger logger = Logger.getLogger(SqlProvider.class);
	public String selectUser(int id){
		String sql = "select * from app_user where id=" + id;
		logger.info(sql);
		return sql;
	}
	
	public String updateUser(final User user){
		String sql =  new SQL() {
            {
                UPDATE("app_user");
                if (user.getEmail() != null) {
                    SET("email = #{email}");
                }
                if (user.getFirst_name() != null) {
                    SET("first_name = #{first_name}");
                }
                WHERE("id = #{id}");
            }
        }.toString();
		
        logger.info(sql);
        
		return sql;
	}
}
```

  mybatis的基本操作已经完成，其实通过一点小技巧，mybatis还可以搞更高级的操作，如分库分表，读写分离，后继会有介绍．