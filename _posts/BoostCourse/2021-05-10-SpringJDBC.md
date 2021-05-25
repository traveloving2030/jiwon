---
layout: post
title: "Spring JDBC"
date: 2021-05-10
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# Spring JDBC

- JDBC 프로그래밍을 보면 반복되는 개발 요소가 있음
- 이러한 반복적인 요소는 개발자를 지루하게 만듦
- 개발하기 지루한 JDBC의 모든 저수준 세부사항을 스프링 프레임워크가 처리해줌
- 개발자는 필요한 부분만 개발하면 됨

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/39.png" height="280" width="300" />


# Spring JDBC 패키지

- org.springframework.jdbc.core
    - JdbcTemplate 및 관련 Helper 객체 제공
- org.springframework.jdbc.datasource
    - DataSource를 쉽게 접근하기 위한 유틸 클래스, 트랜젝션매니져 및 다양한 DataSource 구현을 제공
- org.springframework.jdbc.object
    - RDBMS 조회, 갱신, 저장등을 안전하고 재사용 가능한 객제 제공
- org.springframework.jdbc.support
    - jdbc.core 및 jdbc.object를 사용하는 JDBC 프레임워크를 지원

# JDBC Template

- org.springframework.jdbc.core에서 가장 중요한 클래스
- 리소스 생성, 해지를 처리해서 연결을 닫는 것을 잊어 발생하는 문제 등을 피할 수 있도록 함
- 스테이먼트(Statement)의 생성과 실행을 처리
- SQL 조회, 업데이트, 저장 프로시저 호출, ResultSet 반복호출 등을 실행
- JDBC 예외가 발생할 경우 org.springframework.dao패키지에 정의되어 있는 일반적인 예외로 변환


# JDBC Template 실습 예제 1

- 열의 수 구하기

```java
int rowCount = this.jdbcTemplate.queryForInt("select count(*) from t_actor");
```

- 변수 바인딩 사용하기

```java
int countOfActorsNamedJoe = this.jdbcTemplate.queryForInt("select count(*) from t_actor where first_name = ?", "Joe"); 
```

- String값으로 결과 받기

```java
String lastName = this.jdbcTemplate.queryForObject("select last_name from t_actor where id = ?", new Object[]{1212L}, String.class); 
```

- 한 건 조회하기

```java
Actor actor = this.jdbcTemplate.queryForObject(

  "select first_name, last_name from t_actor where id = ?",

  new Object[]{1212L},

  new RowMapper<Actor>() {

    public Actor mapRow(ResultSet rs, int rowNum) throws SQLException {

      Actor actor = new Actor();

      actor.setFirstName(rs.getString("first_name"));

      actor.setLastName(rs.getString("last_name"));

      return actor;

    }

  });
```

- 여러 건 조회하기

```java
List<Actor> actors = this.jdbcTemplate.query(

  "select first_name, last_name from t_actor",

  new RowMapper<Actor>() {

    public Actor mapRow(ResultSet rs, int rowNum) throws SQLException {

      Actor actor = new Actor();

      actor.setFirstName(rs.getString("first_name"));

      actor.setLastName(rs.getString("last_name"));

      return actor;

    }

  });
```

- 중복 코드 제거(1건 구하기와 여러 건 구하기가 같은 코드에 있을 경우)

```java
public List<Actor> findAllActors() {

  return this.jdbcTemplate.query( "select first_name, last_name from t_actor", new ActorMapper());

}

private static final class ActorMapper implements RowMapper<Actor> {

  public Actor mapRow(ResultSet rs, int rowNum) throws SQLException {

    Actor actor = new Actor();

    actor.setFirstName(rs.getString("first_name"));

    actor.setLastName(rs.getString("last_name"));

    return actor;

  }

}
```

- Insert

```java
this.jdbcTemplate.update("insert into t_actor (first_name, last_name) values (?, ?)",  "Leonor", "Watling");
```

- Update

```java
this.jdbcTemplate.update("update t_actor set = ? where id = ?",  "Banjo", 5276L);
```

- Delete

```java
this.jdbcTemplate.update("delete from actor where id = ?", Long.valueOf(actorId));
```


# DTO (Data Transfer Object)

- 하나의 가방처럼 만들어 데이터를 한꺼번에 들고다닌다
- 계층간 데이터 교환을 위한 자바빈즈
- 여기서의 계층이란 컨트롤러 뷰, 비지니스 계층, 퍼시스턴스 계층을 의미
- 일반적으로 DTO는 로직을 가지고 있지 않고, 순수한 데이터 객체
- 필드와 getter, setter를 가진다. 추가적으로 toString(), equals(), hashCode()등의 Object 메소드를 오버라이딩 가능

 ```java
public class ActorDTO {
    private Long id;
    private String firstName;
    private String lastName;
    public String getFirstName() {
        return this.firstName;
    }
    public String getLastName() {
        return this.lastName;
    }
    public Long getId() {
        return this.id;
    }
    // ......
}
 ```

