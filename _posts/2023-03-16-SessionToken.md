---
title: "[Security] 서버 인증방식과 토큰 인증방식?"
date: '2023-03-16 +0900'
description: '인증 공부하기 - 세션,토큰'
categories: [Study,Security]
tags: [Network,Security]
---

> 3월 3주차 스터디 발표 자료📖                                               
> 오늘은 **일상 생활은 물론 프로젝트에서도 자주 사용하는 개념인 인증에 대해 알아보겠습니다!!**        
> **참고 [10분 테코톡]:** <https://youtu.be/y0xMXlOAfss>{:target="_blank"}       

## **인증(Authentication)**
---
- 인증이란 **사용자가 누구인지 확인하는 절차로, 대표적으로 회원가입, 로그인 과정이 있다.**
- 기존의 **Http Header 인증 방식은 Http의 Stateless 속성으로 인해 매 요청마다 로그인을 여러번 해야한다는 문제점이 있었다.**
    - 그래서 등장한 개념이 **서버(Session/Cookie)기반 인증방식과 토큰기반 인증방식이다.**
    - 🤔***하지만 쿠키만 사용하는 인증 방식은 못본 것 같다. 세션과 쿠키 모두 Http의 무상태성을 보완한 기술인데 왜 우린 세션 기반 인증을 사용하는걸까?***

