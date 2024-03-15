# fluentd-kafka

fluentd와 kafka를 연결하는 예제

- Kafka 사용시 매번 Producer, Consumer를 직접 코딩하여 만드는 것은 비효율적이다.
- 강력한 파이프라인 도구 중 하나인 fluentd로 Producer, Consumer를 대체할 수 있는 유스케이스가 많다.
- 이를 위한 사전테스트, 템플릿을 다루는 레포지토리

## Requirement

- K3s
- Helm

## fluentd

```sh
# fluentd
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-fluentd bitnami/fluentd --version 5.19.0

# kafka
# broker 3, connect 1, private ip, KRAFT
helm install test https://github.com/YunanJeong/simple-kafka-deploy/releases/download/v2.0.3/skafka-2.0.3.tgz \
-f https://github.com/YunanJeong/simple-kafka-deploy/releases/download/v2.0.3/kraft-multi.yaml
```

## MEMO

- 커뮤니티에선 fluentd를 aggregator와 forwarder로 구분하는 데, fluentd 공식은 아니다.
- 공식 fluentd(td-agent)처럼 단일앱으로만 실행하려면, aggregator만 활성화
- forwarder는 DaemomSet으로 구현되어있으므로 비활성화
