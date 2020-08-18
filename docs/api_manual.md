# Cloud Agent 동작 시나리오

#### STEP 1. Faber --> Alice : create invitation & send invitation.
##### create-invitation
​`POST` `/connections​/create-invitation` 새로운 초대장 생성

Parameter

 Name | Description 
 --- | --- 
 alias | Alice에게 전달할 초대장 별칭 (e.g A대학제증명발급처) 
 auto_accept | Alice가 초대장 수락 시 자동 connection 설정. 
 multi_use | 초대장을 일회성/다회성 사용여부. QR코드등 인쇄시 `true` 설정 필요
 public | Public DID를 기반으로 초대장 생성

Response (생성된 초대장)
```
{
  "connection_id": "75f03bb3-88dc-4a1f-a262-bd47d0617d1c",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "a6362105-fbb9-42b9-8429-c272867bcf23",
    "serviceEndpoint": "http://172.27.18.102:8020",
    "label": "faber.agent",
    "recipientKeys": [
      "2CXnk4KnrF5bZ2rp88m3irRv7uYeMeFFk3yn2NHjPgW2"
    ]
  },
  "invitation_url": "http://172.27.18.102:8020?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiYTYzNjIxMDUtZmJiOS00MmI5LTg0MjktYzI3Mjg2N2JjZjIzIiwgInNlcnZpY2VFbmRwb2ludCI6ICJodHRwOi8vMTcyLjI3LjE4LjEwMjo4MDIwIiwgImxhYmVsIjogImZhYmVyLmFnZW50IiwgInJlY2lwaWVudEtleXMiOiBbIjJDWG5rNEtuckY1YloycnA4OG0zaXJSdjd1WWVNZUZGazN5bjJOSGpQZ1cyIl19",
  "alias": "A대학제증명발급처"
}
```
`invitation`내용이나 `invitation_url` 혹은 url을 `QR code`로 변경하여 Alice에게 전달하여 초대

#### STEP 2. Alice : receive invitation & request connection.
##### receive-invitation

`POST' '/connections/receive-invitation` 초대장을 받음

Parameter

 Name | Description 
 --- | --- 
 body | Invitation 내용
 alias | 별칭 (e.g A대학제증명발급처) 
 auto_accept | Faber 초대장 수락 시 자동 connection이 active.

Response (초대를 Accept를 하기 위해 생성한 정보)
```
{
  "invitation_key": "2CXnk4KnrF5bZ2rp88m3irRv7uYeMeFFk3yn2NHjPgW2", // Faber가 생성한 초대장 Key
  "invitation_mode": "once", //once, multi, or static
  "their_label": "faber.agent",
  "my_did": "JTXYZANUAxngnpPttKDuzQ", // Our DID for connection
  "alias": "A대학제증명초대장받음",
  "state": "request", //Current record state
  "updated_at": "2020-08-14 01:33:59.347619Z",
  "connection_id": "62b25970-857c-40b8-90af-23dc003d3e45", //Connection identifier
  "routing_state": "none",
  "request_id": "4ec0d20e-97f0-4108-82ce-1ce2197024cf",
  "created_at": "2020-08-14 01:33:39.309380Z",
  "initiator": "external", //onnection initiator: self, external, or multiuse
  "accept": "auto"
}
```
초대장 받고 연결하기 위한 DID등을 생성 


#### STEP 3. Alice --> Faber : Accept invitation & request connection.
##### accept-invitation

`POST` `/connections/{conn_id}/accept-invitation` 초대를 수락

> STEP2  `/connections/receive-invitation` `auto_accept` **`true`** 이면 STEP3 skip 가능 

Parameter

 Name | Description 
 --- | --- 
 conn_id | Connection identifier
 my_endpoint | My URL endpoint
 my_label | Label for connection

Response (초대를 Accept를 하기 위해 생성한 정보)
```
{
  "their_did": "USVn9w6voMEWb6nHuriZ4i", // Faber DID
  "invitation_key": "2CXnk4KnrF5bZ2rp88m3irRv7uYeMeFFk3yn2NHjPgW2",
  "invitation_mode": "once",
  "their_label": "faber.agent",
  "my_did": "JTXYZANUAxngnpPttKDuzQ",
  "alias": "A대학제증명초대장받음",
  "state": "request", // Current record state
  "updated_at": "2020-08-14 01:52:29.188566Z",
  "connection_id": "62b25970-857c-40b8-90af-23dc003d3e45",
  "routing_state": "none",
  "request_id": "dab768c1-bf20-473f-8628-f55614549cb0",
  "created_at": "2020-08-14 01:33:39.309380Z",
  "initiator": "external",
  "accept": "auto"
}
```
Faber의 초대를 수락

#### STEP 4. Faber --> Faber : accept connection request.

`POST' '/connections/{conn_id}/accept-invitation` Alice의 연결 요쳥 수락

> STEP1의  `/connections/create-invitation` `auto_accept` **`true`** 이면 STEP4 skip 가능 

Parameter

 Name | Description 
 --- | --- 
 conn_id | Connection identifier
 my_endpoint | My URL endpoint
 my_label | Label for connection

Response (초대를 Accept 하기 위해 생성한 정보)
```
{
  "their_did": "USVn9w6voMEWb6nHuriZ4i", // Faber DID
  "invitation_key": "2CXnk4KnrF5bZ2rp88m3irRv7uYeMeFFk3yn2NHjPgW2",
  "invitation_mode": "once",
  "their_label": "faber.agent",
  "my_did": "JTXYZANUAxngnpPttKDuzQ",
  "alias": "A대학제증명초대장받음",
  "state": "request", // Current record state
  "updated_at": "2020-08-14 01:52:29.188566Z",
  "connection_id": "62b25970-857c-40b8-90af-23dc003d3e45",
  "routing_state": "none",
  "request_id": "dab768c1-bf20-473f-8628-f55614549cb0",
  "created_at": "2020-08-14 01:33:39.309380Z",
  "initiator": "external",
  "accept": "auto"
}
```
Faber의 초대를 수락