---
title: "[SpringBoot] Builder 패턴"
date: '2023-01-20 +0900'
description: 'Spring Builder 패턴 정리하기'
categories: [Study,Spring]
tags: [Java,Spring]
pin: true
---

> 1월 3주차 스터디 발표 자료📖                                    
> 개인 프로젝트를 진행하며, DTO를 사용할 때 Builder 패턴을 사용했는데요, 어떤 장점이 있어서 사용했는지       
> 어떻게 적용했는지 정리하면 좋을 것 같아 포스팅 합니다!! 

### **Builder 패턴**
---
#### **1. 정의** ####
객체의 생성 과정과 표현 방법을 분리해 동일한 생성 절차에서, 서로 다른 표현 결과를 만들 수 있게                   
하는 패턴이다.                           
➔ 객체의 **생성 과정**은 항상 **동일**하지만, 결과는 다르게 만들어준다.                   
➔ 간단히 말하면 **생성자 오버로딩**이다.

#### **2. 사용하는 시점** ####
* 너무 많은 인자가 생성자로 넘겨지는 경우 **가독성**을 위해 사용한다.            
➔ 각 파라미터의 의미를 쉽게 파악할 수 있다.

```java
// 빌더 패턴 적용 전
Member member = new Member("nh","1234","나현","nh@naver.com","춘식이","https://open.kakao.com/");

// 빌더 패턴 적용 후
public Member toEntity() {
        return Member.builder()
                .id(id)
                .password(password)
                .name(name)
                .email(email)
                .nickName(nickName)
                .openKakao(openKakao)
                .build();
}
```
* 객체의 변경을 막고싶은 경우에 사용한다.                                    
➔ **@Setter를 사용하지 않음으로써**, 객체의 일관성을 유지할 수 있다.

```java
// 자바 빈 패턴 (setter를 통해 계속 값이 변경됨)
Member member = new Member();
member.setName("nh");
member.setPassword("123");
member.setEmail("email");
```
#### **3. 장점** ####
1. ***불필요한 생성자의 제거***                              
➔ 기존의 점층적 생성자의 경우 1개의 선택적 인자를 받는 생성자, 2개의 선택적 인자를 받는 생성자 등                                              
**여러개의 생성자를 필요할때마다 만들어야하는** 단점이 있었지만 Builder 패턴은 이를 개선하였다.            

2. ***데이터의 순서에 상관없이 객체 생성이 가능하다.***
    ```java
    // 기존 생성자는 지정된 위치에 필요한 인자를 입력해야한다.
    Member member = new Member("nh","1234","나현","nh@naver.com","춘식이","https://open.kakao.com/");

    // 빌더 패턴은 인자의 위치가 바뀌어도 상관없다.
    public Member toEntity() {
            return Member.builder()
                    .id(id)
                    .name(name)
                    .password(password)
                    .email(email)
                    .nickName(nickName)
                    .openKakao(openKakao)
                    .build();
    }
    ```                    


3. ***필요한 인자만 설정할 수 있다.***                             
➔ 생성자의 경우 객체를 생성할 때 필수적이지 않은 변수가 있어도 **null값을 넣어주거나 해당 변수를 뺀 생성자를 만들어야한다.**                
➔ 빌더 패턴은 **필요한 값만 설정** 가능하며, 설정한 값이 무엇인지 명확히 알 수 있다.

    ```java

    // 기존 생성자 - 해당 Null값이 무엇을 의미하는지, Member 클래스를 찾아봐야한다.
    Member member = new Member("nh","1234","나현","nh@naver.com",null,null);

    // 빌더 패턴 - 필요한 값만 설정
    public Member toEntity() {
                return Member.builder()
                        .id(id)
                        .name(name)
                        .password(password)
                        .email(email)
                        .build();
    }
    ```

    ```java
    private String name;
    private String password;
    private Strign email;

    // 선택적으로 필요한 변수의 경우 @Builder.Default를 통해 초기값을 명시 할 수 있다.
    @Builder.Default
    private String nickname;
    @Builder.Default
    private String openKakao;
    ```


4. ***setter 메서드가 없어 **변경 불가능한 객체**를 만들 수 있다.***

5. ***build() 함수를 통해 유효성 검증이 가능하다.(null값인지)***
➔ **NullPointerException 발생**

#### **4. 프로젝트에 적용해보기** ####
➔ ***생성자에 @Builder를 사용해주는 이유는*** Class단위에 @Builder를 걸면 **불필요한 인자 값**이 넘어갈 수 있어서,      
이를 방지하기위해 생성자에 @Builder를 사용한다고 한다.                    

🤔 하지만 현재 BoardEntity는 초기에 모든 인자값을 설정해야하므로, 클래스 단위에 @Builder를 걸어줘도 괜찮을 것 같다! 리팩토링 하는 걸로 !! 

```java
@Entity
@Getter
@NoArgumentConstructor
@Table(name = "boards")
public class Board extends AuditingFields {

    @Id @Setter
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "board_id")
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_id")
    private Member member;

    @Column(nullable = false, length = 100, updatable = false)
    private String createdBy;

    @Enumerated(EnumType.STRING)
    private Category category;

    @Column(nullable = false, length = 250)
    private String title;

    @Column(nullable = false, length = 5000)
    private String content;

    @Enumerated(EnumType.STRING)
    private BoardStatus status;

    @OrderBy("id")
    @OneToMany(mappedBy = "board", cascade = CascadeType.ALL)
    private final List<Comment> comments = new ArrayList<>();

    @Builder
    public Board(String title, String content, Category category, BoardStatus boardStatus, Member member , String createdBy) {
        
        // 미리 notnull 상태를 검증하여, NPE 방지
        Assert.notNull(title,"제목은 필수값입니다.");
        Assert.notNull(content,"내용은 필수값입니다.");
        Assert.notNull(category,"카테고리는 필수값입니다.");
        Assert.notNull(boardStatus,"판매상태는 필수값입니다.");
        Assert.notNull(member,"작성자 id는 필수값입니다.");
        Assert.notNull(member,"작성자 닉네임은 필수값입니다.");

        this.title = title;
        this.content = content;
        this.status = boardStatus;
        this.category = category;
        this.member = member;
        this.createdBy = createdBy;
    }
}
```