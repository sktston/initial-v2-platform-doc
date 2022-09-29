Connection Workflow
================

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

## Connection 소개 

- **기관**(Issuer/Verifier)과 **사용자**(Holder/개인)의 Connection(연결) concept

![did connections](img/connections.png)


Connection이란 기관(Issuer/Verifier)과 사용자(Holder)가 Pairwise DID 및 PKI(Public Key Infrastructure) 기반으로 인증 체널을 생성하는 동작이다.
Auto Connection은 최소한의 API를 사용하여 Key 생성 및 교환으로 상호 연결(connection)을 완료한다.
<p></p>


- Auto Connection Sequence 

    1. 기관(Issuer/Verifier) : /connections/create-invitation (invitation 생성 API 호출)
    2. 기관 : 별도의 API(invitation-url)를 제공하여 invitation을 사용자(holder)에게 전달
    3. 사용자(개인/Holder) : invitation 수락
    4. 기관 : connection request 자동 실행
    5. 사용자 : accept connection request 자동 실행

<p></p>


- Connection 진행동안 State는 아래와 같이 변경되면서 진행 된다.
- State는 Webhook을 통해 기관에게 모두 전달된다.

    기관(issuer/verifier)이 받는 state : `request` → `response` → `active` <br>

    Topic | State | rfc23_state | Description
    --- | :---: | :---: | ---
    Connection | invitation | | Invitation 생성
    Connection | request | request-sent | Connection 요청한
    Connection | response | response-received |Connection accept
    Connection | **<span style="color:red">active</span>** | completed | <span style="color:red">Connection 완료 </span>

    - (참고) initial 및 client 사용자에게는 아래와 같은 state가 전달된다.
      
        사용자App(Holder) state : invitation → request → response → active<br>

<p></p>

### Connection ID Data Model & Example

  connection_id는 앞으로 사용자와 모든 통신에서 사용되는 connection 과정을 통해 생성되는 중요한 identifier 이다.
  `public=true`로 connection을 생성한 기관은 발급/검증 요청할때 항상 같은 connection_id로 항상 요청한다. 
  
  다만 `public=false`로 생성하거나, `public=false` 이더라도 사용자가 모바일단말을 교체하거나, 앱을 재설치하여 기존 connection_id 정보가 삭제된 경우 새로운 connection_id가 생성된다.

  Item | description | example
  --- | --- |
  connection_id | Connection identifier (Unique ID) | 아래 Example 참조
  accept | 자동 Connection 동작 설정  | manual / `auto`
  alias | 향후 재사용을 위한 별칭 설정 <br> Query Parameters의 public = false 에서만 동작 | 설명
  created_at | connection_id 생성 시간 | 아래 Example 참조
  invitation_key | invitation을 위해 사용한 Public key | 아래 Example 참조
  invitation_mode | invitation mode 설정. | `once` / multi / static
  my_did | connection을 위해 생성한 나의 pairwise did | 아래 Example 참조
  request_id | Connection request identifier | 아래 Example 참조
  state | Current state.  | request → response → active <br> 순으로 상태 변경됨
  rfc23_state | state와 동일하나 RFC23 protocol spec 적용 | State 상세내용 참조
  routing_state | 라우팅 기능 사용 시 state. 현재 지원 안함  | `none`
  their_did | 사용자가 연결을 위해 생성한 pairwise did | 아래 Example 참조
  their_label | 사용자의 label | `agency` : 이니셜 App <br> `mwp` : 모바일지갑
  their_role | 사용자 Role | 아래 Example 참조
  updated_at | Time of last record update | 아래 Example 참조


  <br>Connection Record Example


