---
title: "2.2 웹과 HTTP"
excerpt: "2.2 웹과 HTTP"
categories: ['Computer Network']
tags:
  - computer
  - network

toc: true
toc_sticky: true
use_math: true
 
date: 2024-03-26
last_modified_at: 2024-03-26
---
## 1. HTTP 개요

&nbsp;&nbsp;HTTP(HyperText Transfer Protocol)는 웹의 애플리키에션 계층 프로토콜로 웹의 중심이 된다. HTTP는 클라이언트 프로그램과 서버 프로그램으로 구현되고 서로 HTTP 메시지를 교환하여 통신한다. HTTP는 메시지의 구조 및 클라인트와 서버의 메시지 교환에 대해 정의한다.

* **웹 페이지**(Web Page, 문서)
  * 객체로 구성된다.
* **객체**(object)
  * 단일 URL로 지정할 수 있는 하나의 파일
  * HTML 파일, JPEG 이미지, 자바스크립트, CSS 등이 해당된다.
* **웹 브라우저**(Web browser)
  * 네이버 웨일, 크롬...
  * HTTP의 클라이언트 측을 구현한다.
  * 웹 페이지를 보여주고 여러 가지 인터넷 탐색, 검색과 구성 특성을 제공
* **웹 서버**(Web server)
  * URL로 각각을 지정할 수 있는 웹 객체를 가지고 있다.
  * 아파치, 마이크로소프트 인터넷 인포메이션 서버 등...

&nbsp;&nbsp;HTTP는 웹 클라이언트가 웹 서버에게 웹 페이지를 요청하는 방법, 서버가 클라이언트로 웹 페이지를 전송하는 방법을 정의한다. HTTP는 **TCP**(연결지향)를 전송 프로토콜로 사용한다. 

  **1**. HTTP 클라이언트는 서버에 TCP 연결 시작. 
  **2**. 브라우저와 서버 프로세스는 소켓 인터페이스를 통해 TCP로 접속.

&nbsp;&nbsp;서버가 클라이언트에게 요청 파일을 보낼 때, 서버는 클라이언트에 관한 어떠한 상태 정보를 저장하지 않는다. HTTP 서버는 클라이언트에 대한 정보를 유지하지 않으므로, HTTP를 **비상태 프로토콜**(stateless protocol)이라고 한다.


## 2. 비지속 연결과 지속 연결

* **비지속 연결**(non-persistent connection)이란 클라이언트와 서버 간의 각 요청과 응답마다 새로운 TCP 연결을 설정하는 것이다.
* **지속 연결**(persistent connection)이란 하나의 TCP 연결을 유지하고 여러 요청과 응답을 전달하는 것이다.


### 비지속 연결 HTTP

&nbsp;&nbsp;웹 페이지를 서버에서 클라이언트로 전송하는 단계를 살펴보자. 페이지가 기본 HTML 파일과 10개의 WEBP 이미지로 구성되고, 이 11개의 객체가 같은 서버에 있다고 가정하자. 기본 HTML 파일의 URL은 다음과 같다.

`https://www.someSchool.edu/someDepartment/home.index`

1. HTTP 클라이언트는 HTTP의 기본 포트 번호 80을 통해 `www.someSchool.edu` 서버로 TCP 연결을 시도. TCP 연결과 관련하여 클라이언트와 서버에 각각 소켓 존재.
2. HTTP 클라이언트는 1단계에서 설정된 TCP 연결 소켓을 통해 서버로 `/someDepartment/home.index` 경로 이름을 포함하는 HTTP 요청 메시지를 보냄. 
3. HTTP 서버는 1단계에서 설정된 연결 소켓을 통해 요청 메시지 받음. 저장장치에서 `/someDepartment/home.index` 객체를 추출. HTTP 응답 메시지에 객체 캡슐화. 소켓을 통해 클라이언트로 전달.
4. HTTP 서버는 TCP에게 연결을 끊으라고 함.
5. HTTP 클라이언트가 응답 메시지를 받으면, TCP 연결 중단. 

* **RTT**(round-trip time)
  * 패킷이 클라이언트로부터 서버까지 갔다가 다시 클라이언트로 되돌아오는 데 걸리는 시간
  * 패킷 전파 지연, 중간 라우터와 스위치에서의 패킷 큐잉 지연, 패킷 처리 지연 등을 포함한다.

&nbsp;&nbsp;사용자가 하이퍼링크를 클릭하면 브라우저가 웹 서버 사이에서 TCP 연결을 시도한다. 클라이언트가 작은 TCP 메시지를 서버로 보내고, 서버는 작은 메시지로 응답하고(RTT 계산함), 마지막으로 클라이언트가 HTTP 요청 메시지를 TCP 연결로 보내는 **세 방향 핸드셰이크**(three-way handshake)를 한다. 요청 메시지가 서버에 도착하면 서버는 HTML 파일을 TCP 연결로 보내고 이는 또 하나의 RTT가 계산된다. 따라서 총 응답 시간은 2 RTT와 HTML 파일을 서버가 전송하는 데 걸리는 시간을 더한 것이다.

