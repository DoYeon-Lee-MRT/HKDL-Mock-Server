# Disney Hong Kong OTE-OTA Mock Server

홍콩 디즈니랜드 API 통합 가이드 (v1.8) 기반 테스트 서버입니다.

## 개요

이 서버는 실제 홍콩 디즈니랜드 API를 호출하지 않고 로컬에서 테스트할 수 있도록 만든 Mock Server입니다.
문서 (D-OTE-OTA_Integration_Guide_v_1.8.pdf)에 명시된 모든 요청/응답 예시를 그대로 구현했습니다.

## 기능

### 구현된 API

1. **Product Enquiry (상품 조회)**
   - `POST /OTA/GetEvents` - 이벤트 목록 조회
   - `POST /OTA/GetShows` - 특정 이벤트의 쇼 목록 조회
   - `POST /OTA/GetTickets` - 특정 이벤트/쇼의 티켓 목록 조회
   - `POST /OTA/GetPickupDetails` - 특정 이벤트의 픽업 방법 조회

2. **Order Reservation (주문 예약)**
   - `POST /OTA/ReserveOrder` - 주문 예약

3. **Order Cancellation (주문 취소)**
   - `POST /OTA/CancelOrder` - 주문 취소
   - `POST /OTA/GetOrderStatus` - 주문 상태 조회

4. **기타**
   - `GET /health` - 서버 상태 확인

### 인증

- SHA-256 기반 시그니처 검증
- Agent ID 검증
- 요청 시간 검증 (15분 이내)

## 설치 및 실행

### 1. 필요 패키지 설치

```bash
cd disney-hk-mock-server
pip install -r requirements.txt
```

### 2. 서버 실행

```bash
python app.py
```

서버는 `http://localhost:5000`에서 실행됩니다.

## 설정

`config.py` 파일에서 다음 설정을 변경할 수 있습니다:

```python
SECRET_AUTHENTICATION_KEY = "xY378DS731A"  # 인증 키
AGENT_ID = "CITYLINE"                      # Agent ID
REQUEST_TIME_TOLERANCE = 900               # 요청 시간 허용 범위 (초)
```

## 사용 예시

### 1. GetEvents (이벤트 목록 조회)

```bash
# Python 테스트 스크립트 사용
python test_api.py get_events
```

### 2. ReserveOrder (주문 예약)

```bash
python test_api.py reserve_order
```

### 3. GetOrderStatus (주문 상태 조회)

```bash
python test_api.py get_order_status
```

## API 요청 형식

모든 API는 다음 형식으로 요청해야 합니다:

```
POST /OTA/{API_NAME}
Content-Type: application/x-www-form-urlencoded

message={JSON_MESSAGE}&Signature={SHA256_SIGNATURE}
```

### 시그니처 생성 방법

```python
import hashlib

message = '{"agentId":"CITYLINE","requestTime":"2023-10-31 12:00:00"}'
secret_key = "xY378DS731A"
concatenated = f"{message}||{secret_key}"
signature = hashlib.sha256(concatenated.encode('utf-8')).hexdigest()
```

## 응답 코드

| 코드 | 설명 |
|------|------|
| 0000 | 성공 |
| 1001 | 잘못된 시그니처 |
| 1002 | 잘못된 메시지 형식 |
| 1003 | 잘못된 Agent ID |
| 1004 | 요청 만료 |
| 1005 | 잘못된 요청 정보 |
| 1007 | 예약을 찾을 수 없음 |
| 3001 | 중복된 요청 |
| 4101 | 잘못된 Event ID |
| 4103 | Date Specific Event가 아님 |
| 4308 | 잘못된 Pickup ID |
| 4502 | 언어 설정 누락 또는 잘못됨 |
| 9999 | 기타 오류 |

## 파일 구조

```
disney-hk-mock-server/
├── app.py              # 메인 Flask 애플리케이션
├── auth.py             # 인증 관련 유틸리티
├── config.py           # 설정 파일
├── mock_data.py        # Mock 응답 데이터
├── test_api.py         # API 테스트 스크립트
├── requirements.txt    # Python 패키지 목록
└── README.md           # 이 파일
```

## 주의사항

1. 이 서버는 **테스트 목적**으로만 사용하세요.
2. 실제 데이터베이스를 사용하지 않으며, 모든 데이터는 메모리에 저장됩니다.
3. 서버를 재시작하면 모든 예약 데이터가 초기화됩니다.
4. 문서에 명시된 샘플 데이터를 그대로 반환합니다.

## 라이센스

이 프로젝트는 테스트 목적으로만 제공됩니다.
# HKDL-Mock-Server
