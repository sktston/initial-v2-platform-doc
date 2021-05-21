Proof Presentation
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

<br>

**Faber**(검증자/Verifier)와 **Alice**(Holder/Prover)의 VC검증 시나리오
<br>

![verify sequence diagram](img/verify_diagram.png)
[PlantUML](http://www.plantuml.com/plantuml/uml/TPH1JzjA4CVl-ob6d1o4UNT44EXTthjLFN2jJB9fBOpjsciaRW6CH0jIQKj4W3WAhG1H1IaDDCsX_KBUpNVeh3s9XLKa99BT_ypE_7ypMGs55OXeos7S5RROWMqUs0sR1s25CBTEN-Qcjr58DsglhoxkLFV4-CzpwdYuvlkLcSmEmuY4_-kFc82nQXNyW0VyRMI7jk1GhJ96GdCboxm4w__AGISTlmDr7iklnsnMpJ99O0bgdkloch0zrr1dWtGug90wU-95uFbUQ3c6vsj99Nic2lD7A9tEaWwRwYO1rUkef6tu55LXheUb1VRhDuBDvngIJ1DNGEuzOk_ppCplvU0oVUY1EXgff8aTC5D7Z3gJLll9EIQ1IUjUVhi35VSc-maAfow2V1Yh1FyUGTtkgjv7agZUBaopzIXQ9J6LpSoyISk1T3oGxwzLvvUywe3QJsJt1wI39fujCdEvuRa2VNEyeBhXH7nbUMF5TcrXOw3b-nW5KmCWBmbhLsTzAEfJ5wTgU8_lnlAeluYSww2TAW2PJ-qjHngvLnMOdBUmqGpLInm57WemjYotRWalsCbf5Ka9Ue6BaWLv9D5oU6C6goIOiSgdL2Ce4yyyxnvgw-uU8VqvbiTDRI9YLQqOvEUElEcdtmTohxt0Sehgou6yRYyG3sJNJ8U_5zJe51qdrncOU1shm_RJYdZzbIL4O6z4WeSLhLBjNNNRL4S9DL2MuyxKxPf5Sap_QJOYtGFPkmOZv4v3PytqcMZouGG70WdAKHCCrtFDW6zxctXRaADjuI7khAMdgCo1jcR6WCmdImdaywkLZotwNBnGtgvdP0KrIggvbT4Noq2LmEGofbrXHluVDBRH54cvztMTVAVKD3tvJ4_KStdgwDmWVRrem2mMWMhJ0FLn9yQnKLS4oH11nFzfM4x8YpB_jWThjKssYWBynVu3)

present_proof State 및 Webhook event 전달 항목 

Topic | State | Description
--- | --- | ---
present_proof | request_sent | (issuer)proof presentation 요청한 상태 
present_proof | request_received | (holder)proof presentation 요청을 받은 상태
present_proof | presentation_sent | (holder) proof를 presentation 한 상태
present_proof | <font color=red>presentation_received<br><b>(Webhook event 전달) | (issuer) Credential 요청을 받은 상태 
present_proof | <font color=red>verified<br><b>(Webhook event 전달) | (issuer) Proof verification 완료 한 상태  
present_proof | presentation_acked | (holder) Proof verified 응답을 받은 상태 

<br><br>

### STEP 1. Faber(검증자) --> Alice(Holder) : Alice에게 Proof Present를 보낸다. 

* Method and Resource

    `POST` `/present-proof/send-verification-request` proof 제출 요청.  

* Body Parameter

 Name | Description 
 --- | ---
 connection_id | Alice와 connection 정보
 verification_template_id | 사용하고자 하는 검증양식ID(verifTplId) 
 
<p></p>

검증양식ID는 아래 initial Console에서 생성 가능함.

![webconsole 1](img/web_console_create_verification_templete_id.png)

검증양식ID는 아래 initial Console에서 확인 가능함.

![webconsole 1](img/web_console_verification_templete_id.png)

* Example

body example
```json
{
  "connection_id": "{{connectionId}}",
  "verification_template_id": "{{verifTplId}}"
}
```

curl example
```
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/present-proof/send-verification-request' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer 2ca4dd8a-xxxx-421c-xxxx-c5fb0286f2cc' \
--data-raw '{
    "connection_id": "2a6ed506-9577-xxxx-a60c-726241f89aec",
    "verification_template_id": "b43aba3a-xxxx-4a96-xxxx-9ee532b71cab"
}'
```

<p></p>
 
   * Response body
```json
{
   "thread_id":"a1e4441a-2c08-4807-b92f-3e8b6030b037",
   "trace":false,
   "presentation_request_dict":{
      "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/request-presentation",
      "@id":"a1e4441a-2c08-4807-b92f-3e8b6030b037",
      "comment":"{\"verification_template_id\":\"2f2ee9e2-1cd7-48d5-ad63-25aced9abccf\"}",
      "request_presentations~attach":[
         {
            "@id":"libindy-request-presentation-0",
            "mime-type":"application/json",
            "data":{
               "base64":"eyJuYW1lIjogIlx1YWNlMFx1YjgyNFx1YjMwMFx1ZDU1OVx1YzBkZFx1Yzk5ZFx1YWM4MFx1Yzk5ZHYyIiwgInZlcnNpb24iOiAiMS4wIiwgInJlcXVlc3RlZF9hdHRyaWJ1dGVzIjogeyJjYW1wdXNsb2NhdGlvbiI6IHsibmFtZSI6ICJjYW1wdXNsb2NhdGlvbiIsICJub25fcmV2b2tlZCI6IHsiZnJvbSI6IDAsICJ0byI6IDE2MTkwOTk1NTh9LCAicmVzdHJpY3Rpb25zIjogW3siY3JlZF9kZWZfaWQiOiAiM3oyYTJFS2JCMUhBZWpldzN4YXZmQTozOkNMOjE2MTUzNjEzNTY6MzA1M2Q4YzYtMDg4MS00ZTU0LWE4NzQtYmJjYWQxMjRiMDUyIn1dfSwgImNvbGxlZ2UiOiB7Im5hbWUiOiAiY29sbGVnZSIsICJub25fcmV2b2tlZCI6IHsiZnJvbSI6IDAsICJ0byI6IDE2MTkwOTk1NTh9LCAicmVzdHJpY3Rpb25zIjogW3siY3JlZF9kZWZfaWQiOiAiM3oyYTJFS2JCMUhBZWpldzN4YXZmQTozOkNMOjE2MTUzNjEzNTY6MzA1M2Q4YzYtMDg4MS00ZTU0LWE4NzQtYmJjYWQxMjRiMDUyIn1dfSwgInVuaXZlcnNpdHkiOiB7Im5hbWUiOiAidW5pdmVyc2l0eSIsICJub25fcmV2b2tlZCI6IHsiZnJvbSI6IDAsICJ0byI6IDE2MTkwOTk1NTh9LCAicmVzdHJpY3Rpb25zIjogW3siY3JlZF9kZWZfaWQiOiAiM3oyYTJFS2JCMUhBZWpldzN4YXZmQTozOkNMOjE2MTUzNjEzNTY6MzA1M2Q4YzYtMDg4MS00ZTU0LWE4NzQtYmJjYWQxMjRiMDUyIn1dfSwgInN0dWRlbnRpZCI6IHsibmFtZSI6ICJzdHVkZW50aWQiLCAibm9uX3Jldm9rZWQiOiB7ImZyb20iOiAwLCAidG8iOiAxNjE5MDk5NTU4fSwgInJlc3RyaWN0aW9ucyI6IFt7ImNyZWRfZGVmX2lkIjogIjN6MmEyRUtiQjFIQWVqZXczeGF2ZkE6MzpDTDoxNjE1MzYxMzU2OjMwNTNkOGM2LTA4ODEtNGU1NC1hODc0LWJiY2FkMTI0YjA1MiJ9XX0sICJuYW1lIjogeyJuYW1lIjogIm5hbWUiLCAibm9uX3Jldm9rZWQiOiB7ImZyb20iOiAwLCAidG8iOiAxNjE5MDk5NTU4fSwgInJlc3RyaWN0aW9ucyI6IFt7ImNyZWRfZGVmX2lkIjogIjN6MmEyRUtiQjFIQWVqZXczeGF2ZkE6MzpDTDoxNjE1MzYxMzU2OjMwNTNkOGM2LTA4ODEtNGU1NC1hODc0LWJiY2FkMTI0YjA1MiJ9XX0sICJkZXBhcnRtZW50IjogeyJuYW1lIjogImRlcGFydG1lbnQiLCAibm9uX3Jldm9rZWQiOiB7ImZyb20iOiAwLCAidG8iOiAxNjE5MDk5NTU4fSwgInJlc3RyaWN0aW9ucyI6IFt7ImNyZWRfZGVmX2lkIjogIjN6MmEyRUtiQjFIQWVqZXczeGF2ZkE6MzpDTDoxNjE1MzYxMzU2OjMwNTNkOGM2LTA4ODEtNGU1NC1hODc0LWJiY2FkMTI0YjA1MiJ9XX19LCAicmVxdWVzdGVkX3ByZWRpY2F0ZXMiOiB7fSwgIm5vbmNlIjogIjUxNTI4NTg4MDY1MzI1NTU3Mzk5MzA4NCJ9"
            }
         }
      ]
   },
   "role":"verifier",
   "updated_at":"2021-04-22 13:52:38.610876Z",
   "auto_present":false,
   "connection_id":"8d8ff076-f96e-42a8-a227-b86e4c2f8ac2",
   "presentation_exchange_id":"491cdc38-01de-43fd-a0e5-feac18dd7769",
   "initiator":"self",
   "presentation_request":{
      "name":"고려대학생증검증v2",
      "version":"1.0",
      "requested_attributes":{
         "campuslocation":{
            "name":"campuslocation",
            "non_revoked":{
               "from":0.0,
               "to":1.619099558E9
            },
            "restrictions":[
               {
                  "cred_def_id":"3z2a2EKbB1HAejew3xavfA:3:CL:1615361356:3053d8c6-0881-4e54-a874-bbcad124b052"
               }
            ]
         },
         "college":{
            "name":"college",
            "non_revoked":{
               "from":0.0,
               "to":1.619099558E9
            },
            "restrictions":[
               {
                  "cred_def_id":"3z2a2EKbB1HAejew3xavfA:3:CL:1615361356:3053d8c6-0881-4e54-a874-bbcad124b052"
               }
            ]
         },
         "university":{
            "name":"university",
            "non_revoked":{
               "from":0.0,
               "to":1.619099558E9
            },
            "restrictions":[
               {
                  "cred_def_id":"3z2a2EKbB1HAejew3xavfA:3:CL:1615361356:3053d8c6-0881-4e54-a874-bbcad124b052"
               }
            ]
         },
         "studentid":{
            "name":"studentid",
            "non_revoked":{
               "from":0.0,
               "to":1.619099558E9
            },
            "restrictions":[
               {
                  "cred_def_id":"3z2a2EKbB1HAejew3xavfA:3:CL:1615361356:3053d8c6-0881-4e54-a874-bbcad124b052"
               }
            ]
         },
         "name":{
            "name":"name",
            "non_revoked":{
               "from":0.0,
               "to":1.619099558E9
            },
            "restrictions":[
               {
                  "cred_def_id":"3z2a2EKbB1HAejew3xavfA:3:CL:1615361356:3053d8c6-0881-4e54-a874-bbcad124b052"
               }
            ]
         },
         "department":{
            "name":"department",
            "non_revoked":{
               "from":0.0,
               "to":1.619099558E9
            },
            "restrictions":[
               {
                  "cred_def_id":"3z2a2EKbB1HAejew3xavfA:3:CL:1615361356:3053d8c6-0881-4e54-a874bbcad124b052"
               }
            ]
         }
      },
      "requested_predicates":{
         
      },
      "nonce":"515285880653255573993084"
   },
   "created_at":"2021-04-22 13:52:38.610876Z",
   "state":"request_sent"
}
```

<p></p>

* present_proof State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | present_proof state | **`request_sent`** | N/A |
<p></p>


* Next Step


* Ledger Transactions (Indy Node)

    
<br><br>

### STEP 2. Alice : Faber(검증자)의 present proof를 확인. 

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 Holder가 initial app 일 경우 SDK에서 자동으로 처리됨 </p>
</div>

<br><br>


### STEP 3. Alice --> Faber(검증자) : Faber에게 send presentation 보낸다. 

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP3는 Holder가 initial app 일 경우 SDK에서 자동으로 처리되고, 검증자는 Alice로 부터 Webhook Message를 전달 받고 자동 처림 됨 </p>
</div>

<br><br>

### STEP 4. Faber : VP 검증 결과를 확인 한다.

Webhhok Message의 `requested_proof` 및 `verified` 정보 확인
```json
 requested proof:
{
  "revealed_attrs": {
    "college": {
      "sub_proof_index": 0,
      "raw": "공과대학",
      "encoded": "16610701639769159354469803737729974768559429331015161930470692015712320866163"
    },
    "studentid": {
      "sub_proof_index": 0,
      "raw": "11111111",
      "encoded": "11111111"
    },
    "name": {
      "sub_proof_index": 0,
      "raw": "고증명",
      "encoded": "9471975446415845898244366274733369196591763592071417247540506130163033264982"
    },
    "campuslocation": {
      "sub_proof_index": 0,
      "raw": "서울",
      "encoded": "91558467477632645889600363997818936103495823051310911993186983916648377535079"
    },
    "university": {
      "sub_proof_index": 0,
      "raw": "고려대학교",
      "encoded": "82015121962619472303362503621468631267043118886484178232578676624082121630145"
    },
    "department": {
      "sub_proof_index": 0,
      "raw": "AI연구",
      "encoded": "103758450965506634956372864113637628594188561957360798051123090877687816373760"
    }
  },
  "self_attested_attrs": {},
  "unrevealed_attrs": {},
  "predicates": {}
}
```

`"verified": "true"`

verified 결과 True/False 확인

* 참고 
Webhook message에서 사용자 data를 확인 하기 위해서는 아래 json 경로를 parsing 한다
  <br>
  presantation.requested_proof.revealed_attrs.{{requested_attribute}}.raw
  
  ![verify_webhook](img/verify_topic.png)