---
published: true
tags:
  - flask
  - sqlalchemy
  - flask-migrate
  - alembic
title: "alembic에서 Column type 변경 알아내기"
---
flask로 개발을 하시는 분들이라면 flask-migrate를 이용해서 모델의 상태를 데이터베이스로 마이그레이션 하실 겁니다. 하지만 모델에서 column의 타입을 바꿔도 `flask migrate` 명령어를 실행하고 나면 아무런 변화가 이러나지 않는 것을 경험하신 적이 있을 겁니다.

그래서 저도 오늘 검색해본 결과 column을 바꿨을 때 alembic이 인식하게 하려면 `migrations/env.py`에서 `context.configure()`의 파라미터로 `compare_type=True`를 추가해야 합니다.

```python
def run_migrations_online():
    # ...
    context.configure(connection=connection,
                      target_metadata=target_metadata,
                      process_revision_directives=process_revision_directives,
                      compare_type=True,
                      **current_app.extensions['migrate'].configure_arg
 ```
 
 이렇게 추가하고 나서 `flask migrate`를 실행시키면 column type의 변화를 알아채고 바꿔주게 됩니다.
