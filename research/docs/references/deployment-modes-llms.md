# Deployment Modes Reference for LLMs

## 세 가지 모드

- traditional
- decoupled
- standalone

## 해석 포인트

- traditional은 제어면과 데이터면이 결합됨
- decoupled는 역할 분리를 전제로 함
- standalone은 파일 또는 API 기반 설정 공급을 사용함

## agent-first 의미

운영 버그나 기능 분석 시 가장 먼저 확인해야 할 정보 중 하나가 현재 배포 모드다. 동일 기능도 모드에 따라 설정 소스, 상태 전파, 디버깅 경로가 달라진다.

## standalone API-driven 모드의 추가 관찰

문서 설명에 더해 `t/admin/standalone.spec.ts`를 보면 standalone Admin API는 다음과 같은 운영 계약을 가진다.

- GET은 전체 설정과 conf_version을 반환한다.
- HEAD는 body 없이 메타데이터만 반환한다.
- PUT은 digest 기반 중복 감지를 수행한다.
- JSON과 YAML을 모두 소비하거나 반환할 수 있다.
- invalid conf version과 unknown plugin, invalid upstream에 대한 검증 테스트가 존재한다.

따라서 에이전트가 standalone 모드를 다룰 때는 단순 구성 업로드보다 `conf_version`, `digest`, validation endpoint를 포함한 상태 전이 모델로 이해해야 한다.
