---
#layout: posts
excerpt: ""
title: "[Assembly] π. λ°μ΄ν°μ λ μ§μ€ν°"

categories:
  - assembly
# tags:
#   [Blog, jekyll, Markdown]

toc_label: "λͺ©μ°¨"
toc: true
toc_sticky: true

date: 2021-10-14
last_modified_at: 2021-10-15

#published: false
---

# 0. PE(Portable Excutable) ν¬λ§·
- μλμ°μμ μ¬μ©λλ `μ€ν κ°λ₯ν νμΌ` νμ 
- **ex)** .exe, .dll, .obj, .sys β¦  
![image](../../assets/images/assembly_img/2_data_register/peformat.png)
- μ΄μλΈλ¦¬ μΈμ΄ μ€μ΅ κΈ°λ³Έ μ½λ
    ```avrasm
    %include "io64.inc"

    section .text
    global CMAIN
    CMAIN:
        ;write your code here
        xor rax, rax
        ret
        
    section .bss

    section .data
    ```

<br>

# 1. λ°μ΄ν° κΈ°μ΄
## 1. μ»΄ν¨ν°μ λ°μ΄ν° λ¨μ
 - μ»΄ν¨ν°λ 0κ³Ό 1λ‘ μ΄λ£¨μ΄μ§ λ°μ΄ν°λ₯Ό μ¬μ©νλ€.
### 1. λΉνΈ (Bit, Binary Digit)
 - `0` == OFF == `False`(κ±°μ§)
 - `1` == ON == `True`(μ°Έ)

### 2. λ°μ΄νΈ (Byte)
 - `8bit` == 1byte (λΆνΈλ₯Ό κ°μ§λ μ μ μΌ λ, -128~127 μ¬μ΄μ ννμ΄ κ°λ₯νλ€.)

## 2. μ§μ (Notation)
### 1. 10μ§μ
 - `0, 1, 2, 3, 4, 5, 6, 7, 8, 9`μ μ«μλ‘ μλ₯Ό νννλ€.
 - **ex)** 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, β¦ , 20, 21, β¦ , 99, 100, 101, β¦

### 2. 2μ§μ
 - `0, 1`μ μ«μλ‘ μλ₯Ό νννλ€.
 - **ex)** 0, 1, 10, 11, 100, 101, 110, 111, 1000 β¦
 - μ«μκ° μ»€μ§μλ‘ `νκΈ°ν΄μΌ ν  μ«μ`κ° λ§μμ§λ€.
 
### 3. 8μ§μ
 - `0, 1, 2, 3, 4, 5, 6, 7`μ μ«μλ‘ μλ₯Ό νννλ€.
 - **ex)** 0, 1, 2, 3, 4, 5, 6, 7, 10, 11, 12, 13, β¦ , 20, 21, β¦ , 77, 100, 101, β¦  

### 4. 16μ§μ
 - `0, 1, 2, 3, 4, 5, 6, 7, 8, 9, A(10), B(11), C(12), D(13), E(14), F(15)`μ μ«μμ λ¬Έμλ‘ μλ₯Ό νννλ€.
 - **ex)** 0, 1, 2, 3, 4, 5, β¦ , E, F, 10, 11, 12, β¦ , 1F, 20, 21, β¦ , FF, 100, 101, β¦
 - 2μ§μμ μλ‘ `λ³ν`μ΄ μ½λ€.

<br>

# 2. λ μ§μ€ν° κΈ°μ΄
## 1. λ μ§μ€ν° λ°μ΄ν° λ¨μ
 1. `1bit`
 2. 8bit = `1byte`
 3. 16bit = 2byte = `1word`
 4. 32bit = 4byte = 2word = `1dword` (double-word)
 5. 64bit = 8btye = 4word = 2dword = `1qword` (quad-word)

## 2. λ μ§μ€ν°
 - `λ μ§μ€ν°(Register)`
    1. CPU μμ μμ κΈ°μ΅μ₯μΉ μ΄λ€. 
    2. μ°μ°μ μ€κ° κ²°κ³Όλ¬Όμ μ μ₯νλ μ©λμ΄λ€.
    3. μ κ·Όμλκ° λΉ λ₯΄λ€
    4. μ©λμ΄ μ λ€.
    ![image](https://images.anandtech.com/reviews/cpu/amd/x86-64/registers.gif)
    ![image](../../assets/images/assembly_img/2_data_register/register_x64_unit.png)


<br>

# π. μ°Έκ³ 
* [AMD's Voyage into the 64-bit Arena: x86-64 Revealed > How x86-64 Works](https://www.anandtech.com/show/598/5)
* [Rookiss. [C++κ³Ό μΈλ¦¬μΌλ‘ λ§λλ MMORPG κ²μ κ°λ° μλ¦¬μ¦]Part1: C++ νλ‘κ·Έλλ° μλ¬Έ. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-1/dashboard)