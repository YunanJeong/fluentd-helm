# fluentd-helm

fluentd와 kafka를 연결하는 예제

- Kafka 사용시 매번 Producer, Consumer를 직접 코딩하여 만드는 것은 비효율적이다.
- 강력한 파이프라인 도구 중 하나인 fluentd로 Producer, Consumer를 대체할 수 있는 유스케이스가 많다.
- 이를 위한 사전테스트, 템플릿을 다루는 레포지토리

## Requirement

- K3s
- Helm

## fluentd

```sh
# fluentd 기본
# fluentd
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install fltd bitnami/fluentd --version 5.19.0
```

### Example: Kafka Produce/Consume

```sh
# fluentd
helm install fltd bitnami/fluentd --version 5.19.0 -f to_kafka.yaml

# kafka 샘플 설치
helm install kfk https://github.com/YunanJeong/simple-kafka-deploy/releases/download/v2.0.3/skafka-2.0.3.tgz \
-f https://github.com/YunanJeong/simple-kafka-deploy/releases/download/v2.0.3/kraft-multi.yaml \
--set "connect.enabled=false"
```

### Example: fluentd to Loki

```sh
# fluentd
helm install fltd bitnami/fluentd --version 5.19.0 -f to_loki.yaml

# loki 샘플 설치
helm install lok-gra grafana/loki-stack --version 2.9.12 -f https://raw.githubusercontent.com/YunanJeong/plg-stack/main/loki-grafana.yaml
```

## 플러그인 gem 설치

### 온라인

```yaml
# 필요한 gem을 value에 기술하면 자동설치
aggregator:
  extraGems: # []
    - name: fluent-plugin-beats 
      version: 2.1.2  # default: latest
```

### 오프라인

- 필요한 gem파일을 로컬호스트의 정해진 경로에 미리 배치하고, 볼륨마운트 기능으로 컨테이너에 배포

```sh
# 플러그인파일들을 호스트마운트 경로로 옮기기
sudo cp -r plugins/ /etc/
```

- 이후 다음 과정으로 배포된다. 샘플파일 `myvalues.yaml` 참고
  1. 로컬 gem경로가 initContainer에 마운트
  2. initContainer에서 gem이 설치됨
  3. 설치된 파일내역은 initContainer와 App. Container 간 공유경로(볼륨)으로 복사됨

```yaml
# 사용되는 value 섹션들
aggregator:
  initContainers: []
  extraVolumes: []
  extraVolumeMounts: []
```

## MEMO

- fluentd를 aggregator와 forwarder로 구분한다.
  - 원래 커뮤니티에서만 구분했던 것 같은데, 요새는 공식문서에서도 구분하더라.
  - fluentd로 forwarder역할까지 다 해결하려는 추세가 강해진 듯
  - aggregator: 중앙수집해서 처리 후 다음 파이프라인으로 넘김
  - forwarder: fluentbit, beats와 같은 역할. Helm에선 DaemomSet으로 구현됨.
- td-agent(fluentd 공식배포)처럼 단일앱으로만 실행하려면,
  - aggregator만 활성화
  - forwarder는 비활성화
- kafka, prometheus, elasticsearch 등 주요 플랫폼은 이미 gem 플러그인이 내장되어 있어, 추가 설치가 필요없음
