---
layout: page
title: "Linux Web Server 구성하기"
description: "Linux 서버에서 Web Server 구성에 대해 알아보겠습니다."
tags: [Linux Web Server]
comments: true
published: true
categories: Linux
---









### [ Web Server ]

- Web Server란 클라이언트가 Web Browser를 이용하여 웹페이지를 요청할 경우, 웹페이지 데이터를 클라이언트 Web Browser에게 전달하는 역할을 수행하는 서버를 말한다. 

\# Web Browser ( 네이버 웨일, 구글 크롬, MS 익스플로러 엣지..)

 

#### @ Web Server 동작 과정

1. 클라이언트 Web Browser에서 Web Server로 데이터 요청

2. 클라이언트는 IP주소가 아닌 Web Server의 Domain Name 처리를 진행하므로 DNS 서버를 통해 Web Server의 Domain Name을 Web Server의 IP 주소를 변환한다.

3. 변환한 IP 주소를 이용하여 Web Server 80번 포트(HTTP 프로토콜이 사용하는 포트)를 이용하여 TCP 연결을 진행한다.

\# (세션 연결 : TCP 3way – handshake) --> 쿠키를 사용한 세션 유지기능을 구현할 수 있어야한다.

4. 세션 연결 완료 후 Web Server는 DocumentRoot로 정의된 디렉토리에 저장되어있는 Main page(Code 형태)를 Client에 전송한다. 

5. 클라이언트는 서버로부터 전송 받은 Main Page(Code 형태; html. css 파일)를 Web Browser가 이를 해석하여 클라이언트 화면에 출력한다.

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-01.png?raw=true)

\# HTTP Request : 클라이언트가 보낸 데이터 요청 메세지

#### @ HTTP Request 메소드

**GET : 웹페이지 데이터 정보 요청**

ex) 웹툰 페이지 열기

**POST: 클라이언트가 가지고 있는 웹페이지를 서버에 데이터 전송** → 서버는 클라이언트로부터 받은 데이터를 DB에 저장 

ex) 회원가입 정보 기입, 첨부파일 업로드 

**PUT : 클라이언트가 서버 측에 데이터 변경을 요청** (Apache는 기본값으로 허용하지 않음)

\# 대신에 POST 메소드를 사용 (DB에 있는 정보에 덮어씌운다.)

**DELETE : 데이터 삭제** (Apache는 기본값으로 허용하지 않음)

**OPTIONS : 메소드 지원 정보 요청**

 

\# HTTP Response : 클라이언트의 데이터 요청에 대한 서버 측의 응답 메세지

#### @  응답 코드 

1xx : information ( 임시 응답, 지속적인 통신진행) 

**2xx : Success** ( 클라이언트의 요청을 성공적으로 처리, 200 OK) 

**3xx : Redirection** ( 요청을 처리할 문서가 이동되었음을 알림; 301 objects moved) 

ex) 클라이언트를 http 접속 대신 https 연결을 시키기 위해 클라이언트에게 응답해준다.

**4xx : Client error** ( 클라이언트가 서버에 존재하지 않은 페이지를 요청, 404 Page Not Found)

**5xx : Server error** ( 서버에서 요청을 처리하는 과정에서 오류가 발생, 500 Internal Server Error) 

- 이런 오류를 해결하기 위해선 웹서버와 DB서버가 연결 상태를 확인하는 것이 좋다.

 

 

 

#### @ HTTP 포맷

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-02.png?raw=true)

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-03.png?raw=true)

**URL : 클라이언트가 서버에 요청하는 웹 문서의 경로**

**URI : 서버 내의 존재하는 특정 자원의 고유한 주소값** (사용자가 요구하는 요청 데이터를 변수와 변수값(인자값)을 지정해 클라이언트에 응답한다.)

ex) page=3&table=5 (key-value 형식으로 인자값을 지정해 요청처리한다.)

**\# 앵커 기능** → **웹 페이지 지정 표시기능**; 앵커 사용시 원하는 요청 페이지(동일 페이지 내)로 즉시 이동이 가능하다.

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-04.png?raw=true)

- Django를 사용시 변수 지정을 간편하게 URL를 결정할수 있다.

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-05.png?raw=true)

- 이미지 파일, 동영상 등 다수의 클라이언트 요청에도 페이지 처리가 바뀌지 않는 페이지를 정적 페이지라 한다. ( 정적페이지만 구성하는 경우는 거의 없다)

