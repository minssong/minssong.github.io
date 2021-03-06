---
layout: post
title:  "Spring에서 AOP(Aspect Oriented Programing)란? (+추가 : CSS 깨지는 오류 해결)       "
date:   2019-02-24 17:50:23
categories: [Basecamp]
comments: true
---

이번 DB sharding 미션에서 우리 TF는 AbstractRoutingDataSource를 사용하여 구현하였다. Routing Key를 사용하여 Mapper에서 원하는 DB에 접근하도록 하기 위해 'RoutingMapperAspect' class를 아래와 같이 생성하였다. 

```java
@Aspect
@Configuration
public class RoutingMapperAspect {

	@Around("execution(* com.nhnent.rookie.dabada.mapper..*(..))")
	public Object aroundTargetMethod(ProceedingJoinPoint thisJoinPoint) {
		MethodSignature methodSignature = (MethodSignature) thisJoinPoint.getSignature();
		Class<?> mapperInterface = methodSignature.getDeclaringType();
		Method method = methodSignature.getMethod();
		Parameter[] parameters = method.getParameters();
		Object[] args = thisJoinPoint.getArgs();

		RoutingMapper routingMapper = mapperInterface.getDeclaredAnnotation(RoutingMapper.class);
		
		if (routingMapper != null) {
            String Id = findRoutingKey(parameters, args);
            Integer index = determineRoutingDataSourceIndex(Id);
            log.info(Id);
            log.info(extractMailDataSoruce(index).toString());
            log.debug("index: {}", index);
            
            MailDataSourceContextHolder.set(extractMailDataSoruce(index));
        }

        try {
            return thisJoinPoint.proceed();
        } catch (Throwable throwable) {
            throw new RuntimeException(throwable);
        } finally {
        	MailDataSourceContextHolder.clear();
        }
		
	}

 ...

   
}
```
이때 적용되는 개념이 바로 AOP(Aspect Oriented Programing)이다. 알고보니 토비의 스프링 8장에도 이에 대한 개념이 소개되어있다!!!!!

### AOP
AOP(Aspect Oriented Programing)는 '관점 지향 프로그래밍'으로 어플리케이션의 핵심적인 기능에서 부가적인 기능을 분리하여 Aspect라는 모듈로 생성 및 설계하고 개발하는 방법을 말한다. 쉽게 말하자면 공통기능을 모든 모듈에 적용하기 위한 방법으로 핵심 기능과 공통 기능을 분리시켜두고 공통 기능을 필요로 하는 핵심 기능들에서 사용하는 방식이다.
스프링이 제공하는 AOP는 프록시를 이용한다. 프록시를 통해 타겟 오브젝트의 메소드가 호출될 경우, 프록시가 제어를 가로채고 InvocationHandler와 같은 오브젝트를 통해 타겟 메소드의 실행 전 후로 부가적인 기능을 실행한다.

![image](https://user-images.githubusercontent.com/28076434/53297085-aa309200-385b-11e9-843c-a65a5d669f18.png)


## AOP 용어
* Target<br>
핵심 로직을 구현하는 클래스로 충고를 받는 클래스를 대상(target)이라고 한다. 대상은 여러분이 작성한 클래스는 물론, 별도의 기능을 추가하고자 하는 써드파티 클래스가 될 수 있다.
* Aspect (= Advice + Pointcut)<br>
여러 객체에 공통으로 적용되는 공통 관심 사항을 Aspect라고 한다. 트랜잭션이나 보안 등이 Aspect의 좋은 예이다. Aspect는 AOP의 중심단위, Advice와 Pointcut을 합친 것이다.

* Advice <br>
클래스에 조인 포인트에 삽입되어져 동작(적용할 기능)할 수 있는 코드를 'Advice'라 한다. Advice를 어디에서 위빙하는지는 뒤에 나오는 PointCut이라는 단위로 정의한다.
advice는 Pointcut에서 지정한 Jointpoint에서 실행되어야하는 코드이다.<br>
cp.) 스프링의 Advice 타입
1. Around Advice: Joinpoint 앞과 뒤에서 실행되는 Adcvice
2. Before Advice: Joinpoint 앞에서 실행되는 Advice
3. After Returning Advice: Jointpoint 메서드 호출이 정상적으로 종료된 뒤에 실행되는 Advice
4. After Throwing Advice: 예외가 던져질 때 실행되는 Advice
5. Introduction:  클래스에 인터페이스와 구현을 추가하는 특수한 Advice

