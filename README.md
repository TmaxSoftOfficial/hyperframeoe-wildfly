## HyperFrameOE-WildFly
업로드된 바이너리는 HyperFrame Open Edition WildFly 제품 설치를 위한 파일  

## 설치 파일

### WildFly
* Verison : wildfly-preview-22.0.0.Final
* Note : https://www.wildfly.org/downloads/

## 설치 및 실행

### 1) WildFly 압출 풀기

    $ cd ${WILDFLY_HOME}/
    $ tar -zxf wildfly-preview-22.0.0.Final.tar.gz

### 2) Listen Port 확인 및 변경

    $ vi ${WILDFLY_HOME}/wildfly-preview-22.0.0.Final/standalone/standalone.xml

    # 프로토콜에 따른 Port 확인 및 변경
    <socket-binding-group name="standard-sockets" default-interface="public" port-offset="${jboss.socket.binding.port-offset:0}">
        <socket-binding name="ajp" port="${jboss.ajp.port:8009}"/>
        <socket-binding name="http" port="${jboss.http.port:8080}"/>
        <socket-binding name="https" port="${jboss.https.port:8443}"/>
        ...
    </socket-binding-group>
      
### 3) WildFly 실행

    $ vi ${WILDFLY_HOME}/wildfly-preview-22.0.0.Final/bin/
    $ ./standalone.sh

### 4) 종료 

* 관리자 접속 / 종료

      $ cd ${WILDFLY_HOME}/wildfly-preview-22.0.0.Final/bin/
      $ ./jboss-cli.sh 
      You are disconnected at the moment. Type 'connect' to connect to the server or 'help' for the list of supported commands.
      [disconnect /] connect localhost:[manage port]                    # standalone.xml 확인 가능
      [standalone@192.168.3.87:9990 /] shutdown                         # Admin Mode

* 한 줄 종료

      $ cd /home/username/wildfly-preview-22.0.0.Final/bin/
      $ ./jboss-cli.sh --controller:localhost:[manage port] --connect command=:shutdown

## 디렉토리 구조

      wildfly
      ├── standalone         <---------------- 서버에서 사용하는 클래스 라이브러리
      │    ├── configuration 
      │    ├── data          <---------------- standalone server 실행 시 생성되는 파일들을 보관
      │    ├── deployments   <---------------- App 배포 디렉토리 
      │    ├── lib           <---------------- 설치된 라이브러리가 위치 
      │    ├── log
      │    └── tmp           <---------------- 임시 파일 위치
      ├── applicient         <---------------- Application 클라이언트 컨테이너에서 사용하는 파일 담당
      ├── bin                <---------------- WildFly 파일의 기동 및 종료 등의 shell 경로
      ├── docs               <---------------- 라이선스 파일, xml스키마 정의 파일, 예제 구성 파일 및 기타 문서 담당
      ├── welcome-content
      ├── moduels            <---------------- 서버에서 사용하는 클래스 라이브러리
      ├── domain             <---------------- domain 모드에서 사용하는 파일 담당 ( *domain 모드 : 다중 서버 관리 모드 )
      │    └── log
      └── tmp                <---------------- 임시 파일 위치

## 버전 확인

    $ cd ${WILDFLY_HOME}/bin/
    $ ./standalone.sh --version

    =========================================================================

      JBoss Bootstrap Environment

      JBOSS_HOME: /home/wee/wildfly-preview-22.0.0.Final

      JAVA: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.302.b08-0.el7_9.x86_64/jre/bin/java

      JAVA_OPTS:  -server -Xms64m -Xmx512m -XX:MetaspaceSize=96M -XX:MaxMetaspaceSize=256m -Djava.net.preferIPv4Stack=true -Djboss.modules.system.pkgs=org.jboss.byteman -Djava.awt.headless=true

    =========================================================================

    00:00:00,479 INFO  [org.jboss.modules] (main) JBoss Modules version 1.11.0.Final
    WildFly Preview 22.0.0.Final (WildFly Core 14.0.0.Final)
    
## 로그 정보

### 1) 로그 경로

    ${WILDFLY_HOME}/standalone/log/server.log

### 2) 로그 경로 변경
    
    # Log Path 추가
    $ vi ${WILDFLY_HOME}/bin/standalone.sh
    ...
    export JAVA_OPTS=" $JAVA_OPTS -Djboss.server.log.dir=[Log Path]"
    ...

