# Core Beliefs for APISIX Agent-First Research

## 1. 코드보다 구조를 먼저 읽어야 한다

APISIX는 기능 폭이 넓기 때문에 임의 파일을 순차적으로 읽는 방식보다 진입점, 공식 문서, 테스트, 설정 예제를 축으로 삼아 구조를 이해하는 것이 효율적이다.

## 2. 테스트는 실행 가능한 제품 지식이다

`t/` 하위 테스트는 단순 회귀 체크가 아니라 실제 동작 예제를 제공한다. 에이전트는 문서보다 테스트를 더 신뢰할 때가 많다.

## 3. 설정 모델이 곧 제품 모델이다

APISIX의 route, upstream, service, consumer, ssl, plugin metadata는 런타임 동작을 좌우하는 실질적 도메인 모델이다.

## 4. 운영성은 코어 기능과 분리되지 않는다

Prometheus, tracing, status API, deployment modes는 부가 기능이 아니라 APISIX 제품 정체성의 일부다.

## 5. agent-first 도입은 새 기능보다 새 맵이 필요하다

현재 저장소는 이미 방대한 사실을 담고 있다. 부족한 것은 사실 그 자체보다, 에이전트가 적은 컨텍스트로 진입할 수 있는 연구용 맵이다.

