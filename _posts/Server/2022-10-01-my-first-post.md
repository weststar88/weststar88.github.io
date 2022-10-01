---
layout: post
title:  "내 첫 번째 게시물"
categories: update
tags: [test]
---

## 첫 글
- 글은 _posts폴더에 markdown 파일로 작성
- 레이아웃 설정 가능. _layout폴더에 샘플 레이아웃이 존재함 (post.html, article.html, framework.html 등등)
- 최상위폴더의 about.html을 수정하여 about 메뉴를 수정할 수 있다
- home 메뉴의 제목은 _data 폴더의 defaults.yml 파일에서 수정가능
- gemfile, gemfile.lock같은것들은 루비 관련 파일인듯
- _site는 내가 추가하는 파일들을가지고 jekyll이 만들어낸 실제 웹페이지인 것 같다.
- 로컬에서 사이트 실행해보고 싶을땐 cmd로 블로그 레포지토리 경로에 진입하여 bundle exec jekyll serve --trace 명령어 실행

## 내 유튜브 링크 걸기
메이플 무지성강화 영상
![](//https://youtu.be/M7CACLkf8IA)

## 코드 넣기
{% highlight c# %}
public void Test()
{
	Console.WriteLine("Hello World!");
}
{% endhighlight %}
