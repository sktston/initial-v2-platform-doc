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

### Connection Intro

- **기관**(Issuer/Verifier)과 **사용자**(Holder/개인)의 Connection(연결) concept

![did connections](img/connections.png)


Connection이란 기관과 사용자가 Pairwise DID 및 PKI(Public Key Infrastructure) 기반으로 인증 체널을 생성하는 동작이다.
Auto Connection은 최소한의 API를 사용하여 Key 생성 및 교환으로 상호 연결을 완료한다.
<p></p>


- Auto Connection Sequence 

    1. 기관(Issuer/Verifier) : /connections/create-invitation (invitation 생성 API 호출)
    2. 사용자(개인/Holder) : invitation 수락
    3. 기관 : connection request 자동 실행
    4. 사용자 : accept connection request 자동 실행

<p></p>


- Connection 진행동안 State는 아래와 같다.

    Topic | State | rfc23_state | Description
    --- | :---: | :---: | ---
    Connection | invitation | | Invitation 생성
    Connection | request | request-sent | Connection 요청한
    Connection | response | response-received |Connection accept
    Connection | **<span style="color:red">active</span>** | completed | <span style="color:red">Connection 완료 </span>

<p></p>

- Connection ID Data Model & Example

    connection_id는 앞으로 사용자와 모든 통신에서 사용되는 중요한 identifier 입니다. 사용자는 기관에 발급/검증 요청할때 해당 id로 항상 요청합니다.
  
    다만 사용자가 모바일단말을 교체하거나, 앱을 재설치할경우 해당 connection_id는 사용할 수 없고, 새로운 connection_id가 생성됩니다.

    Item | description | example
    --- | --- |
    connection_id | Connection identifier | 아래 Example 참조
    accept | Connection acceptance 설정  | manual / `auto`
    alias | 향후 재사용을 위한 별칭 설정 | 설명
    created_at | Time of record creation | 아래 Example 참조
    invitation_key | invitation을 위해 사용한 Public key | 아래 Example 참조
    invitation_mode | invitation mode 설정. | `once` / multi / static
    my_did | connection을 위해 생성한 나의 pairwise did | 아래 Example 참조
    request_id | Connection request identifier | 아래 Example 참조
    state | Current record state | State 상세내용 참조
    rfc23_state | state와 동일하나 RFC23 protocol spec 적용 | State 상세내용 참조
    routing_state | 라우팅 기능 사용 시 state. 현재 지원 안함  | `none`
    their_did | 사용자가 연결을 위해 생성한 pairwise did | 아래 Example 참조
    their_label | 사용자의 label | `agency` 고정
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

### STEP 1. 기관(Issuer/Verifier) --> 사용자(Holder) : create invitation & send invitation.

* Method and Resource

    `POST` `/connections​/create-invitation` 새로운 초대장 생성
<p></p>

* Swagger Document
  
    [Link](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/connection/post_connections_create_invitation)
<p></p>

  
* Parameter

     Name | Description 
     --- | --- 
     alias | Connection 별칭 지정 (e.g 김증명_대학제증명연결)
     auto_accept | Alice가 초대장 수락 시 자동 connection 설정 <span style="color:red">(**default : true**)</span>
     multi_use | 초대장을 once/multi 사용여부. `public` `true` 세팅시 선택 불가. 자동 multi로 활성 화. <span style="color:red">(**default : false**)</span>
     public | Public DID를 기반으로 초대장 생성 <span style="color:red">(**default : true**)</span>

<p></p>

* Request Example 

```
curl -X 'POST' \
  'https://dev-console.myinitial.io/agent/api/connections/create-invitation?alias=connection%20sample&auto_accept=true&multi_use=false&public=true' \
  -H 'accept: application/json' \
  -H 'Authorization: bearer 4dd1f97a-1234-1234-1234-9ed8cd2cfb6d' \
  -H 'Content-Type: application/json' \
  -d '{}'
```
<p></p>

