Message
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 SMS 문자와 메신저와 같은 Basic Message 송/수신 예제.
<br>
Basice Message는 Webhook을 통해서 전달 되기 때문에, Test를 위해서는 Webhook 서버 세팅이 필요.
<br> Webhook 서버를 포함하고 있는 상세 Demo 참고 <https://github.com/sktston/acapy-controller-java>
<br> Webhook으로 받은 Message는 Client에 따라 Push Notification등으로 사용자에게 전달해야 한다.

Basicmessages State

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
 connection_id | Alice의 connection 정보 
 
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

### STEP 2. Faber / Alice : Webhook Message 확인.

<https://github.com/sktston/acapy-controller-java>

위 Demo code의 Alice console terminal에 webhook message가 정상적으로 표시된다.
```
2020-08-28 15:48:47 [INFO ] [GlobalService.java]handleMessage(37) : handleMessage >>> topic:basicmessages, body:{"connection_id": "77d34f1a-f386-4f18-a8e4-349d87280350", "message_id": "ac0a9621-6e40-4d18-b832-5676db8b4ca9", "content": "Your Credential has been revoked", "state": "received"}
2020-08-28 15:48:47 [INFO ] [GlobalService.java]handleMessage(65) : - Case (topic:basicmessages, state:received) -> Print message
2020-08-28 15:48:47 [INFO ] [GlobalService.java]handleMessage(66) :   - message:Your Credential has been revoked
```