# DAO(Data Access Object)

- DAO란 Data Access Object의 약자로 데이터를 조회하거나 조작하는 기능을 전담하도록 만든 객체
- 보통 데이터베이스를 조작하는 기능을 전담하는 목적으로 만들어짐


# Connection Pool

- DB연결은 비용이 많이 듬
- 커넥션 풀은 미리 커넥션을 여러 개 맺어 둠
- 커넥션이 필요하면 커넥션 풀에게 빌려서 사용한 후 반납
- 커넥션을 반납하지 않으면 어떻게 될까?
    - Connection을 사용하면 되도록 빨리 반납해야함. 그렇지 않으면 Connection Pool에서 사용가능한 Connection이 없어 프로그램이 늦어지거나 장애를 일으킴

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/41.jpg" height="280" width="300" />

- Client1, 2가 Connection Pool로부터 Connection 객체를 얻어 사용
- Client3이 다시 Connection을 얻어 사용
- Client1이 Connection을 close하면 사용한 Connection이 Connection Pool로 반납됨


# Data Source

- DataSource는 커넥션 풀을 관리하는 목적으로 사용되는 객체
- DataSource를 이용해 커넥션을 얻어오고 반납하는 등의 작업을 수행


# Spring JDBC를 이용한 DAO 작성 실습

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/40.png" height="280" width="300" />

- Spring Container인 ApplicationContext는 ApplicationConfig Class 설정파일을 읽어들임
- ApplicationConfig에는 @ComponentScan 어노테이션이 DAO Class를 찾도록 설정할것
- 찾은 모든 DAO 클래스는 Spring Container(ApplicationContext)가 관리하게 됨
- ApplicationContext는 DBConfigClass를 Import함
- DBConfig Class에서는 dataSource와 Tx Manager 객체를 생성
- DAO는 Field로 NamedParameterJDBCTemplate 과 SimpleJDBCInsert를 가지게됨. 두 개의 객체는 Data Source를 필요로 함
    - 두개의 객체 모두 SQL의 실행을 편리하게 하도록 Spring JDBC에서 제공하는 객체
    - 두개의 객체는 RoleDao 생성자에서 초기화하고 RoleDAO의 메소드를 구현
- SpringJDBC를 사용하는 사용자는 파라미터와 SQL을 가장 많이신경써야함
- SQL은 RoleDAOSqls에 상수로 정의를 해놓음으로서 나중에 SQL이 변경될 경우 좀 더 편하게 수정될수 있도록함
- 한건의 Role 정보를 저장하고 전달하기 위한 목적으로 RoleDTO가 사용되고 있음


1. MavenProject 생성 : (quickStart) 후 name은 daoexam

2. pom.xml 수정 (Spring 관련 Artifact, JDBC 관련 Artifact)

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>kr.or.connect</groupId>
  <artifactId>daoexam</artifactId>
  <version>0.0.1-SNAPSHOT</version>

  <name>daoexam</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

   <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
    <spring.version> 4.3.14.RELEASE</spring.version>
  </properties>

  <dependencies>
  	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context</artifactId>
		<version>${spring.version}</version>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-jdbc</artifactId>
		<version>${spring.version}</version>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-tx</artifactId>
		<version>${spring.version}</version>
	</dependency>
	
	<dependency>
		<groupId>org.apache.commons</groupId>
		<artifactId>commons-dbcp2</artifactId>
		<version>2.1.1</version>
	</dependency>

	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
		<version>5.1.45</version>
	</dependency>
		
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

   <build>
     <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.6.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
  </build>
</project>

```

3. Maven Project Update

4. kr.or.connect.daoexam 패키지 내 kr.or.connect.daoexam.config 패키지 생성 후 ApplicationConfig 클래스 파일 생성
    - @Configuration 어노테이션 설정
    - @Import({DBConfig.class}) 어노테이션 설정
        - 설정파일을 여러개로 나눠서 작성할 수 있음!
        - 설정 파일 하나에 다 넣는 것이 아니라 데이터베이스 관련 설정은 따로 빼주고 싶다!

```java
//ApplicationConfig.java
package kr.or.connect.daoexam.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import({DBConfig.class})
public class ApplicationConfig {

}

