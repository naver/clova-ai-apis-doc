## iOS API 사용하기 {#UsingiOSAPI}

iOS API를 사용하려면 다음 절차를 따릅니다.

1. [iOS용 예제](https://github.com/naver/naverspeech-sdk-ios)를 clone하거나 Zip 파일로 다운로드하여 압축을 해제합니다.
```bash
git clone https://github.com/naver/naverspeech-sdk-ios.git
또는
wget https://github.com/naver/naverspeech-sdk-ios/archive/master.zip
unzip master.zip
```
2. iOS 예제에서 *framework/NaverSpeech.framework* 디렉터리를 개발하는 앱의 **Embedded Binaries**에 추가합니다.
3. 다음과 같이 iOS Bundle Identifier를 설정합니다.
  * Bundle Identifier : [사전 준비사항](#Preparation)에서 등록한 *iOS Bundle ID*와 같아야 합니다.
  * 권한 설정 : 사용자의 음성 입력을 마이크를 통해 녹음해야 하고 녹음된 데이터를 서버로 전송해야 합니다. 따라서, *key* 값을 다음과 같이 설정합니다.
```xml
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

<div class="note">
<p><strong>Note!</strong></p>
<ul><li>iOS API를 제공하기 위해 Universal binary(Fat binary) 형태의 프레임워크를 제공하고 있습니다. 따라서 <strong>Build Setting</strong>에서 <strong>Enable Bitcode</strong> 옵션을 사용할 수 없으므로 <em>No</em>로 설정해야 합니다.</li>
<li>네이버 OpenAPI는 iOS 버전 8 이상을 지원합니다. 따라서, <strong>Deployment Target</strong> 값을 이에 맞게 설정해야 합니다.</li>
</ul>
</div>


클라이언트는 "준비", "중간결과 출력", "끝점 추출", "최종결과 출력"과 같은 일련의 이벤트 흐름을 수행합니다. 애플리케이션 개발자는 해당 이벤트가 발생할 때 원하는 동작을 수행하도록 *NSKRecognizerDelegate* protocol을 구현하면 됩니다.

![](/CSR/Resources/Images/CSR_State_Diagram_for_iOS.png)

<div class="note">
<p><strong>Note!</strong></p>
<p>API에 대한 자세한 설명은 <a href="http://naver.github.io/naverspeech-sdk-ios/Classes/NSKRecognizer.html">http://naver.github.io/naverspeech-sdk-ios/Classes/NSKRecognizer.html</a>를 참고합니다. </p>
</div>

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
