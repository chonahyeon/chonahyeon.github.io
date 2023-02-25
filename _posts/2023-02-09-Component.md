---
title: "[Spring] 컴포넌트 스캔과 의존관계 자동 주입"
date: '2023-02-05 +0900'
description: '기술면접 정리하기 - @Component @Autowired'
categories: [Study,Spring]
tags: [Spring,기술면접]
---

> 2월 2주차 스터디 발표 자료📖                                    
> 2주차 기술면접 주제 중 다섯번 째 주제입니다!           
> 컴포넌트 스캔과 의존관계 자동 주입의 개념에 대해 정리하고자 합니다.                   

## **컴포넌트 스캔** ##
---
- 스프링이 **빈으로 등록될 준비가 된 클래스들을 스캔하여 빈으로 등록해주는 과정**
- **스프링 부트의 대표 시작 정보인 *@SpringBootApplication*에 @ComponantScan이 포함되어있다.**
- **@Component** 어노테이션이 붙어있는 클래스들은 전부 **컴포넌트 스캔의 대상이 된다.**
- **@Configuration, @Service, @Repository, @Controller** 등의 어노테이션에도 **@Component가 포함되어있다**    
    ![repository](/assets/img/repository.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" } 
- **컴포넌트 스캔을 사용하면 의존관계 자동 주입인 @Autowired를 사용한다.**          
➔ **🤔 왜?** 컴포넌트 스캔은 ***빈으로 등록만 해주는 것이지 의존관계를 설정해주지 않기때문!!***

### **컴포넌트 스캔의 이름 전략** ###
- 빈 이름은 기존 **클래스명을 사용하되 맨 앞글자만 소문자로 변경된다.**
- 빈 이름을 직접 지정할 수도 있다. ➔ **@Component("memberService2")**

### **컴포넌트 스캔의 특징** ###
- **컴포넌트 스캔은 탐색의 위치를 지정할 수 있다. (지정한 패키지부터 하위 패키지까지)**
    - 모든 자바 클래스를 다 컴포넌트 스캔하면 시간이 오래걸리므로, **꼭 필요한 위치부터 탐색하도록 시작 위치를 지정할 수 있다.**
    - 여러 시작 위치를 지정할 수 있다.
    - **Default? @ComponentScan이 붙은 설정 정보 클래스의 패키지부터 하위 패키지까지 다 탐색한다.**

### **컴포넌트 스캔 기본 대상** ###
- **@Component** 
    - **컴포넌트 스캔**에 사용
- **@Controller** 
    - 스프링 **MVC 컨트롤러**에 사용
- **@Service** 
    - 스프링 **비즈니스 로직**에 사용
- **@Repository** 
    - 스프링 **데이터 접근 계층**에 사용
    - **데이터 계층의 예외를 스프링 예외로 변환해준다.**
- **@Configuration** 
    - 스프링 **설정 정보**에 사용
    - 스프링 빈이 **싱글톤을 유지하도록 추가 처리를 한다.**

### **필터** ###
- 컴포넌트 스캔 **대상의 범위를 지정하는 기능**
- **includeFilters:** 컴포넌트 스캔 **대상을 추가로 지정**한다. (거의 사용x)
- **excludeFilters:** 컴포넌트 스캔에서 **제외할 대상을 지정**한다. (가끔 사용)

```java
@Configuration
@ComponentScan(
    includeFilters = @Filter(classes = MyIncludeComponent.class),
    excludeFilters = @Filter(classes = MyExcludeComponent.class)
)
static class ComponentFilterAppConfig{

}
```
- **Filter Type 옵션**
    1. **ANNOTATION** : 기본 값, **애노테이션을 인식**해서 동작
    2. **ASSIGNABLE_TYPE** : **지정한 타입과 자식 타입을 인식**해서 동작
    3. ASPECTJ : AspectJ 패턴
    4. REGEX : 정규표현식
    5. **CUSTOM :** **'TypeFilter'라는 인터페이스를 구현**

### **🤔 컴포넌트 스캔에서 같은 빈 이름이 등록되면 어떻게 될까?** ###
1. **자동 빈 등록 vs 자동 빈 등록**
- 이름이 같은 빈이 **등록되어있으면, 스프링은 오류를 반환한다.**
- **ConflictBeanDefinitionException** 예외가 발생한다.
2. **수동 빈 등록 vs 자동 빈 등록**
- ***기존 : 수동 빈 등록이 우선이기 때문에*** **수동 빈이 자동 빈을 오버라이딩한다.**           
➔ 현재 스프링부트는 **수동빈 등록과 자동 빈 등록이 충돌나면 오류가 발생하도록 기본 설정 값을**               
**변경하였다.**

## **의존관계 자동 주입** ##
---
- 의존 관계를 외부에서 주입해 주는 것
- ***스프링 컨테이너가 관리하는 스프링 빈이어야 의존관계 자동 주입이 이루어진다.***

### **의존관계 주입 방법** ###

1. **생성자 주입(DI 프레임워크가 권장하는 방법)**           
![생성자 주입](/assets/img/%EC%83%9D%EC%84%B1%EC%9E%90%EC%A3%BC%EC%9E%85.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
    - 이름 그대로 **생성자를 통해서 의존 관계를 주입 받는 방법이다.**
    - ***생성자 호출 시점에 딱 1번만 호출되는 것을 보장한다.***
    - **불변, 필수** 의존관계에 사용           

2. **수정자 주입**
    - **수정자 메서드(setter)**를 통해 의존관계를 주입
    - **자바빈 프로퍼티 규악의 수정자 메서드 방식**
    - **선택, 변경가능성**이 있는 의존관계에 사용한다.

3. **필드 주입**
- **필드에 바로 주입**하는 방법이다.
- **코드가 간결해지지만, 외부에서 변경이 불가능해 테스트가 어렵다**
- ***DI 프레임워크가 없으면 아무것도 할 수 없다.***
- 애플리케이션 코드와 관련없는 테스트 코드에서 사용하자

4. **일반 메서드 주입**
- **일반 메서드를 통해 주입을 받는다.**
- 한번에 **여러 필드를 주입 받을 수 있다.**
- 잘 사용하지 않는다.

### **의존관계 옵션 처리** ###
- 주입할 스프링 빈이 없어도 동작해야 할 때가 있다. **@Autowired**를 사용하면 **required 옵션**이 **true여서** 오류가 발생하는데 어떻게 해야할까?
- **자동 주입 대상을 옵션으로 처리하는 방법?**
1. **@Autowired(required=false)**             
자동 주입할 대상이 없으면 **생성 메서드 자체가 호출이 안된다.**
2. **org.springframework.lang.@Nullable**                 
자동 주입할 대상이 없으면 **호출은 되나 null이 입력된다.**
3. **Optional<>**              
자동 주입할 대상이 없으면 **Optional.empty**가 입력된다.

```java
@Autowired(required=false)
public void setNoBean1(Member noBean1){
    System.out.println("noBean1 = " + noBean1);
}

@Autowired
public void setNoBean2(@Nullable Member noBean2){
    System.out.println("noBean2 = " + noBean2);
}

@Autowired
public void setNoBean3(Optional<Member> noBean3){
    System.out.println("noBean3 = " + noBean3);
}
```

### **생성자 주입을 권장하는 이유** ###
- ***정리: 프레임 워크에 의존하지 않고, 순수한 자바 언어의 특징을 잘 살리는 방법***  

1. **불변성**
- **생성자 주입은** 객체를 생성할 때 **딱 1번만 호출**되므로 이후에 호출되는 일이 없다.(**불변**)
- 대부분의 의존관계 주입은 **한번 일어나면 애플리케이션 종료 시점까지 의존관계를 변경할 일이 없으며, 변하면 안된다.**
- **수정자 주입**의 경우는 **set메서드를 public으로 열어둬야한다.** (변경가능성이 큼)
2. **누락**
- **프레임워크 없이 순수한 자바 코드를 단위 테스트가 가능하다.**
- **수정자 주입**의 경우 **컴파일 단계**에서 오류를 판단할 수 없다.              
➔ ***연관관계가 무엇이 있는지 직접 봐야함 - 누락***
- **생성자 주입**의 경우 **컴파일 단계**에서 오류를 발견할 수 있다.         
**ex. 생성자에 필요한 매개변수의 개수,타입 등이 불일치**

    ```java

    // 테스트 대상
    public Order createOrder(Long memberId, String itemName, int itemPrice){
        Member member = memberRepository.findById(memberId); // 의존관계 누락1
        int discountPrice = discountPolicy.discount(member, itemPrice); // 의존관계 누락2

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }

    // 테스트 코드
    // 생성자 주입 x
    @Test
    void createOrder() {
        OrderServiceImpl orderService = new OrderServiceImpl();
        orderService.createOrder(1L,"itemA",10000); // 컴파일 OK, 그러나 오류 발생!
    }

    // 생성자 주입 o
    @Test
    void createOrder() {
        // 연관관계 미설정 시, 컴파일 오류발생!! - 누락되지 않음
        OrderServiceImpl orderService = new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
        orderService.createOrder(1L,"itemA",10000); 
    }

    ```
3. **final 키워드를 사용할 수 있다.**
- 생성자에서 혹시라도 **값이 설정되지 않는 오류를 컴파일 시점에 막을 수 있다.**

### **롬복 (@RequiredArgsConstructor)** ###
- **@RequiredArgsConstructor를 사용하면 생성자 주입의 코드를 간결화 할 수 있다.**
- **final**이 붙은 **필드를 모아서 생성자를 자동으로 만들어준다.**
- 현재는 **@RequiredArgsConstructor를 사용하고 @Autowired**를 생략하는 방법이 주로 사용된다.

### **조회 대상 빈이 2개 이상일 때** ###
- 빈을 조회했는데 **같은 타입의 빈이 여러개가 있으면 어떻게 할까???**

1. **@Autowired 필드 명 매칭**으로 해결
- @Autowired는 **타입으로 매칭을 시도**하고, **여러 개의 빈이 존재하면 필드 이름, 파라미터 명으로 추가 매칭을 한다.**
2. **Qulifier를 사용**
- **추가 구분자를 붙여주는 방법이다.** 빈 이름을 변경하는 것이 아님!!!
    
    ```java
    @Component
    @Qualifier("mainDiscountPolicy")
    public class RateDiscountPolicy implements DiscountPolicy {...}

    @Component
    @Qualifier("fixDiscountPolicy")
    public class FixDiscountPolicy implements DiscountPolicy {...}

    ```
3. **@Primary 사용 (자주 사용)**
- 우선순위를 지정하는 방법이며, **@Primary가 우선권을 가진다.**