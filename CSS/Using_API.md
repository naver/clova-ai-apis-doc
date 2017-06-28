# CSR API 사용하기 {#UsingAPI}

CSR API는 REST API이며, 음성 합성할 텍스트 데이터를 HTTP 통신으로 음성 합성 서버로 전달하면 됩니다. 음성 합성 서버가 제공하는 REST API의 URI는 다음과 같으며 POST 방식으로 연결을 시도해야 합니다.

```
https://openapi.naver.com/v1/voice/tts.bin
```

HTTP 요청으로 음성 합성을 요청할 때 [사전 준비사항](#Preparation)에서 발급받은 **Client ID**와 **Client Secret** 정보를 헤더에 포함시켜야 합니다. 다음과 같이 HTTP 요청 헤더를 구성할 수 있습니다.

```
[HTTP Request Header]
POST /v1/voice/tts.bin HTTP/1.1
Host: openapi.naver.com
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Naver-Client-Id: {앱 등록 시 발급받은 Client ID}
X-Naver-Client-Secret: {앱 등록 시 발급 받은 Client Secret}
```

HTTP 요청 본문에는 음성 합성할 텍스트 뿐만 아니라 목소리 종류와 속도를 정의할 수 있습니다. 다음은 "만나서 반갑습니다."라는 텍스트를 일반적인 속도의 여성 목소리로 합성하도록 설정한 본문 예입니다. 자세한 설명은 [CSR API 레퍼런스](#APIReference)를 참조합니다.

```
[HTTP Request Body]
speaker=mijin&speed=0&text=만나서 반갑습니다.
```

위와 같은 HTTP 요청을 음성 합성 서버로 전달하면 음성 합성 서버는 MP3 형식의 바이너리 데이터를 HTTP 응답 메시지로 반환합니다. 전달받은 음성 데이터를 재생하여 스피커로 출력하면 됩니다. 다음과 같은 HTTP 응답 예제입니다.

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

<div class="note">
<p><strong>Note!</strong></p>
<p>각 언어별 호출 예제는 <a href="#Examples">CSS API 레퍼런스의 호출 예제</a>를 참조합니다.</p>
</div>
