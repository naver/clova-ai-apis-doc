## Android API 사용하기 {#UsingAndroidAPI}

Android API를 사용하려면 다음 절차를 따릅니다.

1. 다음 구문을 *app/build.gradle* 파일에 추가합니다.
```java
repositories {
    jcenter()
}
dependencies {
    compile 'com.naver.speech.clientapi:naverspeech-sdk-android:1.1.1'
}
```
2. 다음과 같이 Android Manifest 파일(AndroidManifest.xml)을 설정합니다.
  * 패키지 이름 : *manifest* 요소의 *package* 속성 값이 [사전 준비사항](#Preparation)에서 등록한 *안드로이드 앱 패키지 이름*과 같아야 합니다.
  * 권한 설정 : 사용자의 음성 입력을 마이크를 통해 녹음해야 하고 녹음된 데이터를 서버로 전송해야 합니다. 따라서, *android.permission.INTERNET*와 *android.permission.RECORD_AUDIO*에 대한 권한이 반드시 필요합니다.
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
   package="com.naver.naverspeech.client"
   android:versionCode="1" android:versionName="1.0" >
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```
3. (선택) proguard-rules.pro 파일에 다음을 추가합니다. 아래 코드는 앱을 보다 가볍고 안전하게 만들어줍니다.
```java
-keep class com.naver.speech.clientapi.SpeechRecognizer {
      protected private *;
}
```
<div class="note">
<p><strong>Note!</strong></p>
<p>네이버 Open API는 Android SDK 버전 10 이상을 지원합니다. 따라서, <em>build.gradle</em> 파일의 <em>minSdkVersion</em> 값을 이에 맞게 설정해야 합니다. </p>
</div>

클라이언트는 "준비", "녹음", "중간결과 출력", "끝점 추출", "최종결과 출력"과 같은 일련의 이벤트 흐름을 수행합니다. 애플리케이션 개발자는 *SpeechRecognitioinListener* 인터페이스를 상속받아 해당 이벤트가 발생할 때 처리할 동작을 구현하면 됩니다.

![](/CSR/Resources/Images/CSR_State_Diagram_for_Android.png)

<div class="note">
<p><strong>Note!</strong></p>
<p>API에 대한 자세한 설명은 <a href="http://naver.github.io/naverspeech-sdk-android/">http://naver.github.io/naverspeech-sdk-android</a>를 참고합니다. </p>
</div>

다음은 Android에서 CSR API를 사용한 예제 코드입니다.
* CSR API - Android용 예제
* 예제 코드 저장소 : https://github.com/naver/naverspeech-sdk-android
* 설명
  * [Main Activity](https://github.com/naver/naverspeech-sdk-android/blob/master/samples/EPDTypeAutoSample/app/src/main/java/com/naver/naverspeech/client/MainActivity.java) 클래스 : *SpeechRecognitionListener*를 초기화하고, 이후 이벤트를 handleMessage 에서 받아 처리합니다.
  * [SpeechRecognitionListener를 상속한 클래스](https://github.com/naver/naverspeech-sdk-android/blob/master/samples/EPDTypeAutoSample/app/src/main/java/com/naver/naverspeech/client/NaverRecognizer.java) : 음성인식 서버 연결,  음성전달, 인식결과 발생등의 이벤트에 따른 결과 처리 방법 정의합니다.

```java
// 1. Main Activity 클래스
public class MainActivity extends Activity {
	private static final String TAG = MainActivity.class.getSimpleName();
	private static final String CLIENT_ID = "YOUR CLIENT ID"; // "내 애플리케이션"에서 Client ID를 확인해서 이곳에 적어주세요.
    private RecognitionHandler handler;
    private NaverRecognizer naverRecognizer;
    private TextView txtResult;
    private Button btnStart;
    private String mResult;
    private AudioWriterPCM writer;
    // Handle speech recognition Messages.
    private void handleMessage(Message msg) {
        switch (msg.what) {
            case R.id.clientReady: // 음성인식 준비 가능
                txtResult.setText("Connected");
                writer = new AudioWriterPCM(Environment.getExternalStorageDirectory().getAbsolutePath() + "/NaverSpeechTest");
                writer.open("Test");
                break;
            case R.id.audioRecording:
                writer.write((short[]) msg.obj);
                break;
            case R.id.partialResult:
                mResult = (String) (msg.obj);
                txtResult.setText(mResult);
                break;
            case R.id.finalResult: // 최종 인식 결과
            	SpeechRecognitionResult speechRecognitionResult = (SpeechRecognitionResult) msg.obj;
            	List<String> results = speechRecognitionResult.getResults();
            	StringBuilder strBuf = new StringBuilder();
            	for(String result : results) {
            		strBuf.append(result);
            		strBuf.append("\n");
            	}
                mResult = strBuf.toString();
                txtResult.setText(mResult);
                break;
            case R.id.recognitionError:
                if (writer != null) {
                    writer.close();
                }
                mResult = "Error code : " + msg.obj.toString();
                txtResult.setText(mResult);
                btnStart.setText(R.string.str_start);
                btnStart.setEnabled(true);
                break;
            case R.id.clientInactive:
                if (writer != null) {
                    writer.close();
                }
                btnStart.setText(R.string.str_start);
                btnStart.setEnabled(true);
                break;
        }
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        txtResult = (TextView) findViewById(R.id.txt_result);
        btnStart = (Button) findViewById(R.id.btn_start);
        handler = new RecognitionHandler(this);
        naverRecognizer = new NaverRecognizer(this, handler, CLIENT_ID);
        btnStart.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if(!naverRecognizer.getSpeechRecognizer().isRunning()) {
                    mResult = "";
                    txtResult.setText("Connecting...");
                    btnStart.setText(R.string.str_stop);
                    naverRecognizer.recognize();
                } else {
                    Log.d(TAG, "stop and wait Final Result");
                    btnStart.setEnabled(false);
                    naverRecognizer.getSpeechRecognizer().stop();
                }
            }
        });
    }
    @Override
    protected void onStart() {
    	super.onStart(); // 음성인식 서버 초기화는 여기서
    	naverRecognizer.getSpeechRecognizer().initialize();
    }
    @Override
    protected void onResume() {
        super.onResume();
        mResult = "";
        txtResult.setText("");
        btnStart.setText(R.string.str_start);
        btnStart.setEnabled(true);
    }
    @Override
    protected void onStop() {
    	super.onStop(); // 음성인식 서버 종료
    	naverRecognizer.getSpeechRecognizer().release();
    }
    // Declare handler for handling SpeechRecognizer thread's Messages.
    static class RecognitionHandler extends Handler {
        private final WeakReference<MainActivity> mActivity;
        RecognitionHandler(MainActivity activity) {
            mActivity = new WeakReference<MainActivity>(activity);
        }
        @Override
        public void handleMessage(Message msg) {
            MainActivity activity = mActivity.get();
            if (activity != null) {
                activity.handleMessage(msg);
            }
        }
    }
}

