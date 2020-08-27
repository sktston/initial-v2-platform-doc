Revocation
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 연결 및 VC발급/검증 예제를 통한 API 설명
<br>

### STEP 1. Faber : Alice에게 발행한 Credential을 폐기(revoke)한다. 

* Method and Resource

    `POST` `/issue-credential/revoke` credential의 revoke 요청.  

* Parameter

 Name | Description 
 --- | --- 
 cred_rev_id | Credential revocation identifier
 rev_reg_id | Revocation registry identifier
 publish | (True) publish revocation to ledger immediately, or (False) mark it pending (default value)
 
<p></p>

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> `cred_rev_id`는 credential issue 단계에서 발급 받은 Peer와 mapping 하여 별도로 관리해야 함.  </p>
</div>

* Example

    * Proof Request <br>
    `cred_rev_id` : `1`<br>
    `rev_reg_id` : `Th7MpTaRZVRYnPiabds81Y:4:Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_5:CL_ACCUM:c1fde0d5-dddb-495a-9dee-69573e30b656` <br>
    `publish` : `ture`<br>


<p></p>
 
   * Response body
```json
{ }
```

<p></p>

* Next Step
<br> Push Notification/Webhook등으로 Alice에게 전달. 
<br> Alice에게 다시 Present proof 하게 되면 `verified`: `false` return.
<p></p>

* Ledger Transactions (Indy Node)
<br><br> Type: REVOC_REG_ENTRY

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

