Message
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 연결 및 VC발급/검증 예제를 통한 API 설명
<br>
basicmessages State

Topic | State | Description
--- | --- | ---
basicmessages | received | message를 받은 상태 



### STEP 1. Faber <--> Alice : 서로 Message를 주고 받을 수 있다. 

* Method and Resource

    `POST` `/connections/{conn_id}/send-message` message 전송.   

* Parameter

 Name | Description 
 --- | --- 
 body | Schema String 
 connection_id | Alice와 connection 정
 
<p></p>

* Example

    * body
```json
{
  "content": "Your Credential has been revoked"
}
```

<p></p>
 
   * Response body
```json
{ }
```

<p></p>

* present_proof State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | present_proof state | **`received`** | N/A |
<p></p>


* Next Step
<br> Push Notification/Webhook등으로 Alice에게 전달. 
<p></p>

* Ledger Transactions (Indy Node)

    
<br><br>
