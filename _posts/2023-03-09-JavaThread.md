---
title: "[Java] 자바는 스레드를 어떻게 사용할까?"
date: '2023-03-09 +0900'
description: '자바 공부하기 - 스레드'
categories: [Study,Java]
tags: [Java]
---

> 3월 2주차 스터디 발표 자료📖                                               
> 오늘은 **스레드가 자바에서 어떻게 사용되는지 알아보고자 합니다.**       
> 스레드와 멀티스레드의 개념은 **앞 포스팅에서 정리했습니다!!!**         
> 🔥**스레드의 개념:** <https://chonahyeon.github.io/posts/Thread/>{:target="_blank"}

## 자바에서 스레드를 구현하는 방법
---

### 1. extends Thread
- ***스레드를 상속 받는 방법***
- 자바는 다중 상속을 받을 수 없기때문에 다른 클래스를 **상속받을 수 없다.**

```java
@Slf4j
public class HelloThread extends Thread {

    @Override
    public void run() {
        log.info("Thread 실행");
    }

    public static void main(String args[]) {
        Thread thread = new Thread(new HelloThread());
        thread.start();
    }
}
```

### 2. implements Runnable
- **인터페이스를 구현하는 방법**
- **다른 클래스를 상속받아 사용이 가능하다. 따라서 보통 Thread 클래스를 상속받기보단 Runnable 인터페이스를 구현한다.**

```java
@Slf4j
public class HelloRunnable implements Runnable{
    @Override
    public void run() {
        log.info("Thread 실행");
    }

    public static void main(String args[]){
        (new Thread(new HelloRunnable())).start();
    }
}
```

## 자바에서 스레드를 직접 구현해보자! 
---

### 1. extends Thread
- ***멀티 스레드 동작을 확인하기 위해 Thread를 10개 생성한다.***
```java
@Slf4j
public class HelloThread extends Thread {
    int seq;

    public HelloThread(int seq) {
        this.seq = seq;
    }

    @Override
    public void run() {
        log.info("Thread 실행 : {} 번째", this.seq);
        try {
            Thread.sleep(4000); // 4초 대기
        } catch (Exception e) {
        }
        log.info("Thread 종료: {} 번째", this.seq);
    }

    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            Thread t = new HelloThread(i);
            t.start();
        }
        log.info("main 메서드 종료합니다..");
    }
}
```
- **실행 결과**
    - **0번 스레드부터 9번 스레드까지의 종료 순서가 보장되지 않는다.**      
    **즉, 순서에 상관없이 동시에 실행된다는 것이다.**
    - **또한 스레드가 종료되기 전에 main 메서드가 종료된 것을 볼 수있다.**
    ![스레드 실행 결과](/assets/img/thread-result.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height:500px;" }

### 2. Runnable
- extends Thread와 마찬가지로 ***멀티 스레드 동작을 확인하기 위해 Thread를 10개 생성한다.***

```java
@Slf4j
public class HelloRunnable implements Runnable{
    int seq;

    public HelloRunnable(int seq) {
        this.seq = seq;
    }

    @Override
    public void run() {
        log.info("Thread 실행 : {} 번째", this.seq);
        try {
            Thread.sleep(4000); // 4초 대기
        } catch (Exception e) {
        }
        log.info("Thread 종료: {} 번째", this.seq);
    }

    public static void main(String args[]){

        for (int i = 0; i < 10; i++) {
            Thread t = new Thread(new HelloRunnable(i)); // extends Thread 방식과 차이점
            t.start();
        }

        log.info("main 메서드 종료합니다..");
    }
}
```
- **실행 결과**
    - extends Thread와 같은 방식으로 동작된다.
    ![스레드 실행 결과](/assets/img/thread-result2.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height:500px;" }

### 3. join 메서드 ?
- ***멀티 스레드의 실행 순서를 보장하기 위한 메서드***
- 예를 들어 Thread A의 실행결과를 Thread B에서 사용하고싶다면, Thread B는 Thread A의 작업이 끝날 때까지 기다려야 할것이다.
- 밑의 코드는 **main 메서드가 모든 쓰레드가 종료된 후 종료되도록 한다.**
```java
@Slf4j
public class HelloThread extends Thread {
    int seq;

    public HelloThread(int seq) {
        this.seq = seq;
    }

    @Override
    public void run() {
        log.info("Thread 실행 : {} 번째", this.seq);
        try {
            Thread.sleep(4000); // 4초 대기
        } catch (Exception e) {
        }
        log.info("Thread 종료: {} 번째", this.seq);
    }

    public static void main(String[] args) {
        ArrayList<Thread> threads = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            Thread t = new HelloThread(i);
            t.start();
            threads.add(t);
        }

        // 5번 쓰레드가 종료될 때까지 기다린다.
        Thread 
        log.info("main 메서드 종료합니다..");
    }
}
```
- **실행 결과**
    - **모든 스레드가 종료될 때까지 main 메서드가 대기하다 종료되는 것을 볼 수 있다.**
    ![스레드 실행 결과](/assets/img/join-result.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height:500px;" }
    - **만약 다른 스레드와는 무관하게 5번 스레드만 대기하도록 하고싶다면 해당 스레드만 지정하면 된다.**
    ```java
    public static void main(String[] args) {
            ArrayList<Thread> threads = new ArrayList<>();
        
            for (int i = 0; i < 10; i++) {
                Thread t = new HelloThread(i);
                t.start();
                threads.add(t);
            }

            Thread thread5 = threads.get(5); 

            try{
                thread5.join(); // 해당 스레드 지정
            }catch (Exception e){
            }

            log.info("main 메서드 종료합니다..");
    }
    ```
        ![스레드 실행 결과](/assets/img/thread-result3.png){:style="border:0.2px solid ; border-radius: 6px; padding: 0px; height:500px;" }