---
layout: page
title: "Linux Log 관리하기"
description: "Linux 서버에서 Log를 관리하기 위한 명령어 및 도구에 대해 알아보겠습니다."
tags: [Linux Log]
comments: true
published: true
categories: Linux
---



- **텍스트 형식** :  vi 편집기, cat, tail 등 출력 명령어로 출력이 가능하다.

- **바이너리 형식** : 기존 출력 명령어로는 출력이 불가능하다. 로그 관련 명령어를 통해 확인

**원격 로그인 로그 파일** : /var/log/secure

**시스템 운영 로그 파일** : /var/log/messages

**서비스 운영 로그 파일** :서비스마다 로그파일 저장을 달리한다.

 

### @logrotate :로그관리를 자동으로 진행해주는 명령어

- 하나의 로그파일이 무한정의 로그 기록이 생긴다면 로그 관리가 반드시 필요해진다.

- logrotate를 통해 내부적으로 로그 관리를 자동적으로 하게끔하고, 로그파일 내의 로그 기록을 추출 및 수집(필터링, 파싱)하여(logstash) 시각화하여(kibana) 분석하는것)이 트렌드이다. --> ELK Stack

- ELK --> Elasticsearch, Logstash, Kibana

### @logrotate 과정

- /etc/cron.daily/logrotate → /usr/sbin/logrotate 호출 →  /etc/logrotate.conf 를 참조 → /etc/logrotate.d를 호출

ex) 패키지 설치 --> /etc/logrotate.d/ 디렉터리 내에 설정파일로 남게 된다.

  소스 설치는 자동으로 남지 않기 때문에 어려운 과정이 될 수 있다.

 

### @ logrotate 옵션 (알아두기!!)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png)

- copytruncate, postrotate~endscript 옵션 차이 확인해두기!

### [ELK Stack을 이용한 Linux Log 관리]

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image005.png)

- RAM을 최소 6GB에서 진행한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image007.png)

- Elasticsearch는 자바를 기반하기 때문에 JDK가 필수적으로 설치가 되있어야한다.

- CentOS 전체설치에서 GUI 포함 설치를 진행시 기본적으로 포함되있다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image009.png)

- Elasticsearch와 전자서명 키 설치를 한 후 패키지 설치를 진행한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image011.png)

-  **vi /etc/elasticsearch/elasticsearch.yml** : Localhost가 9200번 포트에서 Elasticsearch가 작동하도록 설정

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image013.png)

- **vi /usr/lib/systemd/system/elasticsearch.service**에서 mlockall기능을 활성화하여 모든 작업에 관한 메모리사용을 물리적으로 사용할 수 있도록 설정한다. (가상 메모리로의 할당이 불가하다.)

\# 패키지를 설치하면 systemd가 프로세스를 관리할 수 있게 systemd 디렉토리 내에 서비스가 자동으로 등록되게끔 구현된다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image014.png)

- vi /etc/sysconfig/elasticsearch에서 (가상)메모리 페이징 시 로그 손실이 발생할 위험이 존재하기 때문에 페이징 작업을 하지않도록 설정한다. 

\#Mlockall --> 호출 프로세스의 주소 공간에 매핑된 모든 페이지를 잠근다. 호출이 성공적으로 리턴될 시 맵핑된 모든 페이지가 RAM에 상주하게되고 페이지는 나중에 잠금이 해제될 때까지 RAM에 유지하게 된다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image015.png)

- mlockall 설정을 적용하기 위해 전체 데몬프로세스를 reload한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image017.png)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image019.png)

- Elasticsearch를 자동시작 설정 및 재시작한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image021.png)

- netstat으로 Elasticsearch(9200번 포트)가 잘 작동하는지 확인!

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image023.png)

- curl 명령어를 통해 mlockall이 사용중인지 확인하기 --> mlockall 값이 true가 되어야한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image025.png)

- curl 명령어로 Elasticsearch가 동작하는지 확인

\# curl 명령어는 REST API를 통해 특정 WEB에게 HTTP 요청 메세지를 전달하는 명령어이다.

@ Nginx 웹서비스 & Kibana 설치하기

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image027.png)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image028.png)

- Kibana를 설치해준다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image030.png)

- Kibana 서비스 설정파일(/etc/kibana/kibana.yml)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image031.png)

- 운영 서버 포트를 5601번 포트로 설정, 외부접근을 허용하기 위해 모든 네트워크(0.0.0.0)으로 설정한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image033.png)

- Elasticsearch 호스트는 localhost의 9200번 포트로 지정한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image035.png)

