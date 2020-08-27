Proof Presentation
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 연결 및 VC발급/검증 예제를 통한 API 설명
<br>
present_proof State

Topic | State | Description
--- | --- | ---
present_proof | request_sent | (faber)proof presentation 요청한 상태
present_proof | request_received | (alice)proof presentation 요청을 받은 상태
present_proof | presentation_sent | (alice) proof를 presentation 한 상태
present_proof | presentation_received | (faber) Credential 요청을 받은 상태 
present_proof | verified | (faber) Proof verification 완료 한 상태  
\present_proof | presentation_acked | (alice) Proof verified 응답을 받은 상태 


### STEP 1. Faber --> Alice : Alice에게 Proof Present를 보낸. 

* Method and Resource

    `POST` `/present-proof/send-request` proof presentation 요청.  

* Parameter

 Name | Description 
 --- | --- 
 body | Schema String 
 connection_id | Alice와 connection 정
 proof_request | IndyProofRequest <br> 아래 example 참고 
 
<p></p>

* Example

    * Proof Request <br>
    `requested_attributes` : `list string` 특정 attribute 값 <br>
    `requested_predicates` : `list string` 영지식증명을 위한 값 <br>
    `self_attested` : `list string` 직접 입력할 수 있는 값 <br>

    * body
```json
{
  "connection_id": "2612699e-3b88-412c-a435-b9824b48983c",
  "proof_request": {
    "name": "proof_name",
    "version": "1.0",
    "requested_attributes": {
      "attr_name": {
        "name": "name",
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      },
      "attr_date": {
        "name": "date",
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      },
      "attr_degree": {
        "name": "degree",
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      }
    },
    "requested_predicates": {
      "pred_age": {
        "name": "age",
        "p_type": ">=",
        "p_value": 20,
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      }
    }
  }
}
```

<p></p>
 
   * Response body
