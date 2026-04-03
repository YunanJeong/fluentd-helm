# docker build -t bitnamilegacy/fluentd-rdkafka:1.17.1 .

FROM docker.io/bitnamilegacy/fluentd:1.17.1-debian-12-r2

USER root

RUN install_packages \
    build-essential \
    pkg-config \
    librdkafka-dev \
    libsasl2-dev \
    libssl-dev \
    zlib1g-dev

RUN gem install --no-document rdkafka

# fluent-gem install fluent-plugin-kafka -v 0.19.6

USER 1001