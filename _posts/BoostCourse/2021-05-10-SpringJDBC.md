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