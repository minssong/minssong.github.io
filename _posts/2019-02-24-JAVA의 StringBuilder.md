---
layout: post
title:  "JAVA의 StringBuilder"
date:   2019-02-24 16:00:05
categories: [Basecamp]
comments: true
---

### String builder
CLI를 통해 메일 목록을 조회하는 미션을 진행하던 중 JAVA의 놀라운 StringBuilder에 대해 알게되었다. 각 메일 목록의 메일 리스트를 보여주는 기존 코드는 아래와 같았다.
![image](https://user-images.githubusercontent.com/28076434/53295941-5b79fc80-3849-11e9-8122-86586c48c8ac.png)

때마침 지나가시던 백OO 이사님께서(공개적인 블로그에 실명 거론을 해도 될지 몰라서ㅠㅠ,,) 이를 보시고 문제점을 제기해주시며 StringBuilder에 대해 말씀해주셨다.
이런 방식으로 + 연산자를 통해 계속 문자열을 더해 나가게 되면 상당한 메모리 낭비가 발생하게 된다. 간단한 예시로 아래와 같은 문자열 생성 코드가 있다고 생각해보자.
![image](https://user-images.githubusercontent.com/28076434/53295996-1bffe000-384a-11e9-9822-f67703a187eb.png)

이 경우, String을 사용하여 DEF를 더하게 되면 기존에 선언된 ABC에 DEF를 더하는 것이 아니라 새로운 메모리에 ABCDEF가 생겨나고 기존에 ABC는 GC(Garbage collection)에 의해 삭제가 된다.
따라서 String를 이용하여 지속적으로 문자열을 바꾸는 것은 많은 메모리를 소모하게 된다.
![image](https://user-images.githubusercontent.com/28076434/53295981-fe327b00-3849-11e9-9956-8358aebcd573.png)

이를 해결하기 위해 StringBuffer와 StringBuilder를 사용할 수 있다.

* StringBuffer/StringBuilder의 차이
StringBuffer/StringBuffer는 String 과 다르게 동작한다. 문자열 연산 등으로 기존 객체에 공간이 부족하게 되는 경우, 기존의 버퍼 크기를 늘리며 유연하게 동작한다. StringBuffer와 StringBuilder 클래스가 제공하는 메서드는 서로 동일하다. 그렇다면 두 클래스의 차이는 바로 동기화를 지원하느냐 안하느냐의 차이이다.

1. StringBuffer 클래스 
: 스레드에 안전하게 설계 (ThreadSafe)<br>
여러개의 스레드에서 하나의  StringBuffer 객체를 처리해도 전혀 문제가 되지 않는다.

2. StringBuilder 클래스 
: 단일 스레드에서의 안전성망을 보장<br>
여러개의 스레드에서 하나의 StringBuilder 객체를 처리하면 문제가 발생한다.

* StringBuilder의 적용
아래와 같은 StringBuilder의 사용으로 문자열을 계속 append 해주면서 메모리 낭비 문제를 막을 수 있었다.
![image](https://user-images.githubusercontent.com/28076434/53296053-240c4f80-384b-11e9-8706-95d57ccc5863.png)

### 후기
JAVA에 내장되어있는 많은 라이브러리를 접할수록 그 편리성을 몸소 느끼고 있다!!!!! 주로 사용하던 언어는 원래 JAVA가 아니였지만, 조만간 JAVA로 넘어갈 것 같다 :)
<!--more-->

