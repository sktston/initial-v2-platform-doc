# Cloud Agent 동작 시나리오
<br>

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
  "connection_id": "09d617ee-f6e0-4191-a786-ff8ca325d400",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "b84b4861-d511-4655-a803-9768e9af888b",
    "recipientKeys": [
      "4VLDEQesd8qT5VAG3o9EwzS37225ZkN1LnieDidGjw7V"
    ],
    "serviceEndpoint": "http://host.docker.internal:8020",
    "label": "faber.agent"
  },
  "invitation_url": "http://host.docker.internal:8020?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiYjg0YjQ4NjEtZDUxMS00NjU1LWE4MDMtOTc2OGU5YWY4ODhiIiwgInJlY2lwaWVudEtleXMiOiBbIjRWTERFUWVzZDhxVDVWQUczbzlFd3pTMzcyMjVaa04xTG5pZURpZEdqdzdWIl0sICJzZXJ2aWNlRW5kcG9pbnQiOiAiaHR0cDovL2hvc3QuZG9ja2VyLmludGVybmFsOjgwMjAiLCAibGFiZWwiOiAiZmFiZXIuYWdlbnQifQ==",
  "alias": "create_invitation"
}
```
**invitation**내용이나 `invitation_url` 혹은 url을 **QR code**로 변경하여 Alice에게 전달하여 초대.


​현재 상태에서 `get` `/connections​/{conn_id}` connection state를 확인하면 **`invitation`**

```
{
  "results": [
    {
      "connection_id": "adb2d50a-b668-49a9-97d0-f3b1fdbd90c0",
      "invitation_mode": "once",
      "alias": "create_invitation",
      "initiator": "self",
      "invitation_key": "7Juhmgmk711MVo5MybfUQFuwpfa9XQU7n6dEk9piWznh",
      "accept": "auto",
      "routing_state": "none",
      "created_at": "2020-08-18 04:25:15.779615Z",
      "updated_at": "2020-08-18 04:25:15.779615Z",
      "state": "invitation"
    }
  ]
}
```
<br>
<br>

#### STEP 2. Alice : receive invitation & request connection.
##### receive-invitation

`POST` `/connections/receive-invitation` 초대장을 받음

Parameter

 Name | Description 
 --- | --- 
 body | Invitation 내용
 alias | 별칭 (e.g A대학제증명발급처) 
 auto_accept | Faber 초대장 수락 시 자동 connection이 active.
 

<br>
Response (초대를 Accept를 하기 위해 생성한 정보)
```
{
  "state": "request",
  "invitation_key": "HPGKSgw2nL7Y6EztMnXDwiBFimCFbP6FpmgACAGyKy4Y",
  "routing_state": "none",
  "alias": "receive_invitation",
  "created_at": "2020-08-18 03:51:47.086454Z",
  "my_did": "SoqF5oYpjjGUF4KZQZkVB9",
  "invitation_mode": "once",
  "their_label": "faber.agent",
  "updated_at": "2020-08-18 03:51:47.112529Z",
  "initiator": "external",
  "request_id": "16c2e0d7-aab9-49ce-9296-e5c4a834eac9",
  "accept": "auto",
  "connection_id": "f214391a-b0f5-4291-bac3-e1f64fcc4b06"
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
  "their_did": "Vgr1yhnYfhk3YoeSDj7PT7",
  "routing_state": "none",
  "state": "request",
  "updated_at": "2020-08-18 00:53:13.248382Z",
  "alias": "receive_invitation",
  "invitation_mode": "once",
  "accept": "auto",
  "invitation_key": "2d13nxz4wtJGFr5pzcdu3jWc2gQZkGifURdm9qax6isd",
  "created_at": "2020-08-18 00:51:31.597548Z",
  "connection_id": "1443c96f-5a10-44f8-ba95-5e45c3b4360b",
  "initiator": "external",
  "request_id": "9cf066e6-29e6-41cb-81b1-a258d6c8dab6",
  "their_label": "faber.agent",
  "my_did": "6d4rAztN1m5D1Fegkg2BsG"
}
```
Faber의 초대를 수락

#### STEP 4. Faber --> Faber : accept connection request.
##### accept-request

`POST' '/connections/{conn_id}/accept-request` Alice의 연결 요쳥 수락

> STEP1의  `/connections/create-invitation` `auto_accept` **`true`** 이면 STEP4 skip 가능 

Parameter

 Name | Description 
 --- | --- 
 conn_id | Connection identifier
 my_endpoint | My URL endpoint

Response (초대를 Accept 하기 위해 생성한 정보)
```
{
  "created_at": "2020-08-18 01:19:24Z",
  "updated_at": "2020-08-18 01:19:24Z",
  "their_role": "Point of contact",
  "inbound_connection_id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "connection_id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "their_label": "Bob",
  "error_msg": "No DIDDoc provided; cannot connect to public DID",
  "initiator": "self",
  "routing_state": "active",
  "my_did": "WgWxqztrNooG92RXvxSTWv",
  "invitation_key": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV",
  "request_id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "invitation_mode": "once",
  "their_did": "WgWxqztrNooG92RXvxSTWv",
  "accept": "auto",
  "state": "active",
  "alias": "Bob, providing quotes"
}
```
Faber의 초대를 수락


#### STEP 5. Faber --> Faber : accept connection request.

`POST' '/connections/{conn_id}/accept-invitation` Alice의 연결 요쳥 수락

> STEP1의  `/connections/create-invitation` `auto_accept` **`true`** 이면 STEP4 skip 가능 

Parameter

 Name | Description | Example
 --- | --- | ---
 body | Schema String | <pre> 
 


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