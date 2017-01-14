---
tags: []
layout: post
title: 'Dupremove 사용해보기'
category: Uncategoried
---
 Dupremove 는 [Btrfs](https://ko.wikipedia.org/wiki/Btrfs "Btrfs") 의 offline deduplication 을 위해 개발된 툴이지만 굳이 Btrfs 파일 시스템이 아니더라도 사용은 가능하다. (btrfs 용 기능은 존재함)
 
설치를 위해 github에서 [소스 코드를 클론](https://github.com/markfasheh/duperemove "소스 코드를 클론") 하자.

다음 그냥 디렉토리에서 빌드 하면 되지만 몇 가지 dependencies 가 없을 수도 있다.