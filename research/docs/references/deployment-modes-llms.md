# Deployment Modes Reference for LLMs

## 세 가지 모드

- traditional
- decoupled
- standalone

## 해석 포인트

- traditional은 제어면과 데이터면이 결합됨
- decoupled는 역할 분리를 전제로 함
- standalone은 파일 또는 API 기반 설정 공급을 사용함

## agent-first 의미

운영 버그나 기능 분석 시 가장 먼저 확인해야 할 정보 중 하나가 현재 배포 모드다. 동일 기능도 모드에 따라 설정 소스, 상태 전파, 디버깅 경로가 달라진다.

