## 구현 예제 {#Examples}

다음은 각 언어별 CSR API 구현 예제입니다.
* [Java](#Java)
* [PHP](#PHP)
* [Node.js](#Nodejs)
* [Python](#Python)
* [C#](#Csharp)
* [cURL](#cURL)

### Java {#Java}

```java
// 네이버 음성합성 Open API 예제
import java.io.*;
import java.net.HttpURLConnection;
import java.net.URL;
import java.net.URLEncoder;
import java.util.Date;

public class APIExamTTS {

    public static void main(String[] args) {
        String clientId = "YOUR_CLIENT_ID";//애플리케이션 클라이언트 아이디값";
        String clientSecret = "YOUR_CLIENT_SECRET";//애플리케이션 클라이언트 시크릿값";
        try {
            String text = URLEncoder.encode("만나서 반갑습니다.", "UTF-8"); // 13자
            String apiURL = "https://openapi.naver.com/v1/voice/tts.bin";
            URL url = new URL(apiURL);
            HttpURLConnection con = (HttpURLConnection)url.openConnection();
            con.setRequestMethod("POST");
            con.setRequestProperty("X-Naver-Client-Id", clientId);
            con.setRequestProperty("X-Naver-Client-Secret", clientSecret);
            // post request
            String postParams = "speaker=mijin&speed=0&text=" + text;
            con.setDoOutput(true);
            DataOutputStream wr = new DataOutputStream(con.getOutputStream());
            wr.writeBytes(postParams);
            wr.flush();
            wr.close();
            int responseCode = con.getResponseCode();
            BufferedReader br;
            if(responseCode==200) { // 정상 호출
                InputStream is = con.getInputStream();
                int read = 0;
                byte[] bytes = new byte[1024];
                // 랜덤한 이름으로 mp3 파일 생성
                String tempname = Long.valueOf(new Date().getTime()).toString();
                File f = new File(tempname + ".mp3");
                f.createNewFile();
                OutputStream outputStream = new FileOutputStream(f);
                while ((read =is.read(bytes)) != -1) {
                    outputStream.write(bytes, 0, read);
                }
                is.close();
            } else {  // 에러 발생
                br = new BufferedReader(new InputStreamReader(con.getErrorStream()));
                String inputLine;
                StringBuffer response = new StringBuffer();
                while ((inputLine = br.readLine()) != null) {
                    response.append(inputLine);
                }
                br.close();
                System.out.println(response.toString());
            }
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### PHP {#PHP}

```php
// 네이버 음성합성 Open API 예제
<?php
  $client_id = "YOUR_CLIENT_ID";
  $client_secret = "YOUR_CLIENT_SECRET";
  $encText = urlencode("반갑습니다.");
  $postvars = "speaker=mijin&speed=0&text=".$encText;
  $url = "https://openapi.naver.com/v1/voice/tts.bin";
  $is_post = true;
  $ch = curl_init();
  curl_setopt($ch, CURLOPT_URL, $url);
  curl_setopt($ch, CURLOPT_POST, $is_post);
  curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
  curl_setopt($ch,CURLOPT_POSTFIELDS, $postvars);
  $headers = array();
  $headers[] = "X-Naver-Client-Id: ".$client_id;
  $headers[] = "X-Naver-Client-Secret: ".$client_secret;
  curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
  $response = curl_exec ($ch);
  $status_code = curl_getinfo($ch, CURLINFO_HTTP_CODE);
  echo "status_code:".$status_code."<br>";
  curl_close ($ch);
  if($status_code == 200) {
    //echo $response;
    $fp = fopen("tts.mp3", "w+");
    fwrite($fp, $response);
    fclose($fp);
    echo "<a href='tts.mp3'>TTS재생</a>";
  } else {
    echo "Error 내용:".$response;
  }
?>
```

### Node.js {#Nodejs}

```js
// 네이버 음성합성 Open API 예제
var express = require('express');
var app = express();
var client_id = 'YOUR_CLIENT_ID';
var client_secret = 'YOUR_CLIENT_SECRET';
var fs = require('fs');
app.get('/tts', function (req, res) {
   var api_url = 'https://openapi.naver.com/v1/voice/tts.bin';
   var request = require('request');
   var options = {
       url: api_url,
       form: {'speaker':'mijin', 'speed':'0', 'text':'좋은 하루 되세요'},
       headers: {'X-Naver-Client-Id':client_id, 'X-Naver-Client-Secret': client_secret}
    };
    var writeStream = fs.createWriteStream('./tts1.mp3');
    var _req = request.post(options).on('response', function(response) {
       console.log(response.statusCode) // 200
       console.log(response.headers['content-type'])
    });
  _req.pipe(writeStream); // file로 출력
  _req.pipe(res); // 브라우저로 출력
 });
 app.listen(3000, function () {
   console.log('http://127.0.0.1:3000/tts app listening on port 3000!');
 });
```

### Python {#Python}

```python
// 네이버 음성합성 Open API 예제
import os
import sys
import urllib.request
client_id = "YOUR_CLIENT_ID"
client_secret = "YOUR_CLIENT_SECRET"
encText = urllib.parse.quote("반갑습니다 네이버")
data = "speaker=mijin&speed=0&text=" + encText;
url = "https://openapi.naver.com/v1/voice/tts.bin"
request = urllib.request.Request(url)
request.add_header("X-Naver-Client-Id",client_id)
request.add_header("X-Naver-Client-Secret",client_secret)
response = urllib.request.urlopen(request, data=data.encode('utf-8'))
rescode = response.getcode()
if(rescode==200):
    print("TTS mp3 저장")
    response_body = response.read()
    with open('1111.mp3', 'wb') as f:
        f.write(response_body)
else:
    print("Error Code:" + rescode)
```

### C# {#Csharp}

```csharp
// 네이버 음성합성 Open API 예제
using System;
using System.Net;
using System.Text;
using System.IO;

namespace NaverAPI_Guide
{
    public class APIExamTTS
    {
        static void Main(string[] args)
        {
            string text = "좋은 하루 되세요."; // 음성합성할 문자값
            string url = "https://openapi.naver.com/v1/voice/tts.bin";
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Headers.Add("X-Naver-Client-Id", "YOUR-CLIENT-ID");
            request.Headers.Add("X-Naver-Client-Secret", "YOUR-CLIENT-SECRET");
            request.Method = "POST";
            byte[] byteDataParams = Encoding.UTF8.GetBytes("speaker=mijin&speed=0&text=" + text);
            request.ContentType = "application/x-www-form-urlencoded";
            request.ContentLength = byteDataParams.Length;
            Stream st = request.GetRequestStream();
            st.Write(byteDataParams, 0, byteDataParams.Length);
            st.Close();
            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            string status = response.StatusCode.ToString();
            Console.WriteLine("status="+ status);
            using (Stream output = File.OpenWrite("c:/tts.mp3"))
            using (Stream input = response.GetResponseStream())
            {
                input.CopyTo(output);
            }
            Console.WriteLine("c:/tts.mp3 was created");
        }
    }
}
```

### cURL {#cURL}

```bash
curl "https://openapi.naver.com/v1/voice/tts.bin" \
	-d "speaker=mijin&speed=0&text=만나서 반갑습니다." \
	-H "Content-Type: application/x-www-form-urlencoded; charset=UTF-8" \
	-H "X-Naver-Client-Id: {애플리케이션 등록 시 발급받은 client id 값}" \
	-H "X-Naver-Client-Secret: {애플리케이션 등록 시 발급받은 client secret 값}" -v \
		> out.mp3
```
