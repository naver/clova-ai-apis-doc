## CFR API 사용하기 {#UsingAPI}

CFR API는 REST API이며, 얼굴 인식을 수행할 이미지 데이터를 HTTP 통신으로 음성 합성 서버로 전달하면 됩니다. 음성 합성 서버가 제공하는 REST API의 URI는 다음과 같으며 POST 방식으로 연결을 시도해야 합니다.

```
// 유명인 얼굴 인식 API
https://openapi.naver.com/v1/vision/celebrity

// 얼굴 감지 API
https://openapi.naver.com/v1/vision/face
```


HTTP 요청으로 얼굴 인식을 요청할 때 [사전 준비사항](#Preparation)에서 발급받은 **Client ID**와 **Client Secret** 정보를 헤더에 포함시켜야 합니다. 또한 요청을 *multipart* 형식으로 보내야 하며, 메시지의 이름은 **image**여야 합니다. 다음은 유명인 얼굴 인식 API를 호출할 때 보내는 HTTP 요청 메시지 예입니다.

```
[HTTP Request Header]
POST /v1/vision/celebrity HTTP/1.1
Host: openapi.naver.com
Content-Type: multipart/form-data; boundary={boundary-text}
X-Naver-Client-Id: {앱 등록 시 발급받은 Client ID}
X-Naver-Client-Secret: {앱 등록 시 발급 받은 Client Secret}
Content-Length: 96703

--{boundary-text}
Content-Disposition: form-data; name="image"; filename="test.jpg"
Content-Type: image/jpeg

{image binary data}
--{boundary-text}--
```

HTTP 요청을 통해 보내는 이미지의 포맷에 대한 제약은 없습니다. 다만 *GIF와 같은 이미지 포맷은 첫 번째 프레임의 이미지*를 기준으로 얼굴 인식을 수행합니다. 위와 같은 HTTP 요청을 얼굴 인식 서버로 전달하면 얼굴 인식 서버는 JSON 형태의 분석 결과 데이터를 HTTP 응답 메시지로 반환합니다. 다음은 응답 예제입니다.

```
{
 	"info": {
 		"size": {
 			"width": 900,
 			"height": 675
 		},
 		"faceCount": 2
 	},
 	"faces": [{
 		"celebrity": {
 			"value": "안도하루카",
 			"confidence": 0.266675
 		}
 	}, {
 		"celebrity": {
 			"value": "서효림",
 			"confidence": 0.304959
 		}
 	}]
 }
```

위와 같은 방식으로 얼굴 감지 API도 사용할 수 있으며, API별에 따라 분석 결과에 따라 응답 메시지로 전달되는 JSON 데이터의 내용이 달라집니다. 이에 자세한 명세는 [CFR API 레퍼런스](#APIReference)를 참조합니다.