```json
{
    "connection_id": "217859c1-a30b-4fcf-a0aa-96abb66f1013",
    "created_at": "2021-06-09 00:53:52.845444Z",
    "routing_state": "none",
    "my_did": "KaJnJCdfycaTCsnjwkLtsb",
    "their_label": "agency",
    "rfc23_state": "completed",
    "their_role": "inviter",
    "accept": "auto",
    "updated_at": "2021-06-09 00:53:53.160885Z",
    "invitation_key": "Ct74qafMNkrdBnArzfrNh6EzKSxPyY3gQYsi2D2FSK28",
    "invitation_mode": "once",
    "their_did": "M5RERDK4wDYvhtWSb6SG6U",
    "state": "active"
}
```

<br>

![connection flow](img/webconsole_connection_flow.png)

<br>

## STEP 1. <font color=green>[필수]</font> Invitation 생성

- <font color=blue>[Previous Condition] : </font> 없음
- <font color=blue>[Action] : </font>개발 필요 
- <font color=blue>[Development] : </font> 기관(Issuer/Verifier) 서버 → Cloud Agent(플랫폼) API 요청

<p></p>


#### API Method and Resource

  `POST` `/connections​/create-invitation` 새로운 invitation 생성
<p></p>

* Swagger Document
  
    [Link Click](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/connection/post_connections_create_invitation)
<p></p>

#### Request Parameters

* Query Parameters

     KEY | Value | Required | Description 
     --- | :---: | :---: | ---
     alias | string |  | Connection 별칭 지정 (e.g. 1234-1234-1234-1234). <br>아래 `public=false` 일때 사용 가능
     auto_accept | <span style="color:red">true</span>/false | O | 사용자가 초대장 수락 시 자동 connection 설정.
     multi_use | true/<span style="color:red">false</span> | O | 초대장 다회 사용여부 선택. `public:true` 세팅 시 자동 multi로 활성화. 
     public | <span style="color:red">**true**</span>/false | O | Public DID를 기반으로 초대장 생성 유무.<br>connection_id 마다 alias 설정을 위해서는 false 선택

<p></p>

* Path Variables

     KEY | Value | Required | Description 
     --- | --- | --- | ---

<p></p>

* Body 

    Parameter content type `application/json`

```json
  {} // no data
```

#### Response Parameters

* Body Parameters

     KEY | Value | Description 
     --- | :---: | ---
     connection_id | string | unique connecton identifier. <br> e.g. 30cbd096-0fc9-4d12-b668-ad045345485e <br> `public=ture` 일때 생성되지 않음.
     invitation | json  | invitation 내용
     invitation_url | string | 위 invitation을 base64 encoding 한 값. invitation-url 생성할때 사용 하는 값 


#### Webhook

* Body Parameters

      create-invitation은 별도의 webhook event가 없습니다. Request의 Response data를 Holder에게 전달해 주시면 됩니다.

<br><br><br>

#### Example

##### A. "public=ture" Invitation Request Example 

일반적인 invitation 초대는 public=true로 생성한다. public으로 생성한 connection id는 재사용이 가능하다.

* Curl Request

```
curl -X 'POST' \
  'https://dev-console.myinitial.io/agent/api/connections/create-invitation?alias=connection%20sample&auto_accept=true&multi_use=false&public=true' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer 4dd1f97a-1234-1234-1234-9ed8cd2cfb6d' \
  -H 'Content-Type: application/json' \
  -d '{}'
```
<br>

##### "public=ture" Invitation Response example

* Response body

public=true로 생성한 invitation은 여러번 재사용 가능하다


