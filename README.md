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

def create_app():# 추가
    app = Flask(__name__)# 추가

    @app.route('/')# 추가
    def hello_pybo():# 추가
        return 'Hello, Pybo!'# 추가

    return app# 추가
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

    from .views import main_views# 추가
    app.register_blueprint(main_views.bp)# 추가

    return app

```

<br><br>

3. 라우트 함수 등록하기

myproject/pybo/views/main_views.py

```
from flask import Blueprint

bp = Blueprint('main', __name__, url_prefix='/')


@bp.route('/hello')# 추가
def hello_pybo():
    return 'Hello, Pybo!'


@bp.route('/')# 추가
def index():# 추가
    return 'Pybo index'# 추가
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
from flask_migrate import Migrate# 추가
from flask_sqlalchemy import SQLAlchemy# 추가

import config# 추가

db = SQLAlchemy()# 추가
migrate = Migrate()# 추가


def create_app():
    app = Flask(__name__)
    app.config.from_object(config)# 추가

    # ORM
    db.init_app(app)# 추가
    migrate.init_app(app, db)# 추가

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

class Answer(db.Model): # 추가
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

<br><br>

#### 모델을 이용해 테이블 자동으로 생성하기

1. 모델 가져오기

앞선 실습에서 생성한 모델들을 플라스크의 Migrate 기능이 인식할 수 있도록 `pybo/__init__.py` 파일을 수정하자.

myproject/pybo/__init__.py

```
(... 생략 ...)

    # ORM
    db.init_app(app)
    migrate.init_app(app, db)
    from . import models # 추가

(... 생략 ...)
```

<br><br>

2. 데이터베이스 변경을 위한 리비전 파일 생성하기

이제 명령 프롬프트에서 `flask db migrate` 명령을 수행

commend

```
(myproject) c:\projects\myproject> flask db migrate
INFO  [alembic.runtime.migration] Context impl SQLiteImpl.
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
INFO  [alembic.autogenerate.compare] Detected added table 'question'
INFO  [alembic.autogenerate.compare] Detected added table 'answer'
Generating C:\python_study\do_it_flask\myproject\migrations\versions\1b03657ec5cc_.py ...  done
```

위명령 수행시 versions 폴더에 py파일 생성됨

<br><br>

3. 데이터 베이스 갱신하기

이어서 `flask db upgrade` 명령으로 리비전 파일을 실행하자.

```
(myproject) c:\projects\myproject> flask db upgrade
INFO [alembic.runtime.migration] Context impl SQLiteImpl.
INFO [alembic.runtime.migration] Will assume non-transactional DDL.
INFO [alembic.runtime.migration] Running upgrade -> 18634a293520, empty message
```

<br><br>

#### 생성된 테이블 살펴보기

1. DB Browser for SQLite 설치하기

https://sqlitebrowser.org/dl 에 접속한 다음 DB Browser for SQLite(이하 DB 브라우저) 설치 파일(standard installer)을 내려받아 설치를 진행하자. 설치 시 체크 옵션에 주의(SQLite 부분 Desktop & Program Menu만 체크)

<br><br>

2. DB 브라우저에서 pybo.db 열기

윈도우 바탕화면이나 프로그램 메뉴에서 방금 설치한 DB 브라우저를 실행하고 메뉴에서 [파일 → 데이터베이스 열기]를 선택한다. 그리고 앞선 실습에서 생성한 `C:/projects/myproject/pybo.db` 데이터베이스 파일을 선택하고 `<열기>`를 누른다.

<br><br>

테이블 목록을 보면 question, answer 테이블이 생성되었음을 확인할 수 있다.

<br><br>



#### 모델 사용하기

1. 플라스크 셸 실행하기

commend

```
(myproject) c:\projects\myproject>flask shell
Python 3.8.5 (tags/v3.8.2:7b3ab59, Feb 25 2020, 22:45:29) [MSC v.1916 32 bit (Intel)] on win32
App: pybo [development]
Instance: C:\projects\myproject\instance
>>> 
```

<br><br>

2. 질문 데이터 저장하기

다음 명령을 수행해 Question과 Answer 모델을 플라스크 셸에 불러오자. 그런 다음 Question 모델 객체를 하나 생성해 보자.

commend(이어서)

```
>>> from pybo.models import Question, Answer
>>> from datetime import datetime
>>> q = Question(subject='pybo가 무엇인가요?', content='pybo에 대해서 알고 싶습니다.', create_date=datetime.now())
```

