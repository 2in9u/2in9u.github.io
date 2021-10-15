---
#layout: posts
excerpt: ""
title: "[Assembly] 📂. 어셈블리 언어(데이터)"

categories:
  - assembly
# tags:
#   [Blog, jekyll, Markdown]

toc_label: "목차"
toc: true
toc_sticky: true

date: 2021-10-15
last_modified_at: 2021-10-15

published: false
---

# 1. 변수
⁕ `변수` : 변할 수 있는 데이터를 가지고 있는 메모리 영역 (= 데이터를 저장하는 `바구니`)

## Ⅰ. 변수 선언 영역 (**Data**와 **BSS** 영역)
- 어셈블리 언어에서는 `초기화 여부`에 따라 변수를 선언하는 영역이 다르다.
  - .data
  - .bss
  ```avrasm
  section .data
    ; 초기화 한 전역변수와 정적변수 선언 영역

  section .bss  
    ; 초기화하지 않은 전역변수와 정적변수 선언 영역
    ; 초기값은 0으로 셋팅
  ```
  <details>
  <summary><span style="color:Gray">.data와 .bss로 나눈 이유!</span> 🙋‍♀️</summary>
    <div markdown="1">
      최종적인 크기`만 알고 있으면 파일에다 초기값을 저장할 필요가 없다.  
      따라서, 실행파일(.exe …)의 `용량`이 `작아진다`.
    </div>
  </details>

## Ⅱ. 변수 선언
  - 변수 선언 양식
    1. .data
    > [`변수이름`] [`크기`] [`초기값`]
    2. .bss
    > [`변수이름`] [`크기`] [``]
  - 데이터 지시어
    |.data|.bss|size|
    |:----:|:----:|:----:|
    |db|resb|1byte|
    |dw|resw|1word (2byte)|
    |dd|resd|1dword (4byte)|
    |dq|resq|1qword (8byte)|
    ```avrasm
    section .data
    a db 0x11
    b dq 0x2222222222222222

    section .bss
    c resb 10
    ```


## Ⅲ. 

## Ⅳ. 

## Ⅴ.

<br>

# 2. 문자


<br>

# 3. 레지스터에 데이터를 불러오는 방법 (mov)


<br>


# 📑. 참고
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part1: C++ 프로그래밍 입문. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-1/dashboard)