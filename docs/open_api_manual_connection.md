Manual Connection Example
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


**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 수동 연결 예제
<br>

Connection State

Topic | State | Description
--- | --- | ---
Connection | invitation | Invitation 생성하거나 받은 상태
Connection | request | Connection 요청한 상태
Connection | response | Connection accept 상태
Connection | active | Connection 완료 

<p></p>
#### Manual Connection API 사용 Summary
1. Faber : /connections​/create-invitation (초대장 생성 API 호출)<br>
2. Alice : /connections/receive-invitation (초대장 수락 API 호출)<br>
3. Alice : /connections/{conn_id}/accept-invitation (연결 요청 API 호출) <br>
4. Faber : /connections/{conn_id}/accept-request (연결 요청 수락 API 호출) <br>

### STEP 1. Faber --> Alice : create invitation & send invitation.


* Method and Resource

    `POST` `/connections​/create-invitation` 새로운 초대장 생성
<p></p>

* Parameter

     Name | Description 
     --- | --- 
     alias | Alice에게 전달할 초대장 별칭 (e.g A대학제증명발급처)
     auto_accept | Alice가 초대장 수락 시 자동 connection 설정
     multi_use | 초대장을 일회성/다회성 사용여부. <br>QR코드등 인쇄시 `true` 설정 필요
     public | Public DID를 기반으로 초대장 생성 (현재 미지원)

<p></p>

* Example 

    * input <br>
    `auto_accept` : `false`<br>
    `multi_use` : `false`<br>
    `public` : `false`<br>
<br>
    * Response body (생성된 초대장)
```json
{
  "connection_id": "44bf3739-0e9e-43aa-b702-ed5059e3b450",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "ca2cb9c2-31a2-44c9-abc3-1b9abe22ab35",
    "label": "faber.agent",
    "recipientKeys": [
      "8x3USjfSd1Ynkj3ydh9mAv1AoWWDdctXqCURH6faqHk1"
    ],
    "serviceEndpoint": "http://host.docker.internal:8020"
  },
  "invitation_url": "http://host.docker.internal:8020?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiY2EyY2I5YzItMzFhMi00NGM5LWFiYzMtMWI5YWJlMjJhYjM1IiwgImxhYmVsIjogImZhYmVyLmFnZW50IiwgInJlY2lwaWVudEtleXMiOiBbIjh4M1VTamZTZDFZbmtqM3lkaDltQXYxQW9XV0RkY3RYcUNVUkg2ZmFxSGsxIl0sICJzZXJ2aWNlRW5kcG9pbnQiOiAiaHR0cDovL2hvc3QuZG9ja2VyLmludGVybmFsOjgwMjAifQ==",
  "alias": "create_invitation"
}
```

   * Connection State check : `get` `/connections​/{conn_id}`
   
|  | Faber | Alice |
| --- | --- | --- |
| connection state | **`invitation`** | N/A |

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> Alice의 연결요청에 대한 자동 연결을 위해 `auto_accept` parameter를 `true`로 설정하면 STEP4 Accept Connection Request 생략 가능 </p>
</div>

<p></p>  

* Next Step
    
    **invitation**내용이나 **invitation_url** 혹은 url의 **QR code**를 Alice에게 전달하여 초대.


<br>
<br>

### STEP 2. Alice --> Faber : receive invitation & request connection.

* Method and Resource

    `POST` `/connections/receive-invitation` 초대장을 받음

* Parameter

 Name | Description 
 --- | --- 
 body | Invitation 내용
 alias | 별칭 (e.g A대학제증명발급처) 
 auto_accept | Faber 초대장 수락 시 connection이 자동 active.
 
 <p></p>

* Example

    * input <br>
    `auto_accept` : `false`<br>
    
    * body - STEP1의 Faber가 생성한 invitation json을 입력

    * Response body

```json
{
  "accept": "manual",
  "alias": "receive_invitation",
  "state": "invitation",
  "invitation_key": "8x3USjfSd1Ynkj3ydh9mAv1AoWWDdctXqCURH6faqHk1",
  "routing_state": "none",
  "invitation_mode": "once",
  "initiator": "external",
  "their_label": "faber.agent",
  "created_at": "2020-08-19 04:30:28.542990Z",
  "updated_at": "2020-08-19 04:30:28.542990Z",
  "connection_id": "971f932a-1204-409f-9a9a-ebf2da53d05a"
}
```

