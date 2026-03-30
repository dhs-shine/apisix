# Security Research

## 보안 표면

README와 공식 문서 기준으로 APISIX는 다음 보안 축을 중심으로 기능을 제공한다.

- 인증과 인가 플러그인
- IP, URI, Referer 제한
- rate limiting과 concurrency limiting
- TLS 및 certificate 동적 관리
- secret 관리
- Admin API 접근 제어

## 구조적 의미

보안은 APISIX에서 별도 부가 기능이 아니라 핵심 제품 가치 축 중 하나다. 즉, agent-first 도입 시에도 보안 정책을 플러그인과 구성 리소스 단위로 추론하는 능력이 중요하다.

## 에이전트용 연구 포인트

- 어떤 정책이 route, service, consumer, global rule 레이어에 걸쳐 적용되는가
- 어떤 secret과 인증 재료가 설정 파일 또는 리소스 모델을 통해 주입되는가
- Admin API 노출 범위와 기본 안전장치는 무엇인가
- trace와 로그가 민감 정보와 충돌하지 않도록 어떤 가이드가 필요한가

