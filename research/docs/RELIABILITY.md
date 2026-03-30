# Reliability Research

## 신뢰성 관련 관찰

APISIX는 신뢰성을 단일 기능이 아닌 여러 메커니즘의 조합으로 제공한다.

- upstream health check
- 동적 라우팅과 장애 회피
- decoupled deployment
- standalone mode hot reload
- Control API와 상태 엔드포인트
- 메트릭 및 trace 연동

## 저장소에서 확인되는 신호

- 설정 예제에 worker, timer, shared dict, TLS, resolver, access log 등 운영성 설정이 세밀하게 드러난다.
- health check와 discovery 테스트가 다수 존재한다.
- Prometheus 및 tracing 문서가 운영 피드백 루프를 뒷받침한다.

## 소스코드 기반 신뢰성 메커니즘

### 1. 설정 동기화 실패와 초기화 실패는 런타임 단계에서 분리된다

`apisix/init.lua`는 config center 초기화와 worker 초기화를 분리한다. 이 구조는 장애 분석 시 중요하다.

- 초기 기동 실패는 `core.config.init` 경로에서 나타날 수 있다.
- worker 레벨 문제는 `core.config.init_worker`, discovery init, balancer init, plugin init 이후에 드러날 수 있다.

즉 APISIX는 실패를 한 단계에서 모두 흡수하지 않고 생명주기 단계별로 분산시킨다.

### 2. upstream 가용성은 요청 시점에 재평가된다

`apisix/upstream.lua`는 다음 이유로 요청 처리 중 503이 발생할 수 있음을 보여 준다.

- route 혹은 service에 upstream이 없음
- discovery provider 미초기화
- discovery 결과로 유효한 node를 받지 못함
- node 포맷 검증 실패
- 최종 node 수가 0
- upstream TLS 지원 런타임이 빌드되지 않음

이는 운영상 매우 중요하다. 구성이 저장되었다고 해서 요청 성공이 보장되는 구조가 아니다. 런타임 재해석 단계에서 실패할 수 있다.

### 3. health check는 별도 checker 객체로 결합된다

upstream resolution 뒤에 `healthcheck_manager.fetch_checker`가 호출되어 `api_ctx.up_checker`가 설정된다. 이는 health check가 route matching 전에 실행되는 것이 아니라, upstream이 결정된 뒤 로드밸런싱 직전의 가용성 평가 메커니즘으로 붙는다는 뜻이다.

### 4. standalone mode는 운영 친화적 캐싱 계약을 가진다

`t/admin/standalone.spec.ts`에서 확인되듯, standalone API는 단순 PUT over memory가 아니다.

- `conf_version` 단위의 optimistic concurrency
- `modifiedIndex` 기반 개별 리소스 갱신 제어
- `x-digest`를 활용한 중복 갱신 생략
- `x-last-modified`를 통한 변경 추적

이 계약은 에이전트 기반 운영 자동화에 특히 적합하다. 반복 실행 중에도 불필요한 재적용을 줄이고, stale update를 감지할 수 있기 때문이다.

## agent-first 관점 해석

에이전트가 신뢰성 이슈를 다루려면 다음 정보가 바로 검색 가능해야 한다.

1. 현재 배포 모드
2. 어떤 리소스가 구성 소스인지
3. health check 실패 시 기대 동작
4. 메트릭과 로그 노출 경로
5. 테스트로 재현 가능한 회귀 시나리오

APISIX는 이 단서를 대부분 보유하고 있지만, 문서와 테스트에 분산되어 있다. 따라서 연구 지식층이 이들을 묶어주는 역할을 해야 한다.

## 신뢰성 연구 결론

APISIX의 신뢰성은 단일 고가용성 기능보다 다음의 중첩 구조에서 나온다.

- 생명주기별 초기화 분리
- 요청 시점 upstream 재검증
- health checker 결합
- 상태 기반 standalone config 갱신 계약
- Prometheus와 tracing을 통한 피드백 루프

따라서 향후 연구 확장 시에는 healthcheck manager, balancer, service discovery별 테스트를 별도 문서로 분해하는 것이 가장 효과적이다.