- 정적 페이지를 클라이언트에 포워딩 처리하기 위해 역할을 하는 것이 웹서버가 담당한다.

- 클라이언트의 동적컨텐츠 요청은 웹서버가 담당할 수 없기에 해당 역할을 WAS에 넘긴다.

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-06.png?raw=true)

- 클라이언트 요청에 따라 달라지는 응답을 주는 페이지를 처리하는 서버는 WAS가 된다.

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-07.png?raw=true)

- 네이버 웹툰 페이지; 특정 웹툰의 차수를 선택하는 페이지는 정적 페이지처럼 보이지만, **동적 페이지**이다.

**\# 3-tier : [proxy(로드밸런서 역할) →] WEB → WAS → DB**

**2-tier : [proxy(로드밸런서 역할) →] (WEB & WAS) → DB**

 

 

 

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-08.png?raw=true)

-  초창기 웹서비스는 WEB 서버(정적 콘텐츠만 처리)로만 처리

- 동적 콘텐츠 처리가 필요해짐에 따라 프로그래밍 언어를 통한 모듈을 추가하는 CGI 기술이 사용되었다.

- 더 많은 동적컨텐츠 처리가 필요해지자 CGI로는 한계에 부딪히고 동적 컨텐츠 처리만을 담당하는 WAS 서버가 생겨났다.

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-09.png?raw=true)

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-10.png?raw=true)

 

#### [ 실습 ] : 아파치를 통한 웹서버 구성

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-11.png?raw=true)

- 아파치 웹서버를 설치한다..

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-12.png?raw=true)

- **/etc/httpd** 는 메인 웹서버 파일 디렉토리이다.

- **conf : 메인 설정 파일 디렉토리**

- **conf.d : 부가 설정 파일 디렉토리**

- **conf.modules.d : 모듈에 필요한 추가 설정 파일 디렉토리**

- **logs 파일은 /var/log/httpd 디렉토리**에 링크 되있다.

- **modules 파일은 /usr/lib64/httpd/modules 디렉토리**에 링크되있다.

- **run 파일은 /run/httpd 디렉토리에 링크**되있으며 **웹서버 PID 정보가 담겨있다.** 

(임시 파일 tmpfs; 덤프를 생성하지 않는다.)

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-13.png?raw=true)

- 메인 설정 파일를 편집기로 열어주자



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-14.png?raw=true)

- IncludeOptional 옵션으로 conf.d/ 아래의 *.conf 파일에 메인 설정을 포함시킨다.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-15.png?raw=true)

- 웹서버에 관련된 파일은 지정 (작업)디렉토리에 지정



 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-16.png?raw=true)

- 받아들일 대역대 : 포트번호를 지정한다. 

- IP 대역대를 지정하지 않으면 모든 네트워크에서 지정 포트로 트래픽을 수용한다.

\# 관리를 위한 웹서버 환경 구성 시 특정 포트를 지정해서 트래픽 허용을 할 수 있다.

ex) Listen 80, 444 → 80번 ,444번 포트를 통해 트래픽을 허용한다.





![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-17.png?raw=true)

