# Frontend Surface Research

## 해석

APISIX 저장소는 전통적 의미의 대규모 프론트엔드 애플리케이션을 중심에 두지 않는다. 사용자 상호작용 표면은 주로 다음 두 축으로 나타난다.

- Admin API와 Control API
- 문서, 예제, Dashboard 연계 지점

## 연구 포인트

- UI보다 API와 설정이 사실상 주요 인터페이스다.
- 에이전트 우선 운영에서는 브라우저 UI보다 JSON, YAML, curl 예시가 더 중요한 제어 수단이다.
- 따라서 APISIX에서 agent-first 도입은 프론트엔드 자동화보다 API 및 운영 피드백 루프 자동화가 중심이 된다.

