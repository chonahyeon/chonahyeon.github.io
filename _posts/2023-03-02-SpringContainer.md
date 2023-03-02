---
title: "[Spring] 스프링 컨테이너"
date: '2023-03-02 +0900'
description: '기술면접 정리하기 - 스프링 컨테이너'
categories: [Study,Spring]
tags: [Spring,기술면접]
pin: true
---

> 3월 1주차 스터디 발표 자료📖                                               
> 서블릿 컨테이너에 이어 스프링의 컨테이너 개념에 대해 정리하고자 합니다!!                
> **참고 [김영한-스프링 핵심 원리(기본편)]:** <https://www.inflearn.com/course/스프링-핵심-원리-기본편>{:target="_blank"}

## **스프링 컨테이너 정의** ##
---
- **스프링에서 자바 객체(Bean)들을 관리하는 공간이며,** 빈의 **생성부터 소멸까지 관리한다.**
- **스프링 컨테이너는 객체를 싱글톤으로 관리해준다.** 
- 스프링 컨테이너는 **XML을 기반으로 만들 수 있고, 애노테이션 기반의 자바 설정 클래스로도 만들 수 있다.**
- **일반적으로 ApplicationContext**를 스프링 컨테이너라 한다.  
- 스프링 컨테이너는 **IoC 컨테이너 혹은 DI 컨테이너라고도 부른다.**

### **BeanFactory와 ApplicationContext** ###
![스프링 컨테이너 상속도](/assets/img/beanfactory.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height:300px; " }
1. ***BeanFactory***
    - 스프링 컨테이너의 최상위 인터페이스이며, **스프링 빈을 관리하고 조회하는 역할을 담당한다.**
    - **getBean() 메서드를 제공한다.**
2. ***ApplicationContext***
    - **BeanFactory 기능을 모두 상속받아 제공한다.**
    - **스프링 빈을 관리,조회하는 기능 외에도 앱 개발에 편리한 부가 기능들을 제공한다.**
    - 일반적으로 BeanFactory는 **거의 사용하지 않고**, **ApplicationContext를 사용하기 때문에 스프링 컨테이너라 불린다.**
        - **자바 설정 클래스를 기반으로 스프링 컨테이너를 만들 수 있다.**
        ```java
        // 스프링 컨테이너 생성하기 - 구성 정보로 AppConfig.class 전달
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
        ```

### **ApplicationContext가 제공하는 부가기능** ###
![스프링 컨테이너 상속도](/assets/img/%EC%8A%A4%ED%94%84%EB%A7%81%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%83%81%EC%86%8D%EB%8F%84.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; "}
1. ***MessageSource***
    - **Message를 다국화 할 수 있는 기능을 제공하는 인터페이스**
2. ***EnvironmentCopable***
    - **로컬, 개발, 운영등을 구분해서 처리할 수 있게 하는 기능 (Profile,Property)**
    - 각 서버의 **Bean 필요유무를 따져 상황에 맞게 적용**
3. ***ApplicationEventPublisher***
    - **이벤트 기반의 프로그래밍을 편리하게 지원하는 기능**
4. ***ResourceLoader***
    - **리소스를 편리하게 조회하도록 지원하는 기능**

## **스프링 컨테이너의 생성과정** ##
---
1. ***스프링 컨테이너 생성***
    - **new AnnotationConfigApplicationContext**(**AppConfig.class[설정 정보]**)              
    ➔ ***ApplicationContext의 구현체***
    - **스프링 컨테이너를 생성할 떄는 설정 정보를 지정해주어야 한다.**
2. ***스프링 빈 등록 (@Configuration)***
    - 스프링 컨테이너는 **파라미터로 넘어온 설정 정보를 사용하여 스프링 빈으로 등록한다.**
    - 설정 파일 내 **@Bean이라 적힌 메서드를 모두 호출하여 반환된 객체를 빈으로 등록한다.**
3. ***스프링 빈 의존관계 설정***
    - 스프링 컨테이너는 **설정 정보를 참고하여 의존관계를 주입한다.**
    - ***자바 코드의 경우 생성자를 호출하면서 의존관계 주입도 한번에 처리된다.***

```java
@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(
            memberRepository(),
            discountPolicy());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    @Bean
    public DiscountPolicy discountPolicy() {
        return new RateDiscountPolicy();
    }

}
```

## **스프링 컨테이너와 싱글톤** ##
---
- 위의 코드를 보면 **memberService 빈을 등록할 때 memberRepository 빈도 같이 등록된다.**
- 이런 경우엔 ***memberRepository 빈이 두개가 생성될까? 싱글톤은 유지될까?***
    - 스프링은 이런 경우를 대비하여 **바이트 코드를 조작하는 라이브러리를 사용한다.**

### **@Configuration과 바이트 코드의 조작** ###
- **@Configuration에 파라미터로 넘긴 설정파일은 스프링 빈으로 등록된다.**
- 그럼 AppConfig 빈을 출력해보자.
    ```java
    @Test
    void configurationDeep() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

        AppConfig bean = ac.getBean(AppConfig.class);
        System.out.println("bean = " + bean.getClass());
    }
    ```
    - 예상과는 달리 **bean = class hello.core.AppConfig\$\$EnhancerBySpringCGLIB\$\$bd469d70**이 출력된다.
    - 즉, 스프링이 **내가 만든 클래스가 아닌 CGLIB이라는 바이트 코드 조작 라이브러리를 사용해 AppConfig를 상속받은 임의의 다른 클래스를 만들고, 빈으로 등록한 것이다.**

### **왜 바이트 코드가 조작된 클래스를 빈으로 등록했을까?** ###
- 스프링은 **싱글톤을 보장하기 위해 CGLIB으로 임의의 로직을 추가했을 것이다.**
- **AppConfig@CGLIB 예상코드**
    ```java
    @Bean
    public MemberRepository memberRepository() {
        if (memberRepository가 이미 스프링 컨테이너에 등록되어있으면){
            return 스프링 컨테이너에서 찾아서 반환;
        }else {
            기존 로직을 호출하여 memberRepository 빈을 생성하고 스프링 컨테이너에 등록
            return 반환;
        }
    }
    ```
    - 즉, ***@Bean이 붙은 메서드마다 이미 스프링 빈이 존재하면 해당 빈을 반환하고 없다면 생성해 빈으로 등록하는 코드가 동적으로 생성될 것이다.***