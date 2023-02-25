---
title: "[DB] 정규화 (Normalization)"
date: '2023-02-05 +0900'
description: '기술면접 정리하기 - 정규화'
categories: [Study,DB]
tags: [DB,기술면접]
---

> 2월 2주차 스터디 발표 자료📖                                    
> 2주차 기술면접 주제 중 두번 째 주제입니다!           
> **정규화 개념**에 대해 정리하고자 합니다.

## **정규화 (Normalization)** ##
---
- **관계형 데이터베이스의 설계**에서 ***중복을 최소화하도록 데이터를 구조화하는 프로세스***
- **속성들끼리의 종속 관계를 분석하여, 여러개의 관계로 데이터를 분리한다.**
- 정규화된 결과를 **정규형**이라고 한다.

### **정규화의 목적?**     
- 데이터 구조의 **안정성 및 무결성을 유지하기 위해**
- 중복을 배제하여 **이상현상(Anomaly)을 방지하기 위해**
- 데이터 확장 시, **릴레이션 재구성의 필요성을 최소화하기 위해**
- **효과적인 검색 알고리즘을 생성하기 위해**

### **이상현상(Anomaly)이란?** ###
- 테이블 내 ***데이터들의 중복성으로 인해***  **테이블 조작 시 발생되는 데이터 불일치 현상**
1. **삽입 이상 (Insertion Anomaly)?**               
**데이터를 삽입하기 위해 불필요한 데이터도 함께 삽입**해야한다.     
**⚠️ 문제 상황?**         
➔ **수업을 듣지않는 학번이 125번인 학생을 추가하고싶은데, 불필요한 과목번호와 성적이 필요하다.**  
![Insertion Anomaly](/assets/img/insert-anomaly.jpg){:style="border:0.2px solid ;border-radius: 6px; padding: 0px;" }
2. **삭제 이상 (Deletion Anomaly)?**                
**튜플을 삭제할 때, 필요한 데이터까지 같이 삭제되는 데이터 손실**이 발생한다.                  
**⚠️ 문제 상황?**                   
➔ **학번이 123번인 학생을 제거하면, P1 교수에 대한 과목 정보도 사라진다.** 
![Deletion Anomaly](/assets/img/delete-anomaly.jpg){:style="border:0.2px solid ;border-radius: 6px; padding: 0px;" }
3. **갱신 이상 (Update Anomaly)?**              
**중복 튜플 중 일부만 변경되어, 데이터가 불일치하게 된다.**               
**⚠️ 문제 상황?**            
➔ **학번이 123번인 학생의 정보가 일부만 변경되어, 동일한 학생이지만 지도 교수가 불일치하는 현상이 발생한다.** 
![update Anomaly](/assets/img/update-anomaly.jpg){:style="border:0.2px solid ;border-radius: 6px; padding: 0px;" }

## **정규화의 과정** ##
---
- 정규화는 **1NF, 2NF, 3NF, BCNF, 4NF, 5NF, 6NF까지** 있다.
- 보통 정규화는 **BCNF까지만 진행한다.**    

### **제 1정규화(1NF)** ###     
- 릴레이션의 **모든 속성이 원자값(1개의 값)으로만 구성되어있다.**
- **강좌 이름과 강의실의 다중 속성 값을 별개의 튜플로 분리**한다.
![1NF](/assets/img/1NF.jpg){:style="border:0.2px solid ;border-radius: 6px; padding: 0px; height:500px;" }   

### **제 2정규화(2NF)** ###
- 릴레이션이 제 1정규형에 속하고, **기본키가 아닌 모든 속성이 기본키에 완전 함수 종속인 정규형**
- ***완전 함수 종속?***      
**속성집합 X,Y가 있다고 가정할 때, 속성집합 Y가 X전체에 대해서만 종속된 경우(부분 종속x)**
![2NF](/assets/img/2NF.jpg){:style="border:0.2px solid ;border-radius: 6px; padding: 0px; height:500px;" }  

### **제 3정규화(3NF)** ###
- 릴레이션이 제 2정규형에 속하고, **이행적 종속을 제거한 정규형**
- ***이행적 종속?***         
**A➔B, B➔C가 성립할때 A➔C가 성립되는 것**
- **학번에 따라 등록금이 결정되지 않는다. 학부가 등록금을 결정 하는 것**          
![3NF](/assets/img/3NF.jpg){:style="border:0.2px solid ;border-radius: 6px; padding: 0px; height:500px;" } 

### **BCNF** ###
- 릴레이션이 제 3정규형에 속하고, **모든 결정자가 후보키가 되도록한다.**
- 담당교수는 과목을 결정하는 **결정자**이지만, **후보키는 아니다.**
- **후보키?**            
릴레이션의 **속성들 중에서 튜플을 유일하게 식별하기 위해 사용하는 속성들의 부분집합**                 
**(기본키로 사용할 수 있는 속성들)**   
- ***밑의 그림은 한 교수당 한 과목만 설정할 수 있다고 가정한다.***    
![BCNF](/assets/img/BCNF.jpg){:style="border:0.2px solid ;border-radius: 6px; padding: 0px; height:500px;" } 