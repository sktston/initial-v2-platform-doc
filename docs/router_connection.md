Router Connection Example
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 Connection(연결) 예제<br>
Connection은 기존 Legacy 서비스에서 Log-in과 같은 Authentication(인증) 개념.<br>
Alice와 Faber의 직접 연결이 아닌 Alice의 routing(static) agent를 통한 연결 예제.<br>
Routing agent는  Remote administration interface for an agent, IoT devices, Relays, OpenAPI to DIDComm translator 등의 용도로 사용할 수 있다.
<br>
Alice의 Routing Agent는 별도로 생성해야한다.

Connection State는 아래와 같음.

Topic | State | Description
--- | --- | ---
Connection | invitation | Invitation 생성하거나 받은 상태
Connection | request | Connection 요청한 상태
Connection | response | Connection accept 한 태
Connection | active | Connection 완료 

Routing Connection의 Example

### STEP 1. Faber --> Alice : create invitation & send invitation.

auto connection과 동일하게 invitation 생성

`POST` `/connections​/create-invitation`

### STEP 2. Alice Routing Agent --> Alice : create invitation for inbound.

Alice Routing Agent와 Alice Agent와 연결을 위해 초대 및 연결을 진행.
STEP1과 방법은 동일함

`POST` `/connections​/create-invitation` 새로운 초대장 생성


### STEP 3. Alice --> Alice Routing Agent : Accept invitation. 

초대장을 받고 자동으로 연결됨

`POST` `/connections/receive-invitation` 초대장을 받음

   
### STEP 4. Alice --> Faber : Receive invitation. 

Faber의 초대장을 받음. Auto accept를 설정하면 안됨.

`POST` `/connections/receive-invitation` 초대장을 받음

<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p> Auto Accept를 설정하면 router 없이 직접 연결됨. auto accept `false`로 설정 필요 </p>
</div>
    

### STEP 5. Alice --> Alice Routing Agent : Assign another connection as the inbound connection.

* Method and Resource

    `POST` `/connections/{conn_id}/establish-inbound/{ref_id}` Router를 inbound connection으로 설정 

* Parameter

 Name | Description 
 --- | --- 
 conn_id | Connection identifier
 ref_id | Inbound connection identifier (Routing Agent Connection id)

<p></p>

* Example

    * input <br>
    `conn_id` : `ae354d32-64d8-464b-b00a-2b8041f141e1`<br>
    `ref_id` : `f6a34c00-be0c-4935-b7ae-b4ec722aacb8`

    * Response (초대를 Accept를 하기 위해 생성한 정보)
```json
{ }
```
<p></p>


* Next Step
    Faber의 초대를 수락하고, Connection의 Request 진행. Faber는 최종 Connection Request를 Accept 해야 함.


<br>
<br>



### STEP 6. Alice --> Faber : Accept invitation & request connection.

* Method and Resource

    `POST` `/connections/{conn_id}/accept-invitation` 초대를 수락

    <div class="admonition warning">
    <p class="admonition-title">Warning</p>
    <p> STEP4에서 `auto_accept` parameter를 `true`로 설정하였다면, 이 단계를 진행하면 안됨</p>
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