Question 모델의 create_date 속성은 DateTime 유형이므로 `datetime.now` 함수로 현재 일시를 대입했다. 객체 q를 만들었다고 해서 데이터베이스에 저장되는 것은 아니다. 데이터베이스에 저장하려면 다음처럼 SQLAlchemy의 db 객체를 사용해야 한다.

```
>>> from pybo import db
>>> db.session.add(q)
>>> db.session.commit()
```

코드에서 보듯 신규 데이터를 저장할 때는 add 함수를 사용한 다음 commit 함수까지 실행해야 한다. `db.session`은 데이터베이스와 연결된 세션, 즉 접속된 상태를 의미한다. 데이터베이스를 처리하려면 이 세션이 필요하다. 그리고 세션을 통해서 데이터를 저장, 수정, 삭제 작업을 한 다음에는 반드시 `db.session.commit()`으로 커밋을 해주어야 한다.

<br><br>

여기서 주의할 점은 커밋은 취소할 수 없다는 것이다. 커밋은 일종의 "결정사인" 역할을 한다고 생각하면 이해하기 쉽다. 그래서 수행한 작업을 취소하려면 커밋 이전(세션 작업 도중)에 진행해야 한다. 이때 작업을 취소하고 싶으면 `db.session.rollback()`으로 되돌리기(롤백)를 실행하면 된다.

<br><br>

데이터가 잘 생성되었는지 확인해 보자.

```
>>> q.id
1
```

id는 Question 모델의 기본 키다. id는 앞에서 모델을 생성할 때 설정했던대로 데이터를 생성할 때 속성값이 자동으로 1씩 증가한다. 정말 그런지 두 번째 질문 데이터를 생성한 후 id를 확인해 보자.

```
>>> q = Question(subject='플라스크 모델 질문입니다.', content='id는 자동으로 생성되나요?', create_date=datetime.now())
>>> db.session.add(q)
>>> db.session.commit()
>>> q.id
2
```

<br><br>



#### 데이터 조회하기

이번에는 데이터베이스에 저장된 데이터를 조회해 보자.

```
>>> Question.query.all()
[<Question 1>, <Question 2>]
```

`Question.query.all()`로 데이터베이스에 저장된 질문 데이터 전부를 조회했다. 이 함수는 Question 객체 리스트를 반환한다. 결과에서 보이는 숫자 1과 2는 Question 객체의 id 속성값이다.

이번에는 filter 함수를 이용해 첫 번째 질문 데이터만 조회하자.

```
>>> Question.query.filter(Question.id==1).all()
[<Question 1>]
```

filter 함수는 인자로 전달한 조건에 맞는 데이터를 모두 반환해 준다. 여기서는 기본 키인 id를 이용했으므로 값을 1개만 반환한다.

id는 유일한 값이므로 filter 함수 대신 get 함수를 이용해 조회할 수도 있다.

```
>>> Question.query.get(1)
<Question 1>
```

다만 get 함수로 조회하면 리스트가 아닌 Question 객체 1개만 반환된다.

이번에는 `filter`와 `like`로 subject 속성에 "플라스크"라는 문자열이 포함된 데이터를 조회해 보자.

```
>>> Question.query.filter(Question.subject.like('%플라스크%')).all()
[<Question 2>]
```

"플라스크"라는 문자열이 포함된 두 번째 질문만 반환되었음을 확인할 수 있다. filter 함수에 전달한 `Question.subject.like('%플라스크%')` 코드의 의미는 Question 모델 subject 속성에 "플라스크"라는 문자열이 포함되었는가?이다. 이때 like 함수에 전달한 문자열에 붙은 `%` 표기는 다른 문자열을 포함하는지를 나타낸다.

- `플라스크%`: "플라스크"로 시작하는 문자열
- `%플라스크`: "플라스크"로 끝나는 문자열
- `%플라스크%`: "플라스크"를 포함하는 문자열

> 대소 문자 구분하지 않고 문자열을 찾으려면 like 함수 대신 ilike 함수를 사용한다.

데이터를 조회하는 다양한 사용법은 SQLAlchemy공식 문서를 참조하자.

> SQLAlchemy 공식 문서: https://docs.sqlalchemy.org/en/13/orm/query.html

<br><br>



4. 데이터 수정하기

이번에는 질문 데이터를 수정해 보자. 데이터를 수정할 때는 단순히 대입 연산자를 사용하면 된다.

