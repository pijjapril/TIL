#Promotion, Casting


### Promotion


- 자동 형변환, 묵시적 형변환
- 작은 데이터 타입 -> 큰 데이터 타입
- 자바는 타입이 일치하지 않아도 타입끼리 호환성이 있으면 자동 변환 가능
- 자동 변환이 가능한 방향

<img src="C:\Users\kshol\IdeaProjects\TIL\resources\img\promotion.PNG">

- 확장성에 의한 자동 변환(short -> int)
```java
short shorNum = 100;

int intNum = shortNum;
System.out.println(intNum);
```


---


### Casting


- 강제 형변환, 명시적 형변환
- 데이터 손실 가능성

```java
int a = (int)1.0;   // 컴파일 에러 해결
```

- 업 캐스팅
```java
int a = (int)true;
```
- boolean 자료형과 int 자료형은 서로 성질이 맞지 않으므로 형변환 불가