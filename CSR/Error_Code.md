# 오류 처리 {#HandleError}
 CSR API를 사용할 때 다양한 원인으로 오류가 발생할 수 있으며 이때 오류 callback 함수를 통해 오류 코드가 전달됩니다. 오류 코드를 분석하면 원인을 분석하거나 오류 처리를 할 수 있습니다. CSR API의 오류 callback 함수가 전달하는 오류는 다음과 같습니다.

 | 오류 이름          | 오류 코드 | 설명                        |
 |------------------|--------|-----------------------------|
 | ERROR_NETWORK_INITIALIZE | 10 | 네트워크 자원 초기화 오류                |
 | ERROR_NETWORK_FINALIZE   | 11 | 네트워크 자원 해제 오류                 |
 | ERROR_NETWORK_READ       | 12 | 네트워크 데이터 수신 오류. 클라이언트 기기의 네트워크 환경이 느려 Timeout이 발생하는 경우 주로 발생합니다. |
 | ERROR_NETWORK_WRITE      | 13 | 네트워크 데이터 전송 오류. 클라이언트 기기의 네트워크 환경이 느려 Timeout이 발생하는 경우 주로 발생합니다. |
 | ERROR_NETWORK_NACK       | 14 | 음성 인식 서버 오류. 느린 네트워크 환경으로 인해 클라이언트가 서버로 음성 패킷을 제시간에 보내지 못하면 서버는 Timeout를 발생시킵니다. 이때 발생하는 오류입니다. |
 | ERROR_INVALID_PACKET     | 15 | 유효하지 않은 패킷 전송으로 인한 오류       |
 | ERROR_AUDIO_INITIALIZE   | 20 | 오디오 자원 초기화 오류. 오디오 사용 권한이 있는지 확인합니다. |
 | ERROR_AUDIO_FINALIZE     | 21 | 오디오 자원 해제 오류                   |
 | ERROR_AUDIO_RECORD       | 22 | 음성 입력(녹음) 오류. 오디오 사용 권한이 있는지 확인합니다. |
 | ERROR_SECURITY           | 30 | 인증 권한 오류                        |
 | ERROR_INVALID_RESULT     | 40 | 인실 결과 오류                        |
 | ERROR_TIMEOUT            | 41 | 일정 시간 이상 서버로 음성을 전송하지 못하거나, 인식 결과를 받지 못함. |
 | ERROR_NO_CLIENT_RUNNING  | 42 | 클라이언트가 음성 인식을 수행하지 않는 상황에서 특정 음성 인식 관련 이벤트가 감지됨. |
 | ERROR_UNKNOWN_EVENT      | 50 | 클라이언트 내부에 규정되어 있지 않은 이벤트가 감지됨. |
 | ERROR_VERSION            | 60 | 프로토콜 버전 오류                     |
 | ERROR_CLIENTINFO         | 61 | 클라이언트 정보 오류                    |
 | ERROR_SERVER_POOL        | 62 | 음성 인식 가용 서버 부족                |
 | ERROR_SESSION_EXPIRED    | 63 | 음성 인식 서버 세션 만료                |
 | ERROR_SPEECH_SIZE_EXCEEDED | 64 | 음성 패킷 사이즈 초과                 |
 | ERROR_EXCEED_TIME_LIMIT  | 65 | 인증용 타임 스탬프(time stamp) 불량     |
 | ERROR_WRONG_SERVICE_TYPE | 66 | 올바른 서비스 타입(service type)이 아님.  |
 | ERROR_WRONG_LANGUAGE_TYPE | 67 | 올바른 언어 타입(language type)이 아님. |
 | ERROR_OPENAPI_AUTH       | 70 | OpenAPI 인증 오류. Client ID 또는 등록한 App 정보가 잘못되었을 때 발생합니다. |
 | ERROR_QUOTA_OVERFLOW     | 71 | 정해진 API 호출 제한량(quota)을 모두 소진함. |


위 오류 코드 외에도 다음과 같은 오류가 발생하거나 문의가 있을 수 있습니다.

| 현상 또는 문의                       | 원인 또는 해결 방법                     |
|-----------------------------------|-------------------------------------|
| UnsatifiedLinkError 오류 발생               | CSR API는 armeabi와 armeabi-v7a로 빌드된 라이브러리를 제공합니다. 만약 개발하는 앱에서 사용하는 라이브러리 중 armeabi와 armeabi-v7a를 지원하지 않는 것이 있다면 이 오류가 발생할 수 있습니다. |
| android fatal signal 11 (sigsegv) 오류 발생 | CSR API를 사용하여 음성을 입력받기 전에 우선 자원을 준비해야 합니다. *recognize()* 메서드를 호출하기 전에 initialize() 메서드가 잘 호출되는지 확인해야 합니다. 또한 *release()* 메서드도 호출할 수 있어야 합니다. |
| 인식 결과로 ""(null)이 반환됩니다.              | 사용자가 매우 작은 목소리로 발성하였거나, 주변 소리로 인해 목소리가 인식되지 않았을 경우 발생할 수 있습니다. 극히 드물게 발생하지만 인식 결과가 null일 때도 예외 처리 해주는 것을 권장합니다. |
| 오디오 파일 인식                              | CSR API는 오디오 파일 인식을 지원하지 않습니다.        |
| 저사양 스마트 폰에서 제대로 동작하지 않습니다.       | CSR API는 Android SDK 버전 10 이상과 iOS 버전 8 이상의 기기를 지원하고 있습니다. |