```
>>> q = Question.query.get(2)
>>> q
<Question 2>
>>> q.subject = 'Flask Model Question'
>>> db.session.commit()
```

두 번째 질문 데이터를 조회한 다음 subject 속성을 수정했다. 앞에서 설명했듯이 데이터를 변경한 후에는 반드시 커밋을 수행해야 데이터베이스에 반영된다.

<br><br>



5. 데이터 삭제하기

이어서 데이터를 삭제하는 것도 실습해 보자. 여기서는 첫 번째 질문을 삭제하자.

```
>>> q = Question.query.get(1)
>>> db.session.delete(q)
>>> db.session.commit()
```

첫 번째 데이터를 조회한 다음 delete 함수를 이용해 삭제했다. 이 역시도 커밋을 수행해 데이터베이스에 데이터 삭제 작업을 반영했다.

이어서 실제로 데이터베이스에서 첫 번째 질문이 삭제되었는지 확인해 보자.

```
>>> Question.query.all()
[<Question 2>]
```

첫 번째 질문이 삭제되어서 두 번째 질문만 조회되었다.

<br><br>



6. 답변 데이터 생성 후 저장하기

이번에는 답변(Answer) 데이터를 생성하고 저장해 보자.

```
>>> from datetime import datetime
>>> from pybo.models import Question, Answer
>>> from pybo import db
>>> q = Question.query.get(2)
>>> a = Answer(question=q, content='네 자동으로 생성됩니다.', create_date=datetime.now())
>>> db.session.add(a)
>>> db.session.commit()
```

답변 데이터를 생성하려면 질문 데이터가 필요하므로 우선 질문 데이터를 구해야 한다. id가 2인 질문 데이터를 가져온 다음 q에 저장했다. 그런 다음 Answer 모델의 question 속성에 방금 가져온 q를 대입해 답변 데이터를 생성했다.

Answer 모델에는 어떤 질문에 해당하는 답변인지 연결할 목적으로 question_id 속성이 있다. Answer 모델의 객체를 생성할 때 question에 q를 대입하면 question_id에 값을 지정하지 않아도 자동으로 입력되어 편리하다.

Answer도 Question 모델과 마찬가지로 id 속성이 기본 키이므로 값이 자동으로 생성된다. 다음 명령으로 id값을 확인해 보고 이 값을 이용해 데이터도 확인해 보자.

```
>>> a.id
1
>>> a = Answer.query.get(1)
>>> a
<Answer 1>
```

<br><br>



7. 답변에 연결된 질문 찾기 vs 질문에 달린 답변 찾기

앞에서 구성한 Answer 모델의 question 속성을 이용하면 "답변에 연결된 질문"을 조회할 수 있다.

```
>>> a.question
<Question 2>
```

답변에 연결된 질문 찾기는 Answer 모델에 question 속성이 정의되어 있어서 매우 쉽다. 그런데 반대의 경우도 가능할까? 즉, 질문에서 답변을 찾을수 있을까?

Question 모델과 Answer 모델은 현재 연결된 상태이고, Answer 모델의 question 속성에 역참조 설정 `backref=db.backref('answer_set')`을 적용했다. 그러므로 이를 사용하면 질문과 연결된 답변을 쉽게 가져올 수 있다.

```
>>> q.answer_set
[<Answer 1>]
```

지금 여러분은 역참조 설정의 유용함을 별로 느끼지 못할 것이다. 하지만 이 기능은 개발자에게 큰 편의를 가져다주는 신통방통한 녀석이다. 앞으로도 자주 사용할 예정이므로 꼭 기억해 두자.

이제 플라스크 셸을 종료하자.

> 플라스크 셸에서 빠져 나오려면 `<Ctrl+Z>`를 누르고 `<Enter>`를 입력한다.



### 05 질문 목록과 질문 상세 기능 만들기

#### 질문 목록 기능 만들기

[1] 게시판 질문 목록 출력하기

myproject/pybo/views/main_views.py

```
from flask import Blueprint, render_template # 추가

from pybo.models import Question # 추가

(...생략...)

@bp.route('/')
def index():
    question_list = Question.query.order_by(Question.create_date.desc())# 추가
    return render_template('question/question_list.html', question_list=question_list)# 추가
```

<br><br>



[2] 질문 목록 템플릿 파일 작성하기

render_template 함수에서 사용할 `question/question_list.html` 템플릿 파일을 작성

<br>

<br>



#### 질문 상세 기능 만들기

