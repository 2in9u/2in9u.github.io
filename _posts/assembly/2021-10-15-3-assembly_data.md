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
last_modified_at: 2021-10-18

#published: false
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
  `최종적인 크기`만 알고 있으면 파일에다 초기값을 저장할 필요가 없다.  
  따라서, 실행파일(.exe …)의 `용량`이 `작아진다`.
</div>
</details>

<br>

## Ⅱ. 변수 선언 **(Assembly)**
  - 변수 선언 양식
    1. **.data**
    > [`변수이름`] [`크기`] [`초기값`] <br>
    > ```avrasm
    > a db 0x11
    > ```

    2. **.bss**
    > [`변수이름`] [`크기`] [`개수`]
    > ```avrasm
    > b resb 10 (1btye 크기의 공간 10개 == 10byte)
    > ```

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

<br>

# 2. 문자와 엔디안
> 0과 1로 이루어진 `동일한 데이터`를 `해석`에 따라 `여러가지로 표현`이 `가능`하다.
## 1. 문자열
- cstyle 문자 표현(문자열 표현) <br>
  '문자', '문자', '문자', … , '문자', `'\null'` <br>
  ↪ 문자 마지막에 끝을 알리는 `\null`이 들어간다.

### 문자열 변수 선언 **(Assembly)**
  - 문자 선언 양식
    > [`변수이름`] [`크기`] [`문자열`]
    > ```avrasm
    > msg1 db 'Hello World', 0x00
    > msg2 db 0x48, 0x65, 0x6c, 0x6c, 0x6f, 0x20, 0x57, 0x6f, 0x72, 0x6c, 0x64, 0x00
    ; 0x48('H'), 0x65('e'), 0x6c('l'), 0x6c('l'), 0x6f('o'), 0x20(' '), 0x57('W'), 0x6f('o'), 0x72('r'), 0x6c('l'), 0x64('d'), 0x00('\null')
    > ```

## 2. 엔디안 (Endianness)
### 1. 리틀 엔디안 (Little-Endian)
- `최하위` 바이트(**LSB**: Least Significant Byte)부터 차례로 저장한다.
- `캐스팅`(데이터 크기 변화)에 유리하다.

### 2. 빅 엔디안 (Big-Endian)
- `최상위` 바이트(**MSB**: Most Significant Byte)부터 차례로 저장한다.
- `숫자 비교`에 유리하다.

<br>

# 3. 레지스터에 데이터를 불러오는 방법 (mov)
1. **mov** reg1, cst
    - cst 값을 reg1 레지스터로 삽입

2. **mov** reg1, reg2
    - reg2 레지스터 값을 ref1 레지스터로 복사

3. **mov** rax, a
    - 변수 a의 `주소 값`을 rax 레지스터로 복사

4. **mov** rad, [a]
    - 변수 a의 `값`을 rax 레지스터로 복사

5. **mov** [a], word 0x6666
    - 2btye 크기(word)의 값 0x66를 변수 a의 값으로 초기화

<br>

# 📑. 참고
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part1: C++ 프로그래밍 입문. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-1/dashboard)