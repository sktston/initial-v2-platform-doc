Message
================

**기관**(Issuer/Verifier)와 **사용자**(Holder/Prover)의 SMS 문자와 메신저와 같은 Basic Message 송/수신 예제.

<div class="admonition note">
<p class="admonition-title">note</p>
<p> API 사용을 위해서는 Access Token이 필요함 </p>
</div>

- Access Token Example
```
curl --location --request GET 'http://localhost/wallet/did'\
--header 'Authorization: Bearer ab7aca56-5c36-4fbe-a9fe-2ae4937c63de'
```

<br><br>

### STEP 1. <font color=green>[Option]</font> initial_web_view (기관 → 사용자)

- 발급 기관은 사용자로 부터 추가 정보 입력이 필요하거나 사용자가 선택이 필요한 리스트 내용이 있을 경우 Web view를 통해 요청할 수 있고, initial app은 해당 web-view 화면을 출력 한다
 
<p></p><br>




#### Method and Resource

  `POST` `/connections/{conn_id}/send-message` message 전송.
<p></p>

* Swagger Document
  
    [Link Click](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/basicmessage/post_connections__conn_id__send_message)
<p></p>

#### Parameters

* Query Parameters

     KEY | Value | Required | Description 
     --- | :---: | :---: | ---

<p></p>

* Path Variables

     KEY | Value | Required | Description 
     --- | --- | :---: | ---
     conn_id | | O | connection id. <br>e.g. 3fa85f64-5717-4562-b3fc-2c963f66afa6



* Body 

    Parameter content type `application/json`

```json
{
  // "content":"String" 의 형식을 지켜야 한다.
  "content":"String"
}
```
<br><br>

<div class="admonition warning">
<p class="admonition-title">important</p>
<p>  중요!! {"contents": "{{본문}}"} 형식에서 {{본문}}의 json 규격은 STEP 2의 initial 표준 가이드를 따라야 한다.</p>
</div>

Sample

```json
{
  // "content":"보내고 싶은 내용" 의 형식을 지켜야 한다.
  "content":" {\"type\" : \"initial_web_view\" , \"content\" : { \"web_view_url\":\"https://www.sktelecom.com/test.php?their_did=UtArAzrfSaTF77mNJVcCrA\" }}"
}
```
<br>
#### Request Example 

* Curl

```
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/connections/b62bfc68-8762-4e9d-af4a-221502508944/send-message' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer 2ca4dd8a-xxxx-xxxx-xxxx-c5fb0286f2cc' \
--data-raw '{"content":" {\"type\" : \"initial_web_view\" , \"content\" : { \"web_view_url\":\"https://www.sktelecom.com/test.php?their_did=UtArAzrfSaTF77mNJVcCrA\" }}"}'
```

<div class="admonition warning">
<p class="admonition-title">important</p>
<p>  중요!! {{본문}}이 json일 경우 본문내 "(Double quotation marks)는 \"(Backslash)를 포함 string으로 변환하여 Request를 보내야 한다. 위 Request Sample을 꼭 참조해야 한다.  </p>
</div>


<br>

#### Response example

* Response body

```json
{ }
```

<br>

#### Webhook example


- <b>topic : `basicmessages`
- state : `received` </b>


<br>

### STEP 2. initial Content 본문 표준 규격 (Protocol)

initial은 basicmessage를 이용하여 initial app과 다양한 통신을 할 수 있다.<br>
그중 이미 정의된 규격(Protocol)이 있다.

STEP1에서 설명한 {{본문 or String}}에 다음의 규격을 사용하면 App에서 미리 정의한 서비스를 이용할 수 있다

```json
{"contents": "{{본문}}"}

```

<div class="admonition warning">
<p class="admonition-title">important</p>
<p>  아래는 {"contents": "{{본문}}"}의 {{본문}}에 해당하는 규격입니다. 실제 API 개발시에는 "content" key를 포함해야 합니다.</p>
</div>


<br>
#### 1. Webview 화면 요청 본문 Spec
```json
{
  "type":"initial_web_view",
  "content":{
    "web_view_url":"{{기관 Web-view URL}}"
  }
}
```

##### Sample 화면

![webview](img/webview.jpg)


<br>
#### [Option] Web_view내 닫기(취소) 버튼 개발 Guide

