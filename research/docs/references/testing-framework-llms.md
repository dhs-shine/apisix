# Testing Framework Reference for LLMs

## 핵심 요약

APISIX 테스트는 Test::Nginx 기반이며, `.t` 파일은 요청, 설정, 기대 응답, 에러 로그를 선언적으로 표현한다.

## 읽기 전략

- 테스트 제목부터 읽는다.
- `config` 블록으로 사전 조건을 파악한다.
- `request`, `response_body`, `error_code`, `error_log`를 통해 기대 동작을 파악한다.
- 필요 시 `t/lib/server.lua` 같은 mock upstream 구현을 함께 본다.

## 실제 파일에서 확인한 패턴

### Admin API 테스트 헬퍼

`t/lib/test_admin.lua`는 테스트의 핵심 조립 도구다.

- body와 pattern이 table이면 JSON으로 직렬화한다.
- 숫자 메서드 상수는 실제 HTTP 메서드 문자열로 변환한다.
- 내부 HTTP 클라이언트로 현재 Test::Nginx 서버에 요청한다.
- 300 이상 응답은 실패가 아니라 테스트 대상으로 그대로 반환한다.
- pattern이 있으면 JSON 응답을 부분 비교한다.

이 의미는 중요하다. 많은 `.t` 파일은 실제로 `lib.test_admin`이 제공하는 DSL을 통해 간결해진 것이며, 테스트를 읽을 때는 헬퍼가 무엇을 생략하고 있는지 알아야 한다.

### Route CRUD 테스트가 주는 신호

`t/admin/routes.t`의 초반부만 읽어도 다음 사실을 알 수 있다.

- PUT으로 지정 id route를 생성 가능
- GET으로 동일 리소스 재조회 가능
- DELETE는 존재하지 않는 리소스에 404를 반환
- POST 생성 route는 내부적으로 create_time과 update_time을 기록

즉 테스트는 단순 API 예제가 아니라 저장소가 중요하게 여기는 불변 조건을 드러낸다.
