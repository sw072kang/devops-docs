---

## `troubleshooting.md` (문제 해결 가이드)

```markdown
# OTEL Collector 문제 해결 가이드

OTEL Collector 사용 중 자주 접할 수 있는 문제와 해결책을 정리했습니다.  
이 문서에서는 실행 오류, 데이터 수집 불가, 성능 이슈 등의 상황에 대한 기본적인 진단 방법을 제공합니다.

## 1. Collector 실행 오류

### 증상

- `otelcol: command not found`  
  - **원인**: PATH 설정 문제 또는 Collector 미설치  
  - **해결**: 바이너리 설치 경로 확인 후 `PATH`에 추가하거나, 설치 단계를 다시 진행

- `Error: cannot load config file`  
  - **원인**: 설정 파일 경로가 잘못되었거나, YAML 구문 오류  
  - **해결**: `--config` 경로 확인, YAML 문법 검사(`yamllint` 등 사용) 후 수정

## 2. 데이터 수집 불가

### 증상

- 트레이스/메트릭 데이터가 Collector에 전달되지 않음
- Collector 로그에 `no data received` 또는 `failed to receive` 등의 메시지 표시

### 원인 및 해결

- **Receiver 미설정 또는 포트 미개방**:  
  설정 파일에서 해당 Receiver가 정확히 구성되었는지 확인하고, Collector 실행 호스트의 방화벽 설정 점검.
  
- **Agent/SDK 설정 문제**:  
  데이터를 보내는 서비스 측 SDK나 Agent 설정이 Collector 주소를 올바르게 가리키고 있는지 확인.  
  예: OTLP over gRPC 사용 시 `otelcol` 호스트와 포트를 정확히 지정.

- **네트워크 문제**:  
  Docker 사용 시 `-p` 옵션으로 포트 바인딩을 했는지, 네트워크 정책이 Collector에 접근을 허용하는지 확인.

## 3. 데이터 Export 불가

### 증상

- Collector 로그에 `failed to export` 메시지 표시
- 백엔드(예: Jaeger UI, Prometheus)에서 데이터가 들어오지 않음

### 원인 및 해결

- **Exporters 설정 오류**:  
  Exporter `endpoint`, 인증 정보가 올바른지 확인.  
  예: Jaeger Exporter의 endpoint URL, TLS 설정, 인증 토큰 필요 여부 점검.

- **네트워크 접근성 문제**:  
  Collector가 백엔드 서버에 접근 가능한지 `curl`, `ping` 등으로 사전 테스트.

- **비호환 프로토콜 또는 포맷**:  
  백엔드가 Collector가 내보내는 프로토콜을 지원하는지 공식 문서 확인.  
  예: OTLP만 지원하는 백엔드에 Jaeger 프로토콜로 전송하려 하면 문제 발생.

## 4. 성능 문제

### 증상

- Collector CPU 사용률 과다, 메모리 부족으로 OOM 발생
- 데이터 처리 지연 발생

### 원인 및 해결

- **Batch Processor 설정 조정**:  
  `batch` 프로세서의 `send_batch_size`, `timeout` 등을 조정하여 효율적으로 데이터를 전송.
  
- **메모리 리미터 사용**:  
  `memory_limiter` 프로세서 추가로 Collector가 할당 가능한 메모리를 제한하고, 초과 시 처리 스로틀링 적용.
  
- **Exporter 수 및 형태 점검**:  
  한 Collector에서 너무 많은 Exporter를 동시에 처리할 경우 성능 저하 발생.  
  필요하다면 Collector 인스턴스를 분리(스케일 아웃)하거나, Exporter 줄이기 고려.

## 5. 로깅 및 디버깅

- Collector 실행 시 `--log-level=debug` 옵션을 사용하면 상세 로그 확인 가능.
- 설정 파일 내 `logging` Exporter 추가 후 Collector의 처리 상태를 실시간 모니터링.
- 문제 발생 시 Collector, 백엔드, 서비스 측 로그를 교차 참조하여 원인 파악.

---

위 문제해결 가이드를 바탕으로 Collector 사용 중 발생하는 일반적인 오류를 해결할 수 있습니다. 지속적으로 Collector 릴리즈 노트 및 공식 문서를 참고하여 변경 사항과 알려진 이슈를 파악하시기 바랍니다.