- 웹서버 모듈이 필요할 때 moudles.d/*.conf를 적용한다.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-18.png?raw=true)

- 웹서버 프로그램을 실행 시 해당 프로세스의 소유주를 의미한다. 

- apache 서비스 계정. apache 서비스 그룹을 뜻한다.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-19.png?raw=true)

- 웹서버에 연결하기위해 client는 세션을 맺으면서 apache 서비스 계정으로 접속하게 된다.

- ‘nobody’로 설정하는 것이 보안 상 좋지만, 웹서버 세팅 자체에 번거로움이 존재할 수 있다.

(관리 리소스가 발생할 수 있음 : 캐시 서버를 구성할 필요가 있음 --> 비용이 커짐)

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-20.png?raw=true)

- 관리자의 메일 주소; 웹서버의 이상, 오류 발생 시 해당 메일 주소로 보내게된다. 

- 물론 웹서버가 메일 시스템을 갖추고 있어야한다.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-21.png?raw=true)

- 해당 웹서버에 대한 도메인 네임, 포트 // 웹이랑 DNS랑 연동되는 옵션이 아니다.

- 웹서버에 대한 이름을 지정하는 것뿐, 반드시 필요한 설정은 아니다. 주석처리해도 동작하는데 문제되지 않음.

- **Virtual host로 추가 이름설정**할 때 사용



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-22.png?raw=true)

- **디렉토리 접근 제한에 대한 옵션( Directory <디렉토리명> )**



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-23.png?raw=true)

- 오류 페이지가 발생한 경우, 사용자가 요구하는 페이지의 목록의 원본파일을 띄워주는 옵션이다. ( 보안적으로 안좋기 때문에 불필요한 옵션이다)



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-24.png?raw=true)

- 추가 인증 파일의 이름이 .ht*로 시작하지 않으면 모두 거부한다는 의미이다.

- **AllowOverride 옵션값이 None이라면 의미가 없는 행**이다.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-25.png?raw=true)

- **DirectoryIndex : 메인페이지의 이름을 명시하는 옵션**; 주로 사용하는 이름은 index.html이다.

- 메인페이지를 추가 설정 시 옆에 추가하면된다. (우선순위는 왼쪽이 제일 높다.)

- **DirectoryIndex <메인페이지 이름>**; (.html, .php 등 다양하다.)



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-26.png?raw=true)

- 웹페이지를 생성해주고 방화벽을 설정해주자.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-27.png?raw=true)

- 설정 파일을 편집했을 때 구문상에 오류가 있는지 없는지 확인하는 명령어이다.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-28.png?raw=true)

- 서비스 시작 및 자동시작 설정을 해준다.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-29.png?raw=true)

- Win10에서 [www.itbank.com](http://www.itbank.com/)을 접속하면 Main Page가 잘 뜨는 것을 확인할 수 있다.

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-30.png?raw=true)

- 이번에는 하위 디렉토리의 페이지를 만들어보자.

- Domainroot(/var/www/html) 아래 test 디렉토리를 만들어주고 그 안에 index.html을 만들어준다.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-31.png?raw=true)

- Win10에서 하위 디렉토리 test까지 적어주면 안에 있는 index.html이 출력된다.

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-32.png?raw=true)

- 웹페이지 이름을 aaa.html로 바꿔보자. 

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-33.png?raw=true)

- Win10에서 확인하면 기존 웹페이지 대신 테스팅 페이지가 뜬다. 

- DirectoryIndex 옵션에서 index.html 만 메인페이지로 설정해놨기 때문이다.

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-34.png?raw=true)

- 하지만 도메인 옆에 aaa.html을 입력하면 해당 웹페이지 파일를 검색하게 된다.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-35.png?raw=true)

- DirectoryIndex 옵션에서 index.html 옆에 aaa.html를 추가해준다.

- index.html 가 없는 경우 aaa.html를 메인페이지로 인식하는 것이다.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-36.png?raw=true)

- Win10에서 [www.itbank.com](http://www.itbank.com/) 도메인 이름만 입력해도 웹페이지가 뜨는 것을 확인할 수 있다.











#### [ DocumentRoot 변경 실습 ]

- 변경 후 DocumentRoot : /apache/html

- 메인 페이지의 이름 : test.html



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-37.png?raw=true)

- 디렉토리 접근제한 설정에서 DomainRoot를 /apache/html로 바꿔주자.





![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-38.png?raw=true)

- DirectoryIndex 옵션값에 index.html 대신 test.html로 바꿔주자. 그리고 저장 후 나가기



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-39.png?raw=true)

- DoucmentRoot로 사용할 디렉토리를 만들어주고 설정파일 구문도 체크해준다.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-40.png?raw=true)

- DomainRoot(/apache/html) 안에 test.html 웹 페이지 파일을 만들어주자.



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-41.png?raw=true)

- 웹서비스 재시작하기



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-42.png?raw=true)

- WIn10에서 접속하면 해당 웹페이지가 출력되는 것을 확인할 수 있다.

 

#### [웹서버 로그]

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-43.png?raw=true)

- **Error_log : 웹서버 오류에 대한 로그**

- **Access_log : 웹서버 접근에 대한 로그**

- logs/error_log 파일로 로그가 기록되면 이 파일은 **/var/log/httpd 디렉토리** 아래에 링크되있다.



**\# loglevel (에러로그 위험 수준)**

​								**debug > info > notice > warn > error > crit > alert > emerg**  

로그기록 수준		 **낮음<-------------------------보통 --------------------------->높음**

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-44.png?raw=true)

- LogLevel <에러로그 위험 수준> 



![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-45.png?raw=true)

- log_config_module : 로그 설정 모듈 ( Access 로그에 관한 모듈)

- LogFormat : Access 로그 파일의 내용 포맷 형태(combined, common, combinedio)

- **LogFormat <포맷 스트링> <포맷 형태>**



