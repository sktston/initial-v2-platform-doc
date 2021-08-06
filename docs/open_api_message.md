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

#### 1. <font color=green>[Option]</font> initial_web_view (기관 → 사용자)

- 발급 기관은 사용자로 부터 추가 정보 입력이 필요하거나 사용자가 선택이 필요한 리스트 내용이 있을 경우 Web view를 통해 요청할 수 있고, initial app은 해당 web-view 화면을 출력 한다
 
<p></p><br>

* Method and Resource

  `POST` `/connections/{conn_id}/send-message` message 전송.


* Parameter

  Path Variables

  Name | Description
      --- | ---
  conn_id | 사용자와 연결된 connection_id 정보

* Body
```json
{"contents": "{{본문}}"}
```

<div class="admonition warning">
<p class="admonition-title">important</p>
<p>  중요!! {{본문}}이 json일 경우 string으로 변환하여 {{본문}} 입력 한다  </p>
</div>

- Web_view 요청하기 위한 {{본문}} 양식

```json
{
  "type":"initial_web_view",
  "content":{
    "web_view_url":"{{기관 Web-view URL}}"
  }
}
```

{{본문}} 양식을 String 변환하여 Web_view 요청 Request Sample


```
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/connections/b62bfc68-8762-4e9d-af4a-221502508944/send-message' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer 2ca4dd8a-xxxx-xxxx-xxxx-c5fb0286f2cc' \
--data-raw '{"content":" {\"type\" : \"initial_web_view\" , \"content\" : { \"web_view_url\":\"https://www.sktelecom.com/test.php?their_did=UtArAzrfSaTF77mNJVcCrA\" }}"}'
```

<div class="admonition warning">
<p class="admonition-title">important</p>
<p>  중요!! {{본문}}이 json일 경우 본문내 "(Double quotation marks)는 \"(Backslash)를 포함 변환하여 Request를 보내야 한다. 위 Request Sample을 꼭 참조해야 한다.  </p>
</div>

* Response body
```json
{ }
```


* Basicmessages State

Topic | State | Description
--- | --- | ---
basicmessages | received | message를 받은 상태

<p></p>

* Topic Event

없음


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
