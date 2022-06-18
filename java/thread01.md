# 1. Process & Thread


### process
- 프로그램을 싱행하면 OS로부터 실행에 필요한 자원(메모리)를 할당 받아 프로세스가 됨
- 데이터와 메모리 등의 자원, 스레드로 구성


### 멀티쓰레딩의 장점
- CPU 사용률을 향상시킨다.
- 자원을 보다 효율적으로 사용할 수 있다.
- 사용자에 대한 응답성이 향상된다.
- 작업이 분리되어 코드가 간결해진다.


---


# 2. Thread의 구현과 실행

스레드의 구현 방법은 Thread 클래스를 상속 받는 방법과 Runnable 인터페이스를 구현하는 방법이 있다.

1. Thread 클래스를 상속
```java
class MyThread extends Thread {
    public void run() { /* 작업 내용 */ }   // Thread 클래스의 run()을 오버라이딩
}
```

2. Runnable 인터페이스를 구현
```java
class MyThread implements Runnable {
    public void run() { /* 작업 내용 */ }   // Runnable 인터페이스의 run()을 구현
}
```

```java
public interface Runnable {
    public abstract void run();
}
```

---


- 스레드를 구현하는 2가지 방법

```java
class ThreadEx1 {
    public static void main(String[] args) {
        ThreadEx1_1 t1 = new ThreadEx1_1();
        
        Runnable r = new ThreadEx1_1();
        Thread t2 = new Thread(r);      // 생성자 Thread(Runnable target)
        
        t1.start();
        t2.start();
    }
}

class ThreadEx1_1 extends Thread {
    public void run() {
        for(int i=0; i<5; i++) {
            System.out.println(getName());      // 조상인 Thread의 getName()호출
        }
    }
}

class ThreadEx1_2 implements Runnable {
    public void run() {
        for(int i=0; i<5; i++) {
//            Thread.currentThread()    // 현재 실행 중인 Thread를 반환
            System.out.println(Thread.currentThread().getName());            
        }
    }
}
```

- Thread 클래스를 상속 받은 경우와 Runnable 인터페이스를 구현한 경우의 인스턴스 생성 방법이 다르다.
```java
ThreadEx1_1 t1 = new ThreadEx1_1();     // Thread의 자손 클래스의 인스턴스를 생성

Runnable r = new ThreadEx1_2();         // Runnable을 구현한 클래스의 인스턴스를 생성
Thread t2 = new Thread(r);              // 생성자 Thread(Runnable target)

Thread t2 = new Thread(new ThreadEx1_2());      // 위 두 줄을 한 줄로 간단히
```




---

참고 : 도서<자바의 정석>