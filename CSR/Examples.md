## 구현 예제 {#Examples}

다음은 각 플랫폼별 CSR API 구현 예제입니다.
* [Android 구현 예제](#AndroidExample)
* [iOS 구현 예제](#iOSExample)

### Android 구현 예제 {#AndroidExample}

다음은 Android에서 CSR API를 사용한 예제 코드입니다.
* CSR API - Android용 예제
* 예제 코드 저장소 : https://github.com/naver/naverspeech-sdk-android
* 설명
  * [Main Activity](https://github.com/naver/naverspeech-sdk-android/blob/master/samples/EPDTypeAutoSample/app/src/main/java/com/naver/naverspeech/client/MainActivity.java) 클래스 : `SpeechRecognitionListener`를 초기화하고, 이후 이벤트를 handleMessage 에서 받아 처리합니다.
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

### iOS 구현 예제 {#iOSExample}
다음은 iOS에서 CSR API를 사용한 예제 코드입니다.
* CSR API - iOS용 예제
* 예제 코드 저장소 : https://github.com/naver/naverspeech-sdk-ios

```objectivec
import UIKit
import NaverSpeech
import Common
let ClientID = "YOUR_CLIENT_ID"
class AutoViewController: UIViewController {
    required init?(coder aDecoder: NSCoder) { // NSKRecognizer를 초기화 하는데 필요한 NSKRecognizerConfiguration을 생성
        let configuration = NSKRecognizerConfiguration(clientID: ClientID)
        configuration?.canQuestionDetected = true
        self.speechRecognizer = NSKRecognizer(configuration: configuration)
        super.init(coder: aDecoder)
        self.speechRecognizer.delegate = self
    }
    override func viewDidLoad() {
        super.viewDidLoad()
        self.setupLanguagePicker()
    }
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        if self.isViewLoaded && self.view.window == nil {
            self.view = nil
        }
    }
    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()
        let x = languagePickerButton.frame.minX
        let y = languagePickerButton.frame.maxY
        self.pickerView.frame = CGRect.init(x: x, y: y, width: languagePickerButton.bounds.size.width, height: self.pickerView.bounds.size.height)
    }
    @IBAction func languagePickerButtonTapped(_ sender: Any) {
        self.pickerView.isHidden = false
    }
    @IBAction func recognitionButtonTapped(_ sender: Any) { // 버튼 누르면 음성인식 시작
        if self.speechRecognizer.isRunning {
            self.speechRecognizer.stop()
        } else {
            self.speechRecognizer.start(with: self.languages.selectedLanguage)
            self.recognitionButton.isEnabled = false
            self.statusLabel.text = "Connecting......"
        }
    }
    @IBOutlet weak var languagePickerButton: UIButton!
    @IBOutlet weak var recognitionResultLabel: UILabel!
    @IBOutlet weak var recognitionButton: UIButton!
    @IBOutlet weak var statusLabel: UILabel!
    fileprivate let speechRecognizer: NSKRecognizer
    fileprivate let languages = Languages()
    fileprivate let pickerView = UIPickerView()
}

extension AutoViewController: NSKRecognizerDelegate { //NSKRecognizerDelegate protocol 구현

    public func recognizerDidEnterReady(_ aRecognizer: NSKRecognizer!) {
        print("Event occurred: Ready")
        self.statusLabel.text = "Connected"
        self.recognitionResultLabel.text = "Recognizing......"
        self.setRecognitionButtonTitle(withText: "Stop", color: .red)
        self.recognitionButton.isEnabled = true
    }
    public func recognizerDidDetectEndPoint(_ aRecognizer: NSKRecognizer!) {
        print("Event occurred: End point detected")
    }
    public func recognizerDidEnterInactive(_ aRecognizer: NSKRecognizer!) {
        print("Event occurred: Inactive")
        self.setRecognitionButtonTitle(withText: "Record", color: .blue)
        self.recognitionButton.isEnabled = true
        self.statusLabel.text = ""
    }
    public func recognizer(_ aRecognizer: NSKRecognizer!, didRecordSpeechData aSpeechData: Data!) {
        print("Record speech data, data size: \(aSpeechData.count)")
    }
    public func recognizer(_ aRecognizer: NSKRecognizer!, didReceivePartialResult aResult: String!) {
        print("Partial result: \(aResult)")
        self.recognitionResultLabel.text = aResult
    }
    public func recognizer(_ aRecognizer: NSKRecognizer!, didReceiveError aError: Error!) {
        print("Error: \(aError)")
        self.setRecognitionButtonTitle(withText: "Record", color: .blue)
        self.recognitionButton.isEnabled = true
    }
    public func recognizer(_ aRecognizer: NSKRecognizer!, didReceive aResult: NSKRecognizedResult!) {
        print("Final result: \(aResult)")
        if let result = aResult.results.first as? String {
            self.recognitionResultLabel.text = "Result: " + result
        }
    }
}
extension AutoViewController: UIPickerViewDelegate, UIPickerViewDataSource {
    func numberOfComponents(in pickerView: UIPickerView) -> Int {
        return 1
    }
    func pickerView(_ pickerView: UIPickerView, numberOfRowsInComponent component: Int) -> Int {
        return self.languages.count
    }
    func pickerView(_ pickerView: UIPickerView, titleForRow row: Int, forComponent component: Int) -> String? {
        return languages.languageString(at: row)
    }
    func pickerView(_ pickerView: UIPickerView, didSelectRow row: Int, inComponent component: Int) {
        languages.selectLanguage(at: row)
        languagePickerButton.setTitle(languages.selectedLanguageString, for: .normal)
        self.pickerView.isHidden = true
        if self.speechRecognizer.isRunning { //음성인식 중 언어를 변경하게 되면 음성인식을 즉시 중지(cancel)
            self.speechRecognizer.cancel()
            self.recognitionResultLabel.text = "Canceled"
            self.setRecognitionButtonTitle(withText: "Record", color: .blue)
            self.recognitionButton.isEnabled = true
        }
    }
}
fileprivate extension AutoViewController {
    func setupLanguagePicker() {
        self.view.addSubview(self.pickerView)
        self.pickerView.dataSource = self
        self.pickerView.delegate = self
        self.pickerView.showsSelectionIndicator = true
        self.pickerView.backgroundColor = UIColor.white
        self.pickerView.isHidden = true
    }
    func setRecognitionButtonTitle(withText text: String, color: UIColor) {
        self.recognitionButton.setTitle(text, for: .normal)
        self.recognitionButton.setTitleColor(color, for: .normal)
    }
}
```
