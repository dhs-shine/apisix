# Design Research

## 설계 중심 관찰

APISIX의 설계는 거대한 모놀리스보다는 기능 허브형 게이트웨이에 가깝다. 핵심 런타임은 요청 매칭, phase orchestration, 구성 동기화, upstream 처리라는 공통 기반을 제공하고, 사용자 가치의 대부분은 플러그인 조합에서 발생한다.

## 설계 신념으로 재해석한 현재 상태

- 코어는 작게, 확장은 플러그인으로
- 운영 모드는 명시적 설정으로 드러나야 함
- hot reload와 동적 구성이 핵심 제품 가치임
- 문서와 테스트는 실제 동작을 이해하기 위한 이중 진입점임

## 설계상 주목 지점

- 라우팅, SSL, upstream, consumer, plugin metadata가 서로 독립 리소스로 모델링된다.
- 설정 중심 시스템이므로 정적 코드보다 리소스 관계 해석이 중요하다.
- AI Gateway와 MCP bridge 기능은 APISIX가 현대 에이전트 시스템의 edge runtime이 될 수 있음을 시사한다.

## 딥다이브: 리소스 지향 설계

### route는 단독 객체가 아니다

`apisix/http/route.lua`와 `apisix/plugin_config.lua`를 함께 보면 route는 단일 JSON 문서가 아니라 여러 리소스의 합성 결과다.

- route 자체 필드
- service 참조로부터 가져오는 hosts
- plugin_config로부터 병합되는 plugin 집합
- global_rule로부터 추가되는 전역 정책

즉, APISIX에서 route를 읽는다는 것은 최종적으로 실행되는 정책 그래프를 읽는 일이다. 에이전트가 기능 오작동을 분석할 때 route 원본만 확인하면 불충분한 이유가 여기에 있다.

### plugin metadata는 별도 제어면 레이어다

`t/admin/plugin-metadata.t`를 보면 plugin metadata는 일반 route 설정과 분리된 독립 리소스로 다뤄진다. 이는 특정 plugin의 전역적 동작을 조정하는 제어면이 존재함을 뜻한다.

- plugin name이 필수다.
- 존재하지 않는 plugin name은 400으로 거부된다.
- 같은 metadata를 다시 PUT해도 정상적으로 재적용 가능하다.

이 구조는 agent-first 관점에서 매우 유용하다. 에이전트는 plugin 인스턴스별 config와 plugin family 수준 metadata를 구분해 reasoning해야 한다.

### global rule은 횡단 관심사의 배치점이다

`apisix/global_rules.lua`와 `t/admin/global-rules.t`는 global rule이 단순 convenience feature가 아니라 횡단 정책 계층임을 보여 준다.

- 전역 플러그인 정책이 `/global_rules` watcher를 통해 동기화된다.
- create_time과 update_time이 유지되므로 운영 이력 추적이 가능하다.
- PATCH 시 create_time은 유지되고 update_time만 변경되어 리소스 생명주기 의미가 선명하다.

이는 원문에서 말한 agent-readable architecture와 연결된다. 경계와 적용 범위가 리소스 단위로 드러날수록 에이전트는 더 적은 문맥으로도 시스템을 해석할 수 있다.

## 딥다이브: 구성 공급자 이중 구조

### etcd 중심 동기화 모델

`apisix/core/config_etcd.lua`는 etcd를 단순 key-value 조회가 아니라 장기 실행 watch 기반 동기화 엔진으로 다룬다.

- 최초 revision을 계산한 뒤 watch를 시작한다.
- timeout, broken pipe, compaction 같은 장애를 별도 상태로 처리한다.
- watcher는 결과 큐와 semaphore를 통해 다른 소비자에게 이벤트를 전달한다.

이는 APISIX가 실시간 제어면 업데이트를 운영 시스템 수준에서 고려하고 있음을 의미한다.

### yaml 중심 동기화 모델

`apisix/core/config_yaml.lua`는 standalone mode가 파일 polling 이상의 동작을 수행함을 보여 준다.

- `#END` 플래그로 파일 완결성을 검사한다.
- 환경변수를 resolve한 뒤 메모리 config를 갱신한다.
- Admin API 사용 여부에 따라 `conf_version` 의미가 달라진다.
- worker 상태를 shared dict에 동기화한다.

즉 standalone mode는 단순 정적 파일 모드가 아니라 상태 추적과 버전 감지를 동반한 로컬 제어면이다.

## 설계 결론

APISIX의 설계 핵심은 코드 계층보다 리소스 계층과 구성 공급자 계층에 있다. 따라서 향후 문서 고도화는 플러그인 카탈로그 나열보다 다음 주제를 우선해야 한다.

- 리소스 합성 순서
- 정책 적용 우선순위
- config provider별 상태 전이
- watcher와 reload의 실패 모드
