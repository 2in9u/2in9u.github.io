---
#layout: posts
excerpt: ""
title: "[Assembly] ๐. ์ด์๋ธ๋ฆฌ ์ธ์ด(์ ์ด๋ฌธ)"

categories:
  - assembly
# tags:
#   [Blog, jekyll, Markdown]

toc_label: "๋ชฉ์ฐจ"
toc: true
toc_sticky: true

date: 2021-10-28
last_modified_at: 2021-10-28

#published: false
---

# ์ ์ด๋ฌธ (Control Statement)
- ํ๋ก๊ทธ๋จ์ `์ ์ด`ํ๋ ๊ตฌ๋ฌธ
- `์์ฐจ`, `์ ํ`, `๋ฐ๋ณต`

## 1. ๋ถ๊ธฐ๋ฌธ
- ํน์  ์กฐ๊ฑด์ ๋ฐ๋ผ์ ์ฝ๋ ํ๋ฆ์ ์ ์ด  

### 1. ๋น๊ต
  - ๋น๊ต ์ ์ธ
    ```
    ; cmp [๊ธฐ์ค], [๋น๊ต๋์]
    cmp dst, src
    ; ๊ฒฐ๊ณผ๋ Flag Register์ ์ ์ฅ
    ``` 

### 2. ์ ํ
  - ์ ํ ์ ์ธ
    ```
    ; [์ ํ ์กฐ๊ฑด] [Label]
    ```

    |์ ํ ์กฐ๊ฑด|์ฐ์ฐ์|๊ธฐ๋ฅ|
    |:----:|:----:|:----:|
    |`jmp`||๋ฌด์กฐ๊ฑด ์ ํ (Jump)|
    |`je`|`==`|๊ฐ์ผ๋ฉด ์ ํ (Jump Equals)|
    |`jne`|`!=`|๋ค๋ฅด๋ฉด ์ ํ (Jump Not Equals)|
    |`jg`|`<`|ํฌ๋ฉด ์ ํ (Jump Greater)|
    |`jge`|`<=`|ํฌ๊ฑฐ๋ ๊ฐ์ผ๋ฉด ์ ํ (Jump Greater Equals)|
    |`jl`|`>`|์์ผ๋ฉด ์ ํ (Jump Less)|
    |`jle`|`>=`|์๊ฑฐ๋ ๊ฐ์ผ๋ฉด ์ ํ (Jump Less Equals)|

```
; Input์ด ์ง์์ธ์ง ํ์์ธ์ง ๊ตฌ๋ณ
GET_DEC 12, ax
mov bl, 2
div bl
; ๋๋จธ์ง์ 0์ ๋น๊ต
cmp ah, 0
je LABEL_EQUAL
mov al, 0
jmp LABEL_END

LABEL_EQUAL:
  mov al, 1

LABEL_END:
  PRINT_DEC 1, al
```

<br>

## 2. ๋ฐ๋ณต๋ฌธ
- ํน์  ์กฐ๊ฑด์ ๋ง์กฑํ  ๋๊น์ง ๋ฐ๋ณตํด์ ์คํ

### 0. ์ฆ๊ฐ ์ฐ์ฐ์
  - ์ฆ๊ฐ ์ฐ์ฐ์ ์ ์ธ
    ```
    ; ecx -= 1
    dec ecx
    ; ecx += 1
    inx ecx
    ```

### 1. Jump๋ฅผ ์ด์ฉํ ๋ฐฉ๋ฒ
  ```
  ; ๊ฐ์ ์ฐ์ฐ์ ์ฌ์ฉ
  mov ecx, [N]
  LABEL_LOOP_DEC:
    ; ์์
    dec ecx
    cmp ecx, 0
    jne LABEL_LOOP_DEC
  ```
  ```
  ; ์ฆ๊ฐ ์ฐ์ฐ์ ์ฌ์ฉ
  LABEL_LOOP_INC:
    ; ์์
    inc ecx
    cmp ecx, N
    jne LABEL_LOOP_INC
  ```

### 2. Loop๋ฅผ ์ด์ฉํ ๋ฐฉ๋ฒ
- Loop
  - Loop ์ ์ธ
    ```
    ; loop ํ  ๋ ๋ง๋ค ecx(๋ฐ๋ณตํ์)๊ฐ 1์ฉ ๊ฐ์
    ; ecx๊ฐ 0์ด๋ฉด ๋น ์ ธ๋๊ฐ๊ฑฐ๋ Label๋ก ์ด๋
    loop [Label]
    ```

```
mov ecx, [N]
LABEL_LOOP:
  ; ์์
loop LABEL_LOOP ; ecx -= 1
```

<br>

# ๐. ์ฐธ๊ณ 
* [Rookiss. [C++๊ณผ ์ธ๋ฆฌ์ผ๋ก ๋ง๋๋ MMORPG ๊ฒ์ ๊ฐ๋ฐ ์๋ฆฌ์ฆ]Part1: C++ ํ๋ก๊ทธ๋๋ฐ ์๋ฌธ. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-1/dashboard)