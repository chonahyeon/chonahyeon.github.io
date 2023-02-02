---
title: "[네트워크] HTTP 메서드"
date: '2023-02-02 +0900'
description: '기술면접 준비 - HTTP 메서드'
categories: [Study,Network]
tags: [Network,기술면접]
pin: true
---

> 2월 1주차 스터디 발표 자료📖                                    
> 기술 면접을 준비하며 정했던 주제 중 세번 째 주제입니다!           
> 정말 자주 사용하는 HTTP 메서드를 정리해보고자합니다.

## **HTTP 메서드?** ##
---
* **HTTP 메서드?**
    - 사용자가 **요청을 했을 때** **기대되는 행동**이다.
    - 클라이언트와 서버 사이의 **요청,응답 데이터**를 전송하는 방식

## **HTTP 주요 메서드?** ##
---

### **GET 메서드?** ###
- 주로 리소스를 **조회**할 때 사용한다.                 
- **서버에 전달하고 싶은 데이터**는 **쿼리를 통해** 전달한다.                     
- **메세지 바디를 사용**하여 **데이터를 전달**할 수 있으나, 지원하지 않는 곳이 많아 권장하지 않는다.   
- **GET Method 처리 과정**                  
![get method](/assets/img/get.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height: 700px; " }             
### **POST 메서드?** ###
- **서버는 요청 데이터를 처리**한다.
- **서버가 리소스의 URI를 생성하고 관리**한다.                  
- **메세지 바디**를 통해 ***서버로 요청 데이터를 전달***한다.
- 전달된 데이터로 주로 **신규 리소스 등록, 프로세스 처리**에 사용.
- **POST는 거의 모든걸 할 수 있다.**
    1. ***새 리소스 생성(등록)*** : 서버가 아직 식별하지 않은 **새 리소스 생성**              
    2. ***요청 데이터 처리***:
        * 단순히 데이터를 생성하거나 변경하는 것을 넘어서 **프로세스를 처리**해야 하는 경우                            
        (ex. 주문완료 - 결제완료 - 배달시작 - 배달완료 처럼 값 변경을 넘어 프로세스의 상태가 변하는 경우) 
        * POST의 결과로 **새로운 리소스가 생성되지 않을 수 있다.** (**동사 URI**)                   
        (ex. POST /orders/{orderId}/start-delivery - 컨트롤 URI) 
    3. ***다른 메서드로 처리하기 애매한 경우***:              
        조회지만, POST를 사용해야하는 경우             
        (ex. JSON으로 조회 데이터를 넘겨야 하는데, GET 메서드를 사용하기 힘들 때)                
        ➔ **GET요청의 message body는 무시하고 적용되지 않는 서버가 많다.**
- **POST Method 처리 과정**   
![post method](/assets/img/post.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height: 700px; " } 
### **PUT 메서드?** ###
- **리소스가 있으면 대체**하며, **리소스가 없다면 생성**한다. (덮어쓰기)      
- **클라이언트가 관리하는 자원 저장소**                            
- **⭐ POST와 차이점 : 클라이언트가 리소스를 식별하고 있다.**                   
➔ 클라이언트가 **리소스 위치를 알고 URI를 지정**한다.   
➔ ***수정용도로 쓰기엔 부적합하다.*** 특정 field가 사라질 수 있다.            
- **PUT Method 처리 과정**             
![put method](/assets/img/put.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height: 700px; " }   
### **PATCH 메서드?** ###
- **POST 이후 등장한 메서드**                 
- **리소스를 부분 변경한다.**             
- 현재 지원되지 않는 몇몇 브라우저가 있다.             
![patch method](/assets/img/patch.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height: 500px; " }        
### **DELETE 메서드?** ###
- **리소스를 제거한다.**
![delete method](/assets/img/delete.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; " }

## **HTTP 메서드의 속성?** ##
---
### **안전(Safe Methods)** ###
- 호출해도 **리소스를 변경하지 않는다.**
- 리소스가 단순히 변하냐 변하지 않냐만 고려한다.(ex. GET 방식)

### **멱등(Idempotent Methods)** ###
- 한 번 호출하든 몇 번을 호출하든 결과가 똑같다.
- 멱등은 **외부 요인으로 중간에 리소스가 변경되는 것은 고려하지 않는다.**
- ***멱등 메서드?***
    * **GET?**          
    한 번 조회하든, 두 번 조회하든 같은 결과가 조회된다.                
    * **PUT?**             
    결과를 대체하기 때문에 같은 요청을 여러번 해도 결과는 같다.                         
    ➔ 기존 결과를 날리고 **덮어쓰기 때문에** 항상 동일
    * **DELETE?**             
    결과를 삭제하기 때문에 같은 요청을 여러번 해도 삭제된 결과는 같다.           
- ***⭐멱등 메서드가 아닌 것?***                 
    * **POST?** 여러 번 호출하면 ***같은 요청이 중복해서 발생할 수 있다.***          
    ➔ ex ) 결제 완료 창에서, 새로 고침을 눌렀을 때 결제가 중복해서 발생한다.
- **멱등 메서드의 활용?**
    * **자동 복구 메커니즘?**      
    서버가 TIME OUT등으로 정상 응답이 불가능 할 때, 클라이언트가 같은 요청을 다시해도 되는가?                
    ➔ **🤔 사용자가 delete를 눌렀을 때 반응이 없다면 다시 누를 것이다. 이런 경우엔 괜찮을까?**           
    ➔ 괜찮다! DELETE 메서드는 멱등 메서드기 때문에 몇 번을 호출하든 결과는 동일하다.

### **캐시가능(Cacheable Methods)** ###
- **key가 일치해야 사용할 수 있다.**
- **응답 결과 리소스를 캐시해서 사용해도 되는가?**
    - **가능한 메서드?** GET, HEAD, POST, PATCH 캐시가능            
    ➔ 실제로는 **GET,HEAD만** 캐시로 사용한다.

## **HTTP 메서드 활용하기** ##
---
### **클라이언트에서 서버로 데이터 전송하는 방법** ###
1. **쿼리 파라미터를 통한 데이터 전송**
    * **GET 메서드** 사용
    * 주로 **검색이나 정렬 필터**에 많이 사용
2. **메세지 바디를 통한 데이터 전송**
    * **POST, PUT, PATCH**
    * 회원 가입, 상품 주문, 리소스 등록, 리소스 변경

### **정적 데이터 조회** ###
- **이미지나 정적 텍스트 문서** 조회
- **조회는 GET**을 사용한다.
- 정적 데이터는 일반적으로 **쿼리 파라미터 없이 리소스 경로로 단순 조회 가능**                  
![static data 조회](/assets/img/staticdata.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height: 400px; " }

### **동적 데이터 조회** ###
- **GET 메서드**에 **쿼리 파라미터를 사용**하여 데이터를 전달한다.                               
- 조회 조건을 줄여주는 **필터(검색어)**, 조회 결과를 정렬하는 **정렬 조건(카테고리 같은)**에 주로 사용한다.   
![동적 data 조회](/assets/img/dynamic.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height: 400px; " }    

### **HTML Form 데이터 전송** ###
⭐ HTML Form 전송은 **GET,POST**만 지원한다.                
1. **POST 전송** - 저장 요청하기     
- ***message body에 form 데이터를 담아*** 함께 전송한다.         
➔ **key=value의 형식 ( 쿼리 파라미터 형식 )**              
➔ **전송 데이터를 url encoding 처리한다.**                
![Form 저장하기](/assets/img/form-post.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height: 400px; " }       
2. **GET 전송** - 데이터 조회하기     
- ***쿼리 파라미터에 form 데이터를 담아*** 함께 전송한다.  
- **HTML Form은 GET 전송도 가능하다.**             
![Get 조회하기](/assets/img/form-get.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height: 400px; " }        
3. **multipart/form-data** - 파일 전송하기
- **파일 업로드 같은 바이너리 데이터 전송** 시 사용한다.
- ***multipart인 이유?***  다른 종류의 **여러 파일과 form의 내용을 함께 전송** 할 수 있다.   
![파일 저장하기](/assets/img/multipart.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }  

### **HTML API 데이터 전송** ###
- 서버끼리 통신할 때 사용 (백엔드 시스템)             
- 웹 클라이언트                 
    - HTML에서 Form 전송 대신 **자바 스크립트를 통한 통신**에 사용(AJAX)                 
- **POST,PUT,PATCH**: ***메세지 바디***를 통해 데이터 전송  
![json으로 데이터 전송](/assets/img/jsondata.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height: 400px;" }             
- **Content-Type**: ***application/json을 주로 사용***하며, 사실상 표준이다.              
    - 그 외 Content-Type: TEXT,XML 등            