```json
{
  "connection_id": null,  //unique connecton identifier.
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation", // connection protocol
    "@id": "397d6d51-85e8-453e-8e80-eb675ffeac55",  // thread id
    "did": "did:ssw:NoLL1sbRSGPB19TuqHPWqY",  // 기관의 Public DID
    "label": "(TEST)대학제증명검증 기관", // 기관명
    "imageUrl": "https://kr.object.ncloudstorage.com/dev-image-file/d41d8cd9_e2f52d5a_1622179841" //기관 대표 이미지
  }, // 아래 invitation_url은 위 invitation의 json 값을 base64 encoding한 값
  "invitation_url": "https://dev-console.myinitial.io/agent/endpoint?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiMzk3ZDZkNTEtODVlOC00NTNlLThlODAtZWI2NzVmZmVhYzU1IiwgImRpZCI6ICJkaWQ6c3N3Ok5vTEwxc2JSU0dQQjE5VHVxSFBXcVkiLCAibGFiZWwiOiAiKFRFU1QpXHViMzAwXHVkNTU5XHVjODFjXHVjOTlkXHViYTg1XHVhYzgwXHVjOTlkIFx1YWUzMFx1YWQwMCIsICJpbWFnZVVybCI6ICJodHRwczovL2tyLm9iamVjdC5uY2xvdWRzdG9yYWdlLmNvbS9kZXYtaW1hZ2UtZmlsZS9kNDFkOGNkOV9lMmY1MmQ1YV8xNjIyMTc5ODQxIn0="
}
```


<br>

<br>

##### B. "public=false" Invitation Request Example 

connection_id를 기관의 특정한 key 값과 mapping하여 관리를 원한다면 public=false를 설정하고, alias에 unique값을 부여한다.

* Curl

```
curl -X 'POST' \
  'https://dev-console.myinitial.io/agent/api/connections/create-invitation?alias=a123456789b&auto_accept=true&public=false' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer 4dd1f97a-1234-1234-1234-9ed8cd2cfb6d' \
  -H 'Content-Type: application/json' \
  -d '{}'
```
<br>

##### "public=false" Invitation Response example

* Response body

아래 invitation은 connection_id가 이미 생성된 상태로, 1회(1명)만 사용 가능하다.

```json
{
  "connection_id": "30cbd096-0fc9-4d12-b668-ad045345485e",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "8f215dba-072b-4ccc-b908-0e95ac4ce98c",
    "recipientKeys": [
      "EjD1ieKnuetMQ8E76pXczHKZYfBbmrXrZUTSAP3nmn9F"
    ],
    "serviceEndpoint": "https://dev-console.myinitial.io/agent/endpoint",
    "imageUrl": "https://kr.object.ncloudstorage.com/dev-image-file/d41d8cd9_cdf0a7c0_1624540317",
    "label": "SKT_Issuer_Demo"
  },
  "alias": "a123456789b", //기관이 특정 사용자를 지칭하기 위해 사용하는 key 값
  "invitation_url": "https://dev-console.myinitial.io/agent/endpoint?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiOGYyMTVkYmEtMDcyYi00Y2NjLWI5MDgtMGU5NWFjNGNlOThjIiwgInJlY2lwaWVudEtleXMiOiBbIkVqRDFpZUtudWV0TVE4RTc2cFhjekhLWllmQmJtclhyWlVUU0FQM25tbjlGIl0sICJzZXJ2aWNlRW5kcG9pbnQiOiAiaHR0cHM6Ly9kZXYtY29uc29sZS5teWluaXRpYWwuaW8vYWdlbnQvZW5kcG9pbnQiLCAiaW1hZ2VVcmwiOiAiaHR0cHM6Ly9rci5vYmplY3QubmNsb3Vkc3RvcmFnZS5jb20vZGV2LWltYWdlLWZpbGUvZDQxZDhjZDlfY2RmMGE3YzBfMTYyNDU0MDMxNyIsICJsYWJlbCI6ICJTS1RfSXNzdWVyX0RlbW8ifQ=="
}
```
 

<div class="admonition note">
<p class="admonition-title">note</p>
<p> public=false로 생성한 invitation은 deeplink의 invitation parameter에 value로 보내면 된다<br> * 참고 : <a href="https://initial-v2-platform.readthedocs.io/ko/master/initial_deeplink/">Deeplink 규격 </a></p>
</div>

<br>

##### Webhook example

- create-invitation은 별도의 webhook event가 없습니다. Request의 Response data를 Holder에게 전달해 주시면 됩니다.

<br><br>

## STEP 1-1. <font color=green>[필수]</font> Invitation 전달

