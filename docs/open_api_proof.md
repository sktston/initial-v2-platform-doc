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

### STEP 1. Faber --> Alice : Alice에게 Proof Present를 보낸다. 

* Method and Resource

    `POST` `/present-proof/send-verification-request` proof 제출 요청.  

* Parameter

 Name | Description 
 --- | --- 
 body | Schema String 
 connection_id | Alice와 connection 정보
 verification_template_id | 사용하고자 하는 검증양식ID(verifTplId) 
 
<p></p>

검증양식ID는 아래 initial Console에서 생성 가능함.

![webconsole 1](img/web_console_create_verification_templete_id.png)

검증양식ID는 아래 initial Console에서 확인 가능함.

![webconsole 1](img/web_console_verification_templete_id.png)

* Example


```json
{
  "connection_id": "{{connectionId}}",
  "proof_request": "{{verifTplId}}"
}
```

<p></p>
 
   * Response body
```json

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
