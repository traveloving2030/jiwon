---
layout: post
title: "Layered Architecture 실습"
date: 2021-05-14
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# Layered Architecture 실습1 - 방명록 만들기
- SpringJDBC를 이용한 DAO 작성
- Controller + Service + DAO
- 트랜잭션 처리
- SpringMVC에서 폼 값 입력받기
- SpringMVC에서 redirect하기
- Controller에서 jsp에게 전달한 값을 JSTL과 EL을 이용하여 출력하기

## 방명록 요구사항 1/6
- 방명록 정보는 guestbook 테이블에 저장
- id는 자동으로 입력됨
- id, 이름, 내용, 등록일을 저장

## 방명록 요구사항 2/6
- http://localhost:8080/guestbook 을 요청하면 자동으로 /guestbook/list로 리다이렉팅한다
- 방명록이 없으면 건수는 0이 나오고 아래에 방명록을 입력하는 폼이 보여짐

## 방명록 요구사항 3/6
- 이름과 내용을 입력하고 등록버튼을 누르면 /guestbook/write URL로 입력한 값을 전달하여 저장
- 값이 저장된 이후에는 /guestbook/list로 리다이렉트됨

## 방명록 요구사항 4/6
- 입력한 정보가 보여짐
- 방명록 내용과 폼 사이의 숫자는 방명록 페이지 링크. (방명록 5건당 1페이지로 설정)

## 방명록 요구사항 5/6
- 방명록 6건 입력되자 페이지 수가 2건 보여짐
    - /guestbook/list?start=0 을 요청하고, 2페이지를 누르면 /guestbook/list?start=5 를 요청하게 됨
    - /guestbook/list 는 /guestbook/list? start=0과 같음

## 방명록 요구사항 6/6
- 방명록에 글을 쓰거나, 방명록의 글을 삭제할 때는 Log테이블에 클라이언트의 ip주소, 등록(삭제) 시간, 등록/삭제(method칼럼) 정보를 데이터베이스에 저장
- 사용하는 테이블은 log임
- id는 자동으로 입력되도록 함

## 방령록 클래스 다이어그램(중요!!)
- 웹 레이어 설정 파일 : web.xml, WebMvcContextConfiguration.java
- 비지니스, 레파지토리 레이어 설정파일 : ApplicationConfig.java, DBConfig.java
    - web.xml 에는 2개의 Java Config파일 설정
        - DispatcherServlet이 읽어들이는 WebMvcContextConfiguration.java 설정파일
        - ApplicationContextListener가 읽어들일 ApplicationConfig.java 설정파일
            - ApplicatoinConfig.java 설정파일은 DBConfig.java 파일을 Import할 것


<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/51.PNG" height="280" width="300" />


## 방명록 웹 어플리케이션 전체 아키텍처

<img src = "https://traveloving2030.github.io/jiwon/assets/img/post/부스트코스/52.jpg" height="280" width="300" />

- (1) URL 요청을 처리하는 핸들러인 GuestbookController가 있음
- (2) GuestbookController는 비지니스 로직을 가지고 있는 Guestbook Service 객체를 사용함
    - (2-1)해당 서비스 객체는 Guestbook Service 라는 인터페이스와 GuestbookServiceImpl 라는 구현 클래스로 구성
- (3) GuestbookServiceImpl에서는 LogDao와 GuestbookDao를 이용하여 비지니스 로직을 수행함
    - 이때, 비지니스 로직은 하나의 트랜잭션 단위로 실행됨
    - LogDao는 저장만할 뿐 나머지 작업은 수행하지 않음. 따라서 별도의 SQL은 필요하지 않음
    - (3-1) GuestbookDao에서는 여러개의 SQL작업을 수행하기 때문에 별도의 SQL파일들이 필요함
        - 이런 sql들은 GuestbookDaoSqls 파일에 SQL을 관리함
- (4) 각각 사용할 DTO 필요
- (5) View의 역할을 수행하기 위해 list.jsp 작성
- (6) index.jsp는 Redirect하는 코드만 나오게 될 것


