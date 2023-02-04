---
title: "[Java] 가비지 컬렉션 정의 및 동작원리"
date: '2023-02-05 +0900'
description: '기술면접 정리하기 - 가비지 컬렉션'
categories: [Study,Java]
tags: [Java,기술면접]
pin: true
---

> 2월 1주차 스터디 발표 자료📖                                    
> 기술 면접을 준비하며 정했던 주제 중 5번 째 주제입니다!           
> 자바의 가비지 컬렉션 개념에 대해 정리하고자 합니다.

## **가비지 컬렉션의 정의** ##
---

➔ **동적으로 할당한 메모리 영역** 중 **사용하지 않는 영역을 탐지**하여 **해제**하는 기능            

**🤔동적으로 할당한 메모리 영역이 뭘까?**      
프로그램이 실행되면 **JVM은 OS로부터 메모리를 할당**받고, 그 메모리를 용도에 따라 여러 영역으로 나누어 관리한다.               
그 중 **동적으로 할당된 메모리 영역**은 ***Heap 영역***이라고 불린다.
* **Heap 영역?**       
    * **모든 Object 타입의 데이터**가 할당된다. 
    * **Heap에 저장된 데이터는** Garbage Collector가 처리하지 않는 한 소멸되지 않는다.
    * **참조형 타입**(Reference type)의 데이터가 저장된다.
    * **Heap 영역의 구조?**
    <br>                 
    ![HEAP 메모리 구조](/assets/img/HEAP.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; " } 

### **가비지 컬렉션의 동작 과정** ###
1. **새로 생성된 객체는 Eden 영역에 할당된다.**                       
![HEAP 메모리 구조](/assets/img/eden.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; " }    
2. **Eden 영역이 꽉차면, Minor GC 실행**     
![gc 작동과정 1](/assets/img/minor-gc1.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; " }          
- GC는 **Marking - Deleting - Copying** 순으로 작동한다.     
    * **Marking?**      
    **Garbage Collection Root(Stack 영역의 데이터)** 로부터, 해당 Root가 참조하는 객체들과, 또 그 객체들이 참조하는 객체를 탐색하면서 **Reachable한 상태의 객체는 마킹하고, UnReachable 상태한 객체는 마킹하지 않는다.**
    <br>        

    * **Deleting?**      
    **Unreachable한 상태의 객체(= 마킹되지 않은 객체)들의 메모리를 해제**한다.
    
- 활성화된 객체는 **Survival 영역으로 이동**하나, 두개의 영역 중 **먼저 채워진 곳이 있다면 해당 위치로 이동하게된다.**                   
➔ **즉, Survival 0,1 영역 중 한곳은 비어있어야 한다.**    
3. **1~2 과정을 반복하다, Survival 영역이 꽉차면 마찬가지로 Minor GC 실행**    
➔ *****Survival 영역에서 Survival 영역***으로 이동할 땐 Age가 증가한다.**
![gc 작동과정 2](/assets/img/minor-gc2.jpg){:style="border:0.2px solid ;border-radius: 6px; padding: 0px; " }      
![gc 작동과정 3](/assets/img/minor-gc3.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; " }      
4. **1~3 과정을 반복하다, 특정 Age값에 도달한 객체가 있다면 Promotion 작업 실행**    
➔ 특정 Age값 이상이 되면 **Old generation 영역으로 옮겨진다.(Promotion)**
![gc 작동과정 4](/assets/img/minor-gc4.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; " }      
5. **Old generation 영역이 꽉차게 되면, Major GC가 실행**
![gc 작동과정 5](/assets/img/major-gc.jpg){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; " }    

### **Stop-The-World** ###
---
* GC를 실행하기 위해, **JVM이 어플리케이션의 실행을 멈추는 것**.    
stop-the-world가 발생하면 **GC를 실행하는 스레드를 제외한 나머지 스레드는 모두 작업을 멈추게 되며, GC 작업을 완료한 후에 중단한 작업을 다시 시작한다.**    
<br>
* **GC 튜닝**이란 **stop-the-world 시간을 줄이는 것**을 말한다.

### **가비지 컬렉션 종류** ###
---
1. **Serial GC**
- GC를 처리하는 **스레드가 하나**이다. 
- **적은 메모리와 CPU코어가 한개** 일 때 사용           
![Serial GC](/assets/img/serial-gc.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height:300px;" }    
2. **Parallel GC**
- Young 영역의 GC를 처리하는 **스레드가 여러개**이다. 
- **메모리가 충분하고 코어의 개수가 많을 때** 사용
- Serial GC에 비해 상대적으로 **Stop The World**가 짧다.
![Parallel GC](/assets/img/parallel-gc.png)
3. **CMS GC(Concurrent Mark Sweep)**
- **stop-the-world 시간을 줄이기 위해 고안됨**
- **Reachable**한 객체를 한번에 찾지 않고, **나눠서 찾는다.**
- **compact 과정이 없다.**     
**🤔compact ??**   
➔ **UnReachable한** 상태의 객체를 지운 후 생긴 빈 공간들(=메모리 단편화)을 제거하고자, **Reachable한 객체들을 한 곳으로 모으는 작업**
![CMS GC](/assets/img/cms-gc.png)         
4. **G1 GC**
- CMS GC를 개선
- **Java 9++에서의 Default GC**
- Heap을 **일정한 크기의 Region으로 나눔.**
- 전체 Heap이 아닌 **Region 단위로 탐색하여, Region에만 GC 발생**
- **compact 진행**
![G1 GC](/assets/img/g1-gc.png)