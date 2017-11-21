---
layout: post
tags:
  - glusterfs
title: 'Building and Installing GlusterFS on CentOS 7'
category: install
---
사람의 기억력은 한계가 있고 했던 실수를 계속 반복한다고 한다. 그래서인지 GlusterFS를 빌드 할 때도 불과 몇 시간전에 깔았던 라이브러리를 어떻게 설치했었는지 까먹었던 것 같다. 이 글은 Centos 7 minimal 버전을 설치하고 난 직후에 바로 GlusterFS를 빌드한 후 설치하는 과정을 설명한다.

## 개요
GlusterFS는 확장성 있는 네트워크 파일 시스템으로 오픈소스이며 공짜다. 더 이상 설명하려면 사실 이 글을 쓰는 본인도 많이 써보질 않아서 여기서 생략한다..

## Building
먼저 [빌드 관련 공식문서](http://www.gluster.org/community/documentation/index.php/Building_GlusterFS "공식문서")와 [Quickstart 관련 공식문서](http://www.gluster.org/community/documentation/index.php/QuickStart "Quickstart 관련 공식문서")를 읽어보면 이 글을 안 읽어도 될 수 있다. 공식문서를 읽다보면 엄청나게 많은 걸 설치해야 한다는 걸 알 수 있다.  물론 그것도 당연히 설치해야 하지만 미리 수고를 덜기 위해서 문서에 있는 걸 해보기 전에  development tools를 설치하자.

```sh
$ yum groups install "development tools"
```
다음은 GlusterFS 3.6.0 버전 소스코드 압축파일을 찾아서 다운로드한다. 다음 ./autogen.sh 를 하면 알아서 configure 을 만들어준다.다음 ./configure 를 하면 에러가 발생할 것이다.

```sh
checking for ld... /usr/bin/ld -m elf_x86_64
checking for MD5 in -lcrypto... no
configure: error: OpenSSL crypto library is required to build glusterfs
```

이 쯤에서 첫 에러가 발생하므로 이제 공식문서에 있는 걸 깔아준다.
설치 후에 ./autogen.sh 를 한 번 더 해주자.

```sh
$ yum install automake autoconf libtool flex bison openssl-devel libxml2-devel python-devel libaio-devel libibverbs-devel librdmacm-devel readline-devel lvm2-devel glib2-devel userspace-rcu-devel libcmocka-devel libacl-devel
$ ./configure
$  make && make install 
```
\

이제 gluster라고 치면 gluster 콘솔이 나오는 걸 볼 수 있다.