&nbsp;&nbsp;비지속 연결은 각 요청마다 새로운 연결이 설정되고, 해당 요청에 대한 응답 후에는 연결이 끊어진다. TCP 버퍼가 할당되어야 하고 TCP 변수들이 클라이언트와 서버 양쪽에서 유지되어야 한다. 따라서 수많은 클라이언트들의 요청을 처리하는 웹 서버에 심각한 부담을 줄 수 있다. 또한 각 연결은 2 RTT를 필요로 한다.


### 지속 연결 HTTP

&nbsp;&nbsp;지속 연결 HTTP에서 서버는 응답을 보낸 후에 TCP 연결을 유지한다. 전체 웹 페이지를 한번의 TCP 연결을 통해 보낼 수 있다. 객체에 대한 요구는 진행 중인 요구에 대한 응답을 기다리지 않고 연속해서 만들어 질 수 있다(파이프라이닝(pipelining)). 서버 또한 요청된 객체를 연속해서 보낸다.


## 3. HTTP 메시지 포맷

&nbsp;&nbsp;HTTP 명세서는 HTTP 메시지 포맷을 정의한다. HTTP 메시지에는 **요청 메시지**와 **응답 메시지** 두가지가 존재한다.


### HTTP 요청 메시지

```
GET /somedir/page.html HTTP/1.1
Host: www.someschool.edu
Connection: close
User-agent: Mozilla/5.0
Accept-language: fr
```
* 일반 ASCII 텍스트로 쓰여져 있다.
* 다섯 줄 이상이거나 한 줄로 구성될 수 있고 각 줄은 CR(carriage return, \r)과 LF(line feed, \n)로 구별된다.
* HTTP 요청 메시지의 첫 줄은 **요청 라인**(request line)이라 부르고, 이후의 줄들은 **헤더 라인**(header line)이라고 한다.


#### 요청 라인

```
GET /somedir/page.html HTTP/1.1
```
요청 라인에는 방식(method) 필드, URL 필드, HTTP 버전 필드 3개의 필드를 갖는다. GET 방식은 브라우저가 URL 필드로 식별되는 객체를 요청할 때 사용된다.


#### 헤더 라인

```
Host: www.someschool.edu
```
객체가 존재하는 호스트를 명시하고 있다. 이 정보는 웹 프록시 캐시에서 필요로 한다.

```
Connection: close
```
브라우저는 서버에게 지속 연결 사용을 원하지 않는다는 것을 말하고 있다. 다시 말해 브라우저는 서버가 요청 객체를 보낸 후에 연결을 끊기를 원한다.

```
User-agent: Mozilla/5.0
```
사용자 에이전트, 서버에게 요청을 하는 브라우저 타입을 명시하고 있다. 위 예에서 Mozilla/5.0은 파이어 폭스 브라우저이다. 서버가 같은 객체에 대한 다른 버전을 다른 타입의 사용자 에이전트에게 보낼 수 있으므로 유용하다.

```
Accept-language: fr
```
객체의 프랑스어 버전을 원하고 있음을 나타낸다. 이 헤더가 존재하지 않는다면 서버는 기본 버전을 보낸다. HTTP에서 사용 가능한 많은 콘텐츠 협상 헤더 중 하나이다.


### HTTP 응답 메시지

```
HTTP/1.1 200 OK
Connection: close
Date: Tue, 18 Aug 2015 15:44:04 GET
Server: Apache/2.2.3 (CentOs)
Last-Modified: Tue, 18 Aug 2015 15:11:03 GMT
Content-Length: 6821
Content-Type: text/html
```
* **상태 라인**(status line), 6개의 **헤더 라인**, **개체 몸체**의 3개의 섹션으로 이루어져있다.
* 상태 라인은 프로토콜 **버전 필드**, **상태 코드**, **해당 상태 메시지**로 3개의 필드를 갖는다.
* 개체 몸체는 요청 객체를 포함한다.

```
HTTP/1.1 200 OK - (버전) / (상태 코드) (문장)
```
**상태 코드**
* 200 OK 
  * 요청이 성공했고, 정보가 응답으로 보내졌다.
* 301 Moved Permanently
  * 요청한 리소스가 새로운 위치로 영구적으로 이동되었다. 새로운 URL은 응답 메시지의 Location 헤더에 나와있다.클라이언트 소프트웨어는 자동으로 이 새로운 URL을 추출한다.
