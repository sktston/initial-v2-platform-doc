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

<br><br>

**Faber**(검증자/Verifier)와 **Alice**(Holder/Prover)의 연결 및 VC발급/검증 예제를 통한 API 설명
<br>
present_proof State

Topic | State | Description
--- | --- | ---
present_proof | request_sent | (faber)proof presentation 요청한 상태
present_proof | request_received | (alice)proof presentation 요청을 받은 상태
present_proof | presentation_sent | (alice) proof를 presentation 한 상태
present_proof | presentation_received | (faber) Credential 요청을 받은 상태 
present_proof | verified | (faber) Proof verification 완료 한 상태  
present_proof | presentation_acked | (alice) Proof verified 응답을 받은 상태 

<br><br>

### STEP 1. Faber --> Alice : Alice에게 Proof Present를 보낸. 

* Method and Resource

    `POST` `/present-proof/send-request` proof 제출 요청.  

* Parameter

 Name | Description 
 --- | --- 
 body | Schema String 
 connection_id | Alice와 connection 정보
 proof_request | 아래 example 참고 
 
<p></p>

* Example

    * Proof Request <br>
    `requested_attributes` : `list string` 특정 attribute 값 <br>
    `requested_predicates` : `list string` 영지식증명을 위한 값 <br>
    `self_attested` : `list string` 직접 입력할 수 있는 값 <br>
    `non_revoked` : `unix timestamp` 해당 시점까지 revocation 되지 않는 credential만 접수.

    * body
```json
{
  "connection_id": "{{connectionId}}",
  "proof_request": {
    "name": "SK대학교 재학 및 성인검증",
    "version": "1.0",
    "requested_attributes": {
      "attr_학교이름": {
        "name": "학교이름",
        "restrictions": [
          {
            "cred_def_id": "{{credDefId}}"
          }
        ]
      },
      "attr_과정명": {
        "name": "과정명",
        "restrictions": [
          {
            "cred_def_id": "{{credDefId}}"
          }
        ]
      },
      "attr_성명": {
        "name": "성명",
        "restrictions": [
          {
            "cred_def_id": "{{credDefId}}"
          }
        ]
      }
    },
    "requested_predicates": {
      "pred_성인검증": {
        "name": "생년월일",
        "p_type": "\u003c\u003d",
        "p_value": 20010923,
        "restrictions": [
          {
            "cred_def_id": "{{credDefId}}"
          }
        ]
      }
    },
    "non_revoked": {
      "to": {{$timestamp}}
    }
  }
}
```

<p></p>
 
   * Response body