- <font color=blue>[Previous Condition] : </font> create invitation 생성 완료
- <font color=blue>[Action] : </font>  개발 필요
- <font color=blue>[Development] : </font> 사용자(Holder)에게 `STEP 1`의 Response(invitation) 직접 전달


<p></p>


1. [initial default] **invitation_url**을 전달할 수 있는 API 개발

     [Web Console 개발 Guide](https://initial-v2-platform.readthedocs.io/ko/master/web_console_guide/#5-api) 참조

2. [deeplink] App to App 요청 

     Scheme : initial://reqService?**<span style="color:red">{{Parameter}}</span>**

    - [initial Deeplink Guide](https://initial-v2-platform.readthedocs.io/ko/master/initial_deeplink/) 참조  

3. [QR code] 
    - 2번의 Deeplink를 QR code 생성하여 사용자 scan

<br>
<br>

## STEP 2. Connection Webhook 확인

- <font color=blue>[Previous Condition] : </font> invitation 전달 정상 완료
- <font color=blue>[Action] : </font>  Webhook Topic, State 확인
- <font color=blue>[Development] : </font> 없음 

#### Cloud Agent(플랫폼) → 기관(Issuer/Verifier)으로 Webhook Event 전달


<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 STEP1에서 auto_accept=true 설정하였다면 initial app(Holder)과 자동으로 처리 하기 때문에, 전달되는 Webhook event만 확인 하시면 됩니다. </p>
</div>

* STEP2 실제 과정 참고
    * [Cloud Agent ← **<span style="color:red">모바일</span>**] `receive invitation` 진행 후 `request connection` 전송
    * [**<span style="color:red">Cloud Agent </span>** → 모바일] `request connection` 받고 자동으로 `request connection` 전송
    * [Cloud Agent ← **<span style="color:red">모바일</span>**] `request connection`을 받고 자동으로 `response` 전송
    * [**<span style="color:red">Cloud Agent </span>** → 모바일] `response`를 받고 자동으로 `response` 전송
    
    <br> 위와 같은 process로 인해 기관의 Webhook 서버로 두번의 Webhook event(`request` → `response`) 결과가 전달 된다.
    Cloud Agent에서 자동으로 처리되기 때문에, 정보는 참고만 하면 되고 다른 action은 필요 없다. 

<p></p>

#### Webhook Parameters

* Body Parameters

    Webhook으로 전달되는 Data Model은 아래 링크를 참고 

    [Connection ID Data Model](#connection-id-data-model-example)

<p></p>

#### Webhook Samples

- <b>topic : `connections`
- state : `request` </b>
```json
{
  "created_at":"2021-06-02 06:31:57.255177Z",
  "their_role":"inviter",
  "updated_at":"2021-06-02 06:31:57.255177Z",
  "rfc23_state":"request-sent",
  "state":"request",
  "accept":"auto",
  "routing_state":"none",
  "their_did":"78bCMv53bKrusuxyrrbgWM",
  "invitation_mode":"once",
  "connection_id":"7d670c2a-ef85-4289-81c3-24bb8e4f045d",
  "invitation_key":"81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P",
  "my_did":"66ZaFYLPorqgtQpo8AcpQY",
  "their_label":"agency",
  "topic":"connections"
}
```
<br>

- <b>topic : `connections`
- state : `response` </b>
```json
{
"created_at":"2021-06-02 06:31:57.255177Z",
"their_role":"inviter",
"updated_at":"2021-06-02 06:31:57.385549Z",
"rfc23_state":"response-received",
"state":"response",
"accept":"auto",
"routing_state":"none",
"their_did":"78bCMv53bKrusuxyrrbgWM",
"invitation_mode":"once",
"connection_id":"7d670c2a-ef85-4289-81c3-24bb8e4f045d",
"invitation_key":"81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P",
"my_did":"66ZaFYLPorqgtQpo8AcpQY",
"their_label":"agency",
"topic":"connections"
}
```
<br><br>   

### [참고][Cloud Wallet 전용] Invitation 수락하기


<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> 아래는 Cloud Wallet Holder(사용자)를 위한 개발 가이드 입니다. 기관(Issuer/Verifier) 사용자는 Skip 하셔도 됩니다. </p>
</div>


#### Method and Resource

  `POST` `/connections/receive-invitation` <font color=blue><b>[Holder 전용]</b></font> 초대 수락
<p></p>

* Swagger Document
  
    [Link Click](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/connection/post_connections_receive_invitation)
<p></p>

#### Parameters

* Query Parameters

     KEY | Value | Required | Description 
     --- | :---: | :---: | ---
     alias | string |  | Connection 별칭 지정 (e.g 김증명_대학제증명연결)
     auto_accept | <span style="color:red">true</span>/false | O | 사용자가 초대장 수락 시 자동 connection 설정.
     mediation_id | string | | Identifier for active mediation record to be used

<p></p>

* Path Variables

     KEY | Value | Required | Description 
     --- | --- | --- | ---

* Body 

    Parameter content type `application/json`

```json
// STEP 1 기관의 create-invitation에서 생성된 invitation의 json 값을 입력한다.
{
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "619dd713-545a-4e37-9aca-2d8883e1aac3",
    "did": "did:ssw:RtFNUCp65wqs41U2ZBUDkh",
    "imageUrl": "https://kr.object.ncloudstorage.com/dev-image-file/d41d8cd9_cdf0a7c0_1624540317",
    "label": "SKT_Issuer_Demo"
  }
```
<br>
#### Request Example 

* Curl

```
curl -L -X POST 'https://dev-console.myinitial.io/agent/api/connections/receive-invitation?alias=string&auto_accept=true' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer d1504526-0d60-4a49-8233-d67c6335cea6' \
--data-raw '{
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "619dd713-545a-4e37-9aca-2d8883e1aac3",
    "did": "did:ssw:RtFNUCp65wqs41U2ZBUDkh",
    "imageUrl": "https://kr.object.ncloudstorage.com/dev-image-file/d41d8cd9_cdf0a7c0_1624540317",
    "label": "SKT_Issuer_Demo"
  }'
```
<br>
#### Response example

* Response body

```json
{
  "invitation_msg_id": "619dd713-545a-4e37-9aca-2d8883e1aac3",
  "request_id": "91549d74-1342-4e92-9cb6-b3756d5f4e29",
  "invitation_mode": "once",
  "state": "request",
  "rfc23_state": "request-sent",
  "routing_state": "none",
  "accept": "auto",
  "created_at": "2021-10-13 14:17:34.946411Z",
  "their_role": "inviter",
  "my_did": "71Qd3cF2cJd5fRdBCicvro",
  "connection_protocol": "connections/1.0",
  "connection_id": "7aea189b-9b6b-4d68-a01d-84a85e164db9",
  "updated_at": "2021-10-13 14:17:34.991815Z",
  "their_label": "SKT_Issuer_Demo"
}
```
<br>
#### Webhook example

Webhook을 사용하는 경우 Holder에게는 아래 3번의 event가 순차적으로 자동 전달 된다. (`invitation`→`request`→`response`)<br>
Webhookd을 사용하지 않으면, Polling API를 사용하여 확인해야 한다. (별도 안내)

- <b>topic : `connections`
- state : `invitation` </b>
```json
{
   "state":"invitation",
   "invitation_msg_id":"316e68f8-a413-4080-80d9-501f137cb00d",
   "their_role":"inviter",
   "connection_protocol":"connections/1.0",
   "connection_id":"eb4e8512-1472-4dd1-b294-440aa27aeb76",
   "routing_state":"none",
   "created_at":"2021-10-14 01:14:08.239551Z",
   "updated_at":"2021-10-14 01:14:08.239551Z",
   "rfc23_state":"invitation-received",
   "alias":"Barry",
   "accept":"auto",
   "their_label":"(샘플) SKT 토익성적 증명서",
   "invitation_mode":"once",
   "topic":"connections"
}
```
<br>

- <b>topic : `connections`
- state : `request` </b>
```json
{
   "state":"request",
   "my_did":"PXHgY2S3P1o55ERh5QYWT1",
   "invitation_msg_id":"316e68f8-a413-4080-80d9-501f137cb00d",
   "their_role":"inviter",
   "connection_protocol":"connections/1.0",
   "connection_id":"eb4e8512-1472-4dd1-b294-440aa27aeb76",
   "routing_state":"none",
   "created_at":"2021-10-14 01:14:08.239551Z",
   "updated_at":"2021-10-14 01:14:08.284960Z",
   "rfc23_state":"request-sent",
   "alias":"Barry",
   "accept":"auto",
   "their_label":"(샘플) SKT 토익성적 증명서",
   "request_id":"5b9e0de6-cc2f-4978-91f2-badca97337f7",
   "invitation_mode":"once",
   "topic":"connections"
}
```
<br>

- <b>topic : `connections`
- state : `response` </b>
```json
{
   "state":"response",
   "my_did":"PXHgY2S3P1o55ERh5QYWT1",
   "invitation_msg_id":"316e68f8-a413-4080-80d9-501f137cb00d",
   "their_role":"inviter",
   "their_did":"4jEPS2TzDDYmiNdqcj263H",
   "connection_protocol":"connections/1.0",
   "connection_id":"eb4e8512-1472-4dd1-b294-440aa27aeb76",
   "routing_state":"none",
   "created_at":"2021-10-14 01:14:08.239551Z",
   "updated_at":"2021-10-14 01:14:13.581110Z",
   "rfc23_state":"response-received",
   "alias":"Barry",
   "accept":"auto",
   "their_label":"(샘플) SKT 토익성적 증명서",
   "request_id":"5b9e0de6-cc2f-4978-91f2-badca97337f7",
   "invitation_mode":"once",
   "topic":"connections"
}
```
<br><br>    

## STEP 3. <font color=green>[필수]</font> Connection 완료

- <font color=blue>[Previous Condition] : </font> **topic** `connection`, **state** `response`
- <font color=blue>[Action] : </font>개발 필요 
- <font color=blue>[Development] : </font> webhook으로 전달되는 connection_id 관리


<p></p>

##### Cloud Agent(플랫폼) → 기관(Issuer/Verifier)으로 Webhook Event 전달

- 연결(Connection)이 완료되면 Webhook Event을 통해 아래 Message가 전달 된다.
  
#### Webhook example

- <b> topic: `connections`
- state : `active` </b>

###### public=<font color=red>true</font> example

```json
{
  "created_at":"2021-06-02 06:31:57.255177Z",
  "routing_state":"none",
  "my_did":"66ZaFYLPorqgtQpo8AcpQY",
  "their_label":"agency",
  "rfc23_state":"completed",
  "connection_id":"7d670c2a-ef85-4289-81c3-24bb8e4f045d",
  "their_role":"inviter",
  "accept":"auto",
  "updated_at":"2021-06-02 06:32:01.252322Z",
  "invitation_key":"81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P",
  "invitation_mode":"once",
  "their_did":"78bCMv53bKrusuxyrrbgWM",
  "state":"active",
  "topic":"connections"
}
```
<br>

###### public=<font color=red>false</font> example

기관이 설정한 `alias`가 포함되어 있다


```json
{    
  "routing_state": "none",
  "rfc23_state": "completed",
  "my_did": "RVbyj9N8ZvhAUibVP7hkjq",
  "created_at": "2022-04-27T17:21:46.329877Z",
  "invitation_mode": "once",
  "alias": "a123456789b", // 사용자 mapping을 위해서 기관에서 입력한 값
  "their_role": "invitee",
  "accept": "auto",
  "their_did": "AFdcLYcQb68RQiwTJbYssm",
  "updated_at": "2022-04-27T17:45:09.140698Z",
  "their_label": "SKT_Issuer_Demo",
  "state": "active",
  "connection_id": "30cbd096-0fc9-4d12-b668-ad045345485e",
  "invitation_key": "EjD1ieKnuetMQ8E76pXczHKZYfBbmrXrZUTSAP3nmn9F",
  "connection_protocol": "connections/1.0"
}
```
<p></p>

#### 기관(Issuer/Verifier) 개발 내용 확인

- 기관 개발자는 body의 <b>topic</b> : `connections"`와 <b>state</b>: `active`를 확인 해야 한다.
    - body의 `"state":"active"` 일 경우 연결이 완료 되었기 때문에, their_did(사용자 DID), connection_id(사용자와 communication 필요할때 사용하는 id)등을 확인/기록 하면 된다.
    - `their_did(사용자 DID)`는 정책상 Privacy 보호를 위해 수시로 변경(앱재설치등)되기 때문에, 고정된 고객 식별자로 사용 불가능 하다. 
    - `alias`는 특정 사용자를 구분 및 사용자 mapping을 위해 사용 가능하다
    - `their_label`은 PoC(Point of Contact) App을 구분할 수 있다. `agency`, `agent for ios`(initial App 사용자), `mwp` 및 기타 (모바일지갑 사용자)

<div class="admonition warning">
<p class="admonition-title">important</p>
<p> `their_label`을 통해 initial App, 모바일지갑으로 부터 요청을 구분할 수 있습니다. 해당값에 따라 Verify(검증기관)에서 구분해서 이용 동의를 받아야 합니다.</p>
</div>
  

<div class="admonition note">
<p class="admonition-title">note</p>
<p> <b>connection_id</b>는 앞으로 DID 관련 모든 API에서 사용되는 중요한 <b>key value</b>로 관리가 필요하다. </p>
</div>


<br><br>

## [선택] Connection 정보 확인

Connection 정보는 사용자와 통신을 위한 기본 정보로 Wallet에 저장된다.<br>
`connection_id`는 각 기관에서 관리하는 개인의 식별자(ID, uuid, CI등)에 mapping 하여 관리하면 된다.([참조](/cloud_agent_demo))

- STEP3의 Webhook으로 전달된 connection 관련 message는 아래 API로도 확인 가능하다.

    - 전체 connection list 확인 : `GET` `/connections`
    - 특정 connection 정보 확인 : `GET` `/connections/{conn_id}` 
    * <b>[Swagger Document Link ](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/connection/get_connections__conn_id_)</b>

<br><br>

## [선택] Delete Connection 

`DELETE` `/connections/{conn_id}`

<br><br>


## [선택] 기관 사용자 Key와 Mapping

- 모바일지갑에서 제공하는 이름/전화번호/생년월일 기반으로 사용자 관리가 어려운 기관은, `public=false`와 `alias` 사용하여 기관에서 관리하는 사용자 Key와 mapping 하여 관리할 수 있다

<div class="admonition note">
<p class="admonition-title">note</p>
<p> 발급기관(Issuer)는 불가능하고, deeplink 기반 검증기관(Verifier)만 사용 가능합니다. </p>
</div>

STEP1에서 "public=false" Invitation 생성 방법에 대한 설명이 있다. 이때 alias 입력을 통해 기관사용자의 특정 key 값을 넣을 수 있다.
이 alias를 활용하기 위해서는 [5. 연결 요청 API 개발 안내](https://initial-v2-platform.readthedocs.io/ko/master/web_console_guide/#invitation-url-api-request) 가이드에서 아래와 같이 추가 개발하시면 됩니다.

```https://{{ 기관 도메인 }}/invitation-url?alias={key}```

invitation-url위에 query parameter로 alias를 생성하여 holder에게 전달하고, api로 전달된 alias를 create-invitation 할때 활용하시면 됩니다.

이방법은 invitation-url를 기관에서 직접 생성할때 유효한 방법으로 주로 deeplink 기반 검증기관에서 활용하시면 됩니다.