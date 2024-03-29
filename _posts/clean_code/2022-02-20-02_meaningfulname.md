---
#layout: posts
excerpt: ""
title: "[Clean Code] 📂. Assignment #03. 2장. 의미 있는 이름"

categories:
  - clean_code
  
tags:
    [노마드코더, 북클럽, 노개북]

toc_label: "목차"
toc: true
toc_sticky: true

date: 2022-02-20
last_modified_at: 2022-02-20
---

# TIL (Today I Learned)
## 2022.02.20(일)

<br>

## 오늘 읽은 범위
> - 2장. 의미 있는 이름 (p.38)

<br>

## 책에서 기억하고 싶은 내용을 써보세요.

1. 의도가 분명하게 이름을 지어라
   > - 의도가 드러나는 이름을 사용하면 코드의 이해와 변경이 쉬워진다.

<br>

2. 그릇된 정보를 피하라
   > - 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용하면 안된다.
   > - 서로 흡사한 이름을 사용하지 않도록 주의한다.
   > - 컨테이너 유형을 이름에 넣지 않는다.
   > - 유사한 개념은 유사한 표기법을 사용한다. (`일관성`)
   > - 소문자 L, 대문자 O 가 들어가는 변수 지양 (소문자 L => 1로 보임, 대문자 O => 0으로 보임)

<br>

3. 이름이 달라야 한다면 의미도 달라져야한다.
   > - 연속된 숫자를 붙이거나 불용어(noise word)를 추가하는 방식은 적절하지 못하다.
   > - 아무런 정보도 제공하지 못하는 이름은 지양한다.

<br>

4. 검색하기 쉬운 이름을 사용하라
   > - 긴 이름 보단 `짧은 이름`이, 상수 보다 `검색하기 쉬운 이름`이 좋다.
   > - 이름의 길이는 `범위 크기(사용량)`에 비례해야한다.

<br>

5. 인코딩을 피하라 (옛날 방식)
   > - 헝가리식 표기법 지양 - 현재 IDE 발전으로 이름에 타입이 들어가야 할 필요가 없다.
   > - 멤버 변수 접두어 지양 - IDE에서 변수 종류에 따라 표기법을 바꿀 수 있기 때문에 멤버 변수에 m_ 접두어를 붙일 필요 없다.
   > - 인터페이스 클래스에 접두어 I 지양

6. 문제 영역이나 해법 영역에서 사용하지 않는 이름 지양
   > - `명료하고 남들이 이해하는 코드를 작성`한다.
   > - 의도가 분명하고 솔직하게 표현한다.
   > - 코드를 읽는 사람은 프로그래머이기에 `전산 용어`, `알고리즘 이름`, `패턴 이름` `수학 용어` 등 사용 가능하다.
   > - 프로그래머 용어가 없을 시, `문제 영역`에서 이름을 가져온다.

<br>

7. 클래스와 메서드 이름
   > 1. `클래스` 이름 : `명사`, `명사구`
   >    - (ex) Customer, WikiPage, Account, AddressParser
   > 2. `메서드` 이름 : `동사`, `동사구`
   >    - 접근자 (Accessor) - `get`
   >    - 변경자 (Mutator) - `set`
   >    - 조건자 (Predicate) - `is`
   >    - 생성자를 중복정의 할 때는 `정적 팩토리 메서드`를 사용한다.

<br> 

8. 한 개념에 한 단어를 하용하라
   > - 추상적인 개념 하나에 단어 하나를 선택해 이를 고수한다.
   > - 메서드의 이름은 독자적이고 일관적이어야 한다.
   > - 한 단어를 두 가지 목적으로 사용하지 않는다.

<br>

9. 의미 있는 맥락 추가 & 불필요한 맥락 삭제
    > - 의미 있는 접두어 추가, 맥락 개선
    > - 이름에 불필요한 맥락을 추가하지 않도록 주의

<br>

따라서, 좋은 이름을 선택할려면 `설명 능력`이 뛰어나야하고, `문화적 배경`이 같아야 한다.

## 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
> 누구나 다 알 수 있는 기가막힌 이름을 만드는 것은 정말 어렵다. 나도 개발을 하면서 이름을 어떻게 지어야하는지 정말 많이 고민하고 더 나은 이름이 생각나면 계속 변경을 한다. 옛날 사람들도 비슷한 고민을 하며 명명규칙, 방법들을 생각한 것을 보면 사람들은 생각 하는 것이 비슷비슷한 것 같다.

<br>

## 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요.
- 없음
  
<br>

---
