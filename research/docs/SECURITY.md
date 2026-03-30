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

## 소스코드 관찰

### Admin API 인증 경계

`apisix/admin/init.lua`의 `check_token` 흐름은 Admin API 보안의 핵심을 보여 준다.

- 기본적으로 `admin_key_required`가 false가 아니면 API key를 요구한다.
- key는 query arg, `X-API-KEY` header, cookie에서 수용된다.
- 매칭된 key는 역할을 가지며, `viewer`는 GET 계열만 허용된다.

이 구현은 곧 APISIX 운영면이 단순 shared secret 모델 위에 읽기 전용 역할까지 제공한다는 뜻이다. 연구상 이는 사람과 에이전트의 책임 분리를 설계할 때 중요한 단서다.

### 리소스 기반 보안 적용면

Admin API는 route, consumer, credential, secret, ssl, plugin metadata 같은 리소스를 개별 핸들러로 분리한다. 이는 보안 규칙이 코드 모듈보다 리소스 타입에 걸쳐 퍼진다는 의미이기도 하다.

예를 들어 agent-first 도구가 보안 검토를 하려면 다음 질문을 리소스별로 던져야 한다.

- route에 어떤 auth plugin이 붙어 있는가
- consumer credential은 어떤 plugin schema를 따른는가
- secret은 어떤 리소스에서 참조되는가
- ssl 리소스는 어떤 SNI 매칭 흐름으로 사용되는가

## 에이전트용 연구 포인트

- 어떤 정책이 route, service, consumer, global rule 레이어에 걸쳐 적용되는가
- 어떤 secret과 인증 재료가 설정 파일 또는 리소스 모델을 통해 주입되는가
- Admin API 노출 범위와 기본 안전장치는 무엇인가
- trace와 로그가 민감 정보와 충돌하지 않도록 어떤 가이드가 필요한가

## 보안 연구 결론

APISIX 보안은 플러그인 카탈로그의 개수보다 관리면과 데이터면의 리소스 경계를 얼마나 명확히 이해하느냐에 달려 있다. 특히 Admin API 인증, secret 참조, TLS 리소스, consumer credential 모델은 향후 심화 연구 우선순위가 높다.
