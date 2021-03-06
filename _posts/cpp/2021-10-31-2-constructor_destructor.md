---
#layout: posts
excerpt: ""
title: "[C++] π. μμ±μ(Constructor)μ μλ©Έμ(Destructor)"

categories:
    - cpp
# tag:
#     [cpp, c++, oop]

toc_label: "λͺ©μ°¨"
toc: true
toc_sticky: true

date: 2021-10-31
last_modified_at: 2021-10-31
---

# 0. μμ ν΄λμ€

```c++
class Player
{
    public:
      int _hp;
      int _attack;
      int _defence; 
}
```

# 1. μμ±μ **(Constructor)**
- μμμ(Constructor) : κ°μ²΄μ μμ(`νμ`) (`μ¬λ¬ κ°` μ‘΄μ¬ κ°λ₯)
- μμ±μ νΉμ§
  1. κ°μ²΄κ° μμ±λ  λ νμν μ΄κΈ° μμμ μν¨
  2. μμ±μ ν¨μλ ν λ²λ§ μ€νλ¨
  3. μμ±μ ν¨μμ μ΄λ¦μ ν΄λμ€ μ΄λ¦κ³Ό λμΌ
  4. λ¦¬ν΄ νμμ μ μΈνμ§ μμ
  5. μ€λ³΅λ μμ±μλ λ§€κ° λ³μ κ°μ λλ νμμ΄ μλ‘ λ€λ₯΄κ² μ μΈν¨, μ€λ³΅λ μμ±μ μ€ νλλ§ μ€ν

## 1. μμμ  μμ±μ (Implicit Constructor)
- μμ±μλ₯Ό λͺμμ μΌλ‘ λ§λ€μ§ μμΌλ©΄, μλ¬΄ μΈμλ λ°μ§ μλ `κΈ°λ³Έ μμ±μ`κ° μ»΄νμΌλ¬μμν΄ μλμΌλ‘ λ§λ€μ΄μ§
- **<span style="color:Red">But! </span>** λͺμμ (**Explicit**) μμ±μκ° μμΌλ©΄, `κΈ°λ³Έ μμ±μ`λ μλμΌλ‘ λ§λ€μ΄μ§μ§ μμ

## 2. κΈ°λ³Έ μμ±μ (Default Constructor) 
- μΈμκ° μμ

```c++  
public:
  Player()
  {
      cout << "Player() κΈ°λ³Έ μμ±μ νΈμΆ" << endl;
      // λ©€λ² λ³μ μ΄κΈ°κ° μ€μ 
  }
```

```c++
int main()
{
    Player p0; // κΈ°λ³Έ μμ±μ νΈμΆ
    return 0;
}
```

## 3. λ³΅μ¬ μμ±μ (Copy Constructor) 
- μκΈ° μμ μ ν΄λμ€ μ°Έμ‘° νμμ μΈμλ‘ λ°μ
  
```c++
public:
  Player(const Player& player)
  {
      // μΌλ°μ μΌλ‘ `λκ°μ` λ°μ΄ν°λ₯Ό μ§λ κ°μ²΄κ° μμ±λκΈΈ κΈ°λν¨
      _hp = player._hp;
      _attack = player._attack;
      _defence = player._defence;
  }
```

```c++
int main()
{
    // λ³΅μ¬ μμ±μ (μμ± + λ³΅μ¬)
    Player p1(p0);
    Player p2 = p0;

    // κΈ°λ³Έ μμ±μ
    Player p3;
    // λ³΅μ¬
    p3 = p1;

    return 0;
}
```

## 4. κΈ°ν μμ±μ

```c++
public:
  Player(int hp)
  {
      // μμμ  νμλ³ν μμ±μ
      // λͺμμ μΌ κ²½μ° νΈμΆ o
  }

  explicit Player(int hp)
  {
      // λͺμμ  νμλ³ν μμ±μ
      // μμμ μΌ κ²½μ° νΈμΆ x
  }
```

- **νμ λ³ν μμ±μ** (Conversion Constructor)
  - μΈμλ₯Ό 1κ°λ§ λ°λ κΈ°ν μμ±μ
  
  ```c++
  int main()
  {
      Player p;
      // explicit λ―Έμ¬μ© μ,
      p = 1; // Player(1) νΈμΆ

      // explicit μ¬μ© μ,
      p = (Player)1; // Player(1) νΈμΆ

      return 0;
  }
  ```


<br>

# 2. μλ©Έμ **(Destructor)**
- μλ©Έμ (Destructor) : κ°μ²΄μ λ(`μλ©Έ`) (μ€μ§ 1κ°λ§ μ‘΄μ¬, `μ μΌ`)
- **<span style="color:Red">λΆλͺ¨ ν΄λμ€μ μλ©Έμλ ν­μ κ°μν¨μ(virtual)λ‘ κ΅¬ννκΈ°!</span>**
- μλ©Έμ νΉμ§
  1. κ°μ²΄κ° μλ©Έλ  λ λ§λ¬΄λ¦¬ μμμ μν¨
  2. ν΄λμ€ μ΄λ¦ μμ ~λ₯Ό λΆμ
  3. λ¦¬ν΄ νμ μμ
  4. μλ©Έμκ° μ μΈλμ§ μμμ λ, κΈ°λ³Έ μλ©Έμ(Default Destructor)κ° μμ±

```c++
public:
  ~Player()
  {
      cout << "Player() μλ©Έμ νΈμΆ" << endl;
  }
```

- μλ©Έμ νΈμΆ μμ : μμ±λ λ°λμμΌλ‘ νΈμΆ
```c++
int main()
{
    Player p0;
    // p0 κ°μ²΄ μμ±μ νΈμΆ
    Player p1;
    // p1 κ°μ²΄ μμ±μ νΈμΆ
    Player p2;
    // p2 κ°μ²΄ μμ±μ νΈμΆ

    return 0;
    // p2 κ°μ²΄ μλ©Έμ νΈμΆ
    // p1 κ°μ²΄ μλ©Έμ νΈμΆ
    // p0 κ°μ²΄ μλ©Έμ νΈμΆ
}
```

<br>

# π. μ°Έκ³ 
* [ν©κΈ°ν(2013). (λͺν)C++ Programming. νμ£Ό:μλ₯μΆνμ¬.](https://www.booksr.co.kr/html/book/book.asp?seq=697053)
* [Rookiss. [C++κ³Ό μΈλ¦¬μΌλ‘ λ§λλ MMORPG κ²μ κ°λ° μλ¦¬μ¦]Part1: C++ νλ‘κ·Έλλ° μλ¬Έ. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-1/dashboard)