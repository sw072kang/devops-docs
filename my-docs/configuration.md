---

## `configuration.md` (설정 가이드)

```markdown
# OTEL Collector 설정 가이드

OTEL Collector는 `config.yaml` 파일을 통해 Receivers(수집), Processors(처리), Exporters(전송) 등을 설정합니다.  
이 문서에서는 기본적인 설정 예시와 주요 섹션별 설명을 제공합니다.

## 구성 요소 개요

- **Receivers**: 외부에서 데이터를 수신하는 역할. 예: `otlp`, `prometheus`, `jaeger` 등
- **Processors**: 수집된 데이터를 가공하거나 필터링, 배치 처리. 예: `batch`, `memory_limiter`, `resource` 등
- **Exporters**: 최종 백엔드로 데이터를 내보내는 역할. 예: `otlp`, `logging`, `jaeger`, `prometheusremotewrite`
- **Service**: 파이프라인을 정의하며, 어떤 Receivers -> Processors -> Exporters 조합을 사용할지 지정

## 기본 설정 예시

아래는 OTLP 수신 후, 로깅 및 Jaeger 백엔드로 내보내는 예시 구성입니다.

```yaml
receivers:
  otlp:
    protocols:
      grpc:
      http:

processors:
  batch:

exporters:
  logging:
    loglevel: info
  jaeger:
    endpoint: "jaeger-collector:14250"
    tls:
      insecure: true

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [batch]
      exporters: [logging, jaeger]
    metrics:
      receivers: [otlp]
      processors: [batch]
      exporters: [logging]