# 환경설정

1. pom.xml 수정


```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>kr.or.connect.guestbook</groupId>
<artifactId>guestbook</artifactId>
<packaging>war</packaging>
<version>0.0.1-SNAPSHOT</version>
<name>guestbook Maven Webapp</name>
<url>http://maven.apache.org</url>
<properties>
<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
<spring.version>4.3.5.RELEASE</spring.version>
<!-- jackson -->
<jackson2.version>2.8.6</jackson2.version>
</properties>

<dependencies>
<!-- SPRING -->
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
<artifactId>spring-webmvc</artifactId>
<version>${spring.version}</version>
</dependency>
<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-tx</artifactId>
<version>${spring.version}</version>
</dependency>
<!-- MYSQL -->

<dependency>
<groupId>mysql</groupId>
<artifactId>mysql-connector-java</artifactId>
<version>5.1.45</version>
</dependency>
<!-- DATASOURCE -->
<dependency>
<groupId>org.apache.commons</groupId>
<artifactId>commons-dbcp2</artifactId>
<version>2.0</version>
</dependency>


<!-- Servlet JSP JSTL -->
<dependency>
<groupId>javax.servlet</groupId>
<artifactId>javax.servlet-api</artifactId>
<version>3.1.0</version>
<scope>provided</scope>
</dependency>
<dependency>
<groupId>javax.servlet.jsp</groupId>
<artifactId>javax.servlet.jsp-api</artifactId>
<version>2.3.1</version>
<scope>provided</scope>
</dependency>
<dependency>
<groupId>jstl</groupId>
<artifactId>jstl</artifactId>
<version>1.2</version>
</dependency>


<!-- Jackson Module -->
<dependency>
<groupId>com.fasterxml.jackson.core</groupId>
<artifactId>jackson-databind</artifactId>
<version>${jackson2.version}</version>
</dependency>
<dependency>
<groupId>com.fasterxml.jackson.datatype</groupId>
<artifactId>jackson-datatype-jdk8</artifactId>
<version>${jackson2.version}</version>
</dependency>

<dependency>
<groupId>junit</groupId>
<artifactId>junit</artifactId>
<version>3.8.1</version>
<scope>test</scope>
</dependency>
</dependencies>
<!-- FOR JAVA 1.8 -->
<build>
<finalName>guestbook</finalName>
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

2. MavenUpdate
3. .Settings 모듈 버전 바꿔주기 (2.3 => 3.1)
4. 이클립스 재시작 & Property 탭 Project Facuets 의 Dynamic WebModule 3.1 버전확인


# 실습 Start

1. 4개의 설정파일 패키지 만들기 (kr.or.connect.guestbook.config)
    - WebMvcContextConfiguration.java
    - DBConfig.java
    - ApplicationConfig.java
    - web.xml

- WebMvcContextConfiguration.java 클래스 생성
    - `이 클래스에 명시되는 설정들은 Dispatcher Servlet이 읽어들임`

```java
package kr.or.connect.guestbook.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.DefaultServletHandlerConfigurer;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
import org.springframework.web.servlet.view.InternalResourceViewResolver;

