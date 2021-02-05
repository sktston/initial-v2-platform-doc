Revocation
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 Credential Revocation 예제.
<br>

### STEP 1. Faber : Revocation 하려는 Credential 정보를 확인. 


<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> VC발행(Credential issue) 시 고객정보와 함께 Mapping 관리하는  `revoc_reg_id`, `revocation_id` 정보를 가져옴 </p>
</div>

<br><br>

### STEP 2. Faber : Alice에게 발행한 Credential을 폐기(revoke)한다. 

* Method and Resource

    `POST` `/issue-credential/revoke` credential의 revoke 요청.  

* Parameter

 Name | Description 
 --- | --- 
 `cred_rev_id` | Credential revocation identifier
 `rev_reg_id` | Revocation registry identifier
 `publish` | (True) publish revocation to ledger immediately, or (False) mark it pending (default value)
 
<p></p>


* Example

    * revoke <br>
    `cred_rev_id` : `1`<br>
    `rev_reg_id` : `Th7MpTaRZVRYnPiabds81Y:4:Th7MpTaRZVRYnPiabds81Y:3:CL:2487:tag.91.43.38:CL_ACCUM:de1de25c-1694-4ad2-bf2d-d971575f32fb` <br>
    `publish` : `ture`<br>


<p></p>
 
   * Response body
```json
{ }
```

<br><br>

### [Option] Alice에게 발행한 Credential을 폐기(revoke) 정보 전달 

<br> 폐기 완료 후 문자/Push Notification/Webhook등으로 Alice에게 폐기 완료 전달. 



<br> Alice의 initial app에는 아래와 같이 '사용불가', '폐기됨'이 표시. 

![platform arch](img/initial_revoked.png)


<br> Alice의 폐기된 Credential을 Verifier에게 다시 제출하면 `verified`: `false` 로 실패.
<p></p>