```

5. kr.or.connect.daoexam.config 패키지 내 DBConfig 클래스 파일 생성
    - @Configuration 어노테이션 설정
    - @EnableTransactionManagement 어노테이션 설정 => DB 트랜잭션 시 필요
    - JDBC 연결과 관련된 변수들 지정
    - Spring JDBC를 사용할 때 DataSource를 사용해서 DB에 접속하게 되는데 이때, DataSource를 생성할 수 있는 클래스가 필요함
    - DataSource 객체를 등록할 때 @Bean을 이용!!(우리가 작성할 객체는 아니고 이미 작성된 객체를 사용할 것이기 때문)
        - 메소드의 이름은 Bean등록할 때 id로 지정하는 이름과 같음(dataSource)


```java
//DBConfig.java
package kr.or.connect.daoexam.config;

import javax.sql.*;

import org.apache.commons.dbcp2.BasicDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@Configuration
@EnableTransactionManagement
public class DBConfig {
	private String driverClassName = "com.mysql.jdbc.Driver";
    private String url = "jdbc:mysql://localhost:3306/connectdb?useUnicode=true&characterEncoding=utf8";

    private String username = "connectuser";
    private String password = "connect123!@#";

    @Bean
    public DataSource dataSource() {
    	BasicDataSource dataSource = new BasicDataSource();
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;

    }
}

```

6. dataSource가 잘 동작되는지 확인하기 위한 클래스 생성을 위해 kr.or.connect.daoexam 패키지 내 kr.or.connect.daoexam.main 패키지 생성 후 DataSourceTest.java 클래스 생성
    - Beans를 관리하는 ApplicationContext 공장 생성
        - 어떤 클래스에서 Annotation 정보를 읽어오는지 명시 (ApplicationConfig.java 클래스에서 읽어들이는데 import @DBConfig 때문에 DBConfig.java까지 감)
        - 공장을 통해 얻어내야하는 것은 DataSource객체 ds
        - 얻어온 ds객체를 이용하여 getConnection 메소드 호출하여 Connection 얻어옴

```java
//DataSourceTest.java
package kr.or.connect.daoexam.main;

import java.sql.Connection;

import javax.sql.DataSource;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import kr.or.connect.daoexam.config.ApplicationConfig;

public class DataSourceTest {
    public static void main( String[] args )
    {
        ApplicationContext ac = new AnnotationConfigApplicationContext(ApplicationConfig.class);
        DataSource ds = ac.getBean(DataSource.class);
        Connection conn = null;
        try {
        	conn = ds.getConnection();
        	if(conn != null) {
        		System.out.println("접속성공!");
        	}
        }catch (Exception e) {
			e.printStackTrace();
		}finally {
			if(conn != null) {
				try {
					conn.close();
				}catch (Exception e) {
					e.printStackTrace();
					// TODO: handle exception
				}
			}
		}
        
    }	
}

```


# Select 하는 부분 구현
    - data가 왔다갔다 해야하니 DTO 객체 생성해야함
    - Query문을 가진 RoleDaoSqls 클래스 생성
    - Data Access 할 수 있는 객체도 필요

1. kr.or.connect.daoexam 패키지 내 kr.or.connect.daoexam.dto 패키지 생성 후 Role.java 클래스 생성
    - toString() 메소드 : dto 가방안에 어떤 내용물들이 있는지 쉽게 확인할 수 있음!

```java
package kr.or.connect.daoexam.dto;

public class Role {
	private int roleId;
	private String description;
	
	public int getRoleId() {
		return roleId;
	}
	public void setRoleId(int roleId) {
		this.roleId = roleId;
	}
	public String getDescription() {
		return description;
	}
	public void setDescription(String description) {
		this.description = description;
	}
	@Override
	public String toString() {
		return "Role [roleId=" + roleId + ", description=" + description + "]";
	}
}

```

2. kr.or.connect.daoexam 패키지 내 kr.or.connect.daoexam.dao 패키지 생성 후 RoleDaoSqls.java 클래스 생성
    - 상수는 모든 글자가 대문자로 쓰는게 관례!!

```java
package kr.or.connect.daoexam.dao;

public class RoleDaoSqls {
	public static final String SELECT_ALL = "SELECT role_id, description FROM role order by role_id";
}

```

3. kr.or.connect.daoexam.dao 패키지 내 RoleDao.java 클래스 생성

    - 실제 Spring 컨테이너가 읽어서 사용해야하는데 Bean을 등록할 수 있는 여러가지 방법들이 있었음
    - 그 중 Annotation을 활용하여 @autoWired와 방식으로 읽어낼 수 있었는데 그때 붙일 수 있는 Annotation이 {Component, Service, Repository, Controller} 등이 존재하였는데, DAO 클래스에는 `저장소의 역할`을 하는 의미의 `Repository Annotation`을 붙임
    - 전체 그림을 보면 DAO 클래스 내에서 Select 해올 때 NamedParameterJdbcTemplate 객체가 필요함
    - 코드를 보면, 원래 파라미터를 받을 때 ?를 사용했는데, 이는 어떤 값이 매핑되는지 알아보기 힘듦. 이때, NamedParameterJdbcTemplate를 사용하게 되면 바인딩하거나 결과값 받아올때 사용할 수 있음

```java
//RoleDao.java
package kr.or.connect.daoexam.dao;

