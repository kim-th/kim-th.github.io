---
layout: post
title: '톰캣 멀티인스턴스 구성 (Cent-OS)'
categories: 'tomcat'
---
# 톰캣 멀티인스턴스 구성 (Cent-OS)

참고: https://notesbytom.wordpress.com/2017/03/10/tomcat-multiple-instances-rhel-7-centos-7/

아래의 INSTANCE는 구성시 인스턴스의 실제 명칭

1.  모든 톰캣의 글로벌 환경설정
`/etc/tomcat/tomcat.conf`

2. Tomcat services 만들기 (systemctl)
`/usr/lib/systemd/system/tomcat@.service (기존 템플릿 파일)`
`/usr/lib/systemd/system/tomcat@INSTANCE.service(생성할 파일)`
`cp [기존 템플릿] [생성할 파일]`

3. 인스턴스 별 환경변수 만들기
`/etc/sysconfig/tomcat(기존 톰캣 파일)`
`/etc/sysconfig/tomcat@ INSTANCE (생성할 파일)`

4. 인스턴스 별 CATALINA_BASE 디렉토리 만들기
`/var/lib/tomcats/ INSTANCE`
`/usr/share/tomcat(기존 톰캣) 폴더에서 링크를 확인 가능`
`cp /etc/tomcat  /var/lib/tomcats/ INSTANCE /conf ` port 수정 필요
`cp /var/log/tomcat /var/lib/tomcats/ INSTANCE /logs `
`cp /etc/logrotate.d/tomcat@ INSTANCE`
`cp /var/lib/tomcat/webapps /var/lib/tomcats/ INSTANCE /webapps `
`cp /var/cache/tomcat/work /var/lib/tomcats/ INSTANCE /work `
`cp /var/cache/tomcat/temp /var/lib/tomcats/ INSTANCE /temp `

5. 파일 권한 확인 필요
tomcat 그룹과 유저가 맞는지 확인 후 권한 부여
`chown tomcat.tomcat /var/lib/tomcats/ -R`

6. 서비스 실행
`systemctl status tomcat@ INSTANCE`
`systemctl enable tomcat@ INSTANCE # enable start-on-boot`
`systemctl start tomcat@ INSTANCE # start instance right now`
`systemctl stop tomcat@ INSTANCE # stop instance right now`

------------


##서비스 로그 확인
`journalctl -u tomcat@INSTANCE1`

##포트 권한 에러
java.net.BindException: Permission denied (Bind failed) 
참고: https://support.plesk.com/hc/en-us/articles/115002067794-Java-application-is-not-working-BindException-Permission-denied-Bind-failed-


##리눅스 포트 프로세스 목록 확인
`netstat -tnlp`

------------

# 실제 구성 내용
##INSTANCE1
###서비스
`cp /usr/lib/systemd/system/tomcat@.service /usr/lib/systemd/system/tomcat@INSTANCE1.service`

###개별 환경변수
`cp /etc/sysconfig/tomcat /etc/sysconfig/tomcat@INSTANCE1`

###CATALINA_BASE 만들기
`mkdir /var/lib/tomcats/INSTANCE1`
`cp -r /etc/tomcat  /var/lib/tomcats/INSTANCE1/conf`
###포트 정보 변경
`vi /var/lib/tomcats/INSTANCE1/conf/server.xml --1****`
```xml
...
<Server port="18005" shutdown="SHUTDOWN">
...
<Connector port="18080" protocol="HTTP/1.1"
connectionTimeout="20000"
redirectPort="18443" ></Connector>
...
<Connector port="18009" protocol="AJP/1.3" redirectPort="18443" ></Connector>
...
```

###temp 기본 경로로 변경(주석 처리 시 기본 경로)
`vi /var/lib/tomcats/INSTANCE1/conf/tomcat.conf`
```bash
# System-wide tmp
# CATALINA_TMPDIR="/var/cache/tomcat/temp"

```

