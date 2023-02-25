---
title: "[Spring] DispatcherServlet"
date: '2023-02-24 +0900'
description: '스프링 정리 - 디스패처서블릿'
categories: [Study,Spring]
tags: [Study,Spring]
---

> 2월 4주차 스터디 발표 자료📖                  
> **오늘은 스프링의 DispatcherServlet에 대해 알아보고자 합니다.**    
> **⭐ 추천 자료:** <https://github.com/binghe819/TIL/blob/master/Spring/MVC/Spring%20MVC%20flow.md>{:target="_blank"}           

## **DispatcherServlet** ##
---
![처리 순서](/assets/img/client-process.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
- **스프링 MVC의 핵심 요소이다.**
- **DispatcherServlet은 표현 계층 전면**에서 **HTTP 프로토콜을 통해 들어오는 모든 요청을 중앙집중식으로 처리하는 프론트 컨트롤러이다.**
- **클라이언트로부터 어떤 요청이 들어오면 서블릿 컨테이너(ex.톰캣)가 요청을 받는다.**
    - 이때 **공통 작업은 DispatcherServlet에서 처리하고, 이외 작업은 적절한 세부 컨트롤러로 위임한다.**

### ***프론트 컨트롤러?*** ###
> ***Front Controller is defined as “a controller that handles all requests for a Web site” - [Baeldung]***         
**웹 사이트에 대한 모든 리소스 요청을 처리해주는 컨트롤러**

### **프론트 컨트롤러 패턴?** ###
- **웹 어플리케이션**과 관련된 패턴이다.
- **모든 리소스 요청을 처리해주는 하나의 컨트롤러(프론트 컨트롤러)를 두는 패턴이다.**
- **MVC 패턴과 함께 사용되는 패턴이다.**
- ***기존 패턴***           
    ![기존 패턴](/assets/img/%EA%B8%B0%EC%A1%B4%ED%8C%A8%ED%84%B4.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height: 420px;" }
    - 클라이언트들은 **각자 필요한 Controller A,B,C를 호출하고 있다.**
    - **각 컨트롤러에는 공통으로 처리해야하는 logic이 포함되어있다.**
- ***프론트 컨트롤러 패턴***           
    ![프론트 컨트롤러 패턴](/assets/img/front-controller.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height: 450px;" }
    - 클라이언트들은 **Front Controller에 요청을 보낸다.**
    - **Front Controller는 각 요청에 맞는 Controller를 찾아 호출한다.**
    - **공통 logic도 Front Controller에서 처리한다.**

### **DispatcherServlet의 구현** ###
- **DispatchserServlet을 상속받아 사용한다.**
- ***@WebServlet 어노테이션을 사용하려면 반드시 HttpServlet을 상속받아야한다.***
    - **@DispatcherServlet은 HttpServlet을 상속받고 있다.**

```java
@WebServlet(name = "studyServlet", urlPatterns = "/hello")
public class StudyServlet extends DispatcherServlet{
    ...
}
```

## **DispatcherServlet의 동작 흐름** ##
---
![디스패처 서블릿 동작 흐름](/assets/img/dispatcher-servlet.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
- **[1번 과정]**
    - 클라이언트가 **특정 URL로 요청을 보내면 클라이언트의 요청을 DispatcherServlet에게 전달**
- **[2번 과정]**
    - DispatcherServlet은 **요청 정보를 HandlerMapping에게 전달한다.**
    - **HandlerMapping은 요청에 맞는 Controller를 찾아 DispatcherServlet에게 반환한다.**
- **[3번 과정]**
    - DispatcherServlet은 **반환된 Controller의 정보를 통해 해당 Controller에게 요청을 위임한다.**
    - **Controller는 요청을 처리한 후 ModelAndView를 반환한다.**
- **[4번 과정]**
    - DispatcherServlet은 반환된 **ModelAndView를 통해 view name을 ViewResolver에게 전달한다.**
    - **ViewResolver는 view name과 일치하는 view 객체를 찾아 반환한다.**
    - DispatcherServlet은 **View 객체에 3번 과정에서 얻은 Model을 파라미터로 넘기고 render 메소드를 호출하여 페이지 렌더링을 수행한다.**
    - DispatcherServlet은 **렌더링 된 페이지를 response로 사용자에게 리턴한다. (1번의 Response)**

### **Special Bean Types** ###
---
- 디스패처 서블릿은 **요청을 처리하고 적절한 응답을 제공하기 위한 특수한 빈을 위임한다.**

1. **HandlerMapping**
    - **HTTP 요청 정보를 이용하여 핸들러를 찾는다.**
    - 요청을 처리할 **핸들러와 인터셉터 목록을 가진다.**          
2. **HandlerAdaptor**
    - DispatcherServlet이 요청에 매핑되는 **Handler**를 호출할 수 있도록 돕는 역할
    - **HandlerMapping이 반환한 핸들러를 처리하는 인터페이스** 
3. **HandlerExceptionResolver**               
    - **DispatcherServlet 실행 중에 전반적으로 일어나는 예외들에 대해 예외처리를 해준다.**
    - **요청 매핑 중 예외가 발생하거나 컨트롤러로부터 예외가 발생하면 HandlerExceptionResolver에**          
    **예외 처리를 위임한다.**
4. **ViewResolver**               
    - **Handler에 반환된 String 기반의 View 이름을 실제로 응답에서 렌더링할 View로 전환**
5. **LocalResolver, LocalContextResolver**
    - **Client의 위치 정보, 지역정보를 확인하는데 사용된다. ➔ 지역에 따라 적절한 언어의 메세지로 화면을  전달하기 위해**
6. **MultipartResolver**
    - **파일 업로드 요청 처리에 필요한 인터페이스**
7. **FlashMapManager**
    - **FlashMap 인스턴스를 가져오고 저장하는 인터페이스**
    - 주로 **리다이렉트 시, 요청 매개변수를 사용하지 않고 데이터를 전달할 때 사용** 
    
