---
layout: default
---

## Jekyll을 세팅하지 않고 마크다운만으로 블로그를 할 수 있다?
뭔가 어그로를 잔뜩 끈 제목이였지만 사실 엄청 신기한 내용은 아니다. Github Pages는 [Jekyll](https://jekyllrb-ko.github.io/) 을 호스팅하여 무료로 손쉽게 홈페이지를 만들 수 있는 무척 좋은 도구다. 문제는 한 번이라도 Jekyll을 써보신 분은 아시곘지만 내 컴퓨터에서 Jekyll 을 구동시키는 것은 여간 까다로운 일이 아니다. 왜냐하면 Jekyll은 Ruby로 개발되었는데 Ruby는 윈도우에서 돌리기가 여간 짜증나는 언어가 아니기 때문이다 (요즘은 나아졌다면 말씀주세요) 그리고 gem이라는 Python에 해당하면 pip에 해당하는 명령어도 공부해야 하고 bundler라는 루비 의존성 도구도 공부해야 한다. 물론 README에 써있는대로 하면 된다지만 만약 본인이 개발자가 아니라면 또 루비 개발자가 아니라면 무척 귀찮은 과정이라고 볼 수 있다.

그렇다면 어떻게 내 컴퓨터에 루비를 설치하지 않고 Jekyll로 블로그를 할 수 있을까? 해답은 간단한데 모든 Github Pages는 .nojekyll 파일이 루트에 없는 이상 무조건 Jekyll로 돌아가는 구조이기 때문이다. 따라서 당당하게 README.md 만 달랑 올려놓고 Github Pages 를 활성화시킨 다음 브라우저로 들어가보면 이미 head 에 Jekyll이 세팅된듯한 냄새를 맡을 수 있을 것이다. 당신이 굳이 Jekyll Starter Kit 같은 걸 쓰지 않아도 이미 Github Pages 서버는 Jekyll로 마크다운을 호스팅해주고 있는 것이다. 다만 올리기 전 미리보기를 하고 싶으면 로컬에 루비를 당연히 세팅해야 한다. 

이 블로그의 구조는 https://github.com/maxtortime/maxtortime.github.io 에서 확인하면 되고 https://pages-themes.github.io/minimal/ 테마를 사용했는데 무척 간단하게 사용이 가능하여 심플하게 블로그를 구성하고 싶은 분들께 추천드리고 싶다.