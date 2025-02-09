# 사전세팅된 테스트용 플랫폼 배포하기

fluentd 연동 테스트할 샘플 플랫폼들 모음

## Kafka

```sh
# kafka 샘플 설치 (KRAFT, Private, broker 1, connect 1)
helm install my-kfk skafka-2.0.4.tgz -f skafka.yaml
```

## Loki (PLG)

```sh
# loki 샘플 설치
helm install my-lok-gra loki-stack-2.9.12.tgz -f loki-grafana.yaml
```

## Redis

```sh
# redis 샘플 설치
helm install my-redis bitnami/redis --version 20.6.1 -f redis.yaml
```
