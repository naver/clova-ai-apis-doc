## UX 고려사항 {#UXGuideline}
일반적으로 사용자는 음성 인식 버튼을 누르자마자 발화를 시작하려고 할 것입니다. 하지만 음성 인식을 시작하는 *recognize()* 메서드를 호출하면 음성 인식을 위한 메모리 할당, 마이크 자원 할당, 음성 인식 서버 접속 및 인증 등의 준비 과정을 수행해야 하기 때문에 사용자의 발화 일부가 누락될 수 있습니다. 따라서, 앱은 모든 준비가 완료된 후 사용자에게 발화해도 좋다는 정보를 전달해야 합니다. 이 방법은 다음과 같이 처리할 수 있습니다.

* 모든 준비가 완료되면 *onReady* callback 메서드가 호출됩니다.
* *onReady* callback 메서드가 호출되기 전까지 "준비 중입니다."와 같은 메시지를 표시하거나 준비 중임을 나타내는 UI 표시를 해야 합니다.
* *onReady* callback 메서드가 호출되면 "이야기해주세요."와 같은 메시지를 표시하거나 사용 가능함을 나타내는 UI를 표시해야 합니다.

<div class="note">
<p><strong>Note!</strong></p>
<ul><li>(Android API) <em>SpeechRecognitionListener</em>의 <em>onReady</em>, <em>onRecord</em> 등의 callback 메서드는 <a href="https://developer.android.com/guide/components/processes-and-threads.html">Worker Thread</a>에서 호출되는 메서드이며, Handler에 등록하여 사용해야 합니다.</li>
<li>(iOS API) <em>cancel()</em> 메서드를 호출하면 호출한 시점부터 delegation 메서드들이 호출되지 않습니다. 따라서, 음성 인식이 끝났을 때 처리해야 하는 작업들은 <em>cancel()</em> 메서드 호출 이후에 따로 수행해야 합니다. </li>
</ul>
</div>