- 취소 버튼 Java Script Guide
    - web_view의 취소 버튼 삽입 시 아래 java script code를 적용하면, intial App은 전체 진행을 취소.
    - sample code : <https://github.com/sktston/initial-controller-java/blob/cloud_demo/src/main/resources/static/web-view/form.html>

```javascript
<script>
    function cancle(){
        //initial app에서 web_view 화면을 즉시 종료 하는 code
        var userAgent = navigator.userAgent.toLowerCase();
        if (userAgent.search("android") > -1) {
            Android.failVC ();
        }
        else {
            webkit.messageHandlers.callbackHandler.postMessage("failVC");
        }
    }
</script>
```

<p></p>
<div class="admonition note">
<p class="admonition-title">note</p>
<p>  initial App은 다음 Request가 수신되면 Web View 화면을 자동 종료 합니다. </p>
</div>

<br><br>

#### [Option] Web_view내 Modal 개발 Guide

- Web view내 API의 동작 처리 시간이 오래 걸리는 경우 Modal등 화면을 이용하여 사용자에게 UI/UX 처리해야 함





<br>
#### 2. Popup 알림창 요청 본문 Spec

- 기관(Issuer/Verifier) → Holder(initial App 혹은 Cloud Wallet등)에 알림창 표시를 위해 사용한다.
- "type":"initial_message_popup" 선언 후 정의된 message 본문 전송

   Field | 필수 | Value | Description 
   --- | :---: | :---: | ---
   button | 필수 | [confirm] <br> [cancel] | confirm : 확인버튼 표시(성공 시 사용. main으로 이동 <br> cancel : 취소버튼 표시(실패 시 사용. main으로 이동)
   message_code |	필수	| 별도 Spec 참고 | e.g. D0004 = 승인되지 않았음
   message_main |	필수	| 사용자 정의 | 화면에 표시하고 싶은 Main Title
   message_sub	| X	| 사용자 정의 |	화면에 표시하고 싶은 Sub Title


```json
{
  "type":"initial_message_popup",
  "content":{
    "message_code":"{{code}}",
    "message_main":"{{main message}}",
    "message_sub":"{{sub message}}",
    "button":["확인","취소"]
  }
}
```

Predefined code & message

No | message_code | message_main | Description | Next Action
--- | :---: | --- | --- | ---
1 | 0000 | 성공하였습니다 | | 종료하고 Main으로 이동 |
2 | 0001 | 실패하였습니다 | | 종료하고 Main으로 이동 |
3 | H0001 |	출근 등록이 정상적으로 완료되었습니다 |	노인인력개발원 전용 | |
4 | H0002 |	퇴근 등록이 정상적으로 완료되었습니다 |	노인인력개발원 전용|	|
5 | H0003 |	출근 등록이 완료된 상태입니다	 | 노인인력개발원 전용	| |
6 | H0004 |	퇴근 등록이 완료된 상태입니다 |	노인인력개발원 전용	| |
7 | H0005 |	출근 등록이 완료되지 않았습니다 |	노인인력개발원 전용 | |	
8 | H0006 |	퇴근 등록이 완료되지 않았습니다 |	노인인력개발원 전용 | |
8 | M0001 |로그인이 완료 되었습니다 |	로그인등 완료 후 현재 작업 종료 | 종료하고 Main으로 이동 |

##### Sample 화면

![popup](img/popup.png)


<br>
#### 3. Toast 알림창 요청 본문 Spec

- 기관(Issuer/Verifier) → Holder(initial App 혹은 Cloud Wallet등)에 Toast 알림창 표시를 위해 사용한다.
- "type":"initial_message_toast" 선언 후 정의된 message 본문 전송

   Field | 필수 | Value | Description 
   --- | :---: | :---: | ---
   message_code |	필수	| 별도 Spec 참고 | e.g. D0004 = 승인되지 않았음
   message_main |	필수	| 사용자 정의 | 화면에 표시하고 싶은 Main Title


```json
{
  "type":"initial_message_toast",
  "content":{
    "message_code":"{{code}}",
    "message_main":"{{main message}}"
  }
}
```

Predefined code & message

No | message_code | message_main | Description | Next Action
--- | :---: | --- | --- | ---
1 | 0000 | 성공하였습니다 | | 종료하고 Main으로 이동 |
2 | 0001 | 실패하였습니다 | | 종료하고 Main으로 이동 |
3 | M0001 | {{ 사용자 Message }} | | 종료하고 Main으로 이동 |
