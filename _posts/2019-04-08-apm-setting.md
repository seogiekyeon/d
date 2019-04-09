---
layout: post
title: "CentOS 7.x APM Install"
date: 2012.05.22
excerpt: "CentOS 7.x APM Install"
tags: [AWS, SERVER]
comments: true
---

## BASE

### 1. centOS 접속

### 2. root 접속

  `# sudo su`

### 3. epel 인스톨

  `# yum install -y epel-release`

### 4. mirror 사이트 국내사이트로 잡기

  `# yum install -y bzip2` <br>
  `# bzip2 /etc/yum.repos.d/CentOS-*.repo` <br>
  `# echo '[base]`

  {% highlight html %}
  name=CentOS-$releasever - Base
  baseurl=http://ftp.daum.net/centos/$releasever/os/$basearch/
  gpgcheck=0
  [updates]
  name=CentOS-$releasever - Updates
  baseurl=http://ftp.daum.net/centos/$releasever/updates/$basearch/
  gpgcheck=0
  [extras]
  name=codentOS-$releasever - Extras
  baseurl=http://ftp.daum.net/centos/$releasever/extras/$basearch/
  gpgcheck=0' > /etc/yum.repos.d/Daum.repo
  {% endhighlight %}

### 4. yum 업데이트

    # yum update yum

### 5. ./configure를 위한 패키지 설치

    # yum -y install gcc gcc-c++ <br>
    **Watch Out!** 컴파일시 항상 로그를 체크하여 에러가 발생했는지 확인해주세요.
    {: .notice}

## APACHE

### 1. 임의의 디렉토리 생성후 이동

    # mkdir /root/downloads
    # cd /root/downloads

### 2. wget 패키지 설치

    # yum install -y wget

### 3. 아파치 다운로드 후 압축해제

    # wget http://archive.apache.org/dist/httpd/httpd-2.4.10.tar.gz
    # sudo tar zxvf httpd-2.4.10.tar.gz

### 4. 존속성 패키지 설치

* a. apr 설치

    # wget http://archive.apache.org/dist/apr/apr-1.5.1.tar.gz <br>
    # sudo tar zxvf apr-1.5.1.tar.gz <br>
    # cd apr-1.5.1 <br>
    # ./configure <br>

-------------------------
!. error 출력

  rm: cannot remove 'libtoolT': No such file or directory

  # cp -arp libtool libtoolT

  # ./configure
-------------------------

    # make
    # make test
    # make install
    # cd ..

* b. apr-util 설치

    # sudo wget http://archive.apache.org/dist/apr/apr-util-1.5.4.tar.gz <br>
    # sudo tar zxvf apr-util-1.5.4.tar.gz <br>
    # cd apr-util-1.5.4 <br>
    # sudo ./configure --with-apr=/usr/local/apr <br>
    # sudo make <br>
    # sudo make install <br>
    # cd .. <br>

* c. pcre 설치

    # sudo wget https://sourceforge.net/projects/pcre/files/pcre/8.36/pcre-8.36.tar.gz <br>
    # sudo tar zxvf pcre-8.36.tar.gz <br>
    # cd pcre-8.36 <br>
    # sudo ./configure --prefix=/usr/local/pcre <br>
    # sudo make & make test; <br>
    # sudo make install <br>
    # cd .. <br>

5. 아파치 설치

    # cd httpd-2.4.10 <br>
    # sudo ./configure --prefix=/usr/local/apache --enable-http  --enable-info --enable-cgi --enable-so --with-pcre=/usr/local/pcre <br>
    # sudo make & make test; <br>
    # sudo make install <br>

### 5. AWS로 설치한 CentOS 일 시 httpd.conf 파일 수정

    # vi /usr/local/apache/conf/httpd.conf

    User daemon
    Group daemon

    을 찾아 아래처럼 변경

    User centos
    Group centos

### 6. 아파치 구동 후 확인

    # /usr/local/apache/bin/httpd -k start

    주소창에 해당 ip 접속 후 "It Works!" 확인

