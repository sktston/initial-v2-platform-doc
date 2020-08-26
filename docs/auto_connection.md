Auto Connection Example
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 연결 Example
<br>
Connection State

Topic | State | Description
--- | --- | ---
Connection | invitation | Invitation 생성하거나 받은 상태
Connection | request | Connection 요청한 상태
Connection | response | Connection accept 한 태
Connection | active | Connection 완료 


### STEP 1. Faber --> Alice : create invitation & send invitation.


* Method and Resource

    `POST` `/connections​/create-invitation` 새로운 초대장 생성
<p></p>

* Parameter

     Name | Description 
     --- | --- 
     alias | Alice에게 전달할 초대장 별칭 (e.g A대학제증명발급처)
     auto_accept | Alice가 초대장 수락 시 자동 connection 설정
     multi_use | 초대장을 일회성/다회성 사용여부. QR코드등 인쇄시 `true` 설정 필요
     public | Public DID를 기반으로 초대장 생성 (현재 미지원)

<p></p>

* Example 

    * input <br>
    `auto_accept` : `true`<br>
    `multi_use` : `false`<br>
    `public` : `false`<br>
<br>
    * Response body (생성된 초대장)
```json
{
  "connection_id": "eb180f76-0081-41db-a12c-454fe36b3295",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "66e7239f-93fe-47f2-939d-ddbc31f929d6",
    "label": "faber.agent",
    "serviceEndpoint": "http://host.docker.internal:8020",
    "recipientKeys": [
      "9RGU3g5Mm4Pziza3E3im12pPjFxPhaFDkZWQQvqjw9Sm"
    ]
  },
  "invitation_url": "http://host.docker.internal:8020?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiNjZlNzIzOWYtOTNmZS00N2YyLTkzOWQtZGRiYzMxZjkyOWQ2IiwgImxhYmVsIjogImZhYmVyLmFnZW50IiwgInNlcnZpY2VFbmRwb2ludCI6ICJodHRwOi8vaG9zdC5kb2NrZXIuaW50ZXJuYWw6ODAyMCIsICJyZWNpcGllbnRLZXlzIjogWyI5UkdVM2c1TW00UHppemEzRTNpbTEycFBqRnhQaGFGRGtaV1FRdnFqdzlTbSJdfQ==",
  "alias": "create_invitation"
}
```
    
   * Connection State check : `get` `/connections​/{conn_id}`
   
    |  | Faber | Alice |
    | --- | --- | --- |
    | connection state | **`invitation`** | N/A |
  

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
 

* Example

    * input <br>
    `auto_accept` : `true`<br>
    
    * body - STEP1의 Faber가 생성한 invitation json을 입력

    * Response body

```json
{
  "updated_at": "2020-08-19 04:54:05.847426Z",
  "my_did": "4jaLRZTfHA9MVf2a4QaJGE",
  "state": "request",
  "their_label": "faber.agent",
  "invitation_mode": "once",
  "routing_state": "none",
  "initiator": "external",
  "alias": "receive_invitation",
  "connection_id": "2f139bd4-835a-4a3f-aae7-ddf9936373a6",
  "created_at": "2020-08-19 04:54:05.833098Z",
  "invitation_key": "9RGU3g5Mm4Pziza3E3im12pPjFxPhaFDkZWQQvqjw9Sm",
  "accept": "auto",
  "request_id": "33f861dc-29fa-4025-ae95-b951cd35748b"
}
```


* Connection State check : `get` `/connections​/{conn_id}`

     |  | Faber | Alice |
     | --- | --- | --- |
     | connection state | **`active`** | `request` --> **`active`** |
  
<p></p>
* Next Step

    연결 완료. Schema 생성 및 VC 발행 진행.
