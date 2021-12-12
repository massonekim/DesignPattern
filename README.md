## 목차

1. [Singleton](#1.-Singleton)

---

## 1. Singleton

### 정의

* 생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나이고, 최초 생성 이후에 호출된 생성자는 최초의 생성자가 생성한 객체를 리턴하는 디자인 유형

### 장점

* 고정된 메모리 영역을 사용하기 때문에 추후 해당 객체에 접근할 때, 메모리 접근 속도 측면에서 이점
* 전역으로 사용되는 인스턴스이기 때문에 다른 클래스의 인스턴스들이 접근하여 사용할 수 있다.
* 도메인 관점에서 인스턴스가 한 개만 존재하는 것을 보증할 수 있다.

### 유의할 점
* 여러 클래스의 인스턴스에서 싱글톤 인스턴스의 데이터에 동시에 접근하는 경우, 동시성 문제가 발생할 수 있다.
* 멀티쓰레드 환경에서 동기화 처리를 하지않은 경우, 인스턴스가 여러개 생성될 수 있다.
* 다른 클래스의 인스턴스들 간에 결합도가 높아져 DIP, OCP 원칙을 위반할 수 있다.

### 구현
* Singleton Class
  ```java
  public clas Singleton {
    private static Singleton singletonObject;
    
    private Singleton() {}
    
    public static synchronized Singleton getInstance() {
      if(singletonObject == null)
        singletonObject = new Singleton();
      
      return singletonObject;
      }
    }
  ```
---
