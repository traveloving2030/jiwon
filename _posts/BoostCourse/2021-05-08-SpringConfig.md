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


- kr.or.connect.diexam 패키지 내 Engine.java 클래스 파일 생성

```java
package kr.or.connect.diexam;

public class Engine {
	public Engine() {
		System.out.println("Engine 생성자");
	}
	
	public void exec() {
		System.out.println("엔진이 동작합니다.");
	}
}
```

# 자동차로 실습해보기

- kr.or.connect.diexam 패키지 내 Car.java 클래스 파일 생성

```java
package kr.or.connect.diexam;

public class Car {
	Engine v8;
	
	public Car() {
		System.out.println("Car 생성자");
	}
	
	public void setEngine(Engine e) {
		this.v8 = e;
	}
	
	public void run() {
		System.out.println("엔진을 이용하여 달립니다.");
		v8.exec();
	}
	
	public static void main(String[] args) {
		Engine e = new Engine();
		Car c = new Car();
		c.setEngine(e);
		c.run();
	}
}
```

- 여기서 main 메소드 내에 있는 객체 생성부분을 우리(개발자)가 직접하는게 아니라 `IoC`, 즉 `제어의 역전으로 넘기고 싶다` => `Spring IoC 컨테이너가 만들어 줄것임!`
- 그럴려면 .xml 설정파일에 해당 Beans를 등록해야함!
- resources 폴더내 applicationContext.xml 파일에 bean 등록

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="userBean" class="kr.or.connect.diexam.UserBean"></bean>
	<bean id="e" class="kr.or.connect.diexam.Engine"></bean>
	<bean id="c" class="kr.or.connect.diexam.Car"></bean>
</beans>
```
- 그냥 이렇게 bean만 정의해주면 Engine과 Car의 instance가 싱글톤으로 생성됨
- 위 코드에서는 `Car에다가 Engine을 Set해라 라는 의미는 없음`
- set하기 위해서는 Bean에 Property 생성해야함 (아래 코드 추가)

```xml
	<bean id="userBean" class="kr.or.connect.diexam.UserBean"></bean>
	<bean id="e" class="kr.or.connect.diexam.Engine"></bean>
	<bean id="c" class="kr.or.connect.diexam.Car">
		<property name="engine" ref="e"></property>
	</bean>
```
- 이때, `<property name="engine" ref="e"></property>` 이 부분이 public void setEngine(Engine e) 와 같은 효과를 지님
- setEngine 메소드는 파라미터로 engine 타입을 받음. 해당 부분을 ref에 id가 e로 선언된 인스턴스를 파라미터로 전달해달라는 의미

- 이제 kr.or.connect.diexam 패키지 내 `설정된 xml파일을 읽어들여서 실행`하는 ApplicationContextExam02.java 파일 생성 

```java
package kr.or.connect.diexam;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class ApplicationContextExam02 {
	public static void main(String[] args) {
		ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
		Car car = (Car) ac.getBean("c");
		car.run();
	}
}
```
- 자동차를 생성하는 주체가 내가 아닌 Spring Container가 됨!
- Car 클래스만 등장 => Engine Class는 실행Class에서 등장하지 않음 (`어떤 객체에게 객체를 주입하는것 : DI`)
- 사용자는 사용할 Car 클래스만 알고있으면됨!
- 나중에 Car를 상속받고있는 Bus 클래스가 생성되도록 xml 파일만 바꿔주고 Bus가 상속받고 있는 Engine Class도 Electric Engine으로 주입받도록 바꿔주면 실행클래스의 코드는 하나도 바뀌지 않고 전기버스가 동작하는 코드가 될 수 있음!


# 버스 추가실습

- Engine.java

```java
package kr.or.connect.diexam;

public class Engine {
	public Engine() {
		System.out.println("Engine 생성자");
	}
	
	public void exec() {
		System.out.println("엔진이 동작합니다.");
	}
	
	public void execElec() {
		System.out.println("전기 엔진이 동작합니다.");
	}
}
```

- Car.java

```java
package kr.or.connect.diexam;

public class Car {
	Engine v8;
	
	public Car() {
		System.out.println("Car 생성자");
	}
	
	public void setEngine(Engine e) {
		this.v8 = e;
	}
	
	public void run() {
		System.out.println("엔진을 이용하여 달립니다.");
		v8.exec();
	}
	
	public void runElec() {
		System.out.println("전기 엔진을 이용하여 달립니다.");
		v8.execElec();
	}

}
```

- Bus.java

```java
package kr.or.connect.diexam;

public class Bus {
	Car c;
	
	public Bus() {
		System.out.println("Bus 생성자");
	}
	
	public void setCar(Car c) {
		this.c = c;
	}
	
	public void busRun() {
		System.out.println("버스가 달립니다");
		c.runElec();
	}

}

```

- ApplicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="userBean" class="kr.or.connect.diexam.UserBean"></bean>
	<bean id="e" class="kr.or.connect.diexam.Engine"></bean>
	<bean id="c" class="kr.or.connect.diexam.Car">
		<property name="engine" ref="e"></property>
	</bean>
	<bean id="b" class="kr.or.connect.diexam.Bus">
		<property name="car" ref="c"></property>
	</bean>
</beans>
```

- ApplicationContextExam03.java

```java
package kr.or.connect.diexam;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class ApplicationContextExam03 {
	public static void main(String[] args) {
		ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
		Bus bus = (Bus) ac.getBean("b");
		bus.busRun();
	}
}

```