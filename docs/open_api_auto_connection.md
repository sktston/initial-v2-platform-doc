Auto Connection Example
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


**기관**(Issuer/Verifier)과 **사용자**(Holder/개인)의 Connection(연결) 예제<br>
<br>

![did connections](img/connections.png)


Connection이란 기관과 사용자사이 Pairwise ID 및 PKI(Public Key Infrastructure) 기반으로 신뢰있는 통신 체널을 구축하는 행위이다. <br>
Auto Connection은 최소한의 API 사용으로 Key 생성 및 교환으로 상호 연결을 완료한다.
<br>

Connection State는 아래와 같다.

Topic | State | Description
--- | :---: | ---
Connection | invitation | Invitation 생성하거나 받은 상태
Connection | request | Connection 요청한 상태
Connection | response | Connection accept 상태
Connection | **<span style="color:red">active</span>** | <span style="color:red">Connection 완료 </span>

<p></p>
#### Auto Connection API 사용 순서 

1. 기관(Issuer/Verifier) : /connections/create-invitation (초대장 생성 API 호출)<br>
2. 사용자(개인/Holder) : /connections/receive-invitation (초대장 수락 API 호출)<br>
3. 기관 : connection request 자동 실행 <br>
4. 사용자 : accept connection request 자동 실행<br>

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
     auto_accept | Alice가 초대장 수락 시 자동 connection 설정 (**default : true**)
     multi_use | 초대장을 once/multi 사용여부. QR코드등 인쇄시 `true` 설정 필요 (**default : false**)
     public | Public DID를 기반으로 초대장 생성 (**default : true**)

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
    
   * Connection State
   
    |  | 기관 | 사용자 |
    | --- | --- | --- |
    | connection state | **`invitation`** | N/A |
  

<p></p>
<p></p>


### STEP 1-1. 기관(Issuer/Verifier) --> 사용자(Holder) : Invitation 전달

1. [initial default] **invitation_url**을 전달할 수 있는 API 작성 
    - [Web Console 개발 Guide](web_console_guide/#1-api) 참조
2. [deeplink] 요청 
    - spec : initial://reqService?process=I&ynCloud=Y&invitation=**<span style="color:red">{{invitation_url 내용}}</span>
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

* 기관과 사용자간 연결 중 기관관리자의 Webhook 서버로 아래 두번의 event 결과가 전달 된다.(아래 sample은 JAVA sample 기준). 
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
- 기관 개발자는 body의 `"topic":"connections"`와 `"state":"active"`를 항상 monitoring 해야한다.
- body의 `"state":"active"` 일 경우 연결이 완료 되었기 때문에, their_did(사용자 DID), connection_id(사용자와 communication 필요할때 사용하는 id)등을 확인 하면 된다.
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

- `connection_id`는 앞으로 모든 API에서 사용되는 중요한 uuid로서 별도 관리 필요하다.

<P></P>

### STEP 4. Connection 정보 관리 Guide

Connection 정보는 사용자와 통신을 위한 기본 정보로 Wallet에 저장된다.<br>
`connection_id`는 각 기관에서 관리하는 개인의 식별자(ID, uuid, CI등)에 mapping 하여 관리하면 된다.([참조](/cloud_agent_demo))

STEP3의 Webhook으로 전달된 connection 관련 message는 아래 API로도 확인 가능하다.

- 전체 connection list 확인 : `GET` `/connections`
- 특정 connection 정보 확인 : `GET` `/connections/{conn_id}` 


* Swagger Document : [Link](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/connection/get_connections__conn_id_)

Request Example

```
curl -X 'GET' \
  'https://dev-console.myinitial.io/agent/api/connections/91026652-18dc-4119-b8b9-9957c8430d86' \
  -H 'accept: application/json' \
  -H 'Authorization: bearer 4dd1f97a-1234-1234-1234-9ed8cd2cfb6d'
```

Response Example
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

/connections/{conn_id}/remove