* Response body example(생성된 초대장)
```json
{
  "connection_id": null,
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "397d6d51-85e8-453e-8e80-eb675ffeac55",
    "did": "did:ssw:NoLL1sbRSGPB19TuqHPWqY",
    "label": "(TEST)대학제증명검증 기관",
    "imageUrl": "https://kr.object.ncloudstorage.com/dev-image-file/d41d8cd9_e2f52d5a_1622179841"
  },
  "invitation_url": "https://dev-console.myinitial.io/agent/endpoint?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiMzk3ZDZkNTEtODVlOC00NTNlLThlODAtZWI2NzVmZmVhYzU1IiwgImRpZCI6ICJkaWQ6c3N3Ok5vTEwxc2JSU0dQQjE5VHVxSFBXcVkiLCAibGFiZWwiOiAiKFRFU1QpXHViMzAwXHVkNTU5XHVjODFjXHVjOTlkXHViYTg1XHVhYzgwXHVjOTlkIFx1YWUzMFx1YWQwMCIsICJpbWFnZVVybCI6ICJodHRwczovL2tyLm9iamVjdC5uY2xvdWRzdG9yYWdlLmNvbS9kZXYtaW1hZ2UtZmlsZS9kNDFkOGNkOV9lMmY1MmQ1YV8xNjIyMTc5ODQxIn0="
}
```

<br><br>


### STEP 1-1. 기관(Issuer/Verifier) --> 사용자(Holder) : Invitation 전달

