---
layout: post
title: SSLHandshakeException 에러 해결
tags: [java]
comments: true
---

## 운영서버에서 api 호출시 SSLHandshakeException 문제 발생  

`javax.net.ssl.SSLHandshakeException: Remote host closed connection during handshake`

1. 현재 프로젝트에서 운영서버는 jdk1.7버전을 사용중  
2. 로컬 서버에서는 jdk1.8 버전을 사용중이기때문에 초기 문제 발견을 못한것으로 추측  
3. 로컬 서버에서 jdk1.7 버전으로 실행한 결과 에러가 발생하지 않음  
4. 로컬 서버는 운영서버와는 다르게 http 사용중  

## 에러 검색 결과  
HTTPS통신시 server와 client 간에 사용하는 TLS 버전이 맞지 않기 때문에 발생하는 문제라고 함.

현재 api서버측은 TLS 1.2 버전만 사용중이다. ->  
JDK 1.8 부터 TLS 1.2 가 기본 버전이므로 문제가 없지만, 이전 JDK 버전을 사용하는 client의 경우는 예외가 발생  
(운영중인 프로젝트의 경우 JDK1.7 버전으로 default 프로토콜 버전 TLS 1.0)  

## 해결방법
JDK 1.8로 버전업하는 방식이 가장 간단하지만 현재상황에서 버전업하기에 어려움이 있기때문에  
~~해당 코드를 url connection 이전에 작성하여 문제 해결~~  
프레임워크 초기화 클래스에서 setting해준다.  
~~~java
System.setProperty("https.protocols", "TLSv1,TLSv1.1,TLSv1.2");
~~~

다음과 같이 JVM 옵션을 사용하는 방식도 가능  
(Tomcat 서버의 bin/catalina.sh 의 CATALINA_OPTS에 다음 옵션 추가)  
~~~sh
-Dhttps.protocols=TLSv1,TLSv1.1,TLSv1.2
~~~

## 참고
[Java SSL/TLS 지원 버전과 디폴트 프로토콜 변경하기](https://sarc.io/index.php/java/1306-ssl-tls-and-java-support-default)

[SSL/TLS Handshake 실패오류를 수정하는 방법](https://blog.naver.com/PostView.nhn?blogId=ucert&logNo=222083739966)