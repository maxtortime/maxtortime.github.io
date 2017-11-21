---
layout: post
title: "GitHub Pages 도메인은 namecheap로!"
date: "2017-04-07 23:26:00 +0900"
tags:
    - jekyll
    - namecheap
    - domain
    - https
    - Github pages
---

이 글을 보러 블로그에 오신 분들은 알겠지만 주소창을 보면 `github.io`가 아니라 `.me` 도메인을 사용하고 있는 걸 볼 수 있습니다. 이 글에서는 학교 이메일 주소만 가지고 있다면 `namecheap`를 이용해서 쉽게 도메인을 적용하는 법을 설명하려고 합니다.

## .me 도메인 적용하기
먼저 [Github Student Pack](https://education.github.com/pack)에 접속하셔서 인증하는 절차가 필요합니다. 인증을 성공적으로 하셨다면 어래로 내려보시면 제가 아까 말씀드린 `namecheap`라는 게 보입니다. namecheap에서는 2가지 혜택을 제공하고 있는데요. SSL 인증서 제공과 도메인 제공입니다. 하지만 SSL 인증서는 아래에서 설명드리겠지만 GitHub Pages에서 쓰는 게 불가능합니다. 일단 `Get access by connecting your GitHub account on Namecheap`에서 링크를 클릭하고 GitHub 로그인까지 해줍니다. 그 이후 과정은 정말 쉽습니다. 원하시는 주소를 입력하시고 (.me만 공짜입니다!)Complete Order 버튼을 클릭하시면 알아서 GitHub pages에 연결해주는 설정까지 도맡아서 해줍니다. 그리고 어느 정도 시간이 흐르고 들어가 보시면 적용됩니다.

## https 적용하기
사실 블로그에 https 가 필요 있나 할 수도 있지만 왠지 크롬을 키고 주소 왼쪽에 `안전함` 표시와 함께 좜루쇠가 표시되어 있다면 무척 뿌듯한 느낌이 들죠.. 그래서 저도 그 뿌듯한 감정을 가져보기 위해서 위에서 받은 SSL 인증서를 이용해 HTTPS를 적용해보려고 했습니다. 하지만 제가 막힌 부분은 인증서를 활성화하기 위해 서버에서 CSR 토큰(https://en.wikipedia.org/wiki/Certificate_signing_request)을 받아오는 부분이였습니다. 왜냐하면 제가 임의의 토큰을 받아다 넣으면 되는 부분이 아니였기 때문이죠. 잘 생각해보면 GitHub pages는 깃헙에서 호스팅을 해주는 거라 받아올 수 있는 방법이 없었던 겁니다.. 그래서 namecheap에 문의를 해본 결과.. 친절하게도 그냥 클라우드플레어를 쓰라고 해주셨습니다. 그래서 `CloudFlare`에 도메인을 등록하고 나서 `HSTS` 관련 설정을 만져주니 잘 동작하더군요.

## 결론
1. `namecheap`는 좋은 서비스였다!
2. GItHub Pages에 도메인을 달고 나서 SSL 인증서를 활성화하려면 `CloudFlare`를 쓰자 (다만 아직 자동으로 https로 연결이 안 되는데 이 부분을 연구해보겠습니다.)
