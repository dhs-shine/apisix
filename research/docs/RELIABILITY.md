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

## agent-first 관점 해석

에이전트가 신뢰성 이슈를 다루려면 다음 정보가 바로 검색 가능해야 한다.

1. 현재 배포 모드
2. 어떤 리소스가 구성 소스인지
3. health check 실패 시 기대 동작
4. 메트릭과 로그 노출 경로
5. 테스트로 재현 가능한 회귀 시나리오

APISIX는 이 단서를 대부분 보유하고 있지만, 문서와 테스트에 분산되어 있다. 따라서 연구 지식층이 이들을 묶어주는 역할을 해야 한다.

