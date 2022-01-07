## 목차

1. [Singleton](#1.-Singleton)
2. [Abstract Factory](#2.-Abstract-Factory)
3. [Factory Method](#3.-Factory-Method)
4. [Builder](#4.-Builder)
5. [Prototype](#5.-Prototype)
6. [Adapter](#6.-Adapter)
7. [Bridge](#7.-Bridge)
8. [Composite](#8.-Composite)
9. [Decorator](#9.-Decorator)
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

## 3. Factory Method

### 사용 목적 & 용도

* 객체를 만들기 위한 인터페이스를 정의하고, 어떤 클래스의 인스턴스를 생성할지에 대한 결정은 하위클래스에서 정하는 방법

![factory-method-pattern](https://user-images.githubusercontent.com/95995592/146673775-b157c1f6-f045-45c5-8b9b-208d35bb46c4.png)

* Product : 팩토리 메서드로 생성될 객체의 공통 인터페이스, Creator와 하위 클래스가 생성할 수 있는 모든 객체에 동일한 인터페이스를 선언
* Concreate Product : 구체적으로 객체가 생성되는 클래스, Product가 선언한 인터페이스로 만든 실제 객체
* Creator : Product타입을 반환하는 팩토리 메서드를 갖는 클래스
* Concreate Creator : 팩토리메서드를 재정의하여 Concreate Product 를 리턴


### 클래스 다이어그램

![FactoryMethod](https://user-images.githubusercontent.com/95995592/146674640-da2563b7-41d6-43cb-9b65-1a5c75c51dbd.PNG)


### 구현
* Product
  ```java
  public abstract class Product {
    public abstract void attack();
    public abstract void move();
  }
  
  ```
* Factory Class 
  ```java
  public abstract class Factory {
    public final Product create(String category) {
      Product product = createProduct(category);
      return product;
    }
    
    protected abstract Product createProduct(String category);
  }
  ```
  
* Vulture Class 
  ```java
  public class Vulture extends Product {
    private String category;
    
    public Vulture(String category) {
      this.category = category;
    }
    
    @Override
    public void attack() {
      System.out.println("대인 파편 유탄을 발사합니다.");
    }
    
    @Override
    public void move() {
      System.out.println("중력 제한 부양 기술을 이용해 이동합니다.");
    }
  }
  ```
  
* Tank Class 
  ```java
  public class Tank extends Product {
    private String category;
    
    public Tank(String category) {
      this.category = category;
    }
    
    @Override
    public void attack() {
      System.out.println("대포를 발사합니다.");
    }
    
    @Override
    public void move() {
      System.out.println("무한궤도를 이용해 이동합니다.");
    }
  }
  ```
  
* Goliath Class 
  ```java
  public class Goliath extends Product {
    private String category;
    
    public Goliath(String category) {
      this.category = category;
    }
    
    @Override
    public void attack() {
      System.out.println("기관포를 발사합니다.");
    }
    
    @Override
    public void move() {
      System.out.println("두 발을 이용해 이동합니다.");
    }
  }
  ```  
  
* Munitions Factory Class 
  ```java
  public class MunitionsFactory extends Factory {
    @Override
    protected Product createProduct(String category) {
      switch(category) {
      case CATEGORY.VULTURE:
        return new Vulture(category);
      case CATEGORY.TANK:
        return new Tank(category);
      case CATEGORY.GOLIATH:
        return new Goliath(category);
      }
      return null;
    }
  }
  ```  
  
* Main Class 
  ```java
  public class Main {
    public static void main(String[] args) {
      Factory factory = new MunitionsFactory();
      
      Product vulture = factory.create("Vulture");
      vulture.move();
      vulture.attack();
      
      Product tank = factory.create("Tank");
      tank.move();
      tank.attack();
      
      Product goliath = factory.create("Goliath");
      goliath.move();
      goliath.attack();
    }
  }
  ```    
  
### Abstrac Factory & Factory Method 공통점
1. Template Method Pattern 사용
2. Factory Class를 사용하여 생성
3. Factory Method Pattern 사용
4. 객체 생성을 캡슐화
5. 구상 클래스가 아닌, 추상 클래스와 인터페이스에 맞춰 코딩

### Abstrac Factory & Factory Method 차이점
| Category                                            | Abstract Factory                                                                                                                                                                                  | Factory Method                                                                                                                              |
|-----------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Factory 클래스에서 객체에 대한 생성을 지원하는 범위 | - 한 팩토리에서 서로 연관된 여러 종류 모두 지원( create() 메서드가 팩토리 클래스에 여러 개)<br> - 구상 클래스에 의존하지 않고 여러 개의 관련된 객체를 하나의 팩토리로 묶음                        | - 한 팩토리당 한 종류 ( create 메서드가 Factory 클래스에 1개)<br> - 한 개의 메서드로 여러 개의 객체를 만듦                                  |
| 팩토리 메서드에서 만드는 객체의 종류                | - 인자에 따라 관련된 객체들을 생성하는 팩토리의 종류가 결정됨                                                                                                                                     | - 팩토리 메소드 패턴의 팩토리 메소드                                                                                                        |
| 결합도를 낮추는 대상                                | - ConcreteFactory와 Client간의 결합도를 낮출 때 사용                                                                                                                                              | - ConcreteProduct와  Client 간의 결합도를 낮출때 사용                                                                                       |
| 포커스                                              | - 클래스(Abstract Factory) 레벨에서 포커스를 맞춤<br> - 각 Product들이 다른 클래스와 함께 사용될 때의 제약사항을 강제할 수 있다. | - 메서드(Factory Method) 레벨에서 포커스를 맞춤<br> - 클라이언트의 ConcreteProduct 인스턴스 생성 및 구성에 대한 책임을 덜어줌               |
| 메서드와 오브젝트                                   | - Object                                                                                                                                                                                          | - 단일 Method                                                                                                                               |
| Inhritance(상속), Composition(구성)                 | - 지역 레퍼런스를 두어 , 외부로부터 Factory 객체를 DI 받아서 위임.                                                                                                                                | - 상속을 사용하여 객체의 인스턴스 생성에 대해서는 서브클래스에 의존<br> - 지역 레퍼런스 없이 바로 하위 클래스의 함수를 호출하여 객체를 만듦 |
---

## 4. Builder

### 사용 목적 & 용도

* 객체의 생성 단계들을 캡슐화하여 객체의 생성과정과 표현 방법을 분리함으로써, 객체의 생성을 유연하게 해주는 패턴

### 클래스 다이어그램

![Builder](https://user-images.githubusercontent.com/95995592/147406313-141cb09c-fc3b-41af-acf0-b7290b50c9c0.PNG)

### 구현
* Builder : 인스턴스 생성을 위한 인터페이스(API) 선언
  ```java
  public abstract class Builder {
    protected House house;
    
    public void createHouse() {
      house = new House();
    }
    
    public abstract void buildWalls();
    public abstract void buildDoors();
    public abstract void buildRoof();
    public abstract void buildWindows();
    public abstract House getHouse();
  }
 
  
  ```
* Product : 만들어질 객체의 속성과 기능
  ```java
  public class House {
    private String roof;
    private String doors;
    private String windows;
    private String walls;
    
    public void setRoof(String roof) {
      this.roof = roof;
    }
    
    public void setDoors(String doors) {
      this.doors = doors;
    }
    
    public void setWindows(String windows) {
      this.windows = windows;
    }
    
    public void setWalls(String walls) {
      this.walls = walls;
    }
    
    @Override
    public String toString() {
      return "이집은 [" + roof + " 지붕과, " + walls + " 벽과, " + windows + " 창문과, " + doors  + " 문으로 지어진 집입니다.]";
    }
  }
  
  ```
  
* ConcreteBuilder : Builder 인터페이스를 구현하는 역할
  ```java
  public class ConcreteHouseBuilder extends Builder {
    @Override
    public void buildWalls() {
      house.setWalls("콘크리트");
    }
    
    @Override
    public void buildDoors() {
      house.setDoors("철제");
    }
    
    @Override
    public void buildRoof() {
      house.setRoof("빨간");
    }
    
    @Override
    public void buildWindows() {
      house.setWindows("일반");
    }
    
    @Override
    public House getHouse() {
      return house;
    }
   
  }
  ```
  
* Director : Builder 인터페이스를 사용해 객체를 생성하고 반환
  ```java
  public class Director {
    private Builder builder;
    
    public Director(Builder builder) {
      this.builder = builder;
    }
    
    public void build() {
      builder.createHouse();
      builder.buildWalls();
      builder.buildWindows();
      builder.buildDoors();
      builder.buildRoof();
    }
   
    public House getHouse() {
      return builder.getHouse();
    }
  }
  ```  
  
* Main
  ```java
  public class Main {
    public static void main(String args[]) {
      Builder concreteHouseBuilder  = new ConcreteHouseBuilder();
      Director director = new Director(concreteHouseBuilder);
      director.build();
      
      House house = director.getHouse();
      System.out.println(house);
    }
  }
  ```  
---

## 5. Prototype

### 사용 목적 & 용도

* 생성할 객체들의 타입이 프로토타입인 인스턴스로부터 결정되도록 하며, 인스턴스는 새 객체를 만들기 위해 자신을 복제

### 클래스 다이어그램

![Prototype](https://user-images.githubusercontent.com/95995592/147407792-13f7a1c7-a9ef-4b37-9385-40a3077aa27d.PNG)

### 구현
* Prototype : 인스턴스를 복사하여 새로운 인스턴스를 만들기 위한 메소드를 결정
  ```java
  public abstract class Shape implements Cloneable {
    protected Type type;
    
    abstract void draw();
    
    @Override
    public Object clone() throws CloneNotSupplortedException {
      Object clone = null;
      
      try {
        clone = supper.clone();
      } catch (RuntimeException e) {
        e.printStackTrace();
      } catch (CloneNotSupplortedException e) {
        e.printStackTrace();
      }
      return clone;
    }
  }
 
  
  ```
* ConcretePrototype : 인스턴스를 복사해서 새로운 인스턴스를 만드는 메소드 구현
  ```java
  public class Circle extends Shape {
    public Circle() {
      this.type = Type.CIRCLE;
    }
    
    @Override()
    void draw() {
      System.out.println("[Circle] 입니다.");
    }
    
  }
  
  public class Rectangle extends Shape {
    public Rectangle() {
      this.type = Type.RECTANGLE;
    }
    
    @Override()
    void draw() {
      System.out.println("[Rectangle] 입니다.");
    }
    
  }
  
  public class Triangle extends Shape {
    public Triangle() {
      this.type = Type.TRIANGLE;
    }
    
    @Override()
    void draw() {
      System.out.println("[Triangle] 입니다.");
    }
    
  }
  
  ```
  
* Client : 인스턴스 복사 메소드를 사용해서 새로운 인스턴스를 만듬
  ```java
  public class ShapeStore {
    private static Map<Type, Shape> shapeMap = new HashMap<Type, Shape>();
    
    public void registerShape() {
      Rectangle rec = new Rectangle();
      Circle cir = new Circle();
      Triangle tri = new Triangle();
      
      shapeMap.put(rec.type,rec);
      shapeMap.put(cir.type,cir);
      shapeMap.put(tri.type,tri);
    }
    
    public Shape getShape(Type type) {
      return (Shape)shapeMap.get(type).clone();
    }
  }
  ```
  
* Main
  ```java
  public class Main {
    public static void main(String args[]) {
      ShapeStore shapeStore = new ShapeStore();
      shapeStore.registerShape();
      
      Circle cir1 = (Circle)shapeStore.getShape(Type.CIRCLE);
      cir1.draw();
      Circle cir2 = (Circle)shapeStore.getShape(Type.CIRCLE);
      cir2.draw();
      
      Rectangle rec1 = (Rectangle)shapeStore.getShape(Type.RECTANGLE);
      rec1.draw();
      
      Triangle tri1 = (Triangle)shapeStore.getShape(Type.TRIANGLE);
      tri1.draw();
    }
  }
  ```  
---
## 6. Adapter

### 사용 목적 & 용도

* 클라이언트가 원하는 인터페이스를 제공하기 위해서 클래스의 형태를 변형하는 것으로, 서로 다른 인터페이스를 연결하는 것

### 클래스 다이어그램

![Adapter](https://user-images.githubusercontent.com/95995592/147857296-2f29360e-0acf-426d-9595-4b6db6a2dfe3.PNG)

### 구현
* Adaptee
  ```java
  public interface MediaPlayer {
      void play(String filename);
  }  
  
  public class MP3 implements MediaPlayer {
      @Override
      public void play(String filename) {
          System.out.println("Playing MP3  File "  + filename);
      }
  }
  ```
  
* Target 
  ```java
  public interface MediaPackage {
      void playFile(String filename);
  } 
  
  ```
  
* Client
  ```java
  public class MP4 implements MediaPackage {
      @Override
      public void playFile(String filename) {
          System.out.println("Playing MP4  File "  + filename);
      }
  }
  
  public class MKV implements MediaPackage {
      @Override
      public void playFile(String filename) {
          System.out.println("Playing MKV  File "  + filename);
      }
  }
  ```
  
* Adapter
  ```java
  public class FormatAdapter implements MediaPlayer {
      private MediaPackage media;
      public FormatAdapter(MediaPackage m) {
          media = m;
      }
      
      @Override
      public void play(Strig filename) {
          System.out.print("Using Adapter --> ");
          media.playFile(filename);
      }
  }
  ```
  
* Main
  ```java
  public class Main {
    public static void main(String args[]) {
      MediaPlayer player = new MP3();
      player.play("file.mp3");
      
      player = new FormatAdapter(new MP4());
      player.play("file.mp4");
      
      player = new FormatAdapter(new MKV());
      player.play("file.mkv");
    }
  }
  ```  


---
## 7. Bridge

### 사용 목적 & 용도

* 추상화를 구현으로부터 분리하여 각각 독립적으로 변화할 수 있도록 하는 패턴

### 클래스 다이어그램

![Bridge](https://user-images.githubusercontent.com/95995592/147858046-c9430064-d785-4b3f-812b-39192ed06f18.PNG)

### 구현
* Abstraction : 구현 부분에 해당하는 클래스를 인스턴스를 가지고 해당 인스턴스를 통해 구현부분의 메서드를 호출
  ```java
  public abstract class Shape {
      private  Drawing drawing
      
      protected  Shape(Drawing drawing) {
          this.drawing = drawing;
      }
      
      public abstract void draw();
      public void drawLine(int x, int y) {
          drawing.drawLine(x,y);
      }
      
      public void fill() {
          drawing.fill();
      }
  }  
  
  ```
  
* RefinedAbstraction : 기능 계층에서 새로운 부분을 확장한 클래스
  ```java
  public class Rectangle extends  Shape {
      protected Rectangle(Drawing drawing) {
          super(drawing);
      }
      
      @Override
      public void draw() {
          System.out.println("Rect draw extend");
      }
  } 
  
  public class Circle extends  Shape {
      protected Circle(Drawing drawing) {
          super(drawing);
      }
      
      @Override
      public void draw() {
          System.out.println("Circle draw extend");
      }
  } 
  
  ```
  
* Implementor : 구현 클래스를 위한 인터페이스를 정의한다.
  ```java
  public interface Drawing {
      public void drawLine(int x, int y);
      public void fill();
  }
  
  ```
  
* ConcreteImplementor : Implementor 인터페이스를 구현 즉, 실제 기능을 구현한다.
  ```java
  public class RectDrawing implements Drawing {
      @Override
      public void drawLine(int x, int y) {
          System.out.println("Rect Draw line from " +  x + " to " + y);
      }
      
      @Override
      public void fill() {
          System.out.println("Rect Fill");
      }
  }
  
  public class CircleDrawing implements Drawing {
      @Override
      public void drawLine(int x, int y) {
          System.out.println("Circle Draw line from " +  x + " to " + y);
      }
      
      @Override
      public void fill() {
          System.out.println("Circle Fill");
      }
  }
  ```
  
* Main
  ```java
  public class Main {
    public static void main(String args[]) {
      Shape rectangle = new Rectangle(new RectDrawing());
      Shape circle = new Circle(new CircleDrawing());
      
      rectangle.drawLine(1,2);
      rectangle.fill();
      rectangle.draw();
      
      circle.drawLine(3,4);
      circle.fill();
      circle.draw();
    }
  }
  ```  
  
---
## 8. Composite

### 사용 목적 & 용도

* 클라이언트가 복합 객체(Group of Object)나 단일 객체(Object)를 동일하게 취급하기 위한 패턴
* 트리 구조로 작성하여, 전체-부분(Whole-Part) 관계를 표현

### 클래스 다이어그램

![Composite](https://user-images.githubusercontent.com/95995592/148478784-75c6727d-c7e0-431b-aa8a-c17f1a7b1fed.PNG)

### 구현
* Component : 모든 Component들을 위한 추상화된 개념으로써, Leaf와, Composite Class의 공통 Interface
  ```java
  public interface Graphic {
      public void print();
  }  
  
  ```
  
* Leaf : Component Interface를 구현한 구체 클래스, Composite 객체의 부분으로 설정
  ```java
  public class Rectangle implements Graphic {
      
      public void print() {
          System.out.println("Rectangle");
      }
  } 
  
  public class Circle implements Graphic {
  
      public void print() {
          System.out.println("Circle");
      }
  } 
  
  ```
  
* Composite : 복수 개의 Component를 갖는 전체 클래스
  ```java
  public class CompositeGraphic {
      private List<Graphic> childGraphics = new ArrayList<Graphic>();
      
      public void print() {
          for(Graphic graphic : childGraphics) {
              graphic.print();
          }
      }
      
      public void add(Graphic graphic) {
          childGraphics.add(graphic);
      }
      
      public void remove(Graphic graphic) {
          childGraphics.remove(graphic);
      }
  }
  
  ```
  
* Main
  ```java
  public class Main {
    public static void main(String args[]) {
      Rectangle retangle1 = new Rectangle();
      Rectangle retangle2 = new Rectangle();
      Circle circle1 = new Circle();
      Circle circle2 = new Circle();
      
      CompositeGraphic graphic = new CompositeGraphic();
      CompositeGraphic graphic1 = new CompositeGraphic();
      CompositeGraphic graphic2 = new CompositeGraphic();
      
      graphic1.add(rectangle1);
      graphic1.add(rectangle2);
      
      graphic2.add(circle1);
      graphic2.add(circle2);
      
      graphic.add(graphic1);
      graphic.add(graphic2);
      
      graphic.print();
    }
  }
  ```    

---
## 9. Decorator

### 사용 목적 & 용도

* 객체의 결합을 통해 기능을 동적으로 유연하게 확장할 수 있게 해주는 패턴
* 기본 기능에 추가할 수 있는 기능의 종류가 많은 경우, 각 추가 기능을 Decorator Class로 정의한 후 필요한 Decorator 객체를 조합

### 클래스 다이어그램

![Decorator](https://user-images.githubusercontent.com/95995592/148482280-91100e63-fd56-4853-99e6-128845414d99.PNG)

### 구현
* Component : 기본 기능(ConcreteComponent), 추가 기능(Decorator)의 공통 기능을 정의, 클라이언트는 Component를 통해 실제 객체를 사용
  ```java
  public abstract class Display {
      public abstract void draw();
  }  
  
  ```
  
* ConcreteComponent : 기본 기능을 구현하는 클래스
  ```java
  public class RoadDisplay extends Display {
      
      @Override
      public void draw() {
          System.out.println("기본 도로 표시");
      }
  } 
  
  ```
  
* Decorator : 다수의 구체적인 Decorator의 공통 기능 제공
  ```java
  public abstract class DisplayDecorator extends Display {
      private Display decoratedDisplay;
      
      // 합성 관계를 통해 RoadDisplay 객체에 대한 참조
      public DisplayDecorator(Display decoratedDisplay) {
          this.decoratedDisplay = decoratedDisplay;
      }
      
      @Override
      public void draw() {
          decoratedDisplay.draw();
      }
  }
  
  ```
  
* ConcreteDecorator
  - Decorator의 하위 클래스로 기본 기능에 추가되는 개별적인 기능
  - Concretecomponent 객체에 대한 참조는 Decorator 에서 Component로의 합성관계를 통해 표현
  ```java
  public class LaneDecorator extends DisplayDecorator {
      public LaneDecorator(Display decoratedDisplay) {
          super(decoratedDisplay);
      }
      
      @Override
      public void draw() {
          super.draw();
          drawLane();
      }
      
      private void drawLane() {
          System.out.println("\t차선 표시");
      }
  }
  
  public class TrafficDecorator extends DisplayDecorator {
      public TrafficDecorator(Display decoratedDisplay) {
          super(decoratedDisplay);
      }
      
      @Override
      public void draw() {
          super.draw();
          drawTraffic();
      }
      
      private void drawTraffic() {
          System.out.println("\t교통량 표시");
      }
  }
  
  ```
  
* Client
  ```java
  public class Client {
    public static void main(String args[]) {
      Display road = new RoadDisplay();
      road.draw();
      
      Display roadWithLane = new LaneDecorator(new RoadDisplay());
      roadWithlane.draw();
      
      Display roadWithTraffic = new TrafficDecorator(new RoadDisplay());
      roadWithTraffic.draw();
      
      Display roadWithLaneAndTraffic = new TrafficDecorator(new LaneDecorator(new RoadDisplay()));
      roadWithLaneAndTraffic.draw();
    }
  }
  ```    