- Kibana 서비스를 자동시작 설정 및 재시작해준다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image037.png)

- netstat -antp 로 Kibana 서비스(5601번 포트)가 작동중인지 확인!

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image039.png)

- Kibana 서비스는 Reverse WEB Proxy Server을 통해 화면에 정보를 표시하는데, ELASTIC 공식가이드에선 Ngnix Web Server을 통해 Reverse WEB Proxy Server을 구성한다.

**\## 웹 대시보드를 사용하기 위해 웹 서비스가 부가적으로 필요함**

**\# Reverse Proxy 서버 : 클라이언트와 내부망의 서버 사이에 위치하는 프록시 서버**

- 클라이언트가 요청 시 프록시 서버가 내부망 서버에 요청 후 응답 받은 자원을 클라이언트에게 전달해야한다. (이때 Reverse Proxy 서버는 실제 서버들에 대한 주소를 매핑해야한다.)

- nginx 패키지를 설치하기 위해선 epel 저장소 패키지를 설치해야한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image041.png)

- epel 저장소 패키지를 설치하고난 후 nginx, httpd-tools 패키지를 설치해준다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image043.png)

- 백업 디렉토리를 만들어 nginx.conf 설정파일을 백업해두고

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image045.png)

- nginx.conf 설정 파일을 수정해준다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image047.png)

- 새로운 가상호스트를 추가하여 사용할 것이므로 위의 Server Block 설정을 삭제한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image049.png)

- 가상호스트를 추가하기 위해 kibana.conf 설정 파일을 생성한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image051.png)

- Reverse Proxy 서버를 운영하기 위한 가상 호스트 설정을 진행한다.

- Nginx WEB 서버 접근은 기본적인 Access 접근의 보안형식(Restricted Access)을 사용한다.

- 기본적인 Proxy Server 정보를 Header에 포함하는 설정정보를 함께 지정한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image053.png)

- Access 접근 보안을 사용하기 때문에 계정과 패스워드를 지정한다. (지정한 값은 .kibana-user 파일에 등록된다.)

- 접근시 사용할 계정 : Admin 패스워드: itbank로 설정한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image055.png)

- Nginx 설정파일의 구성정보를 검증하는 작업을 진행한다. (nginx -t 옵션)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image057.png)

- nginx 설정파일에 대한 변경사항을 적용하기 위해 재시작 및 자동시작 설정해준다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image059.png)

- netstat -antp 로 Nginx WEB 서버가 잘 작동하는지 확인!

### @ Logstash 설치하기

\# logstash : 다양한 소스로부터 데이터를 수집하고 곧바로 전환하여 원하는 대상에 전송할 수있도록하는 경량의 오픈 소스 형태의 서버측 데이터 처리 파이프라인이다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image061.png)

- logstash 소스를 가져와서

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image063.png)

- 설치해준다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image065.png)

- 설치 완료

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image067.png)

- 로그 정보는 민감한 데이터이기 때문에 SSL/TLS의 암호화로 전송하기위한 설정파일을 수정한다 (ELK에서 로그 전송을 위해 SSL/TLS 암호화를 권장하고 있다.)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image068.png)

- SSL/TLS Server IP 주소를 명시해준다.(기존 설정파일에 존재하지 않기 떄문에 새로 작성할 것)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image070.png)

- SSL/TLS에서 사용할 공개키 인증서(crt) 및 개인키(key)를 생성한다. 

- -config 옵션으로 SSL/TLS 설정파일을 참조하여 공개키 인증서 및 개인키를 생성할 수 있다,

- 추가 옵션으로 각 키의 만료날짜(-days), RSA bit값 등을 지정할 수 있다.

**\**키를 생성하기 위해서 openssl 패키지가 미리 설치가 되있어야함***

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image072.png)

- 공개키 인증서는 certs 디렉토리에, 개인키는 private 디렉토리에 저장시켜놓았다.

 

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image074.png)

- logstash는 다양한 데이터를 수집하기 때문에 logstash의 설정파일명은 정해지지 않는다.

- 여기서는 syslog의 로그 기록을 수집하기 떄문에 식별하기 쉽게 syslog.conf로 지정하였다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image076.png)

- Client로부터 Filebeat(5044번 포트)를 이용하여 전송된 데이터를 받아들일 형식을 결정한다. 

- 사용 포트(5044번)과 SSL/TLS 설정값을 정의한다.