###로그
`cp /var/log/tomcat /var/lib/tomcats/INSTANCE1/logs` 
`cp /etc/logrotate.d/tomcat /etc/logrotate.d/tomcat@INSTANCE1`
###logrotate.d/tomcat@INSTANCE1 수정
`vi /etc/logrotate.d/tomcat@INSTANCE1`
```
/var/lib/tomcats/INSTANCE1/logs/catalina.out {
copytruncate
weekly
rotate 52
compress
missingok
create 0644 tomcat tomcat
}
```

`cp -r /var/lib/tomcat/webapps /var/lib/tomcats/INSTANCE1/webapps`
`cp /var/cache/tomcat/work /var/lib/tomcats/INSTANCE1/work`
`cp /var/cache/tomcat/temp /var/lib/tomcats/INSTANCE1/temp`

###권한
`chown tomcat.tomcat /var/lib/tomcats/ -R`

###포트 권한
`semanage port -a -t http_port_t -p tcp 18080 2>/dev/null || semanage port -m -t http_port_t -p tcp 18080`
`semanage port -a -t http_port_t -p tcp 18009 2>/dev/null || semanage port -m -t http_port_t -p tcp 18009`
`semanage port -a -t http_port_t -p tcp 18005 2>/dev/null || semanage port -m -t http_port_t -p tcp 18005`

###서비스 실행
`systemctl status tomcat@INSTANCE1`
`systemctl enable tomcat@INSTANCE1 # enable start-on-boot`
`systemctl start tomcat@INSTANCE1 # start instance right now`
`systemctl stop tomcat@INSTANCE1 # stop instance right now`

###서비스 로그 확인
`journalctl -u tomcat@INSTANCE1`

------------

##INSTANCE2
###서비스
`cp /usr/lib/systemd/system/tomcat@.service /usr/lib/systemd/system/tomcat@INSTANCE2.service`
##개별 환경변수
`cp /etc/sysconfig/tomcat /etc/sysconfig/tomcat@INSTANCE2`
###CATALINA_BASE 만들기
`cp -r /var/lib/tomcats/INSTANCE1 /var/lib/tomcats/INSTANCE2`
###포트 정보 변경
`vi /var/lib/tomcats/INSTANCE2/conf/server.xml --2****`
```xml
...
<Server port="28005" shutdown="SHUTDOWN">
...
<Connector port="28080" protocol="HTTP/1.1"
connectionTimeout="20000"
redirectPort="28443" />
...
<Connector port="28009" protocol="AJP/1.3" redirectPort="28443" />
...
```

		
###로그
`cp /etc/logrotate.d/tomcat@INSTANCE1 /etc/logrotate.d/tomcat@INSTANCE2`
###logrotate.d/tomcat@INSTANCE2 수정
'vi /etc/logrotate.d/tomcat@INSTANCE2'
```
/var/lib/tomcats/INSTANCE2/logs/catalina.out {
copytruncate
weekly
rotate 52
compress
missingok
create 0644 tomcat tomcat
}
```

###권한
`chown tomcat.tomcat /var/lib/tomcats/ -R`

###포트 권한
`semanage port -a -t http_port_t -p tcp 28080 2>/dev/null || semanage port -m -t http_port_t -p tcp 28080`
`semanage port -a -t http_port_t -p tcp 28009 2>/dev/null || semanage port -m -t http_port_t -p tcp 28009`
`semanage port -a -t http_port_t -p tcp 28005 2>/dev/null || semanage port -m -t http_port_t -p tcp 28005`

##서비스 실행
`systemctl status tomcat@INSTANCE2`
`systemctl enable tomcat@INSTANCE2 # enable start-on-boot`
`systemctl start tomcat@INSTANCE2 # start instance right now`
`systemctl stop tomcat@INSTANCE2 # stop instance right now`

##서비스 로그 확인
`journalctl -u tomcat@INSTANCE2`


