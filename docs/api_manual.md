Cloud Agent REST APIs Manual
================

Faber(Issuer/Verifier)와 Alice(Holder/Prover)의 연결 및 VC발급/검증 예제를 통한 API 설명
<br>

#### STEP 1. Faber --> Alice : create invitation & send invitation.


* Method and Resource

    `POST` `/connections​/create-invitation` 새로운 초대장 생성

* Parameter

     Name | Description 
     --- | --- 
     alias | Alice에게 전달할 초대장 별칭 (e.g A대학제증명발급처)
     auto_accept | Alice가 초대장 수락 시 자동 connection 설정
     multi_use | 초대장을 일회성/다회성 사용여부. QR코드등 인쇄시 `true` 설정 필요
     public | Public DID를 기반으로 초대장 생성 (현재 미지원)

* Example 

    * input <br>
    `auto_accept` : `false`<br>
    `multi_use` : `false`<br>
    `public` : `false`<br>

    * Response (생성된 초대장)
```json
{
  "connection_id": "e56d0930-8bf6-4ceb-a1e5-8749f65bf553",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "e7ee00e9-16a3-432e-b878-cea93ef67c05",
    "recipientKeys": [
      "Fzz8mQSYUzmvRyjQkVwXpZTJE1RDZR6SbkEsEKeCryLd"
    ],
    "serviceEndpoint": "http://host.docker.internal:8020",
    "label": "faber.agent"
  },
  "invitation_url": "http://host.docker.internal:8020?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiZTdlZTAwZTktMTZhMy00MzJlLWI4NzgtY2VhOTNlZjY3YzA1IiwgInJlY2lwaWVudEtleXMiOiBbIkZ6ejhtUVNZVXptdlJ5alFrVndYcFpUSkUxUkRaUjZTYmtFc0VLZUNyeUxkIl0sICJzZXJ2aWNlRW5kcG9pbnQiOiAiaHR0cDovL2hvc3QuZG9ja2VyLmludGVybmFsOjgwMjAiLCAibGFiZWwiOiAiZmFiZXIuYWdlbnQifQ==",
  "alias": "create_invitation"
}
```

<br>

* Connection State Check : `get` `/connections​/{conn_id}`

     |  | Faber | Alice |
     | --- | --- | --- |
     | connection state | **`invitation`** | N/A |
  
    <div class="admonition Note">
    <p class="admonition-title">Note</p>
    <p> Alice의 연결요청에 대한 자동 연결을 위해 `auto_accept` parameter를 `true`로 설정하면 STEP5 Accept Connection Request 생략 가</p>
    </div>
    
* Next Step
    
    **invitation**내용이나 **invitation_url** 혹은 url의 **QR code**를 Alice에게 전달하여 초대.


<br>
<br>

#### STEP 2. Alice --> Faber : receive invitation & request connection.

* Method and Resource

    `POST` `/connections/receive-invitation` 초대장을 받음

* Parameter

 Name | Description 
 --- | --- 
 body | Invitation 내용
 alias | 별칭 (e.g A대학제증명발급처) 
 auto_accept | Faber 초대장 수락 시 connection이 자동 active.
 

* Example
    * body input - STEP1의 Faber가 생성한 invitation을 입력(QRcode 및 URL등 가능)
```json
{
        "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
        "@id": "9f5e5240-f897-4104-a682-7fb091eeedcb",
        "label": "faber.agent",
        "serviceEndpoint": "http://host.docker.internal:8020",
        "recipientKeys": [
          "7Juhmgmk711MVo5MybfUQFuwpfa9XQU7n6dEk9piWznh"
        ]
      }
```
<br>

* Response (초대를 Accept를 하기 위해 생성한 정보)

```json
{
  "invitation_key": "Fzz8mQSYUzmvRyjQkVwXpZTJE1RDZR6SbkEsEKeCryLd",
  "state": "invitation",
  "initiator": "external",
  "created_at": "2020-08-18 05:25:09.997146Z",
  "invitation_mode": "once",
  "alias": "receive_invitation",
  "their_label": "faber.agent",
  "routing_state": "none",
  "accept": "manual",
  "connection_id": "ae354d32-64d8-464b-b00a-2b8041f141e1",
  "updated_at": "2020-08-18 05:25:09.997146Z"
}
```


* Connection State `get` `/connections​/{conn_id}`

   **`invitation`**

<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>We strongly recommend to <a class="hoverxref tooltip reference internal" data-doc="guides/specifying-dependencies" data-docpath="/guides/specifying-dependencies.html" data-project="docs" data-section="specifying-dependencies" data-version="stable" href="../guides/specifying-dependencies.html#specifying-dependencies"><span class="std std-ref">pin the MkDocs version</span></a>
used for your project to build the docs to avoid potential future incompatibilities.</p>
</div>


* Next Step

    초대장 받기 완료 후 Accept Invitation 진행 
    

#### STEP 3. Alice --> Faber : Accept invitation & request connection.

* Method and Resource

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