\# 로그 파일 포맷 스트링 참조하기!

https://httpd.apache.org/docs/2.2/ko/mod/mod_log_config.html

 

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-46.png?raw=true)

- **Alias 모듈**을 통해 외부로 DomainRoot 경로 노출을 줄일 수 있다.

- **redirect, Alias, ScriptAlias** 3가지 제어 옵션이 있다.

- redirect : 기존에 있던 도메인이름이 예전에 존재했지만 지금은 더이상 없을 때 클라이언트가 예전 도메인이름을 가지고 웹페이지를 요청했을 때 새로운 도메인이름을 가진 주소로 리다이렉션시키는 기능이다. ( 이전 URL  새로운 URL 로 리다이렉션)

- Redirect <URL 경로> [URL]

- Alias : 파일시스템에서 DocumentRoot 밖에 있는 문서에 넘겨줄 수 있다.

- Alias <URL 경로> <파일 경로>

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-47.png?raw=true)

- ScriptAlias : CGI처럼 웹서버에 연결하는 외부모듈이 가지고 있는 스크립트(동적 페이지)로 넘겨줄 수 있다.

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-48.png?raw=true)

- CGI 웹서버 모듈을 사용시 연결하는 DomainRoot를 설정하는 섹터

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-49.png?raw=true)

- 웹서버의 언어셋을 설정한다.

- **3-tier 구성 시  웹서버, WAS서버, DB서버가 같은 언어셋을 맞춰줘야한다.**

 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-50.png?raw=true)

- 웹서버 응답코드 중 에러코드에서 응답메세지 값을 텍스트 출력이나 특정 웹페이지로 출력하는 옵션이다.

- **ErrorDocument <응답 코드> <문자열 또는 웹페이지 파일>**

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-51.png?raw=true)

- 메모리 매핑관련 옵션; 클라이언트가 웹페이지의 캐시 파일을 웹서버에 보내주면 해당 페이지 Read 없이 웹페이지를 보내주는 옵션이다. 캐시 기능을 사용하기 때문에 응답 속도를 높일 수 있다.

- 하지만 관리 리소스가 크게 늘수 있다.

 

#### @ Virtual host → 클라이언트의 다양한 서비스 요청에 따라 가상호스트를 기반으로 하는 웹서비스를 제공할 수 있다.

- 특정 도메인(또는 호스트)에 대한 DomainRoot를 지정하여 다양한 웹페이지를 제공할 수 있다.

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-52.png?raw=true)

- 가상 호스트 설정 파일(/etc/httpd/conf.d/kgbank.conf)를 만들고 적용하기 위해 include 지시어를 사용한다.

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-53.png?raw=true)

- DNS 서비스 관련, kgbank.com 도메인 네임에 대한 정방향 조회 영역 파일을 만들어준다.

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-54.png?raw=true)

- **/etc/httpd/conf.d/kgbank.conf 파일을 생성**하고, 가상호스트에 대한 웹 서버 설정 구역을 나눠준다.

-  **<VirtualHost *(모든주소):[포트번호]>** **~ \</VirtualHost>:**  가상호스트 설정 구역 

- 구역 안에 DomainRoot, ServerName, 디렉토리 접근제한을 설정해주자. 

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-55.png?raw=true)

- www.itbank.com 은 DomainRoot가 /apache/html/로 index.html 내용이 Test Page이기 때문에

웹페이지가 Test Page로 뜬다.

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-56.png?raw=true)

-  www.kgbank.com 은 DomainRoot가 /apache2/html/로 index.html 내용이 kgbank’s home이기 때문에 웹페이지가 kgbank’s home로 뜬다.



**\# host 네임을 구분으로도 virtual host를 사용할 수 있다.**

ex) DomainRoot를 따로 지정

[www.itbank.com](http://www.itbank.com/) → /apache/html/

blog.itbank.com → /apache/blog/

![img](https://github.com/KDY-Cloud/KDY-Cloud.github.io/blob/master/_images/2022-09-17/Linux%20Web%20Server-57.png?raw=true)

**\# 참고**

- ErrorLog 및 CustomLog(Access_log)를 정의해 개별로 Log 기록을 관리할 수 있다.

- Errolog, CustomLog 정의하지 않을 경우 하나의 로그 기록파일로 통합하게된다.

​     → ErrorLog는 /etc/httpd/logs/blog-error.log 로 저장되고, AccessLog는 /etc/httpd/logs/blog-access_log로 common 형식으로 저장된다.

 