### **Cookie**
- 쿠키란 **서버가 클라이언트의 브라우저에 저장하는 작은 데이터이며 브라우저는 쿠키를 저장한 후 동일한 서버에 재요청 시 쿠키를 함께 전송한다.**
- **쿠키를 사용해 인증을 진행한다고 가정해보자!**
    ![쿠키를 사용한 인증](/assets/img/cookie.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
    - 서버는 **로그인에 성공하면 인증 정보와 더불어 신용카드 정보를 담은 쿠키를 생성해 브라우저에 전송한다.**
    - 브라우저는 **서버에서 전송된 쿠키를 쿠키 저장소에 저장한다.**
    - 브라우저는 **인증 이후의 요청에 대해 쿠키를 함께 전송하며, 서버는 쿠키를 보고 인증 유무를 확인한다.**
    - ***그럼 해당 상황에서 발생할 수 있는 문제점은 무엇일까?***
        - **쿠키를 해커에 의해 탈취당했을 때 큰 문제가 생길 수 있다. 즉, 보안이 취약하다는 것...**
        - **로그인 ID에 대한 정보도 위험할 수 있지만 위의 예시처럼 신용카드 정보, 주민번호 같이 민감한 정보들이 노출된다면 심각한 상황이 발생할 수 있다.**

### **Session (Cookie & Session)**
- 쿠키만 사용한 인증방식의 문제를 해결하기 위해 등장한 개념이 바로 **세션을 활용한 인증이다!**
- 하지만 세션 기반도 **쿠키를 사용한다. 대신 쿠키에 사용자의 정보를 바로 담는대신 SessionId를 저장한다. 그렇기 때문에 세션 기반 인증보단 서버 기반 인증 또는 Cookie & Session 기반 인증이라 불린다.**
- **세션과 쿠키를 사용한 인증**
![세션을 사용한 인증](/assets/img/session.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
    - 서버는 **로그인에 성공하면 필요한 정보를 바로 쿠키에 담지 않고 세션 아이디를 생성한다.**
    - **세션 아이디를 생성한 후 필요한 정보를 key(세션 아이디) - value(정보) 형태로 저장한다.**
    - **서버는 세션 아이디를 담은 쿠키를 생성해 브라우저에 전송한다.**
    - 브라우저는 **세션 아이디를 담은 쿠키를 쿠키 저장소에 저장한다.**
- **세션 기반 인증의 장점?**
    - **세션에 만료기간이 있기 때문에 노출되더라도 위험성이 적다.**
    - 만약 세션이 탈취되더라도 **서버에서 탈취된 세션을 삭제하면 세션 사용이 불가능하다.**
- **세션 기반 인증의 단점?**
    - **규모가 큰 서비스라 여러 대의 서버를 운영한다고 가정해보자. 사용자의 요청이 로드 밸런서를 통해 어느 서버로 분산될 지 모르기 때문에 세션 저장소를 만들어 운영해야 할 것이다.**
![세션 기반의 인증 문제](/assets/img/session-problem.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height:400px;" } 
    - ***즉, 세션 저장소는 여러 서버의 요청이 집중되는 곳이므로 클라이언트의 요청량이 많아지면 세션 저장소에 과부하가 생겨 서버가 터질 수 있다.***

### **Token**
![토큰 인증](/assets/img/token.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
- **세션 기반 인증을 사용한다해도 여전히 문제점이 존재하며 HTTP 프로토콜의 Stateless 특성과 충돌한다.** ***따라서 Stateless를 위반하지 않는 인증방식을 만들고자했고 등장한 개념이 토큰 인증 방식이다.***
- ***토큰 인증 방식이란 서버가 인증된 클라이언트에게 토큰을 발급하고 이후 모든 요청에 대해 요청 헤더에 토큰을 함께 보내도록 하여 유효성 검사를 한다.***
- **토큰 인증방식은 Stateless 상태를 유지하므로 이로인한 이점은 다음과 같다.**
    - **서버측 메모리 과부하가 해소된다.**
    - **세션이 존재하지 않기때문에 서버를 확장하기 용이하다.**

## **JWT(Json Web Token)**    
---
- JWT란 **여러가지 정보를 가지고 있는 JSON 형태의 토큰이다.** 즉, ***인증에 필요한 정보들을 암호화시킨 토큰***

### **JWT의 구조**
> **JWT 사진 출처 :** <https://jwt.io/>{:target="_blank"}  

![JWT 구조1](/assets/img/jwt-structure2.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
- JWT는 **Header, Payload, Signature로 이루어져있다. 각 부분은 점(.)으로 구분된다.**

![JWT 구조2](/assets/img/jwt-structure.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
- ***Header*** 
    - **암호화 방식(Algorithm), 타입(Token Type)이 들어간다.**
    - **알고리즘은 서명 및 토큰의 검증과 생성에 사용되며 타입은 토큰의 타입을 지정한다.**
- ***Payload***
    - **토큰의 목적에 따라서 여러가지 정보를 Key-Value 형식으로 이곳에 추가할 수 있다.**
    - **Payload는 암호화가 되어있지 않고 단순히 Base64로 인코딩만 진행하기 때문에 누구나 jwt 토큰을 가지고 디코딩을 한다면 정보를 볼 수 있다. 따라서 민감한 정보는 포함하지 않는 것이 좋다.**
    - ***주로 클라이언트의 고유 ID 값 및 유효기간 등이 포함되는 영역이다.***
- ***Verify Signature***
    - **서명 생성과정?**
        1. **Header와 Payload를 Base64 방식으로 인코딩한 후 합친다.**
        2. **해당 문자열과 서버가 가지고 있는 Secreat Key(개인키)를 사용하여 서명을 생성한다. 이때 Header에 정의한 알고리즘을 사용한다.**
        3. **생성된 서명을 Base64로 인코딩한다.**
        4. **인코딩된 서명을 JWT의 마지막 부분에 추가한다.**
    - ***따라서 Signature는 서버가 가지고 있는 개인키로만 디코딩이 가능하다.***

### **JWT의 인증 과정**
![JWT 인증과정](/assets/img/jwt.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
1. JWT 토큰을 **클라이언트가 서버로 요청과 함께 전달한다.**
2. 서버는 **JWT의 Header와 Payload를 Base64로 디코딩한 후, 서버의 Secreat Key로 해당 Payload와 Header에 대한 서명을 다시 생성한다.**
3. **해당 서명이 JWT에 포함된 서명과 일치하는지 확인하여 JWT가 변조되었는지 검증한다.**
4. **서명이 일치한다면 인증에 성공한다.**

- ***즉, JWT는 Signature를 이용하여 안전성을 보장하지만, Screat Key가 노출되거나 변조된다면 안정성이 보장되지 않을 수 있다.***

### **JWT의 단점**
- **JWT는 토큰의 길이가 길어 인증 요청이 많아질수록 네트워크 부하가 심해진다.**
- ***Payload 자체는 암호화되지 않기 때문에 클라이언트의 중요한 정보를 담을 수 없다.***
- **토큰은 한번 발급되면 유효기간이 만료될 때까지 사용이 가능하기 때문에 탈취당하면 대처하기 어렵다.**
    - ***세션처럼 서버에 저장되는게 아니기 때문에 탈취당한 토큰을 제거할 수 없음***

### **Refresh Token**
- ***토큰은 한번 탈취되면 유효기간동안 대처할 수 있는 방법이 없기 때문에 Refresh Token이라는 개념이 등장했다.***
- Refresh Token은 **보안을 위해 Access Token의 유효기간을 짧고, 자주 재발급하도록 하는 대신 사용자에겐 편의성을 제공하기 위한 토큰**
    - 여기서 말한 사용자의 편의성은 **인증과정이다. Access Token만 있다면 유효기간이 지날 때마다 계속 로그인을 해야하는 불편함이 생길 것이다.**
- ***Access Token이 발급될 때 Refresh Token도 동시에 발급되며 Access Token에 비해 긴 유효기간을 가진다.***
- **Access Token은 권한에 관여하는 토큰이라면 Refresh Token은 재발급에 관여하는 토큰이다.**

### **Refresh Token을 사용한 재발급 과정**
> **Refresh Token 사진 출처 :** <https://nowgnas.github.io/posts/refreshtoken/>{:target="_blank"}  

![JWT 인증과정](/assets/img/refresh-token.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px;" }
1. 클라이언트가 **로그인 요청을하면 서버는 회원 DB를 통해 유효한 사용자인지 확인한다.**
2. 인증정보에 해당하는 회원이 존재하면 **Access Token과 Refresh Token을 발급한다.**
3. **발급받은 사용자의 id와 Refresh Token을 DB 테이블에 저장해 둔다.**
4. 클라이언트에게 **Access Token과 Refresh Token을 전달한다.**
5. 클라이언트가 서버에 리소스 요청시, **Access Token을 확인한 후 유효한 토큰이면 요청한 데이터를 응답한다.**
6. 만약 **요청에 포함된 Access Token이 만료되었다면, Access Token이 만료되었음을 클라이언트에게 전달한다.**
7. **클라이언트는 Access Token의 만료를 확인한 후 서버에 Access Token의 Refresh를 요청한다.**
8. 서버는 **만료된 Access Token에서 사용자의 id 값을 얻은 후 DB에 저장되어있는 Refresh Token이 유효한지 확인한다.**
9. Refresh Token이 유효하다면 **Access Token을 재발급하여 Client에 전송한다.**
10. **만약 Refresh Token의 유효기간이 만료되었다면 재로그인을 요청한다.** 