- logstash의 경우 Filebeat(beats)로부터 반드시 지정된 시간 내의 데이터가 전송되어야하고, 만약 지정된 시간내의 데이터전송이 안될 경우 연결을 해제시킨다.

- client_inactivity_timeout 값을 지정해 Filebeat로부터 들어오는 데이터에 대한 응답대기 시간을 지정한다. (기본값은 60초, 여기서는 600초로 길게 잡아놨다)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image078.png)

- Logstash는 다양한 필터 플러그인을 지원하는데 그중 grok 플러그인을 주로 사용한다.

- grok 패턴식, 표현식 등은 공식 문서에서 제공하는 데로 사용하는 것이 편리하다. 

- ELK 사이트에서 Filter식 예제를 확인할 수 있다.

\- https://www.elastic.co/guide/en/logstash/7.6/config-examples.html ( ELK 공식사이트 filter식 예제 )

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image080.png)

- logstash에서 수집한 데이터를 Elasticsearch로 전송하기 위한 설정을 진행한다.

- Elasticsearch의 주소(localhost:9200) 및 인덱스(syslog-~ : 날짜형식)를 설정한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image082.png)

- beats(로그 수집대상 서버)로부터 로그 데이터를 전송 받은 Logstash는 필터링을 하고 필터가 완료되면 Output으로 Elasticsearch에 보내게된다.

 

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image084.png)

- 변경사항이 끝나면 logstash의 자동시작 설정 및 재시작을 해준다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image086.png)

- 웹서비스와 5044번포트(logstash에서 사용할 포트)의 방화벽 설정도 해준다.

- Elasticsearch(9200번 포트)는 굳이 방화벽 설정을 할 필요없다. (시스템 내부에서 프로세스를 주고받기 때문이다. 실질적으로 웹에서 노출되는 것은 logstash이다. 

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image088.png)

 

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image090.png)

- watch 명령어로 5044번 포트에 대한 서비스 상태를 모니터링한다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image092.png)

 

### @ Filebeat 설치하기 

- Filebeat는 Client에 설치하여 Client에서 발생하는 대량의 데이터를 Logstash & Elasticsearch 서버로 보낼 수 있는 경량 에이전트 데이터 전송 서비스이다.

- Logstash의 Client 역할을 하는 Linux-02 가상머신에서 시작!

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image094.png)

- 먼저 Logstash 서버에서 생성했던 SSL/TLS 인증서를 복사(scp)해온다.

- 로그 데이터를 SSL/TLS 암호화하기 위해 필요한 인증서를 받아오는 과정이다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image096.png)

- 복사한 인증서 파일을 certs 디렉토리 내에 이동시킨다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image098.png)

- Elasticsearch 전자서명 GPG-KEY값을 설치한다. (변조되지 않았음을 인증하게된다.)

**\# 오픈소스에서 사용하는 서비스는 GPG-KEY값을 받는 것으로 무결성을 입증할 수 있다.**

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image100.png)

 

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image102.png)

- 패키지 설치

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image103.png)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image105.png)

- filebeat의 설정파일

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image107.png)

- 수집하는 로그 데이터의 로그 목록을 지정해준다. 

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image108.png)

- filebeat에서 바로 elastisearch로 보내지 않기 때문에 주석 처리해줘야한다.(기본값으로 주석이 풀려있음)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image110.png)

- logstash로의 output 설정 블록에서 주석을 풀어주고, logstash의 호스트, SSL/TLS 인증서 파일을 명시해준다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image112.png)

- filebeat를 자동시작 설정, 재시작해준다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image114.png)

- Linux-01(logstash서버)에서 보면 ESTABLISHED 상태인 것을 확인할 수 있다.

**@ ELK Stack 동작 확인하기 --> ELK Stack이 잘 작동하는지 확인하기 위해 Win10 환경에서 시작한다.**

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image116.png)

- 주소창에 192.168.1.100으로 접속하고 자격 증명 창에 계정명(Admin)과 비밀번호(itbank)를 적는다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image117.png)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image119.png)

- 인덱스 패턴 만들기

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image121.png)

- 인덱스 패턴을 syslog-*로 설정후 다음

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image123.png)

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image124.png)

- Discover로 쿼링, 필터링을 할 수 있다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image126.png)

- 타임스탬프 별로 모든 로그 기록을 볼 수 있다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image128.png)

- 원하는 필드를 넣어서 로그 기록을 필터링할 수 있다.

![img](file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_image130.png)

- Win10에서 Linux-02로 ssh 접속하여 실시간으로 생성된 그래프도 확인할 수 있다.



 

 

 