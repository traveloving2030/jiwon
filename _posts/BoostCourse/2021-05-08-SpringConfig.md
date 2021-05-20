---
layout: post
title: "DI - xml파일을 이용한 설정"
date: 2021-05-08
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---


# xml파일을 이용한 설정-1

- Maven Project diexam 생성(maven-archetype-quickstart)
- pom.xml 파일에 JDK 사용을 위한 플러그인 설정 추가
- Spring Bean Factory를 이용하여 Bean객체 이용하기

```xml
<!-- pom.xml -->
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>kr.or.connect</groupId>
  <artifactId>diexam</artifactId>
  <version>0.0.1-SNAPSHOT</version>

  <name>diexam</name>
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

- maven project Update

- kr.or.connect.diexam 패키지 내 UserBean.java 파일 생성

```java
// UserBean.java

package kr.or.connect.diexam01;

//빈클래스
public class UserBean {
	
	//필드는 private한다.
	private String name;
	private int age;
	private boolean male;
	
	//기본생성자를 반드시 가지고 있어야 한다.
	public UserBean() {
	}
	
	public UserBean(String name, int age, boolean male) {
		this.name = name;
		this.age = age;
		this.male = male;
	}

	// setter, getter메소드는 프로퍼티라고 한다.
	public void setName(String name) {
		this.name = name;
	}

	public String getName() {
		return name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	public boolean isMale() {
		return male;
	}

	public void setMale(boolean male) {
		this.male = male;
	}

}
```

- src/main 폴더 내 resources 폴더 생성 후 applicationContext.xml 파일 생성
- `우리가 생성해야 할 객체를 대신 생성하게끔 하는게 목표임` - 스프링컨테이너에게 알려주는 용도임
 
```xml
<!-- applicationContext.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="userBean" class="kr.or.connect.diexam.UserBean"></bean>

</beans>
```

- 여기서 등록되고 사용된 bean 태그는 다음 객체를 생성하는것과 같은 의미임
- 스프링 컨테이너는 이러한 객체를 딱 하나만 가지고 있음 (싱글턴 패턴)
- 나중에 더 깊숙히 공부하고싶다 => 디자인 패턴 공부

```java
kr.or.connect.diexam.UserBean.UserBean userBean = new kr.or.connect.diexam.UserBean.UserBean();
```

- 이제 설정한 xml 파일을 읽어들이는 객체도 하나 생성해야함
- kr.or.connect.diexam 패키지 내 ApplicationContextExam01 클래스 파일 생성
- 여기서 이제 `Spring이 가지는 공장을 생성`할 것임
  (공장이 들어있는 path를 명시함으로서 거기에 bean정보가 있으니 이 정보를 읽어들여서 공장 세워요!)

```java
// ApplicationContextExam01.java
package kr.or.connect.diexam;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class ApplicationContextExam01 {

	public static void main(String[] args) {
		// TODO Auto-gene rated method stub
		ApplicationContext ac = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
		System.out.println("초기화 완료!!");
		
		UserBean userBean = (UserBean)ac.getBean("userBean");
		userBean.setName("jiwon");
		
		System.out.println(userBean.getName());
		
		UserBean userBean2 = (UserBean)ac.getBean("userBean");
		
		if(userBean == userBean2) {
			System.out.println("같은 인스턴스입니다");
		}
	}

}

```

- ApplicationContext는 인터페이스다. 즉, ApplicationContext를 구현하는 다양한 컨테이너가 존재할 것임
- xml파일을 클래스 path에서 읽어서 사용하는 객체는  ClassPathXmlApplicationContext (만들었던 resources 폴더내 applicationContext.xml 파일이 자동으로 classPath로 지정됨)
- ClassPathXmlApplicationContext 인스턴스가 생성될 때 안에 선언된 Bean들을 모두 메모리에 올려줄 것
- applicationContext.xml 파일에 등록된 Bean들이 여러개가 있다면 ClassPathXmlApplicationContext 인스턴스가 생성될 때 모두 읽어들여 메모리에 올려둠


- 이렇게 `객체를 대신해주고 싱글톤으로 관리해주는 기능`을 `IoC(제어의 역전)`이라고 함



## 싱글톤이란 메모리에 하나만 생성한다는 것입니다. 메모리에 하나만 생성되었을 경우, 해당 객체를 동시에 이용한다면 어떤 문제가 발생할 수 있을까요?

1. 싱글톤객체를 여러명이 동시에 사용하게 될 경우, 데이터가 원치않은방향으로 변조되어 본래의 의미를 잃어버리게 되어 문제가 발생합니다. 

2. xml파일에 bean태그를 입력할 때, scope속성을 줄 수 있습니다. 강의에서와 같이 아무런 scope속성을 명시하지안을경우, singleton타입으로 지정됩니다. 
   이때, scope속성을 prototype으로 지정하게될경우, getBean메소드를 통해, 해당객체를 요청할 때마다 새로운객체를 생성, 반환하게됩니다. 따라서, 1번의 문제를 해결할 수 있습니다. 