```json
{
    "presentation_request": {
        "name": "SK대학교 재학 및 성인검증",
        "version": "1.0",
        "requested_attributes": {
            "attr_학교이름": {
                "name": "학교이름",
                "restrictions": [
                    {
                        "cred_def_id": "9c74RUPtMwtiSXq8tVDqxp:3:CL:5108:SK대학교"
                    }
                ]
            },
            "attr_과정명": {
                "name": "과정명",
                "restrictions": [
                    {
                        "cred_def_id": "9c74RUPtMwtiSXq8tVDqxp:3:CL:5108:SK대학교"
                    }
                ]
            },
            "attr_성명": {
                "name": "성명",
                "restrictions": [
                    {
                        "cred_def_id": "9c74RUPtMwtiSXq8tVDqxp:3:CL:5108:SK대학교"
                    }
                ]
            }
        },
        "requested_predicates": {
            "pred_성인검증": {
                "name": "생년월일",
                "p_type": "<=",
                "p_value": 20010923,
                "restrictions": [
                    {
                        "cred_def_id": "9c74RUPtMwtiSXq8tVDqxp:3:CL:5108:SK대학교"
                    }
                ]
            }
        },
        "non_revoked": {
            "to": 1600852155
        },
        "nonce": "650715231851826352778989"
    },
    "trace": false,
    "auto_present": false,
    "presentation_exchange_id": "79515f86-3fcd-49af-baf9-42a197559a3c",
    "state": "request_sent",
    "initiator": "self",
    "connection_id": "15a3e02b-e1bb-4f10-a1e6-ac0968b71fa7",
    "presentation_request_dict": {
        "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/request-presentation",
        "@id": "f536152e-d808-41e9-88e4-4bd30bce1757",
        "request_presentations~attach": [
            {
                "@id": "libindy-request-presentation-0",
                "mime-type": "application/json",
                "data": {
                    "base64": "eyJuYW1lIjogIlNLXHViMzAwXHVkNTU5XHVhZDUwIFx1YzdhY1x1ZDU1OSBcdWJjMGYgXHVjMTMxXHVjNzc4XHVhYzgwXHVjOTlkIiwgInZlcnNpb24iOiAiMS4wIiwgInJlcXVlc3RlZF9hdHRyaWJ1dGVzIjogeyJhdHRyX1x1ZDU1OVx1YWQ1MFx1Yzc3NFx1Yjk4NCI6IHsibmFtZSI6ICJcdWQ1NTlcdWFkNTBcdWM3NzRcdWI5ODQiLCAicmVzdHJpY3Rpb25zIjogW3siY3JlZF9kZWZfaWQiOiAiOWM3NFJVUHRNd3RpU1hxOHRWRHF4cDozOkNMOjUxMDg6U0tcdWIzMDBcdWQ1NTlcdWFkNTAifV19LCAiYXR0cl9cdWFjZmNcdWM4MTVcdWJhODUiOiB7Im5hbWUiOiAiXHVhY2ZjXHVjODE1XHViYTg1IiwgInJlc3RyaWN0aW9ucyI6IFt7ImNyZWRfZGVmX2lkIjogIjljNzRSVVB0TXd0aVNYcTh0VkRxeHA6MzpDTDo1MTA4OlNLXHViMzAwXHVkNTU5XHVhZDUwIn1dfSwgImF0dHJfXHVjMTMxXHViYTg1IjogeyJuYW1lIjogIlx1YzEzMVx1YmE4NSIsICJyZXN0cmljdGlvbnMiOiBbeyJjcmVkX2RlZl9pZCI6ICI5Yzc0UlVQdE13dGlTWHE4dFZEcXhwOjM6Q0w6NTEwODpTS1x1YjMwMFx1ZDU1OVx1YWQ1MCJ9XX19LCAicmVxdWVzdGVkX3ByZWRpY2F0ZXMiOiB7InByZWRfXHVjMTMxXHVjNzc4XHVhYzgwXHVjOTlkIjogeyJuYW1lIjogIlx1YzBkZFx1YjE0NFx1YzZkNFx1Yzc3YyIsICJwX3R5cGUiOiAiPD0iLCAicF92YWx1ZSI6IDIwMDEwOTIzLCAicmVzdHJpY3Rpb25zIjogW3siY3JlZF9kZWZfaWQiOiAiOWM3NFJVUHRNd3RpU1hxOHRWRHF4cDozOkNMOjUxMDg6U0tcdWIzMDBcdWQ1NTlcdWFkNTAifV19fSwgIm5vbl9yZXZva2VkIjogeyJ0byI6IDE2MDA4NTIxNTV9LCAibm9uY2UiOiAiNjUwNzE1MjMxODUxODI2MzUyNzc4OTg5In0="
                }
            }
        ]
    },
    "updated_at": "2020-09-23 09:09:14.814997Z",
    "created_at": "2020-09-23 09:09:14.814997Z",
    "thread_id": "f536152e-d808-41e9-88e4-4bd30bce1757",
    "role": "verifier"
}
```

<p></p>

* present_proof State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | present_proof state | **`request_sent`** | N/A |
<p></p>


* Next Step
<br> Push Notification/Webhook등으로 Alice에게 전달. 
<br> Alice의 `/issue-credential/records/{cred_ex_id}/send-request` send request를 waiting.
<p></p>

* Ledger Transactions (Indy Node)

    
<br><br>

### STEP 2. Alice : Faber의 present proof를 확인. 

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 Holder가 initial app 일 경우 SDK에서 자동으로 처리됨 </p>
</div>

<br><br>


### STEP 3. Alice --> Faber : Faber에게 send presentation 보낸다. 

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP3는 Holder가 initial app 일 경우 SDK에서 자동으로 처리되고, 검증자는 Alice로 부터 Webhook Message를 전달 받고 자동 처림 됨 </p>
</div>

<br><br>

True/False 확인
