# OpenResty Runtime Reference for LLMs

## 왜 중요한가

APISIX는 Nginx와 ngx_lua 위에서 동작하므로, 일반 웹 애플리케이션과 달리 phase 기반 실행 모델을 이해해야 한다.

## 기억할 점

- 요청은 phase를 따라 이동한다.
- APISIX는 phase마다 플러그인을 실행한다.
- worker 초기화, timer, shared dict, privileged agent 같은 개념이 중요하다.
- 단순 함수 호출 그래프보다 런타임 phase 모델이 더 중요하다.

