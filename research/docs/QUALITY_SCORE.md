# Quality Score Research

## 평가 기준

점수는 구현 완성도가 아니라 연구 관점의 신뢰도와 agent-readability를 함께 고려한 정성 평가다.

| 영역 | 점수 | 근거 |
| --- | --- | --- |
| 코어 아키텍처 가시성 | A- | 진입점과 공식 아키텍처 문서가 존재함 |
| 플러그인 확장성 | A | 구조가 일관되고 문서가 풍부함 |
| 배포 모델 명확성 | A | traditional, decoupled, standalone이 잘 문서화됨 |
| 테스트 자산 | A | `t/` 하위 테스트 범위가 넓고 실행 사양 역할을 함 |
| 운영 관측 가능성 | A- | 메트릭과 trace 문서가 좋으나 탐색 경로가 넓음 |
| agent-first 문서 구조 | B | 정보는 풍부하지만 에이전트용 맵으로 재조직되지는 않음 |
| 지식 집중도 | B- | 핵심 정보가 README, docs, config, tests로 분산됨 |

## 총평

APISIX는 품질 높은 오픈소스 저장소이지만, 원문에서 설명한 형태의 에이전트 우선 운영을 바로 수행하기에는 지식의 구조화 수준이 아직 사람 중심에 가깝다. 다만 자료 밀도와 규칙성은 높아, 연구용 지식 레이어만 추가해도 레버리지가 크게 증가할 가능성이 있다.

## 근거 상세

### 코어 아키텍처 가시성 A-

이 평가는 `apisix/init.lua`, `apisix/router.lua`, `apisix/http/route.lua`를 함께 읽었을 때 부트스트랩과 route build 흐름이 비교적 일관되게 드러난다는 점에 기반한다. 감점 요인은 시스템 이해에 필요한 파일 수가 적지 않다는 점이다.

### 플러그인 확장성 A

`apisix/plugin.lua`는 플러그인 로딩, 정렬, schema 주입, destroy lifecycle을 한곳에서 다룬다. `prometheus` 같은 실제 플러그인 구현도 얇은 wrapper 구조를 가지므로 에이전트가 패턴을 학습하기 좋다.

### 테스트 자산 A

`t/admin/routes.t`, `t/admin/standalone.spec.ts`, `t/lib/test_admin.lua`를 보면 테스트가 단순 assertion이 아니라 실제 API 행위, 메타데이터 갱신, digest 처리, YAML 응답 처리까지 검증한다. 이는 저장소 품질뿐 아니라 행위 기반 연구에 매우 유리하다.

### agent-first 문서 구조 B

공식 문서는 풍부하지만 에이전트용 맵은 부족하다. 예를 들어 route 생성의 실제 불변 조건은 테스트에 있고, standalone 갱신 계약의 세부는 문서보다 테스트에 더 풍부하다. 이런 차이 때문에 에이전트는 아직 여러 출처를 수동으로 종합해야 한다.
