<p>initial Platform DID Agent API 이용 개발 안내</p>

![platform arch](img/initial_platform_architecture.png)

## 기관 참여자 구현/개발이 필요 항목 
위 그림에서 기관사용자 영역의 3개 항목에 대해서 구현 필요

1. 참여사 개인정보 수집 및 이용 동의서 조회 API 서버 
2. DID Agent API 요청 Controller 서버
3. Event 수신 Webhook 서버

### 1-1. 참여사 개인정보 수집 및 이용 동의서 조회 API 구현 안내 
- 모바일 initial App에서 참여사에게 본인확인증명을 제출하기 전 개인정보 수집 및 이용 동의를 받기 위해, 참여사로부터 동의서를 조회하는 인터페이스이다. 

##### Request
```
POST http://{{참여사서버DOMAIN}}/terms/personconsent   
```

Parameter name | value | Description | 필수 
--- | --- | --- | ---
 - |  |  |


##### Response
Field name | value | Description
--- | --- | --- 
result | Object | 
flag | String | 결과 코드
message | String | 결과 메시지
agreement | String | 서비스에 대한 동의 설명
collectiontype | String | 수집 항목
usagepurpose | String | 수집 이용 목적
consentperiod | String | 보유/이용기간 및 파기 

##### Example
example request
```
Header
Accept:applicastion/json
METHOD:POST
URL
/terms/personconsent
Body
```
example response
```
Header 
'content-type': application/json
'status':200
Body 
{
  "result": {
    "flag": "0000",
    "message": "결과 메시지",
"agreement": "Initial 서비스(이하 \u201c서비스\u201d라 한다)와 관련하여, 본인은 동의내용을 숙지하였으며, 이에 따라 본인의 개인정보를 (주)XXXX가 수집 및 이용하는 것에 대해 동의합니다.\n\n본 동의는 서비스의 본질적 기능 제공을 위한 개인정보 수집/이용에 대한 동의로서, 동의를 하는 경우에만 서비스 이용이 가능합니다.\n\n법령에 따른 개인정보의 수집/이용, 계약의 이행/편익제공을 위한 개인정보 취급위탁 및 개인정보 취급과 관련된 일반 사항은 서비스의 개인정보 처리방침에 따릅니다.",
    "collectiontype": "이름,생년월일",
    "usagepurpose": "서비스 이용에 따른 본인확인",
    "consentperiod": "1년"
  }
}
```

### 1-2. 참여사 개인정보 수집 및 이용 동의서 동의 수신 API 개발 안내 
- initial App에서 참여사로 개인정보 수집 및 이용 동의서에 대한 사용자 동의 결과를 전달 하는 인터페이스이다.
- DID Agent 이용시 encryption 및 전자서명에 대해서 지원하지 않으므로 해당값은 무시하면 된다. 

##### Request
```
POST http://{{참여사서버DOMAIN}}/terms/personconsent/decision
```

##### Body Parameter
Parameter name | value | Description | 필수 
--- | --- | --- | ---
pairwise_did | String | 기관과 연결에 사용한 DID | 사용 안함 
enc_data | string | 암호화 해서 동의 결과 전송.(intial JAVA SDK를 사용하는 경우 복호화 가능. DID Agent를 사용하면 지원 불가) | 사용 안함  
non_enc_data | string | 암호화 하지 않고 동의 결과 전송.(DID Agent를 사용하는 경우 사용) | 필수 

##### JSON Data
Parameter name | value | Description | 필수 
--- | --- | --- | ---
agree_yn | String | 동의여부 (Y/N) | 필수
signature | String | pairwise_did+agree_yn를 서명한 결과를 | 사용 안함
                           

##### Response
Field name | value | Description
--- | --- | --- 
result | Object | 
flag | String | 결과 코드
message | String | 결과 메시지



##### Example
example request
```
Header
Accept:applicastion/json
METHOD:POST
URL
/terms/personconsent/decision
Body
{
"pairwise_did": "sdfsdfsdf23232fdsfsf",
"enc_data": "dsiofj239rt9i9bi929if9si92mvj2oj592nj288e8j38nj82j8j8j4n"
}
```

example request
```
{
"agree_yn": "Y",
"signature":"xcvsdfasdfgawdgasdgasdgsdagasdgasdgdsg"
}
```

example response
```
Header 
'content-type': application/json
'status':200
Body 
{
  "result":{
    "flag":"0000",
    "message":"성공"
  }
}
```

### 2. DID Agent Controller 개발 안내 
- 기관사용자는 DID Agent API를 사용하기 위해 controller 서버를 개발해야 한다.
- initial Platform의 DID Agent는 Event Driven(이벤트 구동형) 방식이기 때문에 기관 사용자는 Webhook 서버를 개발하고 url 등록해아 한다.
- 자세한 API 내용은 initial DID Agent API Guide 및 reference code 참조 

  
### 3. Webhook 서버 개발 안내 
- initial DID Agent는 event driven 방식으로 REST API 요청에 대한 응답을 Webhook 서버로 전달합니다
- 처리결과를 받기 위한 기관 사용자의 Webhook URL은 기관정보에 필수 등록해야 합니다. e.g)https://domain.com/webhooks
- Webhook 서버의 API Key는 보안 강화를 위한 선택 사항입니다. https://domain.com/webhooks#org-api-key 와 같이 입력하시면 header의 x-api-key 항목에 값으로 "org-api-key"를 함께 전송합니다.
- 기관 사용자는 보안강화를 위한 IP기반 방화벽 설정 하세요 (initial platform의 IP대역 xxx.xxx.xxx.xxx/24는 별도로 안래 예정입니다)
- Webhook 서버 예제는 아래 reference code (DID 발행/검증 Demo)에서 확인할 수 있습니다. 


