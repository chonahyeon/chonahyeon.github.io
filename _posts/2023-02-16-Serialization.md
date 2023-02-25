---
title: "[Java] 직렬화와 역직렬화"
date: '2023-02-16 +0900'
description: '기술면접 정리 - 직렬화, 역직렬화'
categories: [Study,Java]
tags: [기술면접,Java]
---

> 2월 3주차 스터디 발표 자료📖                                    
> 3주차 기술면접 주제 중 네번 째 주제입니다!           
> **직렬화와 역직렬화에 대해 정리하고자 합니다.**          
> **참고(우아한 형제들 기술블로그):** <https://techblog.woowahan.com/2550/>{:target="_blank"}                
> **참고(우아한 형제들 기술블로그):** <https://techblog.woowahan.com/2551/>{:target="_blank"}              

## **직렬화(Serialization), 역직렬화(Deserialization)** ##
---

### **직렬화?** ###
- **메모리를 디스크에 저장하거나, 네트워크 통신에 사용하기 위한 형식으로 변환하는 것이다.**
- **자바 직렬화?**          
    - **자바 시스템 내부에서 사용하는 객체 또는 데이터를 외부의 자바 시스템에서도 사용할 수 있도록 바이트 형태로 데이터를 변환 하는 것**
    - **JVM의 메모리에 상주되어 있는 객체 데이터를 바이트 형태로 변환하는 기술**

### **자바 직렬화는 왜 사용할까?** ###
- 문자열 형태의 직렬화 방법인 **CSV, XML, JSON**이 존재한다. ***그런데 왜 자바의 직렬화를 사용할까?***
    - 문자열 형태의 직렬화는 **시스템의 고유 특성과 상관없는 대부분의 시스템에서의 데이터 교환 시 많이 사용한다.**
    - **자바 직렬화 형태의 데이터 교환은 자바 시스템 간의 데이터 교환을 위해서 존재한다!!**
    - **문자열 형태의 직렬화든 자바 직렬화든** 모두 사용이 가능하므로 **목적에 따라 적절히 사용하면된다.**

### **자바 직렬화의 장점** ###
- **자바 시스템의 개발에 최적화**되어있다. 따라서 **아무리 복잡한 구조의 클래스의 객체라도 큰 작업없이 바로 직렬화가 가능하다.**
- **객체의 데이터 타입도 자동으로 맞춰지기때문에** 역직렬화가 되면 기존 객체처럼 바로 사용이 가능하다.

### **자바 직렬화는 언제, 어디서 사용할까?** ###
- **JVM의 메모리**에만 상주되어있는 **객체 데이터를 그대로 영속화할 때 사용.**
    - 영속화된 데이터이기 때문에 **시스템이 종료되더라도 없어지지 않으며, 네트워크로 전송도 가능하다.**

1. **서블릿 세션 (Servlet Session)**
    - 세션을 **파일로 저장하거나 세션 클러스터링, DB로 저장하는 옵션 등을 선택할 때 세션 자체가 직렬화되어 저장된다.**
2. **캐시 (Cache)**
    - **Ehcache, Redis, Memcached 라이브러리 시스템에서 많이 사용된다.**
3. **자바 RMI (Remote Method Invocation)**
    - **원격 시스템 간의 메세지 교환을 위해서 사용하는 자바 지원 기술**

### **역직렬화 (Deserialization)** ##
- **byte로 변환된 데이터를 원래의 객체나 데이터로 변환하는 기술**
- **직렬화된 바이트 형태의 데이터를 객체로 변환해서 JVM으로 상주시키는 기술**

### **자바 역직렬화의 문제점** ###
- 역직렬화 시, 클래스 구조가 변경 된다면?
    - 멤버 변수 하나만 추가되어도 ***java.io.InvalidClassException 예외가 발생한다.***

    ```java
    // 기존 클래스
    public class Member implements Serializable {
            private String name;
            private String email;
            private int age;
    }

    //구조가 변경된 클래스
    public class Member implements Serializable {
            private String name;
            private String email;
            private int age;
            // phone 속성을 추가
            private String phone;
    }

    // 역직렬화 시, 에러 발생
    java.io.InvalidClassException: woowahan.blog.exam1.Member; 
    ```
- 역직렬화 **대상의 클래스 멤버 변수 타입의 변경을 지양해야한다. 타입에 엄격함**
    - ***만약 String에서 StringBuilder 클래스로 타입을 변경한다면 ?*** **java.lang.ClassCastException 예외 발생**

