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

-----

-----



## 2장 플라스크 개발 기초 공사

### 01. 플라스크 기초 다지기

#### 플라스크 프로젝트 구조

```
├── pybo/
│      ├─ __init__.py
│      ├─ models.py
│      ├─ forms.py
│      ├─ views/
│      │   └─ main_views.py
│      ├─ static/
│      │   └─ style.css
│      └─ templates/
│            └─ index.html
└── config.py
```



1. DB처리하는 models.py <br><br>



2. 서버로 전송된 폼을 처리하는 forms.py<br><br>



3. 화면 구성하는 views 디렉터리<br><br>



4. CSS, 자바스크립트, 이미지 파일을 저장하는 static 디렉터리<br><br>



5. HTML 파일을 저장하는 templates 디렉터리<br><br>



6. 프로젝트를 설정하는 config.py<br><br>



### 02. 플라스크 app팩토리

1. `myproject/pybo.py` 파일을 `myproject/pybo/__init__.py` 파일로 대체

```
(myproject) c:\projects\myproject> mkdir pybo
```

```
(myproject) c:\projects\myproject> move pybo.py pybo/__init__.py
         
(myproject) c:\projects\myproject>flask run
```

<br><br>

2. 애플리케이션 팩토리 사용

`myproject/pybo/__init__.py` 파일을 열고 create_app 함수를 선언하는 방식으로 코드를 수정

```
from flask import Flask

def create_app():
    app = Flask(__name__)

    @app.route('/')
    def hello_pybo():
        return 'Hello, Pybo!'

    return app
```

<br><br>

### 03. 블루프린트로 라우트 함수 관리하기

1. 블루프린트 생성하기

   `__init__.py` 파일의 hello_pybo 함수에 블루프린트를 적용

```
(myproject) c:\projects\myproject> cd pybo
(myproject) c:\projects\myproject\pybo> mkdir views
```

​		

​		views 디렉터리에 main_views.py 파일을 다음과 같이 작성

myproject/pybo/views/main_views.py

```
from flask import Blueprint

bp = Blueprint('main', __name__, url_prefix='/')


@bp.route('/')
def hello_pybo():
    return 'Hello, Pybo!'
```

<br><br>

2. 플라스크 앱 생성시 블루프린트 적용하기

1단계에서 생성한 블루프린트 파일을 적용하기 위해 `__init__.py` 파일을 다음과 같이 수정

myproject/pybo/__init__.py

```
from flask import Flask
def create_app():
    app = Flask(__name__)

    from .views import main_views
    app.register_blueprint(main_views.bp)

    return app

```

<br><br>

3. 라우트 함수 등록하기

myproject/pybo/views/main_views.py

```
from flask import Blueprint

bp = Blueprint('main', __name__, url_prefix='/')


@bp.route('/hello')
def hello_pybo():
    return 'Hello, Pybo!'


@bp.route('/')
def index():
    return 'Pybo index'
```

<br><br>

### 04. 모델로 데이터 베이스 처리하기

#### 데이터베이스를 쉽게 사용할 수 있게 해주는 ORM 알아보기

*[question 테이블 구성 예]*

| id   | subject       | content              |
| :--- | :------------ | :------------------- |
| 1    | 안녕하세요    | 가입 인사드립니다 ^^ |
| 2    | 질문 있습니다 | ORM이 궁금합니다     |
| ...  | ...           | ...                  |

<br><br>

*[쿼리를 이용한 새 데이터 삽입 예]*

```
insert into question (subject, content) values ('안녕하세요', '가입 인사드립니다 ^^');
insert into question (subject, content) values ('질문 있습니다', 'ORM이 궁금합니다');
```

<br><br>

하지만 ORM을 사용하면 쿼리 대신 파이썬 코드로 다음처럼 작성할 수 있다.

*[ORM을 이용한 새 데이터 삽입 예]*

```
question1 = Question(subject=’안녕하세요’, content='가입 인사드립니다 ^^')
db.session.add(question1)
question2 = Question(subject=’질문 있습니다’, content='ORM이 궁금합니다')
db.session.add(question2)
```

<br><br>



#### 플라스크 ORM 라이브러리 사용하기

1. ORM 라이브러리 설치하기

```
pip install Flask-Migrate
```

오류날시 다운그레이드

```
pip install SQLAlchemy==1.3.23
```

