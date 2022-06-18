# Generics


### 1.1 Generics란

제너릭스는 메소드나 컬렉션 클래스에 컴파일 시 타입 체크를 해주는 기능이다.
객체의 타입을 컴파일 시에 체크하기 때문에 타입 안정성을 높이고 형변환의 번거로움을 줄여준다.


ex) ArrayList와 같은 컬렉션 클래스는 다양한 종류의 객체를 담을 수 있긴 하지만 
보통 한 종류의 객체를 담는 경우가 많다. 리스트에서 꺼낼 때 마다 타입 체크를 하고
형변환을 하는 것은 불편하며 원하지 않는 종류의 객체가 포함되는 것을 막기 위해 제너릭스를 사용한다.


### 1.2 Generic Class Declaration

```java
class Box {
    Object item;
    
    void setItem(Object item) { this.item = item; }
    Object getItem() { retrurn item; }
}

// 제너릭 클래스로 변경

class Box<T> {
    T item;

    void setItem(T item) { this.item = item; }
    T getItem() { retrurn item; }
}
```
`Box<T>`에서 T를 타입 변수(type variable) 이라고 한다. 임의의 참조형 타입을 의미하고
제너릭 클래스가 된 Box 클래스의 객체를 생성할 때는 참조변수와 생성자에 타입 T 대신에 사용될 실제 타입을 지정해주어야 한다.

```java
Box<String> b = new Box<String>();
b.setItem(new Object());    // error, String 이외의 타입은 지정 불가
b.setItem("ABC");
String item = b.getItem();  // (String) b.getItem(); 과 같은 형변환이 필요 없음.
```


### 1.3 Generic Class의 객체 생성과 사용

```java
import java.util.ArrayList;

class Box<T> {
    ArrayList<T> list = new ArrayList<>();
    
    void add(T item)            { list.add(item) }
    T get(int i)                { return list.get(i); }
    ArrayList<T> getList()      { return list; }
    int size()                  { return list.size(); }
    public String toString()    { return list.toString(); }
}
```
- Box < T > 의 객체는 한 가지 종류 T만 저장 가능
- ArrayList를 이용하여 여러 객체를 저장 가능

```java
Box<Apple> appleBox = new Box<Apple>();
appleBox.add(new Apple());      // ok
appleBox.add(new Grape());     // Box<Apple>에는 Apple 객체만 추가 가능
```


### 1.4 제한된 Generic Class


- 타입 매개변수에 지정할 수 있는 타입의 종류를 제한하기 위해 'extends'를 사용하여 해당 클래스의 자손들만 담을 수 있는 제한을 추가할 수 있다.

```java
import java.util.ArrayList;

class FruitBox<T extends Fruit> {
    ArrayList<T> list = new ArrayList<>();
}
```
---


참고 : 도서<자바의 정석>