* JoinPoint<br>
Advice를 적용 가능한 지점을 의미한다. 클래스의 인스턴스 생성 시점', '메소드 호출 시점', '예외 발생 시점'과 같이 어플리케이션을 실행할 때 특정 작업이 시작되는 시점을 'JoinPoint'라고 한다.
실행시의 처리 플로우에서 Advice를 위빙하는 포인트를 JointPoint라고 한다. 구체적으로는 메서드 호출이나 예외발생이라는 포인트를 Joinpoint라고 한다.

* Pointcut<br>
Joinpoint의 부분 집합으로서 실제로 Advice가 적용되는 Jointpoint를 나타낸다. 스프링에서는 정규 표현식이나 AspectJ 문법을 이용하여 Pointcut을 정의할 수 있다.
여러 개의 Joinpoint를 하나로 결합한 것을 Pointcut이라 한다.

1. @annotation : 특정 어노테이션을 찾는 JoinPoint 정의
2. execution () : 가장 강력한 지시자로, 리턴, 타입, 메서드, 패키지 등 조합하여 정교한 PointCut 정의
3. within() : 타입 패턴을 이용하여 JoinPoint 정의
4. this : 빈 오브젝트 타입에 JoinPoint 정의

* Advisor<br>
 Advisor와 Pointcut을 하나로 묶어 다루는 것을 Advisor라고 한다. 단순한 형태의 Aspect라고 볼 수 있다. Advisor는 스프링 AOP에만 있는 것인데, 관점 지향에서 관점을 나타내는 개념이라고 할 수 있다.

* Proxy<br>
대상 객체에 Advice가 적용된 후 생성된 객체

## CSS 깨지는 오류 해결 및 후기
DB sharding을 메일 읽기, 파일 다운로드에 적용하고 난 후로 Dev와 Real에 배포했을 때, css가 깨지는 오류가 나서 매우 난감했었다. ㅠㅠ Project clean과 Maven clean을 해도 해결되지 않았다. 심지어 기존 브랜치로 돌아가 새로 코드를 다 짰는데도 동일한 오류가 발생했다..!! 그때 문득, 책임님께서 "여기는 css 파일이 static 밑에 없네?" 라고 하셨던 말씀이 생각나 static 폴더 하에 파일들을 옮겼더니 놀랍게도 문제가 해결되었다. 아마 경로 문제 때문에 css 파일을 아예 읽어들이지 못했던 것 같다.. 이렇게 단순히 해결되다니 허무했지만 덕분에 폴더와 파일의 구조도 중요하다는 사실을 깨달았다. 
static 폴더 하에 다음과 같이 css, imgs, js 파일을 몽땅 옮겼다!!!! 쨋든 엄청난 오류가 해결되서 정말 다행이다<3


![image](https://user-images.githubusercontent.com/28076434/53310851-e7476380-38f1-11e9-8508-9196feac177d.png)

또, 앞서 설명한 AOP처럼 어려운 개념들을 코드에 잘 적용시키려면 많은 공부가 필요한 것 같다. 토비의 스프링을 정말 열심히 읽어야겠다..!!

### 참고
1. http://blog.naver.com/PostView.nhn?blogId=kbh3983&logNo=220836425242
2. https://devbox.tistory.com/entry/spring-AOP-%EC%9A%A9%EC%96%B4-%EC%84%A4%EB%AA%85
