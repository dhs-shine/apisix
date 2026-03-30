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

