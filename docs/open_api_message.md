Message
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 SMS 문자와 메신저와 같은 Basic Message 송/수신 예제.

- Webhook 서버를 포함하고 있는 상세 Demo 참고 <https://github.com/sktston/acapy-controller-java>

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

Basic Message의 Type은 아래와 같이 정의 (2021.04.09)

type | from | to | Definition
--- | ---| ---| ---
initial_agreement |	issuer/verifier	| initial app | issuer/verifier가 개인정보동의/약관 요청
initial_agreement_decision	| initial app |	issuer/verifier	| 개인정보동의/약관 요청 결과 전송
initial_web_view(개발중) | issuer/verifier	| initial app |	web_view url 표시 요청
initial_input_request(개발중) | issuer/verifier	| initial app |	사용자 입력 data 가 필요할 경우 요청 
initial_input_response(개발중) | initial app | issuer/verifier |	사용자 입력 값을 전달

<p></p>
- Basicmessages State

Topic | State | Description
--- | --- | ---
basicmessages | received | message를 받은 상태 

<p></p>

### STEP 1. 기관이 사용자에게 Message를 전달. 

* Method and Resource

    `POST` `/connections/{conn_id}/send-message` message 전송.   

<br><br>
* Parameter

Path Variables

Name | Description
--- | ---
conn_id | Alice의 connection 정보

<br><br>
* Body
```json
{"contents": "{{string}}"}
```
 
<p></p>

* 개인정보 수집 및 이용 동의서 

Field name | value | Description
--- | --- | --- 
content | String | 제목
agreement | String | 서비스에 대한 동의 설명
collectiontype | String | 수집 항목
usagepurpose | String | 수집 이용 목적
consentperiod | String | 보유/이용기간 및 파기 

<br><br>

* 동의서 Sample

**** 중요!! json에서 string으로 변환하여 body에 입력 한다 ****

```json
{
  "type": "initial_agreement",
  "content": {
    "title": "개인정보 수집 및 이용 동의서",
    "agreement": "Initial 서비스(이하 \u201c서비스\u201d라 한다)와 관련하여, 본인은 동의내용을 숙지하였으며, 이에 따라 본인의 개인정보를 (주)XXXX가 수집 및 이용하는 것에 대해 동의합니다.\n\n본 동의는 서비스의 본질적 기능 제공을 위한 개인정보 수집/이용에 대한 동의로서, 동의를 하는 경우에만 서비스 이용이 가능합니다.\n\n법령에 따른 개인정보의 수집/이용, 계약의 이행/편익제공을 위한 개인정보 취급위탁 및 개인정보 취급과 관련된 일반 사항은 서비스의 개인정보 처리방침에 따릅니다.",
    "collectiontype": "이름,생년월일",
    "usagepurpose": "서비스 이용에 따른 본인확인",
    "consentperiod": "1년"
  }
}
```

Body Example
```
{"content":" {\"type\" : \"initial_agreement\" , \"content\" = { \"title\":\"개인정보 수집 및 이용 동의서\",\"agreement\" :\"Initial 서비스(이하 서비스라 한다)와 관련하여, 본인은 동의내용을 숙지하였으며, 이에 따라 본인의 개인정보를 (주)XXXX가 수집 및 이용하는 것에 대해 동의합니다.\\n\\n본 동의는 서비스의 본질적기능 제공을 위한 개인정보 수집\/이용에 대한 동의로서, 동의를 하는 경우에만 서비스 이용이 가능합니다.\\n\\n법령에 따른 개인정보의 수집\/이용, 계약의 이행\/편익제공을 위한 개인정보 취급위탁 및 개인정보취급과 관련된 일반 사항은 서비스의 개인정보 처리방침에 따릅니다.\",\"collectiontype\" : \"이름,생년월일\",\t\t\t\"usagepurpose\" : \"서비스 이용에 따른본인확인\",\"consentperiod\" : \"1년\" }}"}
```


cURL Request Example

```
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/connections/638d8e47-c488-4245-8102-cfd3eccc7d69/send-message' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer a80c77b8-9889-4a48-918a-95b70fa7d50a' \
--data-raw '{"content":" {\"type\" : \"initial_agreement\" , \"content\" = { \"title\":\"개인정보 수집 및 이용 동의서\",\"agreement\" :\"Initial 서비스(이하 서비스라 한다)와 관련하여, 본인은 동의내용을 숙지하였으며, 이에 따라 본인의 개인정보를 (주)XXXX가 수집 및 이용하는 것에 대해 동의합니다.\\n\\n본 동의는 서비스의 본질적기능 제공을 위한 개인정보 수집\/이용에 대한 동의로서, 동의를 하는 경우에만 서비스 이용이 가능합니다.\\n\\n법령에 따른 개인정보의 수집\/이용, 계약의 이행\/편익제공을 위한 개인정보 취급위탁 및 개인정보취급과 관련된 일반 사항은 서비스의 개인정보 처리방침에 따릅니다.\",\"collectiontype\" : \"이름,생년월일\",\t\t\t\"usagepurpose\" : \"서비스 이용에 따른본인확인\",\"consentperiod\" : \"1년\" }}"}'
```


<p></p>
 
   * Response body
```json
{ }
```

<p></p>



### STEP 2. Holder(initial App등) Message 확인.

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 Holder가 initial app 일 경우 SDK에서 자동으로 동의서 내용을 사용자에게 노출 </p>
</div>



### STEP 3. 동의서 결과 전송 받기.

- 기관은 Webhook Message를 확인 하여 동의서 결과를 전송 받는다.

Webhook Example 

```json
body:{"connection_id":"50a9da1a-e506-4e47-8f5a-37756a9e972c","message_id":"ac706983-3089-4604-926f-7fbb99f82452","content":"{\"type\":\"initial_agreement_decision\",\"content\":{\"agree_yn\":\"Y\"}}","state":"received","sent_time":"2021-05-18T10:10:58.399","locale":"en","topic":"basicmessages"}
```

동의서 내용
```json
{
  "type":"initial_agreement_decision",
  "content":{
    "agree_yn":"Y",
    "signature":"xcvsdfasdfgawdgasdgasdgsdagasdgasdgdsg"
  }
}
```

* Next Step

기관 사용자는 사용자를(CI, unique id등)를 구분하여 정보를 관리한다. 


### STEP 4. Web View 요청

- 기관은 Web View를 요청할 수 있다. initial app은 해당 web-view 화면을 출력 한다

**** 중요!! json에서 string으로 변환해서 Body에 입력 한다 ****

```json
{
  "type":"initial_web_view",
  "content":{
    "web_view_url":"https://www.skinitial.com/web-view/uuid"
  }
}
```