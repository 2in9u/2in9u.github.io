---
#layout: posts
excerpt: ""
title: "[Assembly] ๐. ์ด์๋ธ๋ฆฌ ์ธ์ด(์ฐ์ฐ์)"

categories:
  - assembly
# tags:
#   [Blog, jekyll, Markdown]

toc_label: "๋ชฉ์ฐจ"
toc: true
toc_sticky: true

date: 2021-10-19
last_modified_at: 2021-10-19

#published: false
---

# 0. ์๋ ฅ(Input)๊ณผ ์ถ๋ ฅ(Output)
```
; 10์ง์๋ก ์๋ ฅ ๋ฐ๊ธฐ
GET_DEC 1, al

; 16์ง์๋ก ์๋ ฅ ๋ฐ๊ธฐ
GET_HEX 4, eax 

; 10์ง์๋ก ์ถ๋ ฅ ํ๊ธฐ
PRINT_DEC 1, al

; 16์ง์๋ก ์ถ๋ ฅ ํ๊ธฐ
PRINT_HEX 4, eax

; ๊ฐํ
NEWLINE
```

<br>

# 1. ์ฌ์น์ฐ์ฐ
## 1. ๋ํ๊ธฐ
- ๋ํ๊ธฐ ์ ์ธ
    ```
    ; a += b
    add a, b
    ```

## 2. ๋นผ๊ธฐ
- ๋นผ๊ธฐ ์ ์ธ
    ```
    ; a -= b
    sub a, b
    ```

<details>
<summary><span style="color:Gray">ํผ์ฐ์ฐ์(Operand) ๊ท์น</span></summary>
<div markdown="1">

- `ํผ์ฐ์ฐ์` a, b๋ `๋ ์ง์คํฐ` ๋๋ `๋ฉ๋ชจ๋ฆฌ` ์ด๋ค.
  - <span style="color:Red">โ </span> a, b ๋ชจ๋ ๋ฉ๋ชจ๋ฆฌ์ด๋ฉด ์๋๋ค.

    ```
    ; ๋ ์ง์คํฐ + ์์
    add al, 1

    ; ๋ ์ง์คํฐ + ๋ฉ๋ชจ๋ฆฌ
    add  al, [num]

    ; ๋ฉ๋ชจ๋ฆฌ + ๋ ์ง์คํฐ
    add [num], al

    ; ๋ ์ง์คํฐ + ๋ ์ง์คํฐ
    add al, bl
    
    ; ๋ฉ๋ชจ๋ฆฌ + ์์
    add [num], byte 1
    ```

</div>
</details>

## 3. ๊ณฑํ๊ธฐ
- ๊ณฑํ๊ธฐ ์ ์ธ
    ```
    ; ax = al * bl
    mul bl

    ; dx(์์ 16bit), ax(ํ์ 16bit) = ax * bx
    mul bx 
    ```

## 4. ๋๋๊ธฐ
- ๋๋๊ธฐ ์ ์ธ
    ```
    ; al(๋ชซ), ah(๋๋จธ์ง) = ax / bl
    div bl
    ```

<br>

# 2. ์ํํธ ์ฐ์ฐ๊ณผ ๋ผ๋ฆฌ ์ฐ์ฐ
## 1. ์ํํธ(Shift) ์ฐ์ฐ
### 1. Left-Shift
- left-shift ์ ์ธ
    ```
    ; << 8
    shl eax, 8
    ```


### 2. Right-Shift
- right-shift ์ ์ธ
    ```
    ; >> 8
    shr eax, 8
    ```

## 2. ๋ผ๋ฆฌ(Logical) ์ฐ์ฐ
### 1. NOT
- not 0 โ 1, not 1 โ 0

    |Input|Output|
    |:----:|:----:|
    |0|1|
    |1|0|

- NOT ์ ์ธ
    ```
    ; !al
    not al
    ```

### 2. AND
- 1 and 1 โ 1, ๊ทธ ์ธ 0

    |Input1|Input2|Output|
    |:----:|:----:|:----:|
    |0|0|0|
    |0|1|0|
    |1|0|0|
    |1|1|1|

- AND ์ ์ธ
    ```
    ; al & bl
    and al, bl
    ```

### 3. OR
- 0 or 0 โ 0, ๊ทธ ์ธ 1

    |Input1|Input2|Output|
    |:----:|:----:|:----:|
    |0|0|0|
    |0|1|1|
    |1|0|1|
    |1|1|1|

- OR ์ ์ธ
    ```
    ; al | bl
    or al, bl
    ```

### 4. XOR
- 1 xor 0 โ 1, 0 xor 1 โ 1, ๊ทธ ์ธ 0

    |Input1|Input2|Output|
    |:----:|:----:|:----:|
    |0|0|0|
    |0|1|1|
    |1|0|1|
    |1|1|0|

- XOR ์ ์ธ
    ```
    ; al ^ bl
    xor al, bl
    ```
<details>
<summary><span style="color:Gray">โ XOR์ ํน์ง</span></summary>
<div markdown="1">

A = (A ^ A) ^ A  
โ `์ํธํ`์์ ์ ์ฉํ๊ฒ ์ฌ์ฉ๋๋ค.  
โ `Value XOR Key`

</div>
</details>
<br>

# ๐. ์ฐธ๊ณ 
* [Rookiss. [C++๊ณผ ์ธ๋ฆฌ์ผ๋ก ๋ง๋๋ MMORPG ๊ฒ์ ๊ฐ๋ฐ ์๋ฆฌ์ฆ]Part1: C++ ํ๋ก๊ทธ๋๋ฐ ์๋ฌธ. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-1/dashboard)