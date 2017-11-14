## CSS API 레퍼런스 {#APIReference}
CSS API는 HTTP 요청으로 텍스트를 입력받아 음성 합성한 후 이를 MP3 형식의 바이너리 데이터로 반환하는 REST API입니다. 여기에서는 CSS API에 대해 상세히 설명합니다.

### 기본 정보 {#BasicInfo}
CSS API의 요청 URI 및 요청에 필요한 헤더 정보는 다음과 같습니다.

| 메서드   | 요청 URI                        | 필요 헤더                                     |
|--------|--------------------------------|---------------------------------------------|
| POST   | https://openapi.naver.com/v1/voice/tts.bin | <ul><li>X-Naver-Client-Id: <a href="#Preparation">사전 준비사항</a>에서 발급받은 Client ID</li><li>X-Naver-Client-Secret: <a href="#Preparation">사전 준비사항</a>에서 발급 받은 Client Secret</li></ul> |

### 요청 파라미터 {#RequestParameter}
CSS API에 필요한 요청 헤더는 본문에 입력하며 본문에 다음과 같이 파라미터를 작성해야 합니다.

```
[HTTP Request Body]
speaker={string}&speed={integer}&text={string}
```

다음은 파라미터에 대한 설명입니다.

| 파라미터 이름 | 타입     | 설명                                                       | 필수 여부 |
|------------|---------|----------------------------------------------------------|---------|
| speaker    | string  | 음성 합성에 사용할 목소리 종류 <ul><li>mijin : 한국어, 여성 음색</li> <li>jinho : 한국어, 남성 음색</li> <li>clara : 영어, 여성 음색</li> <li>matt : 영어, 남성 음색</li> <li>yuri : 일본어, 여성 음색</li><li>shinji : 일본어, 남성 음색</li><li>meimei : 중국어, 여성 음색</li><li>liangliang : 중국어, 남성 음색</li><li>jose : 스페인어, 남성 음색</li><li>carmen : 스페인어, 여성 음색</li></ul> | 필수 |
| speed      | integer | 음성 재생 속도. -5에서 5 사이의 정수 값이며, -5이면 0.5배 빠른 속도이고 5이면 0.5배 느린 속도입니다. 0이면 정상 속도의 목소리로 음성을 합성합니다.      | 필수 |
| text       | string  | 음성 합성할 문장. UTF-8 인코딩된 텍스트만 지원합니다. CSS API는 최대 5000자의 텍스트까지 음성 합성을 지원합니다. <div class="danger"><p><strong>Caution!</strong></p>
<p>음성 합성할 텍스트를 URL encoding으로 변환 후 전송해야 합니다.</p>
</div>| 필수 |

### 참고 사항
`text` 필드에 음성 합성할 문장을 입력할 때, 쉼표(`,`)를 넣으면 음성 합성 결과에서 해당 부분을 조금 쉬었다가 말하게 되며, 마침표와 개행 문자를 연속 입력(`.\n`)하면 구분된 문장으로 합성 결과를 보내줍니다. 예를 들면, 다음은 두 문장으로 구분되며, 첫 번째 문장의 쉼표에서 한번 쉬었다가 읽게됩니다.

```
'그는 방으로 들어가고, 그녀는 집 밖으로 나갔다.\n이유는 알 수 없었다.'
```


### 응답 {#Response}
CSS API는 합성한 음성 데이터를 MP3 형식의 바이너리 데이터로 반환합니다. 다음은 HTTP 응답 예입니다.
```
[HTTP Response Header]
HTTP/1.1 200 OK
Server: nginx
Date: Wed, 28 Sep 2016 06:51:49 GMT
Content-Type: audio/mpeg;charset=utf-8
Content-Length: 19794
Connection: keep-alive
Keep-Alive: timeout=5
X-QUOTA: 10

[HTTP Response Body]
{MP3 형식의 바이너리 데이터}
```

### 오류 코드 {#ErrorCode}
CSS API가 발생시킬 수 있는 오류 코드는 다음과 같습니다.

| 오류 코드 | HTTP 응답 코드 | 오류 메시지                         | 설명                                                   |
|---------|-------------|-----------------------------------|-------------------------------------------------------|
| `VS01`    | 400         | speaker parameter is needed.      | speaker 파라미터가 누락되었습니다.                           |
| `VS02`    | 400         | Unsupported speaker.              | speaker 파라미터에 지원하지 않는 값이 입력된 경우 발생합니다.      |
| `VS03`    | 400         | speed parameter is needed.        | speed 파라미터가 누락되었습니다.                             |
| `VS04`    | 400         | Unsupported speed.                | speed 파라미터에 지원하지 않는 값이 입력된 경우 발생합니다.        |
| `VS05`    | 400         | text parameter is needed.         | text 파라미터가 누락되었습니다.                              |
| `VS06`    | 400         | text parameter exceeds max length. | text 파라미터에 입력할 수 있는 최대 글자 수를 초과했습니다.                |
| `VS99`    | 500         | Internal server error             | 서버 내부 오류가 발생했습니다. 포럼에 문의하시면 신속히 조치하겠습니다. |