<p></p>
* Connection State check : `get` `/connections​/{conn_id}`

     |  | Faber | Alice |
     | --- | --- | --- |
     | connection state | **`invitation`** | **`invitation`** |
  
    <div class="admonition Note">
    <p class="admonition-title">Note</p>
    <p> `auto_accept` parameter를 `true`로 설정하면 이 단계에서 Alice의 state는 자동으로 `active` 변경됨 STEP3 Accept Invitation 생략 가능 </p>
    </div>    

<p></p>
* Next Step

    초대장 받기 완료 후 Accept Invitation 진행 
    
<br>
<br>

### STEP 3. Alice --> Faber : Accept invitation & request connection.

* Method and Resource

    `POST` `/connections/{conn_id}/accept-invitation` 초대를 수락

    <div class="admonition warning">
    <p class="admonition-title">Warning</p>
    <p> STEP2에서 `auto_accept` parameter를 `true`로 설정하였다면, 이 단계를 진행하면 안됨</p>
    </div>

* Parameter

 Name | Description 
 --- | --- 
 conn_id | Connection identifier
 my_endpoint | My URL endpoint
 my_label | Label for connection

<p></p>

* Example

    * input <br>
    `conn_id` : `ae354d32-64d8-464b-b00a-2b8041f141e1`<br>

    * Response (초대를 Accept를 하기 위해 생성한 정보)
```json
{
  "request_id": "c9195df7-1c2b-4f39-855d-baec4c5d926b",
  "accept": "manual",
  "alias": "receive_invitation",
  "state": "request",
  "invitation_key": "8x3USjfSd1Ynkj3ydh9mAv1AoWWDdctXqCURH6faqHk1",
  "routing_state": "none",
  "invitation_mode": "once",
  "initiator": "external",
  "my_did": "5TEwzfwHJeTukFEsYd2nys",
  "their_label": "faber.agent",
  "created_at": "2020-08-19 04:30:28.542990Z",
  "updated_at": "2020-08-19 04:31:19.274806Z",
  "connection_id": "971f932a-1204-409f-9a9a-ebf2da53d05a"
}
```
<p></p>
* Connection State check : `get` `/connections​/{conn_id}`

     |  | Faber | Alice |
     | --- | --- | --- |
     | connection state | **`request`** | **`request`** |
     
<p></p>  

* Next Step
    Faber의 초대를 수락하고, Connection의 Request 진행. Faber는 최종 Connection Request를 Accept 해야 함.


<br>
<br>

### STEP 4. Faber --> Alice : accept connection request.

* Method and Resource
    `POST' '/connections/{conn_id}/accept-request` Alice의 연결 요쳥 수락

    <div class="admonition warning">
    <p class="admonition-title">Warning</p>
    <p> STEP1에서 `auto_accept` parameter를 `true`로 설정하였다면, 이미 `active` state 이기 때문에 error 발생 </p>
    </div>
    
* Parameter

     Name | Description 
     --- | --- 
     conn_id | Connection identifier
     my_endpoint | My URL endpoint

<p></p>
* Example

    * input <br>
    `conn_id` : `e56d0930-8bf6-4ceb-a1e5-8749f65bf553`<br>
    
    * Response (초대를 Accept 하기 위해 생성한 정보)
```json
{
  "their_label": "alice.agent",
  "state": "response",
  "routing_state": "none",
  "accept": "manual",
  "their_did": "DYersndQVodwUaJQfvNNkx",
  "initiator": "self",
  "invitation_key": "Fzz8mQSYUzmvRyjQkVwXpZTJE1RDZR6SbkEsEKeCryLd",
  "updated_at": "2020-08-19 03:41:25.873966Z",
  "alias": "create_invitation",
  "connection_id": "e56d0930-8bf6-4ceb-a1e5-8749f65bf553",
  "my_did": "HdLX7RWEvcH2Es7ssnq4xv",
  "created_at": "2020-08-18 05:24:06.092740Z",
  "invitation_mode": "once"
}
```
* Connection State check : `get` `/connections​/{conn_id}`

     |  | Faber | Alice |
     | --- | --- | --- |
     | connection state | `response` --> **`active`** | **`active`** |

<p></p>
* Next Step

    Alice와 Faber 연결이 완료. ping을 통한 연결 확인.<br>
    Schema 생성 및 VC 발행 진행.
<br>
<br>
<br>
<br>
