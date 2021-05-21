---
layout: post
title: "DI - Annotation을 이용한 설정"
date: 2021-05-09
excerpt: "web"
tag:
- WebProgramming
category: [ BoostCourse ]
comments: true

---

# Annotation을 활용한 DI 설정

- ApplicationConfig.java

```java
package kr.or.connect.diexam;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ApplicatoinConfig {
	@Bean
	public Car car(Engine e) {
		Car c = new Car();
		c.setEngine(e);
		return c;
	}
	
	@Bean
	public Engine engine() {
		return new Engine();
	}
}

```
- @Configuration : 이 클래스가 config 파일이구나!
- Annotation Config Application Context는 java Config Class를 읽어들여 IoC와 DI를 적용
- 이때, @Bean 어노테이션이 붙은 메소드들을 Annotation Config Application Context는 자동으로 실행하여 그 결과로 return하는 객체들을 기본적으로 싱글톤으로 관리를 하게 해줌
- ApplicationContext는 파라미터를 받아들이지 않는 Bean 생성메소드를 먼저 모두 다 실행해서 반환받은 객체를 관리함
- 그리고 파라미터의 생성된 객체들과 같은 타입의 객체가 있을 경우 파라미터로 전달해서 객체를 생성함
- 이렇게 전달받은 파라미터를 car의 setEngine 메소드의 파라미터로 설정한 후 return함
- Annotation을 사용하면 이 부분을 더 간단하게 만들 수 있음
- 단, Spring JDBC와 같은 외부라이브러리가 가진 객체를 사용할 경우 라이브러리를 열어서 어노테이션을 붙일 순 없음. 따라서 위와 같이 Bean을 등록하는 방법을 써야 함!



- ApplicationContextExam04.java

```java
package kr.or.connect.diexam;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class ApplicationContextExam04 {

	public static void main(String[] args) {
		
		ApplicationContext ac = new AnnotationConfigApplicationContext(ApplicatoinConfig.class);
		// Car car = (Car) ac.getBean("car");
		Car car = (Car) ac.getBean(Car.class);
		car.run();
	}

}

```

- AnnotationConfigApplicationContext : Bean을 만들어내는 공장
- ApplicatoinConfig.class에서 설정해준 설정을 읽어들여 공장을 세울것이고 Bean들을 만들어 가지고 있음
- 그리고 사용자가 요청했을 때 (getBean) 알맞는 결과를 줄것
- getBean의 파라미터를 car로 줘도 되지만 헷갈릴땐 Car.class로 줘도 무방함.




# Annotation을 활용하여 ApplicationConfig 설정파일 더 간단하게 만들기

- ApplicatoinConfig2.java

```java
package kr.or.connect.diexam;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("kr.or.connect.diexam")
public class ApplicationConfig2 {
	
}

```
- @ComponentScan : 사용자가 일일이 다 알려주지않아도 알아서 너가 Anotation 붙어있는 해당 객체들 읽어들여서 메모리에 올리고 DI를 주입하도록 함
- 이런 Annotation이 붙어있지 않은 객체들은 Bean 어노테이션을 이용하여 직접 생성해주는 방식으로 클래스를 관리할 수 있음


- Car.java
    - Car 클래스 정의 전 @Component 명시
    - setEngine 메소드 지우고 @Autowired 해줌(Engine type의 객체가 있으면 너가 알아서 v8에 주입해줘)

```java
package kr.or.connect.diexam;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Car {
	@Autowired
	Engine v8;
	
	public Car() {
		System.out.println("Car 생성자");
	}
	
//	public void setEngine(Engine e) {
//		this.v8 = e;
//	}
	
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

- ApplicationContextExam05.java

```java
package kr.or.connect.diexam;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class ApplicationContextExam04 {

	public static void main(String[] args) {
		
		ApplicationContext ac = new AnnotationConfigApplicationContext(ApplicationConfig2.class);
		// Car car = (Car) ac.getBean("car");
		Car car = (Car) ac.getBean(Car.class);
		car.run();
	}

}
```