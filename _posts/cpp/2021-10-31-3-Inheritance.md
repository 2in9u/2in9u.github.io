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

published: false
---

# 1. 상속성 (Inheritance)
- 자식(파생) 클래스가 부모(기본) 클래스의 멤버들을 포함
- 이미 만들어진 클래스를 포함하기에 코드의 중복 작성을 피할 수 있음
- 상속의 장점
  1. 간결한 클래스 작성
  2. 클래스 간의 계층적 분류 및 관리 용이
  3. 클래스 재사용과 확장을 통한 소프트웨어의 생산성 향상
- **<span style="color:Red">But! </span>** 연관성 없는 클래스를 상속해서는 안됨
  > 서로 관련있는 클래스들을 상속 관계로 정의해야 객체 지향적 특성이 살아나며, 코드의 재사용성이 높아진다.

<br>

# 2. 상속 선언
- 선언 방법
    ```c++
    class 파생클래스명 : 상속접근지정자 기본클래스명 { … }
    ```
    - 상속접근지정자 ⇨ `public`, `private`, `protected`

```c++
// 부모 클래스 = 기본 클래스(Base Class)
class Player
{
    public:
        Player() { … }
        ~Player() { … }
        void Move();
        void Attack();
        void Die();
    
    public:
        int m_hp;
        int m_attack;
        int m_defence;
}

// 자식 클래스 = 파생 클래스(Derived Class)
class Knight : public Player
{
    public:
        Knight() { … }
        ~Knight() { … }
        // 기본 클래스의 함수 재정의
        void Move();

    public:
        int m_stamina;
}

class Mage : public Player
{
    public:
        Mage() { … }
        ~Mage() { … }

    public:
        int m_mp;
}
```
> Knight 클래스와 Mage 클래스는 Player 클래스를 상속 받았다.

<br>

# 3. 자식 클래스의 생성자와 소멸자


<br>

# 4. 기본 클래스와 파생 클래스의 객체와 멤버 호출


<br>

# 📑. 참고
* [황기태(2013). (명품)C++ Programming. 파주:생능출판사.](https://www.booksr.co.kr/html/book/book.asp?seq=697053)
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part1: C++ 프로그래밍 입문. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-1/dashboard)