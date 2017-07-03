# CSR API 릴리즈 노트
이 페이지는 CSR API의 릴리즈 노트를 제공합니다.

## 2017-05-26 - v1.1.3
**Added/Updated**
* 지원 아키텍처 추가 : arm, arm-v7만 지원하던 점을 개선하여 모든 아키텍쳐에 호환되도록 수정하였습니다.

**Fixed**
없음

**Known Issues**
없음

## 2017-01-25 - v1.1.2
**Added/Updated**
* 지원 언어 추가: 중국어, 일본어 추가

**Fixed**
없음

**Known Issues**
없음

## 2016-12-06 - v1.1.0
**Added/Updated**
* [iOS 버전 openAPI](https://github.com/naver/naverspeech-sdk-ios) 출시
* 끝점 추출(Endpoint detection) 방식 추가
  * 기존에는 발성을 멈추면 자동으로 인식 결과를 반환해주었습니다. 이는 발성의 끝점 추출(Endpoint detection)을 서버에서 자동으로 수행하는 방식입니다. 이번 업데이트에서는, 끝점 추출 동작에 대해 두 가지 방식을 새롭게 추가했습니다. 아래는 기존 방식을 포함한 세 가지 방식에 대한 설명이며, 자세한 사용 방법은 [Android 예제코드](https://github.com/naver/naverspeech-sdk-android) 또는 [iOS 예제코드](https://github.com/naver/naverspeech-sdk-ios)를 참고하시기 바랍니다.
  * Auto : 기존과 같은 방식입니다. 발성을 멈추면, 자동으로 인식 결과를 반환해줍니다.
  * Manual : 발성의 끝점을 사용자가 명시적으로 알립니다. 따라서 발성을 잠시 멈추어도 인식이 끝나지 않습니다. 이 방식을 이용하여 무전기의 push-to-talk과 유사한 동작을 구현할 수 있습니다.
  * Hybrid : 위의 Auto와 Manual 중 어떤 방식을 선택할 것인지는 빌드 타임에, 즉 코드상에서 개발자가 값을 넣어줌으로써 결정됩니다. 하지만 Hybrid 방식을 선택하면 이를 런타임에 결정할 수 있습니다. 가령, 버튼을 짧게 클릭하면 Auto 방식으로, 길게 누른 상태에서 발성하면 Manual 방식으로 다이나믹하게 결정하도록 구현할 수 있습니다.
* 열악한 환경에서의 안정화
  * 네트워크 환경이 열악할 경우에도 음성인식이 보다 안정적으로 동작하도록, 내부적으로 오디오 버퍼링을 비롯한 여러 안정화 로직을 추가했습니다.
  * 다양한 예외 상황에서 라이브러리가 안정적으로 동작하도록 내부적으로 많은 개선을 하였습니다. 그리고 IPv6 device에서 동작 가능하도록 수정했습니다.
* Android 버전 openAPI 사용성 개선
* 기존에는 libs 파일을 복사하여 사용했지만, 이제부터는 android studio에서 gradle에 의존성을 추가하여 바로 SDK를 사용할 수 있도록 수정했습니다. 또한 사용성 개선을 위해 일부 메서드명과 형식을 변경하였습니다.

**Fixed**
없음

**Known Issues**
없음

## 2016-03-23 - v1.0.4
**Added/Updated**
없음

**Fixed**
* package 명이 어느 정도 길어질 경우 잘려 인증오류(70) 발생하는 문제 수정, package 명의 길이가 256bytes 이하면 문제 없음.
* eclipse에서 Javadoc을 볼 경우 한글이 깨지는 문제 수정.

**Known Issues**
없음

## 2016-03-04 - v1.0.3
**Added/Updated**
없음

**Fixed**
* JNI 라이브러리에 armeabi 뿐만 아니라 armeabi-v7a 추가

**Known Issues**
없음

## 2016-02-03 - v1.0.2
**Added/Updated**
* SpeechRecognizer 클래스 생성자에서 SpeechRecognitionException 예외가 발생할 수 있습니다.
* Android 2.3.3부터 SDK를 사용할 수 있도록 수정했습니다.

**Fixed**
* Fatal signal 11 (SIGSEGV) at 0xdeadbaad (code=1), thread 15107 (erspeech.client) 버그 수정

**Known Issues**
없음

## 2016-01-26 - v1.0.0
**Added/Updated**
* CSR API 정식 배포

**Fixed**
없음

**Known Issues**
없음