## 환경 설정 파일 정보

### 1) 환경 설정 파일 경로

    ${WILDFLY_HOME}/standalone/configuration/standalone.xml

### 2) 로그 경로

* 외부 Client 접속 허용
      
      ...
      <interface name="public">
          <!--<inet-address value="${jboss.bind.address:192.168.3.87}"/>-->   # 주석 처리 및 삭제
          <any-address/>                                                      # 추가
      </interface>
      ...
      
* port 번호 변경

      ... 
      <socket-binding-group name="standard-sockets" default-interface="public" port-offset="${jboss.socket.binding.port-offset:0}">
          <socket-binding name="ajp" port="${jboss.ajp.port:8009}"/>
          <socket-binding name="http" port="${jboss.http.port:8080}"/>
          <socket-binding name="https" port="${jboss.https.port:8443}"/>
          <socket-binding name="management-http" interface="management" port="${jboss.management.http.port:9990}"/>
          <socket-binding name="management-https" interface="management" port="${jboss.management.https.port:9993}"/>
      ...

## Web Server 연동

### 1) Apache

* 사전에 apache 설치가 필요 (Apache 제품의 README.MD 파일 참고)

* tomcat-connectors-1.2.46-src.tar.gz 압축 해제, tomcat connector jk 설치

      $ tar xzvf tomcat-connectors.1.2.46-src.tar.gz
      $ cd tomcat-connectors.1.2.46-src/native
      $ ./configure --with-apxs=/${APACHE_HOME}/bin/apxs
      $ make & make install

* 정상 설치 완료 시, ${APACHE_HOME}/mudlues/에서 mod_jk.so 파일 생성

* ${APACHE_HOME}/conf/httpd.conf 파일에 mod_jk 설정 추가

      ...
      LoadModule jk_module modules/mod_jk.so
      Include conf/extra/mod_jk.conf
      ...

* ${APACHE_HOME}/conf/extra/mod_jk.conf 파일 생성

      $ vi ${APACH_HOME}/conf/extra/mkd_jk.conf
      
      # mkd_jk.conf
      <IfModule mod_jk.c>
      JkWorkersFile conf/workers.properties
      JkShmFile logs/mod_jk.shm
      JkLogFile logs/mod_jk.log
      JkLogLevel info
      JkLogStampFormat "[%a %b %d %H:%M:%S %Y] "
      JkMountFile conf/uriworkermap.properties
      </IfModule>
      
* ${APACHE_HOME}/conf/workers.properties 파일 생성, 연동 Tomcat List 작성

      $ vi ${APACHE_HOME}/conf/workers.properties
      
      # workers.properties
      worker.list=worker1
      worker.worker1.port=8009
      worker.worker1.host=192.168.0.120
      worker.worker1.type=ajp13

* ${APACHE_HOME}/conf/uriworkermap.properties 파일 생성, Tomcat에서 처리할 uri 작성

      /*=worker1

* ${WILDFLY_HOME}/standalone/configure/standalone.xml Web Server 통신 Listener 추가

      ...
      <server name="default-server">
          ...
          <ajp-listener name="ajp" socket-binding="ajp" />
          ...
      </server>
      ...
      
* Apache 및 WildFly 기동
      
### 2) Nginx

* Nginx 종료
      
      $ ./nginx -s stop

* [Nginx] ${NGINX_HOME}/conf/nginx.conf 파일 수정
      
      $ vi ${NGINX_HOME}/conf/nginx.conf
      ...
      location / {
           root             html;
           index            index.html index.htm;
           proxy_pass     http://[WildFly IP]:[WildFly Listen Port];
      }
      ...

* [WildFly] ${WILDFLY_HOME}/standalone/configuration/standalone.xml 파일 수정
      
      
      $ vi ${WILDFLY_HOME}/standalone/configuration/standalone.xml
      ...
      <inferface>
           <interface name="management">
                <inet-address value="${jboss.bind.address.management:127.0.0.1}"/>
           </interface>
                <interface name="public">
                <any-address/>
        </interface>
      ...

      ...
      <server name="default-server">
           <http-listener name="default" socket-binding="http" redirect-socket="https" enable-http2="true" proxy-address-forwarding="true"/>
      ...
      </server>
 
* Nginx 및 WildFly 기동 
