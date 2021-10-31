---
#layout: posts
excerpt: ""
title: "[C++] 📂. 생성자(Constructor)와 소멸자(Destructor)"

categories:
    - cpp
# tag:
#     [cpp, c++, oop]

toc_label: "목차"
toc: true
toc_sticky: true

date: 2021-10-31
last_modified_at: 2021-10-31
---

# 0. 예시 클래스

```c++
class Player
{
    public:
      int _hp;
      int _attack;
      int _defence; 
}
```

# 1. 생성자 **(Constructor)**
- 생서자(Constructor) : 객체의 시작(`탄생`) (`여러 개` 존재 가능)
- 생성자 특징
  1. 객체가 생성될 때 필요한 초기 작업을 위함
  2. 생성자 함수는 한 번만 실행됨
  3. 생성자 함수의 이름은 클래스 이름과 동일
  4. 리턴 타입을 선언하지 않음
  5. 중복된 생성자는 매개 변수 개수 또는 타입이 서로 다르게 선언함, 중복된 생성자 중 하나만 실행

## 1. 암시적 생성자 (Implicit Constructor)
- 생성자를 명시적으로 만들지 않으면, 아무 인자도 받지 않는 `기본 생성자`가 컴파일러에의해 자동으로 만들어짐
- **<span style="color:Red">But! </span>** 명시적(**Explicit**) 생성자가 있으면, `기본 생성자`는 자동으로 만들어지지 않음

## 2. 기본 생성자 (Default Constructor) 
- 인자가 없음

```c++  
public:
  Player()
  {
      cout << "Player() 기본 생성자 호출" << endl;
      // 멤버 변수 초기값 설정
  }
```

```c++
int main()
{
    Player p0; // 기본 생성자 호출
    return 0;
}
```

## 3. 복사 생성자 (Copy Constructor) 
- 자기 자신의 클래스 참조 타입을 인자로 받음
  
```c++
public:
  Player(const Player& player)
  {
      // 일반적으로 `똑같은` 데이터를 지닌 객체가 생성되길 기대함
      _hp = player._hp;
      _attack = player._attack;
      _defence = player._defence;
  }
```

```c++
int main()
{
    // 복사 생성자 (생성 + 복사)
    Player p1(p0);
    Player p2 = p0;

    // 기본 생성자
    Player p3;
    // 복사
    p3 = p1;

    return 0;
}
```

## 4. 기타 생성자

```c++
public:
  Player(int hp)
  {
      // 암시적 타입변환 생성자
      // 명시적일 경우 호출 o
  }

  explicit Player(int hp)
  {
      // 명시적 타입변환 생성자
      // 암시적일 경우 호출 x
  }
```

- **타입 변환 생성자** (Conversion Constructor)
  - 인자를 1개만 받는 기타 생성자
  
  ```c++
  int main()
  {
      Player p;
      // explicit 미사용 시,
      p = 1; // Player(1) 호출

      // explicit 사용 시,
      p = (Player)1; // Player(1) 호출

      return 0;
  }
  ```


<br>

# 2. 소멸자 **(Destructor)**
- 소멸자 (Destructor) : 객체의 끝(`소멸`) (오직 1개만 존재, `유일`)
- **<span style="color:Red">부모 클래스의 소멸자는 항상 가상함수(virtual)로 구현하기!</span>**
- 소멸자 특징
  1. 객체가 소멸될 떄 마무리 작업을 위함
  2. 클래스 이름 앞에 ~를 붙임
  3. 리턴 타입 없음
  4. 소멸자가 선언되지 않았을 때, 기본 소멸자(Default Destructor)가 생성

```c++
public:
  ~Player()
  {
      cout << "Player() 소멸자 호출" << endl;
  }
```

- 소멸자 호출 순서 : 생성된 반대순으로 호출
```c++
int main()
{
    Player p0;
    // p0 객체 생성자 호출
    Player p1;
    // p1 객체 생성자 호출
    Player p2;
    // p2 객체 생성자 호출

    return 0;
    // p2 객체 소멸자 호출
    // p1 객체 소멸자 호출
    // p0 객체 소멸자 호출
}
```

<br>

# 📑. 참고
* [황기태(2013). (명품)C++ Programming. 파주:생능출판사.](https://www.booksr.co.kr/html/book/book.asp?seq=697053)
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part1: C++ 프로그래밍 입문. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-1/dashboard)