import static kr.or.connect.daoexam.dao.RoleDaoSqls.*;

import java.util.Collections;
import java.util.List;
import java.util.Map;

import javax.sql.DataSource;

import org.springframework.dao.EmptyResultDataAccessException;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.jdbc.core.namedparam.BeanPropertySqlParameterSource;
import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
import org.springframework.jdbc.core.namedparam.SqlParameterSource;
import org.springframework.jdbc.core.simple.SimpleJdbcInsert;
import org.springframework.stereotype.Repository;

import kr.or.connect.daoexam.dto.Role;

@Repository
public class RoleDao {
	private NamedParameterJdbcTemplate jdbc;
	private SimpleJdbcInsert insertAction;
	private RowMapper<Role> rowMapper = BeanPropertyRowMapper.newInstance(Role.class);

	public RoleDao(DataSource dataSource) {
		this.jdbc = new NamedParameterJdbcTemplate((DataSource) dataSource);
		this.insertAction = new SimpleJdbcInsert((DataSource) dataSource)
                .withTableName("role");
	}
	
	public List<Role> selectAll(){
		return jdbc.query(SELECT_ALL, Collections.emptyMap(), rowMapper);
	}
}


```


 - 생성자 부분을 보면 dataSource 객체를 파라미터로 받음
    - Spring v4.3부터는 ComponentScan으로 객체를 찾았을 때 기본 생성자가 없다면 `자동으로 객체를 주입`해줌
        - `DBConfig에서 Bean으로 등록했던 dataSource가 파라미터로 전달`됨
        - NamedParameterJdbcTemplate 객체 생성

```java
	public RoleDao(DataSource dataSource) {
		this.jdbc = new NamedParameterJdbcTemplate((DataSource) dataSource);
		this.insertAction = new SimpleJdbcInsert((DataSource) dataSource)
                .withTableName("role");
	}
```

- 실제로 우리가 생성할 부분은 selectAll 해서 가져오는 부분
    - 복잡한 코드를 NamedParameterJdbcTemplate이 다 해줌
    - import static kr.or.connect.daoexam.dao.RoleDaoSqls.*  : static 해주면 RoleDaoSqls 내에 선언된 변수들을 클래스 이름없이 바로 사용할 수 있게끔 해줌(SELECT_ALL 변수를 그대로 가져다 쓸 수 있음)
    - Collections.emptyMap() : sql문에 바인딩할 값이 있을 경우 바인딩 할 값을 전달할 목적으로 사용
    - rowMapper : Select 한건 한건의 결과를 DTO에 저장(BeanPropertyRowMapper을 이용하여 컬럼의 값을 자동으로 DTO에 담아주게 됨)
    - query 메소드는 결과가 여러번일 때 내부적으로 반복하면서 DTO 생성
      - 생성한 DTO를 List에 담아주고 그 해당 List를 반환해줌
    
```java
  import static kr.or.connect.daoexam.dao.RoleDaoSqls.*;
	private RowMapper<Role> rowMapper = BeanPropertyRowMapper.newInstance(Role.class);

	public List<Role> selectAll(){
		return jdbc.query(SELECT_ALL, Collections.emptyMap(), rowMapper);
	}
```

4. kr.or.connect.daoexam.config 패키지 내 ApplicationConfig 클래스에 RoleDAO에서 명시한 Bean 읽어오기
  - ComponentScan으로 읽어낼거야!
  - @ComponentScan을 추가함으로서 RoleDAO의 @Repository가 붙은 클래스를 Bean으로 등록해둔 것과 같은 역할을 해줄 것!

```java
package kr.or.connect.daoexam.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@ComponentScan(basePackages = {"kr.or.connect.daoexam.dao"})
@Import({DBConfig.class})
public class ApplicationConfig {

}
```

5. 이제 이 부분이 제대로 잘 동작하는지 확인
  - kr.or.connect.daoexam.main 패키지 내 SelectAllTest.java 클래스 생성
  - 동작확인

```java
package kr.or.connect.daoexam.main;

import java.util.List;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import kr.or.connect.daoexam.config.ApplicationConfig;
import kr.or.connect.daoexam.dao.RoleDao;
import kr.or.connect.daoexam.dto.Role;

public class SelectAllTest {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		ApplicationContext ac = new AnnotationConfigApplicationContext(ApplicationConfig.class); 
		
		RoleDao roleDao =ac.getBean(RoleDao.class);

		List<Role> list = roleDao.selectAll();
		
		for(Role role: list) {
			System.out.println(role);
		}
	}

}

```

