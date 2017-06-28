# CSR API 레퍼런스
CSR API는 HTTP 요청으로 텍스트를 입력받아 음성 합성한 후 이를 MP3 형식의 바이너리 데이터로 반환하는 REST API입니다. 여기에서는 CSR API에 대해 상세히 설명합니다.

## 기본 정보 {#BasicInfo}
CSR API의 요청 URI 및 요청에 필요한 헤더 정보는 다음과 같습니다.

| 메서드 | 요청 URI                                    | 필요 헤더              |
|------|--------------------------------------------|----------------------|
| POST | https://openapi.naver.com/v1/voice/tts.bin | <ul><li>X-Naver-Client-Id: <a href="#Preparation">사전 준비사항</a>에서 발급받은 Client ID</li><li>X-Naver-Client-Secret: <a href="#Preparation">사전 준비사항</a>에서 발급 받은 Client Secret</li></ul> |

## 요청 파라미터 {#RequestParameter}
CSR API에 필요한 요청 헤더는 본문에 입력하며 본문에 다음과 같이 파라미터를 작성해야 합니다.

```
[HTTP Request Body]
speaker={string}&speed={integer}&text={string}
```

다음은 파라미터에 대한 설명입니다.

| 파라미터 이름 | 타입     | 설명 | 필수 여부 |
|------------|---------|
| speaker    | string  | 음성 합성에 사용할 목소리 종류 <ul><li>mijin : 한국어, 여성 음색</li> <li>jinho : 한국어, 남성 음색</li> <li>clara : 영어, 여성 음색</li> <li>matt : 영어, 남성 음색</li> <li>yuri : 일본어, 여성 음색</li><li>shinji : 일본어, 남성 음색</li> <li>meimei : 중국어, 여성 음색</li></ul> | 필수 |
| speed      | integer | 음성 재생 속도. -5에서 5 사이의 정수 값이며, -5이면 0.5배 빠른 속도이고 5이면 0.5배 느린 속도입니다. 0이면 정상 속도의 목소리로 음성을 합성합니다.      | 필수 |
| text       | string  | 음성 합성할 문장. UTF-8 인코딩된 텍스트만 지원합니다. CSR API는 최대 4KB 크기의 텍스트까지 음성 합성을 지원합니다. | 필수 |


## 응답 {#Response}
CSR API는 합성한 음성 데이터를 MP3 형식의 바이너리 데이터로 반환합니다. 다음은 HTTP 응답 예입니다.
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

## 호출 예제 {#Examples}
다음은 각 언어별 CSR API 사용 예제입니다.

{% codetabs name="Java", type="java" -%}
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
{%- language name="PHP", type="php" -%}
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
{%- language name="Node.js", type="js" -%}
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
{%- language name="Python", type="python" -%}
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
{%- language name="C#", type="csharp" -%}
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
{%- language name="cURL", type="bash" -%}
curl "https://openapi.naver.com/v1/voice/tts.bin" \
	-d "speaker=mijin&speed=0&text=만나서 반갑습니다." \
	-H "Content-Type: application/x-www-form-urlencoded; charset=UTF-8" \
	-H "X-Naver-Client-Id: {애플리케이션 등록 시 발급받은 client id 값}" \
	-H "X-Naver-Client-Secret: {애플리케이션 등록 시 발급받은 client secret 값}" -v \
		> out.mp3
{%- endcodetabs %}

## 오류 코드 {#ErrorCode}
다음은 CSR API가 발생시킬 수 있는 오류 코드는 다음과 같습니다.

| 오류 코드 | HTTP 응답 코드 | 오류 메시지                         | 설명                                                   |
|---------|-------------|-----------------------------------|-------------------------------------------------------|
| VS01    | 400         | speaker parameter is needed.      | speaker 파라미터가 누락되었습니다.                           |
| VS02    | 400         | Unsupported speaker.              | speaker 파라미터에 지원하지 않는 값이 입력된 경우 발생합니다.      |
| VS03    | 400         | speed parameter is needed.        | speed 파라미터가 누락되었습니다.                             |
| VS04    | 400         | Unsupported speed.                | speed 파라미터에 지원하지 않는 값이 입력된 경우 발생합니다.        |
| VS05    | 400         | text parameter is needed.         | text 파라미터가 누락되었습니다.                              |
| VS06    | 400         | text parameter exceeds max bytes. | text 파라미터의 최대 허용 글자 수를 초과했습니다.                |
| VS99    | 500         | Internal server error             | 서버 내부 오류가 발생했습니다. 포럼에 문의하시면 신속히 조치하겠습니다. |
