---
title: "[Java] 서블릿과 서블릿 컨테이너"
date: '2023-03-02 +0900'
description: '서블릿과 서블릿 컨테이너 정리하기'
categories: [Study,Java]
tags: [Java,CS]
---

> 3월 1주차 스터디 발표 자료 📖                      
> **오늘은 Servlet과 Servlet Container에 대해 알아보고자 합니다.**              
> **참고 자료:** <https://tecoble.techcourse.co.kr/post/2021-05-23-servlet-servletcontainer/>{:target="_blank"}

## **Servlet** ##
---
- **서블릿은 클라이언트의 요청을 처리하고, 그 결과를 반환하는 자바 기반의 웹 프로그래밍 기술이다.**
- **동적 웹 페이지를 만들 때 사용한다.**
- 웹 요청과 응답의 흐름을 **간단한 메서드 호출만**으로 다룰 수 있게 한다.

### **Servlet의 생명주기 메서드** ###

|생명주기 단계|호출 메서드|특징|
|:---:|:---:|:---:|
|***초기화***|**init()**|**서블릿 요청 시 최초 1번만 호출된다.**<br>서블릿 생성 시 초기화 작업을 수행한다.|
|***작업 수행***|**doGet(), doPost()**|**서블릿 요청 시 매번 호출된다.**<br>클라이언트가 요청하는 작업을 수행한다.|
|***종료***|**destroy()**|서블릿이 요청된 기능을 수행한 뒤 **메모리에서 소멸될 때 호출된다.**<br>서블릿의 마무리 작업을 수행한다.|

### **Servlet의 생명주기 흐름** ###
![서블릿 생명주기](/assets/img/servlet-lifecycle2.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height:300px;" }
1. 클라이언트의 요청이 들어오면 **요청에 해당하는 서블릿이 메모리에 있는지 확인한다.**        
메모리에 없다면 **init()메서드를 호출하여 메모리에 적재한다.**
2. 클라이언트의 요청마다 **service()를 호출되며 요청에 따라 doGet(), doPost()등 메서드가 호출된다.**
    - 이 시점에 **HttpServletRequest, HttpServletResponse에 의해 request, response 객체가 제공된다.**
3. 컨테이너가 서블릿에 **종료 요청을 하면 destroy() 메서드가 호출된다. 종료 시 처리할 작업은**     
**destroy() 메서드를 오버라이딩하여 구현하면 된다.**
    - **실행 중 서블릿이 변경되면 destroy() 메서드가 호출되어 기존 서블릿을 파괴하고**       
    **init()을 통해 다시 메모리에 적재한다.**
4. 서블릿 **소멸 시, Garbage Collection이 실행된다.**

## **ServletContainer** ##
---
- 서블릿의 **생명주기를 관리해주는 프로그램**
- 클라이언트의 요청을 받고 응답할 수 있도록 **웹 서버와 소켓으로 통신한다.**
- **서블릿 객체를 싱글톤으로 관리한다.**
- **동시 요청을 위한 멀티 쓰레드 처리를 지원한다.**

### **ServletContainer의 작동과정** ###
![서블릿컨테이너 작동과정](/assets/img/servlet-container.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
- 사용자가 **URL**을 입력하면 **HTTP Request**가 **Servlet Container**에 전송된다.
- 요청을 전송받은 **Servlet Container는 HTTPServletRequest, HTTPServletResponse 객체를 생성한다.**
- 사용자가 요청한 **URL이 어느 서블릿에 대한 요청인지 찾는다.**
- 요청된 서블릿이 **이전에 요청된 적이 없다면 해당 서블릿 객체를 생성하고 init() 메서드를 호출하여**      
**메모리에 적재**          
- **요청에 맞는 서블릿을 생성하거나 찾았다면 Servlet Container는 해당 요청을 처리할 스레드를 생성하거나 Thread Pool에서 Thread를 가져온다.**
- Thread가 생성되었다면 **서블릿 객체의 service() 메서드를 호출**한 후 **클라이언트의 Get,Post 여부에 따라 doGet(), doPost()를 호출한다.**
    - 메소드 실행 결과를 **HttpServletResponse에 담아 다시 컨테이너에게 전달한다.**
- 컨테이너는 **HttpServletResponse 객체를 HTTP Response**로 전환하여 웹 서버로 보낸다.
- 컨테이너는 전환이 끝나면 **HttpServletRequset, HttpServletResponse 객체를 소멸시킨다.** 

### **ServletContainer의 역할** ###
1. ***웹 서버와의 통신 지원***
    - **서블릿 컨테이너는 서블릿과 웹서버가 손쉽게 통신할 수 있게 해준다.**
    - 이러한 통신 과정이 생략됨으로써, **개발자는 비즈니스 로직 개발에만 집중할 수 있다.**
2. ***서블릿 생명주기 관리***
    - 서블릿의 **생성과 소멸을 관리한다. 서블릿을 로딩하여 인스턴스화하고 초기화 메서드를 호출한다.**
    - **요청이 들어오면 적절한 메서드를 호출하며, 서블릿의 역할이 끝나면 GC를 진행한다.**
3. ***멀티쓰레드 지원 및 관리***
    - 서블릿 컨테이너는 **요청마다 자바 쓰레드를 새롭게 생성하고 요청이 완료되면 쓰레드도 자동 소멸된다.**
    - 서버가 **다중 스레드를 생성 및 운영해주니 쓰레드 안정성은 고려하지 않아도된다.**
 