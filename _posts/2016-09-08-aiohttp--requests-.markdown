---
layout: post
title: "Aiohttp로 대량의 requests 처리하기"
date: "2016-09-08 01:42:23 +0900"
---
# 목차
* *Problem: URL 100군데에 GET 요청을 보내보자.*
* *Solution 1: for loop*
* *Solution 2: threading and queue*
* *Solution 3: aiohttp*
* *aiohttp의 기능*
* *Generator 와 Coroutine*
* *asyncio의 동작 방식*
* *async, blocking? 짚고 넘어가자*


# Problem: URL 100개에 GET 요청 보내기
* http://httpbin.org/get 에 1부터 100까지 숫자로 쿼리를 날려서 총 100번의 GET요청을 보내야 한다.
* 최대한 빠르고 간결한 코드를 짜는 것을 목표로 한다.

# Sol. 0: for loop
```python
import requests
import timeit

url_basis = 'http://httpbin.org/get?'
start = timeit.default_timer()

for i in range(100):
    url = url_basis + str(i)
    print('Start', url)
    r = requests.get(url)
    print('Done', url)

duration = timeit.default_timer()-start

print(duration)
```
# Sol 0: 해설
* for 문을 돌며 하나씩 GET 요청을 보내는건 가장 쉽게 생각할 수 있는 해법이다.
* 하지만 한 번에 한 URL 밖에 처리할 수 없기 때문에 무척 비효율적이다.
* 그래서 무려 **40.84**초라는 시간이 소모되었다.

# Sol. 1: threading and queue
* `threading` 은 파이썬 표준 라이브러리에 포함된 High-level 쓰레딩 인터페이스이다.
* `Queue`는 여러 쓰레드들이 자원을 공유할 때 유용하게 쓰일 수 있는 파이썬 표준 라이브러리에 포함된 FIFO queue이다. (물론 다른 방식의 queue도 구현되어 있음)