```json
{
  "auto_present": false,
  "connection_id": "2612699e-3b88-412c-a435-b9824b48983c",
  "created_at": "2020-08-26 08:42:08.160843Z",
  "trace": false,
  "updated_at": "2020-08-26 08:42:08.160843Z",
  "presentation_exchange_id": "e0dfd985-885d-4247-8dc1-4bfca2beff0a",
  "thread_id": "aedf3784-21b5-4bcd-900b-d0e573b77bf8",
  "presentation_request": {
    "name": "proof_name",
    "version": "1.0",
    "requested_attributes": {
      "attr_name": {
        "name": "name",
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      },
      "attr_date": {
        "name": "date",
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      },
      "attr_degree": {
        "name": "degree",
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      }
    },
    "requested_predicates": {
      "pred_age": {
        "name": "age",
        "p_type": ">=",
        "p_value": 20,
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      }
    },
    "nonce": "345295919909492182712117"
  },
  "presentation_request_dict": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/request-presentation",
    "@id": "aedf3784-21b5-4bcd-900b-d0e573b77bf8",
    "request_presentations~attach": [
      {
        "@id": "libindy-request-presentation-0",
        "mime-type": "application/json",
        "data": {
          "base64": "eyJuYW1lIjogInByb29mX25hbWUiLCAidmVyc2lvbiI6ICIxLjAiLCAicmVxdWVzdGVkX2F0dHJpYnV0ZXMiOiB7ImF0dHJfbmFtZSI6IHsibmFtZSI6ICJuYW1lIiwgInJlc3RyaWN0aW9ucyI6IFt7ImNyZWRfZGVmX2lkIjogIlRoN01wVGFSWlZSWW5QaWFiZHM4MVk6MzpDTDoyMTQzOmZhYmVyX2NvbGxlZ2VfMyJ9XX0sICJhdHRyX2RhdGUiOiB7Im5hbWUiOiAiZGF0ZSIsICJyZXN0cmljdGlvbnMiOiBbeyJjcmVkX2RlZl9pZCI6ICJUaDdNcFRhUlpWUlluUGlhYmRzODFZOjM6Q0w6MjE0MzpmYWJlcl9jb2xsZWdlXzMifV19LCAiYXR0cl9kZWdyZWUiOiB7Im5hbWUiOiAiZGVncmVlIiwgInJlc3RyaWN0aW9ucyI6IFt7ImNyZWRfZGVmX2lkIjogIlRoN01wVGFSWlZSWW5QaWFiZHM4MVk6MzpDTDoyMTQzOmZhYmVyX2NvbGxlZ2VfMyJ9XX19LCAicmVxdWVzdGVkX3ByZWRpY2F0ZXMiOiB7InByZWRfYWdlIjogeyJuYW1lIjogImFnZSIsICJwX3R5cGUiOiAiPj0iLCAicF92YWx1ZSI6IDIwLCAicmVzdHJpY3Rpb25zIjogW3siY3JlZF9kZWZfaWQiOiAiVGg3TXBUYVJaVlJZblBpYWJkczgxWTozOkNMOjIxNDM6ZmFiZXJfY29sbGVnZV8zIn1dfX0sICJub25jZSI6ICIzNDUyOTU5MTk5MDk0OTIxODI3MTIxMTcifQ=="
        }
      }
    ]
  },
  "role": "verifier",
  "state": "request_sent",
  "initiator": "self"
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

* Method and Resource

    `GET` `/present-proof/records` presentation_exchange_id를 모를 경우. 모든 present proof records 가져와서 state가 `request_received` 항목을 확인.   
    `GET` `/present-proof/records/{pres_ex_id}` webhook 혹은 push를 통해 presentation_exchange_id를 알고 있을 경우.
    `GET` `/present-proof/records/{pres_ex_id}/credentials` presentation_exchange_id 해당하는 Credential ID(`referent`) 확인 

* Parameter

 Name | Description 
 --- | --- 
 presentation_exchange_id | Faber의 present proof request로 부터 받은 `presentation_exchange_id`
 
<p></p>

* Example

    * input <br>
    `presentation_exchange_id`: "34cc650c-7e2c-4beb-a6b6-8ac2e1eb2e70"

<p></p>
 
   * Response body
```json
{
  "initiator": "external",
  "trace": false,
  "connection_id": "5a2db0d7-0683-4dbd-a1ae-ec61d54f0b39",
  "state": "request_received",
  "presentation_exchange_id": "34cc650c-7e2c-4beb-a6b6-8ac2e1eb2e70",
  "thread_id": "aedf3784-21b5-4bcd-900b-d0e573b77bf8",
  "role": "prover",
  "created_at": "2020-08-26 08:42:08.221376Z",
  "updated_at": "2020-08-26 08:42:08.221376Z",
  "presentation_request": {
    "name": "proof_name",
    "version": "1.0",
    "requested_attributes": {
      "attr_name": {
        "name": "name",
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      },
      "attr_date": {
        "name": "date",
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      },
      "attr_degree": {
        "name": "degree",
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      }
    },
    "requested_predicates": {
      "pred_age": {
        "name": "age",
        "p_type": ">=",
        "p_value": 20,
        "restrictions": [
          {
            "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_3"
          }
        ]
      }
    },
    "nonce": "345295919909492182712117"
  }
}
```

<p></p>

* issue_credential State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | issue_credential state | **`request_sent`** | **`request_received`** |
<p></p>

* Next Step
<p></p>

* Ledger Transactions (Indy Node)

    
<br><br>


### STEP 3. Alice --> Faber : Faber에게 send presentation 보낸다. 

* Method and Resource

    `POST` `/present-proof/records/{pres_ex_id}/send-presentation` Sends a proof presentation.  

* Parameter

 Name | Description 
 --- | --- 
 body | `string` 
 pres_ex_id | `presentation_exchange_id`
 
<p></p>

* Example

    * input <br>
    `cred_id`: Wallet에 있는 해당 Credential ID <br>
    
    * body
```json
{ 
  "requested_attributes": {
    "attr_name": {
      "cred_id": "68cf71b1-5816-444c-91e3-e3b2edcd92bc",
      "revealed":true
    },
    "attr_date": {
      "cred_id":"68cf71b1-5816-444c-91e3-e3b2edcd92bc",
      "revealed":true
    },
    "attr_degree": {
      "cred_id":"68cf71b1-5816-444c-91e3-e3b2edcd92bc",
      "revealed":true
    }
  },
  "requested_predicates": {
    "pred_age": {
      "cred_id":"68cf71b1-5816-444c-91e3-e3b2edcd92bc"
    }
  },
  "self_attested_attributes": {
  }
}
```

<p></p>
 
   * Response body

<p></p>

* issue_credential State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | issue_credential state | **`verified`**  | **`presentation_acked`** |
<p></p>

* Next Step
<p></p>

* Ledger Transactions (Indy Node)

    
<br><br>