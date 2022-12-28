---
title: "[SpringBoot] 상품 주문 서비스 - 도메인 분석 및 설계"
date: '2022-12-28 +0900'
description: 'Spring 강의 정리하기'
categories: [Study,Spring]
tags: [Java,Spring,Domain]
pin: true
---

> 12월 4주차 스터디 발표 자료                        
> **김영한님의 실전! 스프링 부트와 JPA 활용** 1편을 보면서 정리한 내용입니다.              
> Part1. 프로젝트 설계 ~ Part3.도메인 분석 설계 

### **기초 개념**
---

#### **ORM(Object Relational Mapping)** ####         
객체가 테이블이 되도록 매핑시켜주는 프레임워크로, 프로그램의 복잡도를 줄이고 객체와 쿼리를 분리할 수 있다.    
트랜잭션 처리나 기타 데이터베이스 관련 작업들을 좀 더 편리하게 처리할 수 있는 프레임워크이다.      
ORM은 객체와 DB의 데이터를 자동으로 매핑해주기 때문에 직접 SQL문을 작성하지 않는다.

#### **Hibernate** ####         
JPA를 사용하기 위해 JPA를 구현한 ORM 프레임워크 중 하나이다. SQL문을 사용하지 않고 메서드를 통해 데이터를 조작할 수 있다.
<details>
<summary>✔️Hibernate 간단 예제</summary>
<!-- summary 아래 한칸 공백 두어야함 -->
<div markdown="1">

```java

package jpabook.jpashop;

import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;

@Repository
public class MemberRepository {

    @PersistenceContext
    private EntityManager em;

    public Long save(Member member){
        em.persist(member); // 엔티티 메니저로 저장 (persist())
        return member.getId();
    }

    public Member find(Long id){
        return em.find(Member.class,id); // 메서드 호출만으로 Query문이 실행된다.
    }
}

```

```java

package jpabook.jpashop;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
@Getter @Setter
public class Member {

    @Id @GeneratedValue
    private Long id;
    private String username;
}

```
</div>        
</details>    

