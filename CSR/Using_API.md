## API 사용하기 {#UsingAPI}
CSR API는 Android용과 iOS용 SDK를 통해 제공되고 있습니다. 여기에서는 각 플랫폼별 CSR API를 사용하는 방법에 대해 설명합니다.

* [Andorid API 사용하기](#UsingAndroidAPI)
* [iOS API 사용하기](#UsingiOSAPI)

### Android API 사용하기 {#UsingAndroidAPI}

Android API를 사용하려면 다음 절차를 따릅니다.

<ol>
  <li>다음 구문을 <code>app/build.gradle</code> 파일에 추가합니다.
  <pre><code>repositories {
    jcenter()
}
dependencies {
    compile 'com.naver.speech.clientapi:naverspeech-sdk-android:1.1.1'
}</code></pre>
  </li>
  <li>다음과 같이 Android Manifest 파일(AndroidManifest.xml)을 설정합니다.
    <ul>
      <li>패키지 이름 : <code>manifest</code> 요소의 <code>package</code> 속성 값이 <a href="Preparation">사전 준비사항</a>에서 등록한 <strong>안드로이드 앱 패키지 이름</strong>과 같아야 합니다.</li>
      <li>권한 설정 : 사용자의 음성 입력을 마이크를 통해 녹음해야 하고 녹음된 데이터를 서버로 전송해야 합니다. 따라서, <code>android.permission.INTERNET</code>와 <code>android.permission.RECORD_AUDIO</code>에 대한 권한이 반드시 필요합니다.</li>
    </ul>
  <pre><code>&lt;manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.naver.naverspeech.client"
          android:versionCode="1" android:versionName="1.0" &gt;
&lt;uses-permission android:name="android.permission.INTERNET" /&gt;
&lt;uses-permission android:name="android.permission.RECORD_AUDIO" /&gt;
&lt;uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" /&gt;
&lt;uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /&gt;</code></pre>
  </li>
  <li>(선택) proguard-rules.pro 파일에 다음을 추가합니다. 아래 코드는 앱을 보다 가볍고 안전하게 만들어줍니다.
  <pre><code>-keep class com.naver.speech.clientapi.SpeechRecognizer {
        protected private *;
}</code></pre>
  </li>
</ol>

<div class="note">
<p><strong>Note!</strong></p>
<p>네이버 Open API는 Android SDK 버전 10 이상을 지원합니다. 따라서, <em>build.gradle</em> 파일의 <code>minSdkVersion</code> 값을 이에 맞게 설정해야 합니다. </p>
</div>

클라이언트는 "준비", "녹음", "중간결과 출력", "끝점 추출", "최종결과 출력"과 같은 일련의 이벤트 흐름을 수행합니다. 애플리케이션 개발자는 `SpeechRecognitioinListener` 인터페이스를 상속받아 해당 이벤트가 발생할 때 처리할 동작을 구현하면 됩니다.

![](/CSR/Resources/Images/CSR_State_Diagram_for_Android.png)

<div class="note">
<p><strong>Note!</strong></p>
<p>API에 대한 자세한 설명은 <a href="http://naver.github.io/naverspeech-sdk-android/">http://naver.github.io/naverspeech-sdk-android</a>를 참고합니다. </p>
</div>

### iOS API 사용하기 {#UsingiOSAPI}

iOS API를 사용하려면 다음 절차를 따릅니다.

<ol>
  <li><a href="https://github.com/naver/naverspeech-sdk-ios">iOS용 예제</a>를 clone하거나 Zip 파일로 다운로드하여 압축을 해제합니다.
  <pre><code>git clone https://github.com/naver/naverspeech-sdk-ios.git
또는
wget https://github.com/naver/naverspeech-sdk-ios/archive/master.zip
unzip master.zip</code></pre>
  </li>
  <li>iOS 예제에서 <code>framework/NaverSpeech.framework</code> 디렉터리를 개발하는 앱의 <strong>Embedded Binaries</strong>에 추가합니다.</li>
  <li>다음과 같이 iOS Bundle Identifier를 설정합니다.
    <ul>
      <li>Bundle Identifier : <a href="#Preparation">사전 준비사항</a>에서 등록한 <strong>iOS Bundle ID</strong>와 같아야 합니다.</li>
      <li>권한 설정 : 사용자의 음성 입력을 마이크를 통해 녹음해야 하고 녹음된 데이터를 서버로 전송해야 합니다. 따라서, <code>key</code> 값을 다음과 같이 설정합니다.
        <pre><code>&lt;key&gt;NSMicrophoneUsageDescription&lt;/key&gt;
&lt;string&gt;&lt;/string&gt;</code></pre>
      </li>
    </ul>
  </li>
</ol>

<div class="note">
<p><strong>Note!</strong></p>
<ul><li>iOS API를 제공하기 위해 Universal binary(Fat binary) 형태의 프레임워크를 제공하고 있습니다. 따라서 <strong>Build Setting</strong>에서 <strong>Enable Bitcode</strong> 옵션을 사용할 수 없으므로 <strong>No</strong>로 설정해야 합니다.</li>
<li>네이버 Open API는 iOS 버전 8 이상을 지원합니다. 따라서, <strong>Deployment Target</strong> 값을 이에 맞게 설정해야 합니다.</li>
</ul>
</div>


클라이언트는 "준비", "중간결과 출력", "끝점 추출", "최종결과 출력"과 같은 일련의 이벤트 흐름을 수행합니다. 애플리케이션 개발자는 해당 이벤트가 발생할 때 원하는 동작을 수행하도록 `NSKRecognizerDelegate` protocol을 구현하면 됩니다.

![](/CSR/Resources/Images/CSR_State_Diagram_for_iOS.png)

<div class="note">
<p><strong>Note!</strong></p>
<p>API에 대한 자세한 설명은 <a href="http://naver.github.io/naverspeech-sdk-ios/Classes/NSKRecognizer.html">http://naver.github.io/naverspeech-sdk-ios/Classes/NSKRecognizer.html</a>를 참고합니다. </p>
</div>
