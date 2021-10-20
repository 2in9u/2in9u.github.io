---
#layout: posts
excerpt: ""
title: "[Assembly] 📂. 어셈블리 언어(연산자)"

categories:
  - assembly
# tags:
#   [Blog, jekyll, Markdown]

toc_label: "목차"
toc: true
toc_sticky: true

date: 2021-10-19
last_modified_at: 2021-10-19

#published: false
---

# 0. 입력(Input)과 출력(Output)
```
; 10진수로 입력 받기
GET_DEC 1, al

; 16진수로 입력 받기
GET_HEX 4, eax 

; 10진수로 출력 하기
PRINT_DEC 1, al

; 16진수로 출력 하기
PRINT_HEX 4, eax

; 개행
NEWLINE
```

<br>

# 1. 사칙연산
## 1. 더하기
- 더하기 선언
    ```
    ; a += b
    add a, b
    ```

## 2. 빼기
- 빼기 선언
    ```
    ; a -= b
    sub a, b
    ```

<details>
<summary><span style="color:Gray">피연산자(Operand) 규칙</span></summary>
<div markdown="1">

- `피연산자` a, b는 `레지스터` 또는 `메모리` 이다.
  - <span style="color:Red">⚠</span> a, b 모두 메모리이면 안된다.

    ```
    ; 레지스터 + 상수
    add al, 1

    ; 레지스터 + 메모리
    add  al, [num]

    ; 메모리 + 레지스터
    add [num], al

    ; 레지스터 + 레지스터
    add al, bl
    
    ; 메모리 + 상수
    add [num], byte 1
    ```

</div>
</details>

## 3. 곱하기
- 곱하기 선언
    ```
    ; ax = al * bl
    mul bl

    ; dx(상위 16bit), ax(하위 16bit) = ax * bx
    mul bx 
    ```

## 4. 나누기
- 나누기 선언
    ```
    ; al(몫), ah(나머지) = ax / bl
    div bl
    ```

<br>

# 2. 시프트 연산과 논리 연산
## 1. 시프트(Shift) 연산
### 1. Left-Shift
- left-shift 선언
    ```
    ; << 8
    shl eax, 8
    ```


### 2. Right-Shift
- right-shift 선언
    ```
    ; >> 8
    shr eax, 8
    ```

## 2. 논리(Logical) 연산
### 1. NOT
- not 0 → 1, not 1 → 0

    |Input|Output|
    |:----:|:----:|
    |0|1|
    |1|0|

- NOT 선언
    ```
    ; !al
    not al
    ```

### 2. AND
- 1 and 1 → 1, 그 외 0

    |Input1|Input2|Output|
    |:----:|:----:|:----:|
    |0|0|0|
    |0|1|0|
    |1|0|0|
    |1|1|1|

- AND 선언
    ```
    ; al & bl
    and al, bl
    ```

### 3. OR
- 0 or 0 → 0, 그 외 1

    |Input1|Input2|Output|
    |:----:|:----:|:----:|
    |0|0|0|
    |0|1|1|
    |1|0|1|
    |1|1|1|

- OR 선언
    ```
    ; al | bl
    or al, bl
    ```

### 4. XOR
- 1 xor 0 → 1, 0 xor 1 → 1, 그 외 0

    |Input1|Input2|Output|
    |:----:|:----:|:----:|
    |0|0|0|
    |0|1|1|
    |1|0|1|
    |1|1|0|

- XOR 선언
    ```
    ; al ^ bl
    xor al, bl
    ```
<details>
<summary><span style="color:Gray">✍ XOR의 특징</span></summary>
<div markdown="1">

A = (A ^ A) ^ A  
⁕ `암호학`에서 유용하게 사용된다.  
⁕ `Value XOR Key`

</div>
</details>
<br>

# 📑. 참고
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part1: C++ 프로그래밍 입문. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-1/dashboard)