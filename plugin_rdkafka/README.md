# Fluentd Kafka Output @type Selection (2026.04.)

- `fluent-plugin-kafka`에서 쓸 수 있는 type이 너무 많아서 헷갈린다. 이를 간략히 정리.

## 범용/사실상 표준

### 1티어 (Production급 사실상 표준)

- input: rdkafka_group
- output: rdkafka2
- librdkafka C 라이브러리 의존성 있음. 공식 도커 이미지에 미포함되므로 별도 관리 필요.

### 2티어 (간단한 Test급)

- input: kafka_group
- output: kafka2
- Kafka 4 미호환, 사실상 deprecated

## 총정리

### 쉽게 파악하기

- kafka 계열
  - ruby-only (간단히 테스트하기 좋음)
- rdkafka 계열
  - C엔진 기반으로 속도/안정성 강화. 사실상 표준
  - kafka 계열과 config 호환 안됨
  - C 라이브러리 의존성 별도 설치 필요
- 접미사 `_group`
  - 옛날 버전에서는 consumer-group 생성도 안하고, 단일 consumer로서 Kafka통신하다 보니까, consumer-group 지원하는 플러그인으로 구분해서 새로 나온 것. 현시점에선 당연히 이게 표준이다.
- 접미사 `2`
  - fluentd v1이상에서 씀.
  - 2가 따로 있으면 무조건 2 쓰면된다. 2 아닌 것들은 너무 구버전용이므로 무시.

### input type
- kafka (input): ruby-kafka 기반 단일 컨슈머로 topic을 직접 지정해서 consume 하는 가장 기본적인 Kafka 인풋.
- kafka_group : ruby-kafka 기반 consumer group 인풋으로, group.id 기준으로 파티션을 분배받는 일반적인 그룹 컨슈머 방식.
- rdkafka_group : rdkafka-ruby+librdkafka 기반 consumer group 인풋으로, Kafka 2.x 이상 호환성과 성능을 노린 비교적 최신 타입(프로덕션 헤비로드는 아직 경고 문구 있음).
​
## output type
- kafka2 : fluentd v1용 ruby-kafka 기반 출력으로, 헤더/record_key/jsonpath 등 기능 풍부하지만, 환경에 따라 호환성 이슈가 있어 요즘은 rdkafka2 권장.
- kafka_buffered : v0.12 시절 buffered 출력용 ruby-kafka 기반 타입으로, v1에서는 사실상 kafka2 alias로 취급되고 앞으로 제거 예정.
- kafka (output, non-buffered) : 버퍼 없이 바로 전송하는 테스트용 ruby-kafka 출력으로, 신뢰성/성능 문제 때문에 실제 운영에서는 거의 안 쓰는 타입.
- rdkafka2 : fluentd v1용 rdkafka(C 라이브러리) 기반 출력으로, 성능·기능·버전 호환성이 좋아 현재 Kafka 연동의 사실상 표준 아웃풋.
- rdkafka : fluentd v0.12용 rdkafka 기반 출력으로, 레거시 환경에서만 사용하는 옛 타입(rdkafka2의 0.12 버전 대응격).


