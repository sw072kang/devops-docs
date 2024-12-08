# OTEL Collector 설치 가이드

이 문서에서는 OpenTelemetry Collector(이하 OTEL Collector)를 설치하는 다양한 방법에 대해 다룹니다.  
Collector는 로그, 메트릭, 트레이스 데이터를 수집하고 처리한 뒤 다양한 백엔드(예: Jaeger, Prometheus, Tempo, New Relic 등)로 내보내기 위한 범용 솔루션입니다.

## 사전 준비 사항

- 지원 플랫폼:  
  - Linux (x86_64, ARM64)  
  - macOS  
  - Windows  
- Python 등의 추가적인 런타임은 필요하지 않으나, 특정 Exporter/Receiver가 필요한 라이브러리를 요구할 수 있으므로 관련 문서 확인이 필요합니다.
- Docker 환경이 구성되어 있다면 Docker 이미지를 통한 설치를 권장할 수 있습니다.
- Collector 버전은 공식 [릴리즈 페이지](https://github.com/open-telemetry/opentelemetry-collector-releases)에서 확인 가능합니다.

## 설치 방법 선택 가이드

1. **바이너리 다운로드**:  
   운영체제별 사전 컴파일된 바이너리를 다운받아 바로 실행할 수 있습니다.  
   - 장점: 빠르고 간단한 설치  
   - 단점: 패키지 관리자와의 통합 어려움

2. **Docker 컨테이너 사용**:  
   Docker 이미지를 통해 Collector를 실행할 수 있습니다.  
   - 장점: 손쉬운 배포, 빠른 확장성, 종속성 격리  
   - 단점: Docker 환경 필요, 호스트 접근 설정 필요

3. **패키지 매니저(Homebrew, apt, yum 등) 이용**:  
   운영체제별 패키지 매니저를 통해 설치하면 자동 업데이트, 관리가 편리합니다.  
   - 장점: 쉬운 업데이트 관리, 표준 설치 경로  
   - 단점: 사용 환경에 따라 지원 미비 가능

## 1. 바이너리 설치

### Linux 예시

```bash
# 최신 버전 바이너리 다운로드 (예: v0.75.0)
curl -L https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/v0.75.0/otelcol_0.75.0_linux_amd64.tar.gz -o otelcol.tar.gz
tar -xvf otelcol.tar.gz
sudo mv otelcol /usr/local/bin/