### 7. 서비스 등록

    # ln -s /usr/local/apache/bin/apachectl /etc/init.d/httpd
    # vi /etc/init.d/httpd

    #!/bin/sh
    #
    # Apache This starts and stops Apache.
    #
    # chkconfig: 35 20 80
    # description: Apache Web Service
    #

    # service httpd start

## Mysql

### 1. 임시 다운로드 폴더 이동 후 필요 패키지 설치

    # cd /root/downloads
    # yum install -y cmake ncurses-devel openssl-devel libtool-ltdl expat-devel db4-devel
    # yum install perl perl-devel -y
    # yum install 'perl(Data::Dumper)' -y

### 2. MySql 설치

    # wget http://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-5.6.14.tar.gz
    # tar zxvf mysql-5.6.14.tar.gz
    # cd mysql-5.6.14
    # cmake \

    {% highlight html %}
    -DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
    -DMYSQL_DATADIR=/var/mysql/data \
    -DMYSQL_UNIX_ADDR=/var/mysql/mysql.sock \
    -DSYSCONFDIR=/etc \
    -DMYSQL_TCP_PORT=3306 \
    -DMYSQL_USER=mysql \
    -DDEFAULT_CHARSET=utf8 \
    -DDEFAULT_COLLATION=utf8_general_ci \
    -DWITH_EXTRA_CHARSETS=all \
    -DENABLED_LOCAL_INFILE=1 \
    -DWITH_INNOBASE_STORAGE_ENGINE=1 \
    -DWITH_ARCHIVE_STORAGE_ENGINE=1 \
    -DWITH_BLACKHOLE_STORAGE_ENGINE=1
    {% endhighlight %}

    # gmake

    **Watch Out!** ※ 약 20분 소요
    {: .notice}

    # gmake install

### 3. msyql 유저, 그룹 생성

    # groupadd dba
    # useradd -M -c "MySQL" -d /var/mysql -g dba -s /bin/nologin mysql
    # mkdir /var/mysql

### 4. mysql_install_db 실행

    # cd /usr/local/mysql
    # ./scripts/mysql_install_db --user=mysql --datadir=/var/mysql/data

### 5. 유저, 그룹 권한 이동

    # chown -R mysql:dba /usr/local/mysql
    # chown -R mysql:dba /var/mysql

### 6. my.cnf 파일 수정

    # mv ./my.cnf /etc/my.cnf
    # vi /etc/my.cnf

    {% highlight html %}
    sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
    innodb_buffer_pool_size = 16M
    innodb_additional_mem_pool_size = 2M
    innodb_log_file_size = 5M
    innodb_log_buffer_size = 8M
    innodb_flush_log_at_trx_commit = 1
    innodb_lock_wait_timeout = 50
    {% endhighlight %}

### 7. 주요기능 PATH 설정

    # sudo ln -s /usr/local/mysql/bin/mysql /usr/bin/mysql
    # sudo ln -s /usr/local/mysql/bin/mysqldump /usr/sbin/mysqldump
    # sudo ln -s /usr/local/mysql/bin/mysql_config /usr/sbin/mysql_config
    # sudo ln -s /usr/local/mysql/bin/mysqladmin /usr/sbin/mysqladmin

### 8. 서비스 등록

    # sudo ln -s /usr/local/mysql/support-files/mysql.server /etc/rc.d/init.d/mysqld
    # sudo chkconfig --add mysqld
    # sudo chkconfig --level 24 mysqld off
    # sudo service mysqld start

### 9. 외부 접속 라이브러리 추가

    # echo "/usr/local/mysql/lib" >> /etc/ld.so.conf
    # ldconfig

### 10. 설정

    # mysql -u root -p

    Enter password: (미설정이니 Enter)

    > use mysql;
    > update user set password = password('설정할 루트 비밀번호') where user = 'root';
    > create user '생성할 유저명'@'%' identified by '생성할 유저의 비밀번호';
    > create database 생성할DB명;
    > grant all privileges on 권한을 줄  DB명.테이블명 to '권한을 줄 유저'@'%' identified by '루트비밀번호';
     flush privileges;
        ex) 권한 설정 저장
        -------------------------
        !. 권한을 잘못 주었을 시
          > revoke all on DB명.테이블명 from '유저'@'%';
        -------------------------
    > \q;
        ex) 종료
    -- sqlYog OR sqlWorkBench 로 접속을 확인해주세요.
    {: .notice}