# 잠깐! 쓰레드는 뭔가요?
* 개발자 입장에서 보면 쓰레드라는 건 내 몸에 팔이 2개 달려 있는 거랑 비슷하다고 생각하면 된다.
* 우리의 팔은 같은 몸(주소공간)을 공유하지만 몸과는 독립적으로 작업을 수행할 수 있다.
* 즉 개발자 입장에서는 원래 프로그램과 독립된 개별적인 작업 흐름이라고 생각하면 됨.
* 너무 자세한 설명은 이 세션의 목적을 벗어나므로 운영체제 수업을 듣거나 [이곳(pthread tutorial)](https://computing.llnl.gov/tutorials/pthreads/#Thread)을 참고하면 좋다.

# 예제 코드
```python
import threading
import queue
import requests
import timeit

thread_num = 100

def doWork():
    while True:
        print(threading.current_thread().name)
        url = q.get()
        print(threading.current_thread().name,url)
        r = requests.get(url)
      	print(threading.current_thread().name,r.status_code)
        q.task_done()

start = timeit.default_timer()

q = queue.Queue(thread_num)

for i in range(thread_num):
    t = threading.Thread(target=doWork)
    t.daemon = True
    t.start()

for i in range(thread_num):
    q.put('http://httpbin.org/get?key=' + str(i))

q.join()
duration = timeit.default_timer() - start
print(duration)
```

# 코드 동작 해설
* 먼저 쓰레드를 `thread_num` 만큼 `doWork`를 호출하도록 실행함.
* `doWork`가 호출되지만 아직 queue가 비어있는 상태이므로 쓰레드들은 계속 루프를 돈다. (queue가 비어있으면 lock을 걸어놓고 있다)
* queue에 `thread_num` 만큼의 URL을 넣는다.
* `q.join()`으로 쓰레드들이 계속 `doWork`의 무한루프를 돌게 한다.
* `doWork`에서 request를 수행하고 결과를 출력한 후 `task_done()`을 할 때마다 queue는 task가 처리된걸로 본다.
* 이렇게 `thread_num` 만큼의 job을 전부 수행하면 `q.join()`의 lock이 풀리고 프로그램이 종료된다.

# 그래서 그냥 threading을 쓰면 안 되나?..
써도 되는데 생각해볼 점이 있다. (참고로 동작에 1.25초 걸림)

1. *성능 문제* : 파이썬의 메모리 관리가 thread safe 하지 않기 때문에 GIL(Global Interpreter Lock)이라는 걸 만들어놨다. 덕분에 실험 결과를 보면 [성능이 2배](http://www.dabeaz.com/python/GIL.pdf) 정도 느려진다.

2. *많은 비용* : 쓰레드는 프로세스 stack에 자신의 독립된 주소공간을 마련하기 때문에 메모리가 무척 많이 든다. 그리고 스레드를 시작하는 비용 자체도 많이 든다.


# Solution 2. Aiohttp
멀티쓰레드를 사용하니 빨리 해결할 수 있었지만 전술한 문제점이 존재한다. 그래서 이 솔루션에서는  새롭게 파이썬 표준에 추가된 `asyncio`를 이용해 개발된 `aiohttp`를 이용하려고 한다.

# AIOHTTP는 무엇인가?
[aiohttp]()는 Python 3.4부터 표준 라이브러리로 추가된 [asyncio](https://docs.python.org/dev/library/asyncio.html) [(PEP3156)](https://www.python.org/dev/peps/pep-3156/) 를 위한 HTTP 서버/클라이언트 프레임워크이다.

## aiohttp 의 다양한 기능
aiohttp는 유용한 다수의 기능을 갖추고 있다.
- Client/Server API
- Persistent connections
- Websockets
- ... (PyCon 슬라이드 참고)

# aiohttp로 구축한 서버와 클라이언트
`flask`처럼 간단한 웹 서버를 구축하는 것도 가능하다.

```python
from aiohttp import web

async def handle(request):
    name = request.match_info.get('name', "Anonymous")
    text = "Hello, " + name
    return web.Response(body=text.encode('utf-8'))

app = web.Application()
app.router.add_route('GET', '/{name}', handle)

web.run_app(app)
```

# 클라이언트 구축
```python
import asyncio
import aiohttp

async def fetch_page(session, url):
    with aiohttp.Timeout(10):
        async with session.get(url) as response:
            assert response.status == 200
            return await response.read()

loop = asyncio.get_event_loop()
with aiohttp.ClientSession(loop=loop) as session:
    content = loop.run_until_complete(
        fetch_page(session, 'http://python.org'))
    print(content)
```

이외에도 아직 소개하지 않은 많은 API와 기능이 있으므로 [aiohttp 공식문서](http://aiohttp.readthedocs.io/en/stable/index.html)를 참고하시면 됩니다.

# 그렇다면 Solution 2
```python
import asyncio
from aiohttp import ClientSession

async def hello(url):
    async with ClientSession() as session:
        async with session.get(url) as response:
            r = await response.read()
            print(r)

loop = asyncio.get_event_loop()

tasks = []

url = 'http://httpbin.org/get?{0}'
for i in range(100):
    task = asyncio.ensure_future(hello(url.format(i)))
    tasks.append(task)

loop.run_until_complete(asyncio.wait(tasks))
# ...
# 0.6390966979670338
```

# 결과 분석
* Solution 1보다 훨씬 코드가 짧고 간결하다.
* 동작 속도도 0.63초로 `threading`을 사용하는 것보다 빨랐다
* 뭔가 새로운 예약어들이 생겼다. `async`? `await`?


# `async`, `await`가 뭐지?
* `async def`는 코루틴을 정의하기 위한 키워드.
* `reponse.read()`는 코루틴이므로 여기서의 `await`는 다른 코루틴이 결과값을 만들때까지 기다리는 용도이다.
* 또한 `await future`인 경우에도 코루틴이 `future`가 작업을 완료할 때까지 기다리다가 값을 받아오게 된다.


# Generator & Coroutine
혹을 떼려다 혹을 붙인 느낌으로 모르는 단어만 점점 나오고 있다.
코루틴을 이해하기 위해서는 먼저 Generator에 대한 이해가 필수적이다.

```python
def myrange(n):
    i = 0

    while(i<n):
        yield i
        i+=1

for x in myrange(10):
    print(x)

test = myrange(20)
print(test)

print(next(test)) # 0
print(next(test)) # 1
```

# Python Generator?
* 위의 `myrange` 함수가 수행되다가 `yield`를 만나게 되면 `i`를 반환한다. 그리고 끝나는 게 아니라 그 상태에서 머물게 된다.

* 보통의 함수는 호출되면 `return`을 만나거나 블록의 끝까지 수행되고 나면 끝이다.

* 하지만 제너레이터의 경우 `next()`로 함수를 `yield`까지 가게 해서 계속 함수를 수행시킬 수 있다. 그리고 더 이상 `yield`까지 갈 수 없을 경우 `StopIteration` 예외를 내며 끝난다.

# Coroutine이란?
* [코루틴](https://en.wikipedia.org/wiki/Coroutine)이란 위의 제너레이터처럼 진입점(Entry points)이 여러개인 함수를 말한다.
* 보통 우리가 작성하는 함수([Subroutine](https://en.wikipedia.org/wiki/Subroutine))는 처음 호출된 시점이 유일한 진입점이다.
* 그러나 제너레이터는 `next()`를 호출할 때마다 그 함수에 진입할 수 있다.

# 그래서 asyncio랑 무슨 상관이죠?
발표자를 포함해서 Solution 2는 이미 까먹었을 우리들의 이해를 돕기 위해 [예제 코드](https://docs.python.org/3/library/asyncio-task.html)를 하나 더 준비했다.

```python
import asyncio

async def compute(x, y):
    print("Compute %s + %s ..." % (x, y))
    await asyncio.sleep(1.0)
    return x + y

async def print_sum(x, y):
    result = await compute(x, y)
    print("%s + %s = %s" % (x, y, result))

loop = asyncio.get_event_loop()
loop.run_until_complete(print_sum(1, 2))
loop.close()
```

# 코드 해설
* `asyncio.get_event_loop` 에서 이벤트 루프를 초기화한다.
* `run_until_complete` 함수는 `print_sum` 코루틴이 작업을 마칠때까지(`StopIteration` 예외를 던질 때까지) 기다려줌.
* `await compute(1,2)`를 만나면 그 상태로 멈추고 `compute`가 작업을 마칠때까지 기다린다.
* 그런데 `compute`에서 1초를 슬립하라는 함수가 있다.. 역시 그 상태로 멈추고 나서 1초후 재개된다.

# 예제코드 참고 그림
![](https://docs.python.org/3/_images/tulip_coro.png)

# 그러면 어떻게 동시에?
Solution 2를 다시 보면...

- `asyncio.ensure_futrue()` 는 코루틴(`async def`로 정의된)을 asyncio가 스케쥴링 할 수 있는 `Task` 오브젝트(`Future`의 서브클래스)로 wrapping 해s준다.
- 다음 asyncio.wait() 는 이 `Task`들의 list인 `tasks`가 전부 끝날때까지 기다린다.
- `run_until_complete`는 `asyncio.wait()`가 'done'이라는 Future를 반환할때까지 계속 실행된다.