* 400 Bad Request
  * 서버가 요청을 이해할 수 없다는 오류 코드
* 404 Not Found
  * 요청 문서가 서버에 존재하지 않는다.
* 505 HTTP Version Not Supported
  * 요청 HTTP 프로토콜 버전을 서버가 지원하지 않는다.
```
Connection: close
```
클라이언트에게 메시지를 보낸 후 TCP 연결을 닫는데 사용된다.

```
Date: Tue, 18 Aug 2015 15:44:04 GET
```
HTTP 응답이 서버에 의해 생성되고 보낸 날짜와 시간을 나타낸다. 서버가 파일 시스템으로부터 객체를 추출하고 응답 메시지에 그 객체를 삽입하여 응답 메시지를 보낸 시간을 의미한다.

```
Server: Apache/2.2.3 (CentOs)
```
메시지가 아파치 웹 서버에 의해 만들어졌음을 나타낸다.

```
Last-Modified: Tue, 18 Aug 2015 15:11:03 GMT
```
객체가 생성되거나 마지막으로 수정된 시간과 날짜를 나타낸다. 이 헤더는 객체를 로컬 클라이언트와 네트워크 캐시 서버에 캐싱하는 데 매우 중요하다.

```
Content-Length: 6821
```
송신되는 객체의 바이트 수를 나타낸다.

```
Content-Type: text/html
```
개체 몸체 내부의 객체가 HTML 텍스트인 것을 나타낸다. 객체 타입은 파일 확장자로 나타내는 것이 아닌 공식적으로 Content-Type: 헤더로 나타낸다.


## 4. 사용자와 서버 간의 상호작용: 쿠키

&nbsp;&nbsp;HTTP 서버는 비지속 연결 방식을 사용한다. 이것은 서버 설계를 간편하게 하고 동시에 수많은 TCP 연결을 다룰 수 있도록 하였다. 한편 서버가 사용자 접속을 제한하거나 사용자에 따라 콘텐츠를 제공하기 위해 웹사이트가 사용자를 확인할 때가 있다. 이를 위해 HTTP는 **쿠키**(cookie)를 사용한다. 쿠키는 사이트가 사용자를 추적하게 해준다.

### 쿠키가 가진 네가지 요소
* HTTP 응답 메시지 쿠키 헤더 라인
* HTTP 요청 메시지 쿠키 헤더 라인
* 사용자의 브라우저에 사용자 종단 시스템과 관리를 지속시키는 쿠키 파일
* 웹사이트의 백엔드 데이터베이스


## 5. 웹 캐싱

&nbsp;&nbsp;**웹 캐시**(Web cache, 프록시 서버(proxy server))는 기점 웹 서버(origin Web server)를 대신하여 HTTP 요구를 충족시키는 네트워크 개체다. 웹 캐시는 자체의 저장 디스크를 갖고 있어 최근 호출된 객체의 사본을 저장한다. 브라우저의 요청은 기점 웹 서버가 아닌 웹 캐시에 가장 먼저 보내진다.

1. 브라우저는 웹 캐시와 TCP 연결을 설정하고 웹 캐시에 있는 객체에 대한 HTTP 요청을 보낸다.
2. 웹 캐시는 객체의 복사본이 자기에게 저장되어 있는지 확인한다. 저장되어 있다면 웹 캐시는 클라이언트 브라우저로 HTTP 응답 메시지와 함께 객체를 전송한다.
3. 웹 캐시가 객체를 가지고 있지 않다면, 웹 캐시는 기점 서버와 TCP 연결을 설정한다. 그 후 웹 캐시는 기점 서버로 객체에 대한 HTTP 요청을 보낸다. 기점 서버는 웹 캐시로 HTTP 응답 메시지와 함께 객체를 보낸다.
4. 웹 캐시가 웹 서버로부터 받은 객체를 자신의 로컬 저장장치에 복사하고 클라이언트 브라우저에 HTTP 응답 메시지와 함께 객체의 사본을 보낸다.

&nbsp;&nbsp;위의 예시에서 살펴보면 웹 캐시는 서버이면서 클라이언트의 역할을 한다.브라우저로부터 요구를 받고 응답을 보낼 때는 서버, 기점 서버에게 요구를 보내거나 응답을 받을 때에는 클라이언트가 된다. 일반적으로 웹 케시는 ISP가 구입하고 설치한다.


### 웹 캐싱의 장점

* 웹 캐시는 클라이언트의 요구 처리에 대한 응답 시간을 줄일 수 있다.
* 웹 캐시는 한 기관에서 인터넷으로의 접속하는 링크상의 웹 트래픽을 대폭 줄일 수 있다. 이는 비용 절감의 효과도 있다.
* 인터넷 전체의 웹 트래픽을 실질적으로 줄임으로써 모든 애플리케이션을 위한 성능을 개선한다.


