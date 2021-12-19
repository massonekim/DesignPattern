## 목차

1. [Singleton](#1.-Singleton)
2. [Abstract Factory](#2.-Abstract-Factory)
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

## 2. Abstract Factory

### 사용 목적 & 용도

* 다양한 구성요소에서 특정 목적에 따라 호환성을 갖는 객체들을 생성하기 위해서 사용
* 각 객체의 생성에 있어서 사용 목적에 어떤 객체들이 부합하는지 확인하고 검증하는 비용이 사라짐으로써 편의성을 얻고, 유지보수 비용을 줄일 수 있다.

### 클래스 다이어그램

![AbstractFactory](https://user-images.githubusercontent.com/95995592/146671567-ab0c56d6-029d-46a4-80f4-537bdd45e0ec.PNG)

### 구현
* Interface
  ```java
  public interface Buildable {
    public void build();
  }
  
  public interface Gatherable {
    public void gather();
  }
  
  public interface Creatable {
    public void createWorker();
  }
  
  ```
* Worker Class & Sub Class 
  ```java
  public abstract class Worker implements Gatherable, Buildable {
  }
  
  public class WorkerForTerran extends Worker {
    @Override
    public void gather() {
      System.out.println("미네랄을 도구로 절단한다.");
    }
    
    @Override
    public void build() {
      System.out.println("건물을 짓는다.");
    }
  }
  
  public class WorkerForZerg extends Worker {
    @Override
    public void gather() {
      System.out.println("미네랄을 집게로 절단한다.");
    }
    
    @Override
    public void build() {
      System.out.println("건물로 변한다.");
    }
  }
  ```
  
* Building Class & Sub Class 
  ```java
  public abstract class Building implements Creatable {
  }
  
  public class CenterForTerran extends Building {
    @Override
    public void createWorker() {
      System.out.println("SCV 생성");
    }
  }
  
  public class CenterForZerg extends Building {
    @Override
    public void createWorker() {
      System.out.println("드론 생성");
    }
  }
  ```
  
* Initialization Class & Sub Class 
  ```java
  public abstract class InitializationFactory {
    abstract public Building createBuilding();
    abstract public List<Worker> createUnit();
  }
  
  public class InitializationFactoryForTerran  extends InitializationFactory {
    @Override
    public Building createBuilding() {
      return new CenterForTerran();
    }
    
    @Override
    public List<Worker> createUnit() {
      int healthPoint = 60;
      int attackPower = 5;
      
      List<Worker> scvList = new ArrayList<>();
      for(int i=0; i<4; i++) {
        scvList.add(new  WorkerForTerran(attackPower,healthPoint));
      }
      return scvList;
    }
  }
  
  public class InitializationFactoryForZerge  extends InitializationFactory {
    @Override
    public Building createBuilding() {
      return new ZergForTerran();
    }
    
    @Override
    public List<Worker> createUnit() {
      int healthPoint = 50;
      int attackPower = 4;
      
      List<Worker> droneList = new ArrayList<>();
      for(int i=0; i<4; i++) {
        scvList.add(new  WorkerForZerg(attackPower,healthPoint));
      }
      return droneList;
    }
  }
  
  ```  
  
* Game Class 
  ```java
  public class Game {
    public static void main(String[] args) {
      InitializationFactory initFactory = new InitialzationFactroyForTerran();
      Building builgindg = initFactory.createBuilgindg();
      List<Worker> workers = initFactory.createUnit();
      workers.get(0).gather();
      workers.get(1).build();
      builgding.createWorker();
    }
  }
  
  ```    
---
