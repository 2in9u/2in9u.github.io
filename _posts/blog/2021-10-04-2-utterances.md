---
#layout: posts
excerpt: ""
title: "[GitHub Blog] ๐. ๐ฎUtterances๋ก ๋๊ธ ๊ธฐ๋ฅ ์ ์ฉํ๊ธฐ"

categories:
  - blog
  
toc_label: "๋ชฉ์ฐจ"
toc: true
toc_sticky: true

date: 2021-10-04
last_modified_at: 2021-10-04
---

# 1. ๐ฎUtterances ์ ํ ์ด์ 
github blog๋ฅผ ์ํํ๋ฉด์ ๋๊ธ ๊ธฐ๋ฅ ์ ์ฉ์ ๋ํด์ ์์๋ณด๋ ์ค `disqus`๊ฐ ๊ฐ์ฅ ๋ง์ด ๋์ค๊ธธ๋ disqus๋ก ์ ์ฉํ ๋ ค๊ณ  ํ์๋ค. ํ์ง๋ง, ๋ฌด๋ฃ๋ฒ์ ์ `๊ด๊ณ `๊ฐ ๋ถ๊ณ  `๋ฌด๊ฒ๋ค`๊ณ  ํ๋ค. ๊ทธ๋์ ๋ค๋ฅธ ๊ฒ์ ์ฐพ์๋ณด๋ ์ค `utterances`๋ฅผ ๋ฐ๊ฒฌํ์๋ค. 

## ๐ฎUtterances ์ฅ์ 
 > 1. utterances๋ `github app`์ด๊ธฐ์ github ๊ณ์ ๋ง ์์ผ๋ฉด ๋๋ค.  
 > 2. ์ค์น, ์ํ, ๊ด๋ฆฌ๊ฐ `์ฝ๋ค`.  
 > 3. ๋๊ธ์ด ๋ฑ๋ก๋๋ฉด ์๋ก์ด issue๊ฐ ๋ฑ๋ก๋์ด `๋ฉ์ผ์ผ๋ก ์๋`์ ๋ฐ์ ์ ์๋ค.  
 > 4. `Markdown ๋ฌธ๋ฒ์ ์ด์ฉ`ํ์ฌ ๋๊ธ ์์ฑ์ด `๊ฐ๋ฅ`ํ๋ค.  

<br>

# 2. ๐ฎUtterances ์ค์น ๋ฐ ์ํ
## โ . Utterances ์ค์น
1. [GitHub App utterances Install Link](https://github.com/apps/utterances
)์์ utterances๋ฅผ `์ค์น` ํ๋ค.
![image](../../assets/images/blog_img/2_utterances/utterances_install.png)

2. Install ๋ฒํผ์ ๋๋ฅธ ํ, ๋๊ธ์ ๊ด๋ฆฌํ  `์ ์ฅ์`๋ฅผ ์ ํํด์ค๋ค. 
   > โ ๋๊ธ์ ์ ์ฅ ํ  repository๋ public์ผ๋ก ์๋ก ๋ง๋ค์ด ์ฃผ์๋ค. (2in9u/GithubBlogComments)

    ![image](../../assets/images/blog_img/2_utterances/utterances_install_setting.png)

## โก. Utterances ์ค์ 
1. Repository ์ค์ 
   * `repo:`์ `username/repo-name`์ ์๋ ฅํ๋ค.
   ![image](../../assets/images/blog_img/2_utterances/utterances_repository_setting.png)

2. Blog Post โ Issue Mapping ์ค์ 
   * ๋ธ๋ก๊ทธ ํฌ์คํธ ์ค ์ด๋ค ๊ฒ์ `์ด์ ์ ๋ชฉ`์ผ๋ก `์ค์ `ํ  ๊ฒ์ธ์ง ์ ํํ๋ค.
   * ์ ๋ณ๊ฒฝ์ด ๋์ง ์๋ ๊ฒ์ผ๋ก ์ค์ ํด ์ฃผ๋ ๊ฒ์ด ์ข๋ค.
    ![image](../../assets/images/blog_img/2_utterances/utterances_issueMapping_setting.png)

3. Theme ์ค์ 
    * utterances์ `ํ๋ง`๋ฅผ ์ ํํ๋ค.
    > โ ํ๋ง๋ ์ ํํ ์ต์์ ๋ฐ๋ผ์ ํ์ฌ ํ์ด์ง ์์์ด ๋ณํ๊ธฐ ๋๋ฌธ์ ์ํ๋ ํ๋ง๋ก ์ ํํ๋ค.

    ![image](../../assets/images/blog_img/2_utterances/utterances_theme_setting.png)


## โข. minimal-mistakes์ Utterances ์ค์ 
1. `minimal-mistakes ํ๋ง`๋ `_config.yml`์ ๋ช ๊ฐ์ง ์ค์ ๋ง ํด์ฃผ๋ฉด ๋ฐ๋ก ์ ์ฉ๋๋ค. ๊ทธ ์ธ๋ `Enable Utterances`๋ฅผ ๋ณต์ฌํด์ ์ฝ๋์ ์ถ๊ฐํ๋ฉด๋๋ค.
    > minimal-mistakes ํ๋ง utterances ์ค์  ๋ฐฉ๋ฒ  (`_config.yml`)
    > - `repository` : "๋๊ธ์ ๊ด๋ฆฌํ  ์ ์ฅ์(username/repo-name)"  
    > - comments-`provider` : "utterances"
    > - comments-utterances-`theme` : "์ ํํ ํ๋ง"
    > - comments-utterances-`issue_term` :  "์ ํํ ์ด์ ๋งตํ"

    ![image](../../assets/images/blog_img/2_utterances/mm_setting.png)
    

## โฃ. ๊ฒฐ๊ณผ
1. ์์ ๊ณผ์ ์ ์๋ฃ ํ ๊นํ๋ธ์ `push` ํ ํ ๋ธ๋ก๊ทธ๋ฅผ ํ์ธํ๋ค.
2. ๋ค์๊ณผ ๊ฐ์ด ์ ์ฉ๋ ๊ฒ์ ํ์ธ ํ  ์ ์๋ค.
   ![image](../../assets/images/blog_img/2_utterances/comments_result.png)
> โ ์ ์ฉ ๋ ๊ฒฐ๊ณผ๋ ๋ก์ปฌ ์๋ฒ๋ก ์ ์ํ์ ๋ ๋ํ๋์ง ์๋๋ค.

<br>

# ๐. ์ฐธ๊ณ 

* [utterances๋ฅผ ๋ธ๋ก๊ทธ ๋๊ธ๋ก ์ ์ฉํ๊ธฐ \| ์์ํ ํธ๋ ์ด๋](https://baek.dev/post/4/)  
* [[Github] ๋ธ๋ก๊ทธ์ ๋๊ธ ๊ธฐ๋ฅ ์ถ๊ฐํ๊ธฐ (ft.Utterances)](https://outstanding1301.github.io/dev/2021/01/07/utterances/)  
* [[Github ๋ธ๋ก๊ทธ] utterances ์ผ๋ก ๋๊ธ ๊ธฐ๋ฅ ๋ง๋ค๊ธฐ (+ disqus ๋น์ถํ๊ธฐ)](https://ansohxxn.github.io/blog/utterances/#4-github-%EB%A9%94%EC%9D%BC-%EC%95%8C%EB%A6%BC-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0-%EB%A9%94%EC%9D%BC%EB%A1%9C-%EB%8C%93%EA%B8%80-%EC%95%8C%EB%A6%BC-%EB%B0%9B%EA%B8%B0)  