1. [initial default] **invitation_url**을 전달할 수 있는 API 작성 
    - [Web Console 개발 Guide](web_console_guide/#1-api) 참조
2. [deeplink] App to App 요청 
    - spec : initial://reqService?process=I&ynCloud=Y&invitation=**<span style="color:red">{{invitation_url 내용}}</span>**
    - sample : `\initial://reqService?process=I&ynCloud=Y&invitation=https://dev-console.myinitial.io/agent/endpoint?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiMzk3ZDZkNTEtODVlOC00NTNlLThlODAtZWI2NzVmZmVhYzU1IiwgImRpZCI6ICJkaWQ6c3N3Ok5vTEwxc2JSU0dQQjE5VHVxSFBXcVkiLCAibGFiZWwiOiAiKFRFU1QpXHViMzAwXHVkNTU5XHVjODFjXHVjOTlkXHViYTg1XHVhYzgwXHVjOTlkIFx1YWUzMFx1YWQwMCIsICJpbWFnZVVybCI6ICJodHRwczovL2tyLm9iamVjdC5uY2xvdWRzdG9yYWdlLmNvbS9kZXYtaW1hZ2UtZmlsZS9kNDFkOGNkOV9lMmY1MmQ1YV8xNjIyMTc5ODQxIn0=`
3. [QR code] 
    - 위 Deeplink를 QR code 생성하여 사용자 scan


<br>
<br>

### STEP 2. 사용자(Holder) --> 기관(Issuer/Verifier) : receive invitation & request connection.



<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 Holder가 initial app 일 경우 SDK에서 자동으로 처리된다</p>
</div>

* STEP2 실제 과정 참고
    * [Cloud Agent <<< **<span style="color:red">모바일</span>**] `receive invitation` 진행 후 `request connection` 전송
    * [**<span style="color:red">Cloud Agent </span>**>>> 모바일] `request connection` 받고 자동으로 `request connection` 전송
    * [Cloud Agent <<< **<span style="color:red">모바일</span>**] `request connection`을 받고 자동으로 `response` 전송
    * [**<span style="color:red">Cloud Agent </span>**>>> 모바일] `response`를 받고 자동으로 `response` 전송
    
    <br> 위와 같은 process로 인해 기관의 Webhook 서버로 아래 두번의 event(`request`,`response`) 결과가 전달 된다.
    정보는 참고만 하면 되고 다른 action은 필요 없다.(아래 sample은 JAVA 서버 기준) 
```
2021-06-02 15:31:57 [INFO ] [GlobalService.java]handleEvent(78) : handleEvent >>> topic:connections, state:request, body:{"created_at":"2021-06-02 06:31:57.255177Z","their_role":"inviter","updated_at":"2021-06-02 06:31:57.255177Z","rfc23_state":"request-sent","state":"request","accept":"auto","routing_state":"none","their_did":"78bCMv53bKrusuxyrrbgWM","invitation_mode":"once","connection_id":"7d670c2a-ef85-4289-81c3-24bb8e4f045d","invitation_key":"81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P","my_did":"66ZaFYLPorqgtQpo8AcpQY","their_label":"agency","topic":"connections"}
2021-06-02 15:31:57 [INFO ] [GlobalService.java]handleEvent(78) : handleEvent >>> topic:connections, state:response, body:{"created_at":"2021-06-02 06:31:57.255177Z","their_role":"inviter","updated_at":"2021-06-02 06:31:57.385549Z","rfc23_state":"response-received","state":"response","accept":"auto","routing_state":"none","their_did":"78bCMv53bKrusuxyrrbgWM","invitation_mode":"once","connection_id":"7d670c2a-ef85-4289-81c3-24bb8e4f045d","invitation_key":"81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P","my_did":"66ZaFYLPorqgtQpo8AcpQY","their_label":"agency","topic":"connections"}```
```
<br><br>    

### STEP 3. 기관(Issuer/Verifier) : Webhook Message 확인 및 Connection 정보 관리 

- 연결(Connection)이 완료되면 Webhook을 통해 아래 Message가 전달 된다.
  
```
2021-06-02 15:32:01 [INFO ] [GlobalService.java]handleEvent(78) : handleEvent >>> topic:connections, state:active, body:{"created_at":"2021-06-02 06:31:57.255177Z","routing_state":"none","my_did":"66ZaFYLPorqgtQpo8AcpQY","their_label":"agency","rfc23_state":"completed","connection_id":"7d670c2a-ef85-4289-81c3-24bb8e4f045d","their_role":"inviter","accept":"auto","updated_at":"2021-06-02 06:32:01.252322Z","invitation_key":"81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P","invitation_mode":"once","their_did":"78bCMv53bKrusuxyrrbgWM","state":"active","topic":"connections"}
```

- 기관 개발자는 body의 `"topic":"connections"`와 `"state":"active"`를 확인 해야 한다.
    - body의 `"state":"active"` 일 경우 연결이 완료 되었기 때문에, their_did(사용자 DID), connection_id(사용자와 communication 필요할때 사용하는 id)등을 확인/기록 하면 된다.
    - `their_did(사용자 DID)`는 정책상 Privacy 보호를 위해 수시로 변경(앱재설치등)되기 때문에, 고객 식별자로 사용 불가능 하다. 

- body로 전달 된 connection 정보 example
```json
{
  "created_at": "2021-06-02 06:31:57.255177Z",
  "routing_state": "none",
  "my_did": "66ZaFYLPorqgtQpo8AcpQY",
  "their_label": "agency",
  "rfc23_state": "completed",
  "connection_id": "7d670c2a-ef85-4289-81c3-24bb8e4f045d",
  "their_role": "inviter",
  "accept": "auto",
  "updated_at": "2021-06-02 06:32:01.252322Z",
  "invitation_key": "81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P",
  "invitation_mode": "once",
  "their_did": "78bCMv53bKrusuxyrrbgWM",
  "state": "active",
  "topic": "connections"
}
```
<br>
<div class="admonition note">
<p class="admonition-title">note</p>
<p> `connection_id`는 앞으로 DID 관련 모든 API에서 사용되는 중요한 uuid로서 관리가 필요하다. </p>
</div>


<br><br>

### STEP 4. Connection 정보 관리 Guide

Connection 정보는 사용자와 통신을 위한 기본 정보로 Wallet에 저장된다.<br>
`connection_id`는 각 기관에서 관리하는 개인의 식별자(ID, uuid, CI등)에 mapping 하여 관리하면 된다.([참조](/cloud_agent_demo))

- STEP3의 Webhook으로 전달된 connection 관련 message는 아래 API로도 확인 가능하다.

    - 전체 connection list 확인 : `GET` `/connections`
    - 특정 connection 정보 확인 : `GET` `/connections/{conn_id}` 
    * Swagger Document : [Link](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/connection/get_connections__conn_id_)
<p></p>
- Request Example
```
curl -X 'GET' \
  'https://dev-console.myinitial.io/agent/api/connections/91026652-18dc-4119-b8b9-9957c8430d86' \
  -H 'accept: application/json' \
  -H 'Authorization: bearer 4dd1f97a-1234-1234-1234-9ed8cd2cfb6d'
```
<p></p>

- Response Example
```json
{
  "created_at": "2021-06-02 08:08:42.520752Z",
  "routing_state": "none",
  "rfc23_state": "invitation-sent",
  "connection_id": "91026652-18dc-4119-b8b9-9957c8430d86",
  "their_role": "invitee",
  "accept": "auto",
  "updated_at": "2021-06-02 08:08:42.520752Z",
  "invitation_key": "7WzP4NDjmVTgunG7natwnFNZekBV7eBSkHo1aAfvjaqj",
  "invitation_mode": "once",
  "state": "invitation"
}
```

<br><br>

### [Option] Delete Connection 

`DELETE` `/connections/{conn_id}/remove`