### **JPA와 DB 설정하기**
---
* SQL OPTION:                             

    ⚠️ **모든 로그의 출력은 테스트가 아닌 이상 System.out이 아닌 logger를 통해 남기는 것이 좋다.**

    1. `show_sql: true` ➔ hibernate가 DB에 전달하는 Query문을 System.out에 남기는 기능
    2. `org.hibernate.SQL: true` ➔ hibernate가 DB에 전달하는 Query문을 logger를 통해 남기는 기능
    3. `format_sql: true` ➔ Query의 가독성을 위해 Format을 설정하는 기능
    4. `org.hibernate.type: trace` ➔ Query문의 파라미터를 확인하는 기능
    5. `spring.jpa.hibernate.ddl-auto: create`        
    ➔ 애플리케이션 실행 시점에서 테이블을 drop하고, 다시 생성하는 기능
    
    ![domain](/assets/img/queary.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }    
    
    <details>
    <summary>✔️Query문의 파라미터 옵션 살펴보기 (외부 라이브러리 및 hibernate option)</summary>

    <!-- summary 아래 한칸 공백 두어야함 -->
    <div markdown="1">

    1. `org.hibernate.type: trace`
    ![trace](/assets/img/hibernateTypeDefault.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
    ➔ 로거에 출력되는 Query문의 파라미터가 ?로 뜨기때문에, 해당 파라미터가 무엇인지 알 수 없다.         
    이를 해결하기위해 해당 옵션을 사용하면 ***Trace 22420 로그에서 1번,2번 파라미터***를 확인할 수 있다.

    2. `spring-boot-data-source-decorator`
    ![trace](/assets/img/DataSourceDecorator.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
    
    ➔ 해당 옵션을 사용하면 p6spy 로그 밑에 ***기존 쿼리문과 파라미터가 적용된 쿼리문 2가지 형태*** 를 확인할 수 있다. 가독성은 1번 옵션보다 좋으나, 성능 문제를 야기할 수 있으므로 적절한 선택이 필요하다.

    </div>        
    </details>    

```yaml

spring:
 datasource:
 url: jdbc:h2:tcp://localhost/~/jpashop # h2 db의 server url
 username: db 접근 유저 이름
 password: 패스워드
 driver-class-name: org.h2.Driver
 
 jpa:
 hibernate:
 ddl-auto: create
 properties:
 hibernate:
 #show_sql: true 
 format_sql: true 

logging:
  level:
    org.hibernate.SQL: debug
    org.hibernate.type: trace # sql 파라미터 확인하기

```
### **도메인 모델과 테이블 설계하기**
---
![domain](/assets/img/domainStructure.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }                  

---
#### **1. 도메인 모델의 관계** #### 

|대상|관계|
|:---:|:---:|
|주문 - 상품|ManyToMany|
|<span style="color:green">주문 - 주문상품|OneToMany|
|<span style="color:green">주문상품 - 상품|ManyToOne|
|회원 - 주문|OneToMany|
|주문 - 배송|OneToOne|
|카테고리 - 상품|ManyToMany|
|상품 - 하위 구분|상속(Extends)|

* **다대다 관계**   
다대다 관계는 사용하지 않는 것이 좋다. 하지만 위의 예제는 간단한 프로젝트이므로 주문 상품 엔티티(중간테이블)를 추가하여 다대다 관계를 일대다, 다대일 관계로 풀어냈다.                             
    **`⭐ 다대다 관계를 피해야하는 이유?`**
    1. 데이터 무결성을 위반한다.
    2. 데이터의 삭제와 추가, 수정 시 어느 테이블의 데이터를 변경해야하는지 혼란이 올 수 있다.
    3. 중간 테이블을 도입한다하더라도, 추가적인 데이터를 넣을 수 없다.
    4. 중간 테이블로 인해 의도치 않은 쿼리문이 생길 수 있다.          

#### **2. 테이블 설계** #### 
![domainTable](/assets/img/tableStructure.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }                  
![MemberEntity](/assets/img/memberEntity.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }  

---

#### **2-1. Member** ####             

|컬럼명|데이터타입|조건|설명
|:---:|:---:|
|MEMBER_ID|Long|PK|고유 값|
|NAME|String|Not Null|사용자 이름|
|ADDRESS|Address|Not Null|사용자 주소|
|ORDERS|List|Not Null|주문 내역|

#### **2-2. Order** ####             

|컬럼명|데이터타입|조건|설명
|:---:|:---:|
|ORDER_ID|Long|PK|고유 값|
|MEMBER|Member|FK,Not Null|주문한 사용자 정보|
|ORDERITEMS|List|FK,Not Null|주문한 상품 목록|
|DELIVERY|Delivery|FK,Not Null|배송 정보|
|ORDERDATE|Date|Not Null|주문 날짜|
|STATUS|OrderStatus|Not Null|주문 상태(취소,진행)|

#### **2-3. Delivery** ####             

|컬럼명|데이터타입|조건|설명
|:---:|:---:|
|DELIVERY_ID|Long|PK|고유 값|
|ORDER|Order|Not Null|주문 정보|
|ADDRESS|Address|Not Null|배송지 주소|
|STATUS|DeliveryStatus|Not Null|배송 상태|

#### **2-4. OrderItem** ####             

|컬럼명|데이터타입|조건|설명
|:---:|:---:|
|ORDER_ID|Long|PK|고유 값|
|ITEM|Item|FK, Not Null|제품 정보|
|ORDER|Order|FK, Not Null|주문 정보|
|ORDERPRICE|Int|Not Null|주문 금액|
|COUNT|Int|Not Null|주문한 제품 개수|

#### **2-5. Item** ####             

|컬럼명|데이터타입|조건|설명
|:---:|:---:|
|ITEM_ID|Long|PK|고유 값|
|NAME|String|Not Null|제품 이름|
|PRICE|Int|Not Null|제품 가격|
|STORCKQUANTITY|Int|Not Null|제품 재고|
|CATEGORIES|List|Not Null|재품 카테고리|

#### **2-6. Category** ####             

|컬럼명|데이터타입|조건|설명
|:---:|:---:|
|CATEGORY_ID|Long|PK|고유 값|
|NAME|String|Not Null|카테고리 이름|
|ITEMS|List|Not Null|제품 목록|
|PARENT|Category|Not Null|카테고리|
|CHILD|List|Not Null|카테고리에 속한 제품들|

#### **2-7. Address(임베디드 타입)** ####             
값 타입 테이블은 변경이 불필요한 영역이다.        
⭐ 값 타입은 변경 불가능하게 설계해야한다. Setter를 제거하고, 생성자에서 값을 모두 초기화하여 변경이 불가능한 클래스를 만든다.

|컬럼명|데이터타입|조건|설명
|:---:|:---:|
|CITY|String|Not Null|지역|
|STREET|String|Not Null|주소|
|ZIPCODE|String|Not Null|우편 번호|

#### **3. 연관 관계 매핑** #### 

---

* **연관관계의 주인?**         
양방향 연관관계의 두 객체 중 제어의 권한 ( 데이터 조회, 저장, 수정, 삭제)를 갖는 실질적인 관계가 누구인지 JPA에게 알리는 것.   
연관관계의 주인은 두 객체 사이에서 데이터의 조회, 저장, 수정, 삭제가 가능하나 반대편은 조회만 가능하다. `mappedBy`를 사용하는 객체는 **연관관계의 주인**이 아니다.        
⭐ **보편적으로 외래키를 가진 쪽이 연관관계의 주인이며, 일대일 관계에서는 둘 중 어디든 외래키를 둘 수 있으나 데이터에 대한 접근이 많은 쪽에 외래키(연관관계의 주인)를 둔다.**

```java

@Entity
@Getter @Setter
public class Member {

    @Id
    @GeneratedValue
    @Column(name = "member_id") // pk 이름 ( column명 미지정 시, 변수명 그대로 입력된다 )
    private Long id;

    private String name;

    @Embedded
    private Address address;

    // mappedBy는 연관관계의 주인이 아니라는 소리, Order 테이블의 member 필드에 의해 매핑이 되었다.
    @OneToMany(mappedBy = "member")
    private List<Order> orders = new ArrayList<>();
}

```

```java

@Entity
@Table(name="orders") 
@Getter @Setter
public class Order {

    @Id @GeneratedValue
    @Column(name = "order_id")
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY) // 다대일 관계
    @JoinColumn(name = "member_id") // 매핑 관계 설정 ( 외래키 설정 )
    private Member member;

    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
    private List<OrderItem> orderItems = new ArrayList<>();

    @OneToOne(cascade = CascadeType.ALL, fetch = LAZY) // 일대일 매핑
    @JoinColumn(name = "delivery_id")
    private Delivery delivery;

    private LocalDateTime orderDate; // 주문 시간

    @Enumerated(EnumType.STRING)
    private OrderStatus status; // 주문의 상태 [ORDER,CANCEL]
}

```
* **연관관계 편의 메서드**             
양방향 연관관계일 때 양쪽 객체 모두 연관관계를 설정해야한다. 이 과정에서 ***한쪽만 연관관계를 설정하는 등*** 실수가 발생할 수 있으므로 연관관계 편의 메서드를 통해     
하나의 메소드에서 양쪽의 관계를 설정하게 하는 것이 안전하다. 해당 메서드의 위치는 핵심 제어사항이 있는 쪽에 생성하는 것이 좋다.


```java

@Getter @Setter
public class Order {

    @Id @GeneratedValue
    @Column(name = "order_id")
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY) 
    @JoinColumn(name = "member_id") 
    private Member member;

    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
    private List<OrderItem> orderItems = new ArrayList<>();

    @OneToOne(cascade = CascadeType.ALL, fetch = LAZY) 
    @JoinColumn(name = "delivery_id")
    private Delivery delivery;

    private LocalDateTime orderDate; 

    @Enumerated(EnumType.STRING)
    private OrderStatus status; // 주문의 상태 [ORDER,CANCEL]

    //== 연관관계 메서드(양방향) ==//

    public void setMember(Member member){
        this.member = member;
        member.getOrders().add(this);
    }

    public void addOrderItem(OrderItem orderItem){
        orderItems.add(orderItem);
        orderItem.setOrder(this);
    }

    public void setDelivery(Delivery delivery){
        this.delivery = delivery;
        delivery.setOrder(this);
    }

}

```
#### **4. Entity 설계시 주의점** #### 

---
1. ***Entity는 Setter를 가급적 사용하지않는다.***            
➔ 외부에서 Setter를 통해 값을 변경한다면, 어디서 오류가 발생했는지 찾기 힘들기에 유지 보수가 어렵다.                        
2. ***모든 연관관계는 지연로딩으로 설정한다.***       
➔ 즉시로딩(Eager)는 예측이 어렵고, 어떤 SQL이 실행될지 추적이 어렵다. 실무에서 모든 연관관계는 지연로딩(Lazy)로 설정해야한다.         
➔ ⭐ **@XToOne(OneToOne, ManyToOne)**관계는 기본 설정이 즉시 로딩이므로, 반드시 직접 지연로딩으로 설정해야한다.
* **Fetch Type**
    1. **`즉시로딩:`** Join을 사용하여 연관된 객체를 한번에 가져온다. 이러한 경우 불필요한 테이블까지 불러오게되어 큰 오류가 발생할 수 있다.
    2. **`지연로딩:`** 필요한 시점에 연관된 객체의 데이터를 불러온다. 

    ```java

    @OneToOne(cascade = CascadeType.ALL, fetch = FetchType.LAZY) 
        @JoinColumn(name = "delivery_id")
        private Delivery delivery;

    @ManyToOne(fetch = FetchType.LAZY) 
        @JoinColumn(name = "member_id") 
        private Member member;
    ```
                                  
3.***컬렉션은 필드에서 초기화 한다.***                                          
➔ 컬렉션은 필드에서 바로 초기화하는 것이 null 문제에서 안전하다.      
➔ 하이버네이트는 엔티티를 영속화할 때, 컬랙션을 감싸서 하이버네이트의 내장 컬랙션으로 변경하기 때문에 이 이후에 값의 변경이 일어나면 하이버네이트 내부 메커니즘의 오류 발생 위험이 크다.           
따라서 내장 컬랙션으로 변경된 이후엔 값 변경을 하지 않는 것이 좋기때문에 필드 레벨에서 생성하는 것이 가장 안전하다.        

    ```java

    Member member = new Member();
    System.out.println(member.getOrders().getClass());
    em.persist(member);
    System.out.println(member.getOrders().getClass());

    //출력 결과
    class java.util.ArrayList
    class org.hibernate.collection.internal.PersistentBag

    ```