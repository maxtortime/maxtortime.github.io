---
category: python
layout: post
tags:
  - flask
  - 'i18n'
title: 'Flask-babel 사용법'
---

# 개요
flask-babel 은 flask 의 i18n 을 도와주는 유용한 라이브러리이다. timezone도
맞춰줄 수 있지만 여기서는 일단 문자열을 번역하는 기능을 설명하고자 한다.

# 사용법
* flask-babel 설치하기


```sh
$ pip install flask-babel
```
* Python 코드에서는 **gettext** , html 에서는 _('문자열') 을 써서 번역할 단어 표시


```python
gettext(u'A simple string')
gettext(u'Value: %(value)s', value=42)
ngettext(u'%(num)s Apple', u'%(num)s Apples', number_of_apples)
```

* 아래 명령어를 사용하면 아까 표시한 단어들이 messages.pot 에 표시됨


```sh
$ pybabel extract -F babel.cfg -o messages.pot .
```

* 아래 명령어를 사용하면 새로운 언어를 번역할 수 있음 [ISO 언어 코드](http://www.mcanerin.com/en/articles/meta-language.asp "ISO 언어 코드") 참고하기


```sh
$ pybabel init -i messages.pot -d translations -l 'iso 언어코드(ko,en 등)'
```

* 위 명령어 실행 후 **translations/언어코드/LC_MESSAGES/messages.po** 파일이 생기는데 적절히 번역기를 돌리든 본인의 뛰어난 어학 실력을 동원하든 해서 대응하는 단어를 번역하면 됨. ([Poedit](https://poedit.net/) 라는 프로그램 추천)

* 번역 후 아래 명령어를 실행시켜 mo 파일을 만든다


```sh
$ pybabel compile -d translations
```

* 자세한 건 [Flask-babel](https://pythonhosted.org/Flask-Babel/) 홈페이지를 참고하자!
* 새로운 언어를 추가했다면 config.py 의 LANGUAGES 변수에 대응하는 언어와 코드를 넣어줄 것
* 잘 됬나 테스트를 해보고 싶다면 [크롬 확장 프로그램](https://chrome.google.com/webstore/detail/quick-language-switcher/pmjbhfmaphnpbehdanbjphdcniaelfie) 추천 (locale 을 바꿔줌)
