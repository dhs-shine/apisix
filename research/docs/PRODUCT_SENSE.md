# Product Sense Research

## APISIX의 제품 포지션

APISIX는 단순 reverse proxy가 아니라 다음 속성을 함께 제공하는 플랫폼이다.

- 동적 API 게이트웨이
- 멀티 프로토콜 트래픽 허브
- 보안 및 정책 실행 지점
- 관측 가능성 신호 생성 지점
- 플러그인 기반 확장 플랫폼
- AI Gateway 및 MCP bridge 기반 에이전트 친화 엣지 계층

## 사용자 가치 축

1. 동적 라우팅과 hot reload
2. 인증, 인가, rate limit 등 정책 시행
3. Prometheus, Zipkin, OpenTelemetry, SkyWalking 등 관측 가능성 연동
4. standalone과 decoupled 모드를 통한 다양한 운영 모델
5. 외부 플러그인 러너와 Wasm을 통한 확장성

## agent-first 관점의 제품 해석

APISIX는 에이전트를 직접 생성하는 개발 플랫폼은 아니지만, 에이전트가 사용하는 네트워크 경계와 정책 경계를 제공하는 실행 기반에 가깝다. 따라서 Codex 활용 관점에서는 APISIX를 다음과 같이 볼 수 있다.

- 에이전트 트래픽 제어 지점
- 모델 호출 안전장치 삽입 지점
- 추적과 감사의 중앙 수집 지점
- MCP 서버를 외부 서비스화하는 게이트웨이 지점

