# flask

## 1장 플라스크 개발준비!

### 첫번째 애플리 케이션 만들기

#### 1. 새 파이썬 파일 만들기

myproject/pybo.py

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_pybo():
    return 'Hello, Pybo!'
```



#### 2. 플라스크 서버 실행

commend

```
(myproject) c:\projects\myproject>flask run
```

1단계를 마쳤다면 가상 환경에서 `flask run` 명령을 실행해 플라스크 개발서버를 실행

but!오류난다. 그이유는 플라스크 서버를 실행하려면 반드시 `FLASK_APP`이라는 환경 변수에 플라스크 애플리케이션을 지정해 주어야 한다.



#### 3. 기본 앱 설정

commend

```commend
(myproject) c:\projects\myproject>set FLASK_APP=pybo
```

위 과정후 

```
(myproject) c:\projects\myproject>flask run
```

실행잘됨



#### 4. 플라스크 서버를 개발 환경으로 실행

commend

```
(myproject) c:\projects\myproject>set FLASK_ENV=development
```



#### 5. 실습 더 간편하게

플라스크 서버 실행시 다음 명령어 매번 입력해야함.

```
(myproject) c:\projects\myproject> set FLASK_APP=pybo
(myproject) c:\projects\myproject> set FLASK_ENV=development
(myproject) c:\projects\myproject> flask run
```



그과정을 줄여주는 방법

[파일명: C:/venvs/myproject.cmd]

```
@echo off
cd c:/projects/myproject
set FLASK_APP=pybo
set FLASK_ENV=development
c:/venvs/myproject/scripts/activate
```