// 2. SpeechRecognitionListener 를 상속한 클래스
class NaverRecognizer implements SpeechRecognitionListener {
	private final static String TAG = NaverRecognizer.class.getSimpleName();
	private Handler mHandler;
	private SpeechRecognizer mRecognizer;
	public NaverRecognizer(Context context, Handler handler, String clientId) {
		this.mHandler = handler;
		try {
			mRecognizer = new SpeechRecognizer(context, clientId);
		} catch (SpeechRecognitionException e) {
			e.printStackTrace();
		}
		mRecognizer.setSpeechRecognitionListener(this);
	}
	public SpeechRecognizer getSpeechRecognizer() {
		return mRecognizer;
	}
	public void recognize() {
		try {
			mRecognizer.recognize(new SpeechConfig(LanguageType.KOREAN, EndPointDetectType.AUTO));
		} catch (SpeechRecognitionException e) {
			e.printStackTrace();
		}
	}
	@Override
	@WorkerThread
	public void onInactive() {
		Message msg = Message.obtain(mHandler, R.id.clientInactive);
		msg.sendToTarget();
	}
	@Override
	@WorkerThread
	public void onReady() {
		Message msg = Message.obtain(mHandler, R.id.clientReady);
		msg.sendToTarget();
	}
	@Override
	@WorkerThread
	public void onRecord(short[] speech) {
		Message msg = Message.obtain(mHandler, R.id.audioRecording, speech);
		msg.sendToTarget();
	}
	@Override
	@WorkerThread
	public void onPartialResult(String result) {
		Message msg = Message.obtain(mHandler, R.id.partialResult, result);
		msg.sendToTarget();
	}
	@Override
	@WorkerThread
	public void onEndPointDetected() {
		Log.d(TAG, "Event occurred : EndPointDetected");
	}
	@Override
	@WorkerThread
	public void onResult(SpeechRecognitionResult result) {
		Message msg = Message.obtain(mHandler, R.id.finalResult, result);
		msg.sendToTarget();
	}
	@Override
	@WorkerThread
	public void onError(int errorCode) {
		Message msg = Message.obtain(mHandler, R.id.recognitionError, errorCode);
		msg.sendToTarget();
	}
	@Override
	@WorkerThread
	public void onEndPointDetectTypeSelected(EndPointDetectType epdType) {
		Message msg = Message.obtain(mHandler, R.id.endPointDetectTypeSelected, epdType);
		msg.sendToTarget();
	}
}
```