@Configuration
@EnableWebMvc
@ComponentScan(basePackages = { "kr.or.connect.guestbook.controller" })
public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter{

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/css/**").addResourceLocations("/css/").setCachePeriod(31556926);
        registry.addResourceHandler("/img/**").addResourceLocations("/img/").setCachePeriod(31556926);
        registry.addResourceHandler("/js/**").addResourceLocations("/js/").setCachePeriod(31556926);
    }
 
    // default servlet handler를 사용하게 합니다.
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }
   
    @Override
    public void addViewControllers(final ViewControllerRegistry registry) {
    		System.out.println("addViewControllers가 호출됩니다. ");
        registry.addViewController("/").setViewName("index");
    }
    
    @Bean
    public InternalResourceViewResolver getInternalResourceViewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        return resolver;
    }
}
```

2. kr.or.connect.guestbook.config 내 DBConfig.java 파일 생성
    - @EnableTransactionManagement는 트랜잭션과 관련된 설정을 자동으로 해줌
    - 사용자의 트랜잭션 처리를 위한 Flatform TransactionManager를 설정하기 위해서는 TransactionManagementConfigurer를 구현하고 annotationDrivenTransactionManager()를 Overiding해줘야함

```java
package kr.or.connect.guestbook.config;

import javax.sql.DataSource;

import org.apache.commons.dbcp2.BasicDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;
import org.springframework.transaction.annotation.TransactionManagementConfigurer;

@Configuration
@EnableTransactionManagement
public class DBConfig implements TransactionManagementConfigurer {
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

	@Override
	public PlatformTransactionManager annotationDrivenTransactionManager() {
		return transactionManger();
	}

	@Bean
	public PlatformTransactionManager transactionManger() {
		return new DataSourceTransactionManager(dataSource());
	}
}
```

3. kr.or.connect.guestbook.config 내 ApplicationConfig.java 파일 생성
    - DAO 또는 Service Component에 구현된 내용들을 읽어오면됨

```java
package kr.or.connect.guestbook.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@ComponentScan(basePackages = { "kr.or.connect.guestbook.dao",  "kr.or.connect.guestbook.service"})
@Import({ DBConfig.class })
public class ApplicationConfig {

}
```

4. web.xml 파일 수정
    - `모든 요청 /을 받을 때 DispatcherServlet이 받음`(이 Servlet을 Front Controller로 등록하는 과정)
    - 위에서 설정파일들을 나누었다(레이어드 아키텍처의 특징상` Presentation Layer와 나머지 부분들을 분리`하기 위해)
        - `Business Logic쪽에서 사용`되는 것이 `DBConfig와 ApplicationConfig에 나누어 줌`
        - 이 부분을 읽어들이기 위해 사용하는 것이 아래 `listener 부분임`
    - filter부분은 요청이 수행되기 전, 응답이 나가기 전에 한번씩 걸쳐서 수행을 할 수 있도록 해줌

```xml
 <?xml version="1.0" encoding="UTF-8"?>
<web-app>

	<display-name>Spring JavaConfig Sample</display-name>
	<context-param>
		<param-name>contextClass</param-name>
		<param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext
		</param-value>
	</context-param>
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>kr.or.connect.guestbook.config.ApplicationConfig
		</param-value>
	</context-param>
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener
		</listener-class>
	</listener>

	<servlet>
		<servlet-name>mvc</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet
		</servlet-class>
		<init-param>
			<param-name>contextClass</param-name>
			<param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext
			</param-value>
		</init-param>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>kr.or.connect.guestbook.config.WebMvcContextConfiguration
			</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>mvc</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter
		</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
</web-app>
```

4. WEB-INF/views 폴더 생성후 밑에 index.jsp 파일 생성

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<% 
	response.sendRedirect("list");
%>
```

5. MySQL guestbook, log Table 생성

```sql
CREATE TABLE guestbook(
id bigint(20) unsigned NOT NULL AUTO_INCREMENT,
name varchar(255) NOT NULL,
content text,
regdate datetime,
PRIMARY KEY (id)
);

CREATE TABLE log(
id bigint(20) unsigned NOT NULL AUTO_INCREMENT,
ip varchar(255) NOT NULL,
method varchar(10) NOT NULL,
regdate datetime,
PRIMARY KEY(id)
);
```

# Repository Layer 설계
- 즉, 실제 데이터베이스에 접근해서 데이터를 가져오는 일들을 수행함

1. kr.or.connect.guestbook.dto 패키지 생성
    - Guestbook과 Log 클래스 생성

```java
//Guestbook.java
package kr.or.connect.guestbook.dto;

import java.util.Date;

public class Guestbook {
	private Long id;
	private String name;
	private String content;
	private Date regdate;
	public Long getId() {
		return id;
	}
	public void setId(Long id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getContent() {
		return content;
	}
	public void setContent(String content) {
		this.content = content;
	}
	public Date getRegdate() {
		return regdate;
	}
	public void setRegdate(Date regdate) {
		this.regdate = regdate;
	}
	@Override
	public String toString() {
		return "Guestbook [id=" + id + ", name=" + name + ", content=" + content + ", regdate=" + regdate + "]";
	}
}
```

```java
//Log.java
package kr.or.connect.guestbook.dto;

import java.util.Date;

public class Log {
	private Long id;
	private String ip;
	private String method;
	private Date regdate;
	public Long getId() {
		return id;
	}
	public void setId(Long id) {
		this.id = id;
	}
	public String getIp() {
		return ip;
	}
	public void setIp(String ip) {
		this.ip = ip;
	}
	public String getMethod() {
		return method;
	}
	public void setMethod(String method) {
		this.method = method;
	}
	public Date getRegdate() {
		return regdate;
	}
	public void setRegdate(Date regdate) {
		this.regdate = regdate;
	}
	@Override
	public String toString() {
		return "Log [id=" + id + ", ip=" + ip + ", method=" + method + ", regdate=" + regdate + "]";
	}
}
```

2. kr.or.connect.guestbook.dao 패키지 생성
- LogDao클래스 생성
    - usingGeneratedKeyColumns("id")
        - id 컬럼의 값을 자동으로 입력하도록 설정하게 해줌
    - insertAction.executeAndReturnKey
        - insert문을 내부적으로 생성해서 실행하고 자동으로 생성된 id값을 리턴하게 됨

```java
package kr.or.connect.guestbook.dao;

import javax.sql.DataSource;

import org.springframework.jdbc.core.namedparam.BeanPropertySqlParameterSource;
import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
import org.springframework.jdbc.core.namedparam.SqlParameterSource;
import org.springframework.jdbc.core.simple.SimpleJdbcInsert;
import org.springframework.stereotype.Repository;

import kr.or.connect.guestbook.dto.Log;

@Repository
public class LogDao {
	private NamedParameterJdbcTemplate jdbc;
    private SimpleJdbcInsert insertAction;

    public LogDao(DataSource dataSource) {
        this.jdbc = new NamedParameterJdbcTemplate(dataSource);
        this.insertAction = new SimpleJdbcInsert(dataSource)
                .withTableName("log")
                .usingGeneratedKeyColumns("id");
    }

	public Long insert(Log log) {
		SqlParameterSource params = new BeanPropertySqlParameterSource(log);
		return insertAction.executeAndReturnKey(params).longValue();
	}
}
```

- GuestbookDaoSqls 쿼리문 클래스 생성

```java
package kr.or.connect.guestbook.dao;

public class GuestbookDaoSqls {
	public static final String SELECT_PAGING = "SELECT id, name, content, regdate FROM guestbook ORDER BY id DESC limit :start, :limit";
	public static final String DELETE_BY_ID = "DELETE FROM guestbook WHERE id = :id";
	public static final String SELECT_COUNT = "SELECT count(*) FROM guestbook";
}
```

- GuestbookDao 클래스 생성

```java
package kr.or.connect.guestbook.dao;

import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import javax.sql.DataSource;

import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.jdbc.core.namedparam.BeanPropertySqlParameterSource;
import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
import org.springframework.jdbc.core.namedparam.SqlParameterSource;
import org.springframework.jdbc.core.simple.SimpleJdbcInsert;
import org.springframework.stereotype.Repository;

import kr.or.connect.guestbook.dto.Guestbook;

import static kr.or.connect.guestbook.dao.GuestbookDaoSqls.*;

@Repository
public class GuestbookDao {
	 private NamedParameterJdbcTemplate jdbc;
	    private SimpleJdbcInsert insertAction;
	    private RowMapper<Guestbook> rowMapper = BeanPropertyRowMapper.newInstance(Guestbook.class);

	    public GuestbookDao(DataSource dataSource) {
	        this.jdbc = new NamedParameterJdbcTemplate(dataSource);
	        this.insertAction = new SimpleJdbcInsert(dataSource)
	                .withTableName("guestbook")
	                .usingGeneratedKeyColumns("id");
	    }
	    
	    public List<Guestbook> selectAll(Integer start, Integer limit) {
	    		Map<String, Integer> params = new HashMap<>();
	    		params.put("start", start);
	    		params.put("limit", limit);
	        return jdbc.query(SELECT_PAGING, params, rowMapper);
	    }


		public Long insert(Guestbook guestbook) {
			SqlParameterSource params = new BeanPropertySqlParameterSource(guestbook);
			return insertAction.executeAndReturnKey(params).longValue();
		}
		
		public int deleteById(Long id) {
			Map<String, ?> params = Collections.singletonMap("id", id);
			return jdbc.update(DELETE_BY_ID, params);
		}
		
		public int selectCount() {
			return jdbc.queryForObject(SELECT_COUNT, Collections.emptyMap(), Integer.class);
		}
}
```

- 지금까지 만든거 잘 작동하는지 확인하기 위해 GuestbookDaoTest Class 생성
    - 어떤 메소드를 하나씩 구현하고나서 그 메소드가 `잘 동작하는지 테스트하는것이 매우 중요!!`    
    - 매번 main 메소드 적어서 작동 확인하기가 번거롭다
    - `J유닛`과 같은 `단위테스트 공부 필요`

```java
package kr.or.connect.guestbook.dao;

import java.util.Date;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import kr.or.connect.guestbook.config.ApplicationConfig;
import kr.or.connect.guestbook.dto.Guestbook;
import kr.or.connect.guestbook.dto.Log;

public class GuestbookDaoTest {

	public static void main(String[] args) {
		ApplicationContext ac = new AnnotationConfigApplicationContext(ApplicationConfig.class); 
//		GuestbookDao guestbookDao = ac.getBean(GuestbookDao.class);
//		
//		Guestbook guestbook = new Guestbook();
//		guestbook.setName("정지원");
//		guestbook.setContent("반갑습니다. 여러분.");
//		guestbook.setRegdate(new Date());
//		Long id = guestbookDao.insert(guestbook);
//		System.out.println("id : " + id);
		
		LogDao logDao = ac.getBean(LogDao.class);
		Log log = new Log();
		log.setIp("127.0.0.1");
		log.setMethod("insert");
		log.setRegdate(new Date());
		logDao.insert(log);
	}

}
```


# Service Layer 설계
- 비지니스 메소드를 가지고 있는 Service 객체가 동작하게 함
- 즉, Repository Layer에 있는 DAO 객체를 사용하게 됨
- kr.or.connect.guestbook.service 패키지 생성
- 실제로 구현체를 가지고 있을 kr.or.connect.guestbook.service.impl 패키지 생성

1. kr.or.connect.guestbook.service 패키지에 GuestbookService Interface 생성
    - 비지니스가 어떤것들이 있는지 잘 생각
    - 방명록 요구사항을 살펴보자
        - 방명록 정보 페이지별로 읽어오기
        - 페이징 처리를 위해 전체 건수 구하기
        - 방명록 저장하기 등등
		
```java
package kr.or.connect.guestbook.service;
import java.util.List;

import kr.or.connect.guestbook.dto.Guestbook;

public interface GuestbookService {
	public static final Integer LIMIT = 5;
	public List<Guestbook> getGuestbooks(Integer start);
	public int deleteGuestbook(Long id, String ip);
	public Guestbook addGuestbook(Guestbook guestbook, String ip);
	public int getCount();
}

```

2. kr.or.connect.guestbook.service.impl 패키지 내 위 Interface를 구현하는 GuestbookServiceImpl.java `서비스`클래스 작성
	- Bean 자동으로 등록해주는 @Autowired 사용
		- 선언만 해주면 알아서 Bean 생성해서 주입시켜줌
	- Query만 하는 Transaction은 @Transactional 붙여주면됨
	- Invoke하는 Transaction은 @Transactional(readOnly=false)

```java
package kr.or.connect.guestbook.service.impl;

import java.util.Date;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import kr.or.connect.guestbook.dao.GuestbookDao;
import kr.or.connect.guestbook.dao.LogDao;
import kr.or.connect.guestbook.dto.Guestbook;
import kr.or.connect.guestbook.dto.Log;
import kr.or.connect.guestbook.service.GuestbookService;

@Service
public class GuestbookServiceImpl implements GuestbookService{
	@Autowired
	GuestbookDao guestbookDao;
	
	@Autowired
	LogDao logDao;

	@Override
	@Transactional
	public List<Guestbook> getGuestbooks(Integer start) {
		List<Guestbook> list = guestbookDao.selectAll(start, GuestbookService.LIMIT);
		return list;
	}

	@Override
	@Transactional(readOnly=false)
	public int deleteGuestbook(Long id, String ip) {
		int deleteCount = guestbookDao.deleteById(id);
		Log log = new Log();
		log.setIp(ip);
		log.setMethod("delete");
		log.setRegdate(new Date());
		logDao.insert(log);
		return deleteCount;
	}

	@Override
	@Transactional(readOnly=false)
	public Guestbook addGuestbook(Guestbook guestbook, String ip) {
		guestbook.setRegdate(new Date());
		Long id = guestbookDao.insert(guestbook);
		guestbook.setId(id);
		
//		if(1 == 1)
//			throw new RuntimeException("test exception");
//			
		Log log = new Log();
		log.setIp(ip);
		log.setMethod("insert");
		log.setRegdate(new Date());
		logDao.insert(log);
		
		
		return guestbook;
	}

	@Override
	public int getCount() {
		return guestbookDao.selectCount();
	}
	
	
}
```

3. kr.or.connect.guestbook.service.impl 패키지 내 GuestbookServiceTest.java 파일 생성 후 테스트

```java
package kr.or.connect.guestbook.service.impl;

import java.util.Date;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import kr.or.connect.guestbook.config.ApplicationConfig;
import kr.or.connect.guestbook.dto.Guestbook;
import kr.or.connect.guestbook.service.GuestbookService;

public class GuestbookServiceTest {

	public static void main(String[] args) {
		ApplicationContext ac = new AnnotationConfigApplicationContext(ApplicationConfig.class); 
		GuestbookService guestbookService = ac.getBean(GuestbookService.class);
		
		Guestbook guestbook = new Guestbook();
		guestbook.setName("jiwon");
		guestbook.setContent("반갑습니다.");
		guestbook.setRegdate(new Date());
		Guestbook result = guestbookService.addGuestbook(guestbook, "127.0.0.1");
		System.out.println(result);
		
	}

}
```

## Transaction Test
- 바로 위의 GuestbookServiceImpl.java에서 주석친 부분을 살려서 일부러 Exception을 실행시켜보자
	 - addGuestbook 내의 모든 로직들이 다 실행되어야 `하나의 트랜잭션`
	 - 그런데, if문을 만나 Exception이 발생되면 아래 `if 아래부분은 실행안되면서 트랜잭션 RollBack` => MySQL 확인해보면 반영안된것을 확인
	 - 또한가지, @Transactional(readOnly=false)을 만약 지우면, 아래 로직은 트랜잭션으로 보지 않고 실행되어 MySQL에 반영이 됨
	 - 따라서 @Transactional(readOnly=false) 어노테이션은 반드시 필요!!
	
```java
	@Transactional(readOnly=false)
	public Guestbook addGuestbook(Guestbook guestbook, String ip) {
		guestbook.setRegdate(new Date());
		Long id = guestbookDao.insert(guestbook);
		guestbook.setId(id);
		
		if(1 == 1)
			throw new RuntimeException("test exception");
			
		Log log = new Log();
		log.setIp(ip);
		log.setMethod("insert");
		log.setRegdate(new Date());
		logDao.insert(log);
		
		return guestbook;
	}
```