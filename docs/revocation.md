Revocation
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 Credential Revocation 예제.
<br>

### STEP 1. Faber : Revocation 하려는 Credential 정보를 확인. 

* Method and Resource

    `GET` `/issue-credential/records` 발행한 credential의 전체 정보를 가져옴.   
    `GET` `/issue-credential/records/{cred_ex_id}` 특정 cred_ex_id를 알고 있을 경우 해당 정보만 가져옴.
    
* Parameter

 Name | Description 
 --- | --- 
 cred_ex_id | Credential exchange identifier
 
<p></p>

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> 현재 Demo는 Cloud Agent의 Credential 정보를 모두 가질 수 있도록 --preserve-exchange-records 세팅이 되어 있어 Record 확인이 가능하지만, 상용서버에서 고객 정보를 보관하지 않기 위해 해당 기능을 제공하지 않음. 사용자(개발자) Client Controller에서 해당 정보와 고객정보를 Mapping 하여 DB로 별도 관리해야 한다.</p>
</div>

* Example

    * Get Issue Credential Record <br>
    `cred_ex_id` : `7503aae5-71ec-4d24-82a2-a2b7b2394c95`<br>


<p></p>
 
   * Response body<br>
    Response 항목 중 필요한 2개(`revoc_reg_id`, `revocation_id`)의 값을 Revocation시 사용한다. 
    
```json
"revoc_reg_id": "Th7MpTaRZVRYnPiabds81Y:4:Th7MpTaRZVRYnPiabds81Y:3:CL:2487:tag.91.43.38:CL_ACCUM:de1de25c-1694-4ad2-bf2d-d971575f32fb",
"revocation_id": "1",
```

<p></p>

* Next Step
<p></p>

* Ledger Transactions (Indy Node)

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

<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p> `cred_rev_id`는 STEP 1에서 `revocation_id`로 확인 가능하나, 개발자는 상용 구축 시 credential issue 단계에서 별도 기록하고 관리해야 함.  </p>
</div>

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

<p></p>

* Next Step
<br> Push Notification/Webhook등으로 Alice에게 Revocation 여부 전달 전달. 
<br> Alice가 직접 삭제하기 전까지는 Revocation 완료한 Alice의 Credential은 wallet에 그대로 존재함.
<br> 하지만 Alice의 폐기된 Credential을 Verifier에게 다시 present proof 하게 되면 `verified`: `false` 로 실패.
<p></p>

* Ledger Transactions (Indy Node)

    Revoke 작업은 Ledger에 기록되어 Secure 하게 관리됨. 

    Type: REVOC_REG_ENTRY

```json
{
  "auditPath": [
    "DCwYWnL54pndqBr3r56DKajZWRJJagUjVnpkTj8TiUn7",
    "8Y78S1t94RGWcM8UEE6oRyFKGSA3V5UWhxTmGv4ek6N8",
    "DEcDYuWwpCWNDKy8WKW6jnrUPdXCHBJp8YfAaE9Fso3o",
    "q5bYosWuPBXWAjgS1WxwoZgiiUSY6NpDhUUYFDzpm8Y",
    "132WxZ7w4wFozKGqMVzeWgfhSicFLGmvLPQetWeT7q5i",
    "9nFBAG9WHGdcPMFWzDpF8nDmdgm4nsb8ccYyxPEYtEiL",
    "6Lai2Ns9nXVpXqikoXkrorcneXYoXwbCHnWKoaRADGhH"
  ],
  "ledgerSize": 2423,
  "reqSignature": {
    "type": "ED25519",
    "values": [
      {
        "from": "Th7MpTaRZVRYnPiabds81Y",
        "value": "4Z5vc2kpN1VpFcfujGxqiPZjpkUsNuPYQBYqJryJwxGtBr1kvpVAQ5xsgLcqqjUQfYQUnZEmzeFC21MKdrEK44WR"
      }
    ]
  },
  "rootHash": "7yDELfzBHskQPXqJZvuSeDggMrAd7YB2XsPMKoqKW78S",
  "txn": {
    "data": {
      "revocDefType": "CL_ACCUM",
      "revocRegDefId": "Th7MpTaRZVRYnPiabds81Y:4:Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_5:CL_ACCUM:c1fde0d5-dddb-495a-9dee-69573e30b656",
      "value": {
        "accum": "21 11B04888645330FD4D7EDFE80E15ADE936765D79C70CE15662C088B01A81D23D5 21 117C52F5650B47FB250D4B2BED1589BAD5E913A9F63F94A92D1D420F34E241285 6 6B4C34D9F11B4E5E04D02198698120F62A25EF51567DD08C8517B270AEA8A753 4 0E8A4E290B57F81A03327D84A80C3577B53054F5626A762C6E7A688A4B6845F2 6 6ADE4B6A657BD0174721B2827F813C10E97367D5B789258A8AC2831A50D8E66B 4 191BFD4B3B71023BCCFBC614E4B8AF4E6D0F6C4C10DCB0A1F0F9C6BB7CEDA112",
        "prevAccum": "21 13FE88D988D8FDF2BEFA91F88A0D19D9C0C8C5C466020D094B27E6A3B68855352 21 128F18FB2EE2C5BD59E18C9CC515A2465C99615EF26219E51232F55EA436992F2 6 73740584D799499C82B475F148DC51A10012158D883DD2B6915AA9E573D4E569 4 25091DD4B6CE6ED1E00280BA06D3615D181A871E710A14CC87A94D2787CC4A87 6 6D9D30F93C4F979F11B7503F94053A1AD59F33A46F72C091FFEB0E1A1B3EBA14 4 0DF59D96796893F6EA14E1AEF8B6CFE0808BA85C0D443CFCCB1B068369698C09",
        "revoked": [
          1
        ]
      }
    },
    "metadata": {
      "digest": "f96e88e7e752b994a7bfadc8505d51f66e1f732855d00c4f910f0fd104327b0c",
      "from": "Th7MpTaRZVRYnPiabds81Y",
      "payloadDigest": "5cb2f606eafafde8f04cec3ce7d5aa5220f06b5235eacf1bcc5df44474d26fd7",
      "reqId": 1598459531423724500
    },
    "protocolVersion": 2,
    "type": "114"
  },
  "txnMetadata": {
    "seqNo": 2423,
    "txnId": "5:Th7MpTaRZVRYnPiabds81Y:4:Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_5:CL_ACCUM:c1fde0d5-dddb-495a-9dee-69573e30b656",
    "txnTime": 1598459531
  },
  "ver": "1"
}
```
<br><br>