## PHP

### 1. 임시 다운로드 폴더 이동 후 필요 패키지 설치

    # cd /root/downloads <br>
    # sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm <br>
    # sudo rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm <br>
    # sudo rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm <br>
    # sudo rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm <br>
    # sudo yum -y install libxml2-devel bzip2-devel libcurl-devel gdbm-devel libvpx libvpx-devel libjpeg-turbo-devel libpng-devel libXpm \
libXpm-devel freetype-devel t1lib t1lib-devel gmp-devel libc-client libc-client-devel pam-devel libicu libicu-devel openldap-devel \
readline-devel libedit-devel libtidy libtidy-devel libxslt libxslt-devel expat* krb5-devel openssl-devel db4-devel <br>
    # sudo yum -y install libdb-devel <br>
    # sudo yum -y install db4* <br>

### 2. libmcrypt 설치

    # sudo wget https://sourceforge.net/projects/mcrypt/files/Libmcrypt/2.5.8/libmcrypt-2.5.8.tar.gz
    # sudo tar zxvf libmcrypt-2.5.8.tar.gz
    # cd libmcrypt-2.5.8
    # sudo ./configure
    # sudo make & make test;
    # sudo make install
    # sudo ln -s /usr/local/lib/libmcrypt.so.4 /usr/local/lib64/libmcrypt.so.4
    # cd ..

### 3. PHP 설치

    # sudo ln -s /usr/local/mysql/lib /usr/local/mysql/lib64
    # sudo wget http://cn2.php.net/distributions/php-5.6.4.tar.gz
    # sudo tar zxvf php-5.6.4.tar.gz
    # cd php-5.6.4
    # sudo ./configure --prefix=/usr/local/php \

    --with-apxs2=/usr/local/apache/bin/apxs \
    --enable-mod-charset \
    --with-config-file-path=/usr/local/php/lib \
    --enable-sigchild \
    --with-libxml-dir \
    --with-openssl \
    --with-zlib \
    --with-zlib-dir \
    --with-bz2 \
    --enable-calendar \
    --with-curl --enable-dba \
    --with-gdbm \
    --enable-exif \
    --enable-ftp \
    --with-gd \
    --with-jpeg-dir \
    --with-png-dir \
    --with-freetype-dir \
    --enable-gd-native-ttf \
    --with-gettext \
    --with-imap-ssl \
    --with-kerberos \
    --enable-mbstring \
    --with-mhash \
    --with-mcrypt \
    --with-mysql=/usr/local/mysql \
    --with-mysqli=/usr/local/mysql/bin/mysql_config \
    --enable-sockets \
    --with-regex=php \
    --enable-sysvmsg \
    --enable-sysvsem \
    --enable-sysvshm \
    --enable-wddx --enable-opcache=no --disable-fileinfo

    Error 발생 apxs was not found. Try to pass the path using --with-apxs2=/path/to/apxs

    # vi /usr/local/apache/bin/apxs
    #!/replace/with/path/to/perl/interpreter -w 를  #!/usr/bin/perl -w 로 수정
    # sudo make & make test
    # sudo make install

### 4. php.ini 파일 복사

    # sudo cp php.ini-production /usr/local/php/lib/php.ini

### 5. 시간대 설정

    # sudo vi /usr/local/php/lib/php.ini
    date.timezone = "Asia/Seoul" 으로 수정

### 6. PATH 등록

    # sudo ln -s /usr/local/php/bin/php /bin/php
    # sudo ln -s /usr/local/php/bin/php-config /usr/local/bin/php-config
    # sudo ln -s /usr/local/php/bin/phpize /usr/local/bin/phpize

## ETC

### 1. 원하는 시간대 찾기

    # sudo su
    # ls /usr/share/zoneinfo

### 2. 새 표준 시간대로 업데이트

    # ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
    # ls -al /etc/localtime

### 3. 재부팅

    # reboot

    Error 발생 Server unexpectedly closed network connection

    # service httpd start
