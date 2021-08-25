## HyperFrameOE-WildFly

## 설치 방법
다음 tar 파일의 압축 해제
tar -zxvf wildfly-preview-22.0.0.Final.tar.gz

## 파일 기동 

${WILDFLY_HOME}/bin > ./standalone.sh

http:// ip:8080 주소로 접속하여 WEB UI 화면을 확인

## 종료 

${WILDFLY_HOME}/bin > jboss-cli.sh --controller=IP:9990 --connect command=:shutdown 명령어로 종료

controller의 IP는 standalone.xml에서 설정한 management IP

## 실행 / 종료 명령어


### 실행
${WILDFLY_HOME}/bin > ./standalone.sh

### 종료
${WILDFLY_HOME}/bin > jboss-cli.sh --controller=IP:9990 --connect command=:shutdown

## 디렉토리 구조

<pre>
    $ cd /home/username/wildfly
    wildfly
    ├── standalone	<----------------------- 서버에서 사용하는 클래스 라이브러리
    │   ├── configuration 
    │   ├── data	<----------------------- standalone server 실행 시 생성되는 파일들을 보관
    │   ├── deployments	<----------------------- App 배포 디렉토리 
    │   ├── lib		<----------------------- 설치된 라이브러리가 위치 
    │   ├── log
    │   ├── tmp		<----------------------- 임시 파일 위치
    ├── applicient	<----------------------- Application 클라이언트 컨테이너에서 사용하는 파일 담당
    ├── bin		<----------------------- WildFly 파일의 기동 및 종료 등의 shell 경로
    ├── docs		<----------------------- 라이선스 파일, xml스키마 정의 파일, 예제 구성 파일 및 기타 문서 담당
    ├── welcome-content
    ├── moduels		<----------------------- 서버에서 사용하는 클래스 라이브러리
    ├──domain		<----------------------- domain 모드에서 사용하는 파일 등을 담당 ( *domain 모드 : 다중 서버를 관리할 수 있는 모드 )
    │    ├── log
    └── ── tmp	<----------------------- 임시 파일 위치
</pre>

## 로그

${WILDFLY_HOME}/standalone/log/server.log : 서버가 시작된 이후 모든 로그 메시지가 기록되는 파일

## 로그 경로 변경

${WILDFLY_HOME}/bin/standalone.sh에

export JAVA_OPTS=" $JAVA_OPTS -Djboss.server.log.dir=경로"

위와 같이 로그 디렉터리를 변경하는 옵션 추가

## 환경 설정 파일

${WILDFLY_HOME}/standalone/configuration/standalone.xml

ip 변경
외부 접근 허용 가능하게 할 시
  <!--<inet-address value="${jboss.bind.address:192.168.3.87}"/> -->    해당 부분 주석처리 후

  <any-address/> 추가
port 번호 변경
         <socket-binding-group name="standard-sockets" default-interface="public" port-offset="${jboss.socket.binding.port-offset:0}">
       
         <socket-binding name="ajp" port="${jboss.ajp.port:8009}"/>
       
         <socket-binding name="http" port="${jboss.http.port:8080}"/>
       
         <socket-binding name="https" port="${jboss.https.port:8443}"/>
       
         <socket-binding name="management-http" interface="management" port="${jboss.management.http.port:9990}"/>
       
         <socket-binding name="management-https" interface="management" port="${jboss.management.https.port:9993}"/>
       
...

## 웹서버 연동방법

- apache & WildFly 연동

1. 사전에 apache 설치가 필요. (Apache 제품의 README.MD 파일 참고)

2. tomcat-connectors-1.2.46-src.tar.gz 압축 해제하여  tomcat connector jk 설치

$ tar xzvf tomcat-connectors.1.2.46-src.tar.gz
$ cd tomcat-connectors.1.2.46-src/native
$ ./configure --with-apxs=/${APACHE_HOME}/bin/apxs
$ make
$ make install

설치가 정상완료되면 apache 설치 디렉토리 하위에 위치한 mudlues 디렉토리에 mkd_jk.so 파일이 생성

3. ${APACH_HOME}/conf 파일에 mkd_jk 설정 추가

oadModule jk_module modules/mod_jk.so

Include conf/extra/mod_jk.conf

4. ${APACH_EHOME}/conf/extra 경로에 mkd_jk.conf 파일을 생성하여 아래의 설정 추가

<IfModule mod_jk.c>

JkWorkersFile conf/workers.properties

JkShmFile logs/mod_jk.shm

JkLogFile logs/mod_jk.log

JkLogLevel info

JkLogStampFormat "[%a %b %d %H:%M:%S %Y] "

JkMountFile conf/uriworkermap.properties

</IfModule>

5. ${APACHE_HOME}/conf의 workers.properties 파일 생성해서 아래와 같이 연동할 톰캣리스트 작성

worker.list=worker1

worker.worker1.port=8009

worker.worker1.host=192.168.0.120

worker.worker1.type=ajp13

6. ${APACHE_HOME]/conf에 uriworkermap.properties 파일 생성해서 톰캣에서 처리할 uri 작성

/*=worker1

7. ${WILDFLY_HOME}/standalone/configure의 standalone.xml에 아래의 내용을 <server name...>절 이하에 추가

<ajp-listener name="ajp" socket-binding="ajp" />

8. apache 및 WildFly 기동

## 버전 확인

Wildfly : ${WILDFLY-HOME}/bin : $ ./standalone.sh --version

Java : $java -version

## 라이센스

## 버전 이력

wildfly-preview-22.0.0.Final
