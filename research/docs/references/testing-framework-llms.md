# Testing Framework Reference for LLMs

## 핵심 요약

APISIX 테스트는 Test::Nginx 기반이며, `.t` 파일은 요청, 설정, 기대 응답, 에러 로그를 선언적으로 표현한다.

## 읽기 전략

- 테스트 제목부터 읽는다.
- `config` 블록으로 사전 조건을 파악한다.
- `request`, `response_body`, `error_code`, `error_log`를 통해 기대 동작을 파악한다.
- 필요 시 `t/lib/server.lua` 같은 mock upstream 구현을 함께 본다.