### 조건부 GET

&nbsp;&nbsp;웹 캐싱이 사용자가 느끼는 응답 시간을 줄일 수 있지만, 캐시 내부에 있는 객체의 복사본이 새것이 아닐 수 있다는 문제점이 있다. 즉 복사본이 클라이언트에 캐싱된 이후에 웹 서버에 있는 객체가 갱신되었을 수도 있다. 이러한 문제를 해결하기 위한 것이 **조건부 GET**(conditional GET)이다. 이는 클라이언트가 브라우저로 전달되는 모든 객체가 최신의 것임을 확인하며 캐싱을 가능하게 한다.

**1.** 
```
GET /fruit/kiwi.gif HTTP/1.1
Host: www.exotiquecuisine.com
```
&nbsp;&nbsp;프록시 캐시(프록시 서버)는 브라우저의 요청 메시지를 웹 서버로 보낸다.

**2.**
```
HTTP/1.1 200 OK
Date: ⋯
Server: ⋯
Last-Modified: Wed, 9 Sep 2015 09:23:24
Content-Type: ⋯

(데이터 데이터 데이터 ⋯)
```
&nbsp;&nbsp;웹 서버는 캐시에게 객체와 **마지막으로 수정된 날짜**가 포함된 응답 메시지를 보낸다. 캐시는 객체와 마지막으로 수정된 날짜를 함께 저장한다.

**3.**
&nbsp;&nbsp;일주일 후 다른 브라우저가 같은 객체를 캐시에게 요청하면 일주일 동안 객체가 수정되었는지 확인하기 위해 조건부 GET으로 갱신 여부를 조사한다.

```
GET /fruit/kiwi.gif HTTP/1.1
Host: www.exotiquecuisine.com
If-modified-since: Wed, 9 Sep 2015 09:23:24
```

&nbsp;&nbsp;여기서 일주일 전에 서버가 보낸 Last-Modified 헤더 라인의 값과 이번에 캐시가 보낸 If-modified-since 헤더 라인의 값이 같다는 사실에 주의하자. 이 조건부 GET은 서버에게 그 객체가 명시된 날짜 이후 수정된 경우에만 그 객체를 보낼 것을 말하고 있다.

**4.**

```
HTTP/1.1 304 Not Modified
Date: ⋯
Server: ⋯
```
&nbsp;&nbsp;웹 서버는 클라이언트에게 위의 내용과 같은 응답 메시지를 보낸다. 조건부 GET에 대한 응답으로 메시지를 보내지만 응답 메시지에 요청된 객체는 포함하지 않는다. 요청 메시지를 포함하는 것은 대역폭을 낭비하는 것이고, 요청하는 객체가 크다면 사용자가 느끼는 응답 시간이 증가된다. 

&nbsp;&nbsp;마지막 응답 메시지는 **304 Not Modified** 상태 라인을 갖고 있으며, 이는 클라이언트에게 요청 객체의 캐싱된 복사본을 사용하라는 것을 의미한다.


## 6. HTTP/2

* **HTTP/1.1의 단점**
  * 웹 페이지당 하나의 TCP 연결을 가지므로 웹 페이지에 있는 모든 객체를 보내면 **HOL**(Head of Line) **블로킹** 문제가 발생할 수 있다.
  * HOL 블로킹이란 링크를 통과하는 데 오래 걸리는 비디오 클립과 같은 객체로 인해 작은 객체들이 링크를 지나가지 못하는 현상을 말한다.
  * 이를 해결하기 위해 여러개의 병렬 TCP 연결을 열었다. 하지만 이는 서버에서 열고 유지되는 데 필요한 소켓의 수가 많아지고 TCP 혼잡 제어를 어렵게 하는 단점이 있다.
* **HTTP/2**
  * 이러한 HTTP/1.1의 단점을 해결하고자 병렬 TCP 연결의 수를 줄이거나 제거하는데에 목표를 두고 있다.
  * 서버에서 열고 유지되는 데 필요한 소켓의 수를 줄이고 목표한 대로 TCP 혼잡 제어를 구현할 수 있다.


### HTTP/2 프레이밍

* HTTP 메시지를 독립된 프레임들로 쪼개고 인터리빙하고 반대편 사이트에서 재조립한다.


### 메시지 우선순위화 및 서버 푸싱

* 개발자들로 하여금 요청들의 상대적 우선 순위를 조정할 수 있게 함으로써 애플리케이션의 성능을 최적화할 수 있게 한다.
* 서버로 하여금 특정 클라이언트 요청에 대해 여러 개의 응답을 보낼 수 있게 한다.