[1] 라우트 함수 구현하기

myproject/pybo/views/main_views.py

```
(...생략...)

@bp.route('/detail/<int:question_id>/')
def detail(question_id):
    question = Question.query.get(question_id)
    return render_template('question/question_detail.html', question=question)
```

<br><br>



[2] 질문 상세 템플릿 작성하기

이어서 질문 상세 화면에 해당하는 `question/question_detail.html` 템플릿을 작성하자. `templates/question` 디렉터리에 question_detail.html 파일을 만들고 다음 코드를 작성하자.

templates/question/question_detail.html

```
<h1>{{ question.subject }}</h1>

<div>
    {{ question.content }}
</div>
```

<br><br>



[3] 404 오류 페이지 표시하기

이번에는 웹 브라우저에서 `localhost:5000/detail/30/` 페이지를 요청해 보자. 그러면 빈 페이지가 나타날 것이다. 왜냐하면 앱이 전달받은 question_id가 30이므로 main_views.py 파일의 detail 함수에서 `Question.query.get(30)`이 호출되는데 question_id가 30인 질문은 없기 때문

<br><br>

그런데 이처럼 잘못된 URL을 요청받을 때 단순히 빈 페이지를 표시하면 안된다. 이때는 보통 "Not Found (404)"처럼 오류 페이지를 표시해야 한다. 404는 HTTP 주요 응답 코드의 하나이다. 아래 표에서 HTTP 주요 응답 코드의 종류를 확인하자.

<br><br>

존재하지 않는 페이지를 요청받으면 빈 페이지 대신 404 오류 페이지를 표시하도록 다음처럼 detail 함수의 일부를 수정해 보자.

myproject/pybo/views/main_views.py

```
(...생략...)

@bp.route('/detail/<int:question_id>/')
def detail(question_id): 
    question = Question.query.get_or_404(question_id) # 변경
    return render_template('question/question_detail.html', question=question)
```

<br><br>

#### 블루프린트로 기능 분리하기

[1] 질문 목록, 질문 상세 기능 분리하기

`pybo/views` 디렉터리에 question_views.py 파일을 새로 만들고 질문 목록과 질문 상세 기능을 이동해 보자.

pybo/views/question_views.py

```
from flask import Blueprint, render_template

from pybo.models import Question

bp = Blueprint('question', __name__, url_prefix='/question')


@bp.route('/list/')
def _list():
    question_list = Question.query.order_by(Question.create_date.desc())
    return render_template('question/question_list.html', question_list=question_list)


@bp.route('/detail/<int:question_id>/')
def detail(question_id):
    question = Question.query.get_or_404(question_id)
    return render_template('question/question_detail.html', question=question) 
```

<br><br>

이제 question_views.py 파일에 등록한 블루프린트를 적용하기 위해 `pybo/__init__.py` 파일도 수정하자.

pybo/__init__.py

```
(...생략...)

def create_app():
    (...생략...)

    # 블루프린트
    from .views import main_views, question_views # 추가
    app.register_blueprint(main_views.bp)
    app.register_blueprint(question_views.bp) # 추가

    (...생략...)
```

<br><br>

[2] url_for로 리다이렉트 기능 추가하기

question_views.py 파일에 질문 목록과 질문 상세 기능을 구현했으므로 main_views.py 파일에서는 해당 기능을 제거하자.

views/main_views.py

```

from flask import Blueprint, url_for
from werkzeug.utils import redirect

bp = Blueprint('main', __name__, url_prefix='/')


@bp.route('/hello')
def hello_pybo():
    return 'Hello, Pybo!'


@bp.route('/')
def index():
    return redirect(url_for('question._list'))
```

`localhost:5000`에 접속하면 리다이렉트 기능 덕분에 자동으로 `localhost:5000/question/list/` 페이지가 호출될 것이다. 확인해 보자.

<br><br>

[3] 하드 코딩된 URL에 url_for 함수 이용하기

앞서 url_for 함수를 이용하면 라우트가 설정된 함수명으로 URL을 찾아준다고 했다. 이 기능을 이용해 질문 목록 템플릿에서 질문 상세를 호출하는 코드도 url_for를 이용해 수정

templates/question/question_list.html

```
{% if question_list %}
    <ul>
    {% for question in question_list %}
        <li><a href="{{ url_for('question.detail', question_id=question.id) }}">{{ question.subject }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>질문이 없습니다.</p>
{% endif %}
```

