## CFR API 레퍼런스 {#APIReference}
CFR API는 다음과 같은 API를 제공합니다.

* [유명인 얼굴 인식 API](#CelebrityAPI)
* [얼굴 감지 API](#FaceAPI)

### 유명인 얼굴 인식 API {#CelebrityAPI}
입력받은 이미지로부터 얼굴을 감지하고 감지한 얼굴이 어떤 유명인과 닮았는지 분석하여 그 결과를 반환하는 REST API입니다. 이미지에서 다음과 같은 정보를 분석합니다.

* 감지된 얼굴의 수
* 감지된 각 얼굴을 분석한 정보
  * 닮은 유명인 이름
  * 해당 유명인을 닮은 정도

#### 기본 정보
유명인 얼굴 인식 API의 요청 URI 및 요청에 필요한 헤더 정보는 다음과 같습니다.

| 메서드  | 요청 URI                      | 필요 헤더                             |
|-------|------------------------------|-------------------------------------|
| POST  | https://openapi.naver.com/v1/vision/celebrity | <ul><li>X-Naver-Client-Id: <a href="#Preparation">사전 준비사항</a>에서 발급받은 Client ID</li><li>X-Naver-Client-Secret: <a href="#Preparation">사전 준비사항</a>에서 발급 받은 Client Secret</li></ul> |

#### 요청 파라미터
Multipart 메시지에 이름이 `image`라는 메시지로 이미지의 바이너리 데이터를 전달해야 합니다. **최대 2MB의 이미지 데이터를 지원**합니다. 다음은 헤더를 포함한 HTTP 요청 예제입니다.

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

#### 응답
유명인 얼굴 인식 API는 분석한 결과를 JSON 형식의 데이터로 반환합니다. JSON 응답의 각 필드에 대한 설명은 다음과 같습니다.

| 필드 이름                      | 데이터 타입     | 설명                                                |
|------------------------------|--------------|----------------------------------------------------|
| `info`                         | object       | 입력된 이미지 크기와 인식된 얼굴의 개수 정보를 가지는 객체       |
| `info.size`                    | [place object](#placeObject) | 입력된 이미지의 크기 정보를 가지는 객체      |
| `info.faceCount`               | number       | 닮은 유명인의 수                                       |
| `faces[]`                      | object array | 닮은 유명인별 분석 결과를 가지는 객체 배열                   |
| `faces[].celebrity`            | object       | 닮은 유명인의 정보를 가지는 객체                           |
| `faces[].celebrity.value`      | string       | 닮은 유명인의 이름                                      |
| `faces[].celebrity.confidence` | number       | 해당 유명인과 닮았음을 확신하는 정도. **0에서 1사이의 실수**로 표현됩니다. 1에 가까울수록 높은 확신을 나타냅니다. |

 다음은 얼굴 감지 API 요청에 대한 응답 예입니다.
```json
// 닮은 유명인을 찾은 경우
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

// 닮은 유명인을 찾지 못한 경우
{
	"info": {
		"size": {
			"width": 768,
			"height": 1280
		},
		"faceCount": 0
	},
	"faces": []
}
```

### 얼굴 감지 API {#FaceAPI}
입력받은 이미지로부터 얼굴을 감지하고 입력된 이미지에서 얼마나 많은 얼굴이 감지되었고 각 얼굴이 어디에 어떤 크기로 위치하며 어떤 모습을 하고 있는지 반환하는 REST API입니다. 이미지에서 다음과 같은 정보를 분석합니다.

* 감지된 얼굴의 수
* 감지된 각 얼굴을 분석한 정보
  * 감지된 각 얼굴의 좌표 및 크기
  * 감지된 각 얼굴의 눈, 코, 입의 좌표
  * 감지된 얼굴의 추정 성별 및 추정치
  * 감지된 얼굴의 추정 나이 및 추정치
  * 감지된 얼굴에서 분석된 감정
  * 감지된 얼굴의 방향

#### 기본 정보
얼굴 감지 API의 요청 URI 및 요청에 필요한 헤더 정보는 다음과 같습니다.

| 메서드  | 요청 URI                          | 필요 헤더                                     |
|-------|----------------------------------|---------------------------------------------|
| POST | https://openapi.naver.com/v1/vision/face | <ul><li>X-Naver-Client-Id: <a href="#Preparation">사전 준비사항</a>에서 발급받은 Client ID</li><li>X-Naver-Client-Secret: <a href="#Preparation">사전 준비사항</a>에서 발급 받은 Client Secret</li></ul> |

#### 요청 파라미터
Multipart 메시지에 이름이 `image`라는 메시지로 이미지의 바이너리 데이터를 전달해야 합니다. **최대 2MB의 이미지 데이터를 지원**합니다. 다음은 헤더를 포함한 HTTP 요청 예제입니다.

```
[HTTP Request Header]
POST /v1/vision/face HTTP/1.1
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

#### 응답
얼굴 감지 API는 분석한 결과를 JSON 형식의 데이터로 반환합니다. JSON 응답의 각 필드에 대한 설명은 다음과 같습니다.

| 필드 이름                     | 데이터 타입     | 설명                                                |
|-----------------------------|--------------|----------------------------------------------------|
| `info`                        | object       | 입력된 이미지 크기와 인식된 얼굴의 개수 정보를 가지는 객체       |
| `info.size`                   | [place object](#placeObject) | 입력된 이미지의 크기 정보를 가지는 객체     |
| `info.faceCount`              | number       | 감지된 얼굴의 수                                       |
| `faces[]`                     | object array | 감지된 얼굴의 개별 분석 결과를 가지는 객체 배열               |
| `faces[].roi`                 | [place object](#placeObject) | 감지된 특정 얼굴의 좌표 및 크기 정보를 가지는 객체 |
| `faces[].landmark`            | object       | 감지된 얼굴의 눈, 코, 입의 위치를 가지는 객체                |
| `faces[].landmark.leftEye`    | [place object](#placeObject) | 왼쪽 눈의 위치              |
| `faces[].landmark.rightEye`   | [place object](#placeObject) | 오른쪽 눈의 위치             |
| `faces[].landmark.nose`       | [place object](#placeObject) | 코의 위치                  |
| `faces[].landmark.leftMouth`  | [place object](#placeObject) | 왼쪽 입 꼬리의 위치           |
| `faces[].landmark.rightMouth` | [place object](#placeObject) | 오른 쪽 입 꼬리의 위치        |
| `faces[].gender`              | object       | 감지된 얼굴의 성별을 추정한 정보를 가지는 객체                |
| `faces[].gender.value`        | string       | 인식된 성별. `"male"` 또는 `"female"` 값을 가집니다.          |
| `faces[].gender.confidence`   | number       | 인식된 성별을 확신하는 정도. **0에서 1사이의 실수**로 표현됩니다. 1에 가까울수록 높은 확신을 나타냅니다. |
| `faces[].age`                 | object       | 감지된 얼굴의 나이를 추정한 정보를 가지는 객체                |
| `faces[].age.value`           | string       | 인식된 나이. "22~26"와 같이 나이의 범위가 표현된 문자열입니다.  |
| `faces[].age.confidence`      | number       | 인식된 나이를 확신하는 정도. **0에서 1사이의 실수**로 표현됩니다. 1에 가까울수록 높은 확신을 나타냅니다. |
| `faces[].emotion`             | object       | 감지된 얼굴의 감정을 추천한 정보를 가지는 객체                |
| `faces[].emotion.value`       | string       | 인식된 감정. "smile"과 같이 얼굴의 표정이나 감정을 나타내는 문자열입니다. 다음과 같은 값을 가집니다. <ul><li>angry</li><li>disgust</li><li>fear</li><li>laugh</li><li>neutral</li><li>sad</li><li>surprise</li><li>smile</li><li>talking</li></ul> |
| `faces[].emotion.confidence`  | number       | 인식된 감정을 확신하는 정도. **0에서 1사이의 실수**로 표현됩니다. 1에 가까울수록 높은 확신을 나타냅니다. |
| `faces[].pose`                | object       | 감지된 얼굴이 어떤 포즈인지 추정한 정보를 가지는 객체           |
| `faces[].pose.value`          | string       | 인식된 얼굴의 포즈. "frontal_face"와 같이 얼굴의 방향을 나타내는 문자열입니다. 다음과 같은 값을 가집니다. <ul><li>part_face</li><li>false_face</li><li>sunglasses</li><li>frontal_face</li><li>left_face</li><li>right_face</li><li>rotate_face</li></ul> |
| `faces[].pose.confidence`     | number       | 인식된 얼굴의 방향을 확신하는 정도. **0에서 1사이의 실수**로 표현됩니다. 1에 가까울수록 높은 확신을 나타냅니다. |


 다음은 얼굴 감지 API 요청에 대한 응답 예입니다.
```json
// 1개의 얼굴을 감지한 경우
{
 "info": {
   "size": {
     "width": 900,
     "height": 1349
   },
   "faceCount": 1
 },
 "faces": [{
   "roi": {
     "x": 235,
     "y": 227,
     "width": 326,
     "height": 326
   },
   "landmark": {
     "leftEye": {
       "x": 311,
       "y": 289
     },
     "rightEye": {
       "x": 425,
       "y": 287
     },
     "nose": {
       "x": 308,
       "y": 346
     },
     "leftMouth": {
       "x": 306,
       "y": 425
     },
     "rightMouth": {
       "x": 383,
       "y": 429
     }
   },
   "gender": {
     "value": "male",
     "confidence": 0.91465
   },
   "age": {
     "value": "22~26",
     "confidence": 0.742265
   },
   "emotion": {
     "value": "simile",
     "confidence": 0.460465
   },
   "pose": {
     "value": "frontal_face",
     "confidence": 0.937789
   }
 }]
}

// 감지한 얼굴이 없을 경우
{
 	"info": {
 		"size": {
 			"width": 700,
 			"height": 800
 		},
 		"faceCount": 0
 	},
 	"faces": []
 }
```

### 오류 코드 {#ErrorCode}
CFR API가 발생시킬 수 있는 오류 코드는 다음과 같습니다.

| 오류 코드 | HTTP 응답 코드 | 오류 메시지                              | 설명                                                |
|---------|-------------|----------------------------------------|----------------------------------------------------|
| `ER01`  | 400         | image parameter is needed.             | image 파라미터가 누락되었습니다.                          |
| `ER02`  | 400         | Failed to receive image content.       | 이미지 데이터를 수신하는데 실패했습니다.                     |
| `ER03`  | 400         | Bad reqeust.                           | 잘못된 요청을 수신했습니다.                               |
| `ER04`  | 400         | Image size is too large.               | 이미지의 크기가 2MB를 넘었습니다.                         |
| `ER11`  | 400         | Abnormal image format.                 | 인식할 수 없는 이미지 데이터가 입력되었습니다.                |
| `ER12`  | 400         | Abnormal image width v.s height ratio. | 이미지의 너비가 높이의 4배 이상입니다.                      |
| `ER13`  | 400         | Image width is to small.               | 이미지의 너비가 50 픽셀보다 작습니다.                      |
| `ER14`  | 400         | Image height is too small.             | 이미지의 높이가 50 픽셀보다 작습니다.                      |
| `ER15`  | 400         | Failed to analyze image.               | 분석할 수 없는 이미지가 입력되었습니다.                     |
| `ER21`  | 400         | Timeout error.                         | 서버에서 이미지 분석을 시간 내에 처리하지 못했습니다.           |
| `ER22`  | 400         | Server is too busy.                    | 현재 이미지 분석 요청이 많아 처리할 수 없습니다.              |
| `ER92`  | 500         | Failed to generate valid json string.  | 서버에서 유효한 형식의 JSON 데이터를 결과로 생성하지 못했습니다. |
| `ER99`  | 500         | Internal server error.                 | 내부 서버 오류입니다. 포럼에 문의하시면 신속히 조치하겠습니다.    |

### Place object {#placeObject}
CFR API는 HTTP 응답의 JSON 데이터에 감지한 얼굴 및 얼굴의 부위를 표시하기 위해 다음과 같이 place 객체를 공유하여 사용합니다. 각 필드는 선택적이며, 크기 정보를 나타낼 때와 위치 정보를 나타낼 때 선택적으로 사용될 수 있습니다.

| 필드 이름  | 데이터 타입 | 설명                                 | 필수 여부 |
|----------|--------|---------------------------------------|---------|
| `width`    | number | 입력된 이미지, 인식된 얼굴의 너비 정보(px)     | 선택     |
| `height`   | number | 입력된 이미지, 인식된 얼굴의 높이 정보(px)     | 선택     |
| `x`        | number | 인식된 얼굴 및 얼굴 부위의 위치 정보를 나타내기 위한 x 좌표(px). 기준점은 이미지의 좌상단 모서리입니다. | 선택     |
| `y`        | number | 인식된 얼굴 및 얼굴 부위의 위치 정보를 나타내기 위한 y 좌표(px). 기준점은 이미지의 좌상단 모서리입니다. | 선택     |
