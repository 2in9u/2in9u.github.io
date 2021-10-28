---
#layout: posts
excerpt: ""
title: "[Assembly] 📂. 어셈블리 언어(제어문)"

categories:
  - assembly
# tags:
#   [Blog, jekyll, Markdown]

toc_label: "목차"
toc: true
toc_sticky: true

date: 2021-10-28
last_modified_at: 2021-10-28

#published: false
---

# 제어문 (Control Statement)
- 프로그램을 `제어`하는 구문
- `순차`, `선택`, `반복`

## 1. 분기문
- 특정 조건에 따라서 코드 흐름을 제어
### 1. 비교
- 비교 선언
  ```
  ; cmp 기준, 비교대상
  cmp dst, src
  ; 결과는 Flag Register에 저장
  ```

### 2. 점프
- 점프 선언
  ```
  ; [점프 조건] [Label]
  ```
  - 점프 조건
    1. `jmp` : 무조건 점프 (Jump)
    2. `je` : 같으면 점프 (Jump Equals , `==`)
    3. `jne` : 다르면 점프 (Jump Not Equals , `!=`)
    4. `jg` : 크면 점프 (Jump Greater, `<`)
    5. `jge` : 크거나 같으면 점프 (Jump Greater Equals, `<=`)
    6. `jl` : 작으면 점프 (Jump Less, `>`)
    7. `jle` : 작거나 같으면 점프 (Jump Less Equals, `>=`)

```
; Input이 짝수인지 홀수인지 구별
GET_DEC 12, ax
mov bl, 2
div bl
; 나머지와 0을 비교
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

## 2. 반복문
- 특정 조건을 만족할 때까지 반복해서 실행
### 0. 증감 연산자
- 증감 연산자 선언
  ```
  ; ecx -= 1
  dec ecx
  ; ecx += 1
  inx ecx
  ```

### 1. Jump를 이용한 방법
  ```
  mov ecx, [N]
  LABEL_LOOP_DEC:
    ; 작업
    dec ecx
    cmp ecx, 0
    jne LABEL_LOOP_DEC
  ```
  ```
  LABEL_LOOP_INC:
    ; 작업
    inc ecx
    cmp ecx, N
    jne LABEL_LOOP_INC
  ```

### 2. Loop를 이용한 방법
#### 0. Loop
  - Loop 선언
    ```
    ; loop 할 때 마다 ecx(반복횟수)가 1씩 감소
    ; ecx가 0이면 빠져나가거나 Label로 이동
    loop [Label]
    ```

```
mov ecx, [N]
LABEL_LOOP:
  ; 작업
loop LABEL_LOOP ; ecx -= 1
```

<br>

# 📑. 참고
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part1: C++ 프로그래밍 입문. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-1/dashboard)