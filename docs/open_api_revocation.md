Revocation
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 Credential Revocation 예제.
<br>

### STEP 1. Faber : Revocation 하려는 Credential 정보를 확인. 


<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> VC발행(Credential issue) 시 고객정보와 함께 Mapping 관리하는  `revocation_id`, `revoc_reg_id`, `revocation_id` 정보를 가져옴 </p>
</div>

<br><br>

### STEP 2. Faber : Alice에게 발행한 Credential을 폐기(revoke)한다. 

* Method and Resource

    `POST` `/revocation/revoke` credential의 revoke 요청.  

* Body Parameter

 Name | Description 
 --- | --- 
 `cred_rev_id` | Credential revocation identifier
 `rev_reg_id` | Revocation registry identifier
 `publish` | (True) publish revocation to ledger immediately, or (False) mark it pending (default value)
 `cred_ex_id` | Credential Exchange ID
<p></p>


* Body Example

아래 두가지 모두 지원하기 때문에, 둘중에 하나만 사용하면 됨

`cred_rev_id`와 `rev_reg_id`로 폐기 하는 경우
```json
{
    "cred_rev_id": "12345",
    "publish": true,
    "rev_reg_id": "WgWxqztrNooG92RXvxSTWv:4:WgWxqztrNooG92RXvxSTWv:3:CL:20:tag:CL_ACCUM:0"
}
```
`cred_ex_id` 로 폐기 하는 경우 
```json
{
    "cred_ex_id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "publish": true,
}
```

cURL Request Example
```
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/revocation/revoke' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer 2ca4dd8a-xxxx-xxxx-xxxx-c5fb0286f2cc' \
--data-raw '{
    "cred_ex_id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "publish": true,
}'
```

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