<br><br>

2. 설정 파일 추가하기

myproject/config.py

```
import os

BASE_DIR = os.path.dirname(__file__)

SQLALCHEMY_DATABASE_URI = 'sqlite:///{}'.format(os.path.join(BASE_DIR, 'pybo.db'))
SQLALCHEMY_TRACK_MODIFICATIONS = False
```

<br><br>

3. ORM 적용하기

이어서 `pybo/__init__.py` 파일을 수정해 SQLAlchemy를 적용하자.

myproject/pybo/__init__.py

```
from flask import Flask
from flask_migrate import Migrate
from flask_sqlalchemy import SQLAlchemy

import config

db = SQLAlchemy()
migrate = Migrate()


def create_app():
    app = Flask(__name__)
    app.config.from_object(config)

    # ORM
    db.init_app(app)
    migrate.init_app(app, db)

    # 블루프린트
    from .views import main_views
    app.register_blueprint(main_views.bp)

    return app
```

<br>

<br>

4. 데이터베이스 초기화하기

이제 ORM을 사용할 준비가 되었으므로 `flask db init` 명령으로 데이터베이스를 초기화하자.

```
(myproject) c:\projects\myproject>flask db init
```

migration 폴더 자동으로 생성해줌

<br><br>

데이터 베이스 관리 명령어 정리

| 명령어             | 설명                                                         |
| :----------------- | :----------------------------------------------------------- |
| `flask db migrate` | 모델을 새로 생성하거나 변경할 때 사용 (실행하면 작업파일이 생성된다.) |
| `flask db upgrade` | 모델의 변경 내용을 실제 데이터베이스에 적용할 때 사용 (위에서 생성된 작업파일을 실행하여 데이터베이스를 변경한다.) |

<br><br>

#### 모델 만들기

1. 모델 속성 구상하기

*[질문 모델 속성]*

| 속성명      | 설명                    |
| :---------- | :---------------------- |
| id          | 질문 데이터의 고유 번호 |
| subject     | 질문 제목               |
| content     | 질문 내용               |
| create_date | 질문 작성일시           |

*[답변 모델 속성]*

| 속성명      | 설명                                                         |
| :---------- | :----------------------------------------------------------- |
| id          | 답변 데이터의 고유 번호                                      |
| question_id | 질문 데이터의 고유 번호(어떤 질문에 달린 답변인지 알아야 하므로 질문 데이터의 고유 번호가 필요하다) |
| content     | 답변 내용                                                    |
| create_date | 답변 작성일시                                                |

<br><br>

2. 질문 모델 생성하기

myproject/pybo/models.py

```
from pybo import db

class Question(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    subject = db.Column(db.String(200), nullable=False)
    content = db.Column(db.Text(), nullable=False)
    create_date = db.Column(db.DateTime(), nullable=False)
```

<br><br>

3. 답변 모델 생성하기

myproject/pybo/models.py

```
from pybo import db

class Question(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    subject = db.Column(db.String(200), nullable=False)
    content = db.Column(db.Text(), nullable=False)
    create_date = db.Column(db.DateTime(), nullable=False)

class Answer(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    question_id = db.Column(db.Integer, db.ForeignKey('question.id', ondelete='CASCADE'))
    question = db.relationship('Question', backref=db.backref('answer_set'))
    content = db.Column(db.Text(), nullable=False)
    create_date = db.Column(db.DateTime(), nullable=False)
```

question_id 속성은 질문 모델과 연결하려고 추가

어떤 질문에 대한 답변인지 알아야 하므로 2단계에서 생성한 질문 모델과 연결된 속성을 포함

이처럼 어떤 속성을 기존 모델과 연결하려면 `db.ForeignKey`를 사용

<br><br>

question 속성은 답변 모델에서 질문 모델을 참조하기 위해 추가

답변 모델 객체에서 질문 모델 객체의 제목을 참조하려면 `answer.question.subject`처럼 할 수 있다. 이렇게 하려면 속성을 추가할 때 `db.Column`이 아닌 `db.relationship`을 사용해야 한다.

<br><br>

**파이썬 코드를 이용하여 질문 데이터 삭제 시 연관된 답변 데이터 모두 삭제하는 방법**

```
question = db.relationship('Question', backref=db.backref('answer_set', cascade='all, delete-orphan'))
```

