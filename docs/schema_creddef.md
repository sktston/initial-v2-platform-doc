Schema & Credential Definition Example
================

    
### STEP 1. Faber : Create Schema

* Method and Resource

    `POST` `/schemas` Schema를 Ledger에 생성하고 Schema ID를 return 한다.  

* Parameter

 Name | Description 
 --- | --- 
 body | Schema String 
 
<p></p>

* Example

    * input <br>
    `schema_name` : `string`<br>
    `schema_version` : `string`<br>
    `attributes` : `list`<br>

    * body
```json
{
  "schema_name": "대학제증명",
  "schema_version": "1.0",
  "attributes": [
    "학년",
    "입학년월일",
    "생년월일",
    "성명",
    "학교이름",
    "과정명"
  ]
}
```

<p></p>
 
   * Response body
```json
{
    "schema_id": "9c74RUPtMwtiSXq8tVDqxp:2:대학제증명:1.0",
    "schema": {
        "ver": "1.0",
        "id": "9c74RUPtMwtiSXq8tVDqxp:2:대학제증명:1.0",
        "name": "대학제증명",
        "version": "1.0",
        "attrNames": [
            "성명",
            "학교이름",
            "과정명",
            "입학년월일",
            "생년월일",
            "학년"
        ],
        "seqNo": 5108
    }
}
```

<p></p>

* Next Step
<br> Credential Definition 설정 
<p></p>

* Ledger Transactions (Indy Node)
<br><br> Type: SCHEMA

```json
{
  "auditPath": [
    "CYT9WaaQRuaxtt5Vytz7dQx5H9AdgBaQZAtmQT1UXGrk",
    "3suwRS6WoDpQxR2RPVxmYjWZDsCgXLiFW1hsne7kdGKZ",
    "534ZdtmEDD4roUU4AHxyg6K4jjB4p2e6oaWvcE2goQpe",
    "GDk46VDrycJrnidyVNEw89HL7ywAR35AZr3LSfwaV9yv",
    "9gBoSsmAY5mSLd95PAa7KuiCiqKZoyLQmBr96taH39cU",
    "DCMMAtwy3bXm2AGTxCxSB88BJtuoxyPNDJ9xRJgzb6SS",
    "CcNYpVNwWXiuSZvN9uSUCVBXKiREp7zSD63s2PpCy9Db",
    "6frSji5FdLTZarZHcWSmVceV81KBZUHR8JDEgagqKt5h",
    "h6sCZx4A4jsnTmLN43rfP2xp6iD49DLwdP6RPXqgt72"
  ],
  "ledgerSize": 5108,
  "reqSignature": {
    "type": "ED25519",
    "values": [
      {
        "from": "9c74RUPtMwtiSXq8tVDqxp",
        "value": "5pg3GqpWgbJLhNDU8b1Y1g7hGXSdkCYyxzTjBn29kTJo2a7pfddgcAreyaCsDi47ptm1xBFfddVVsWWozzoX3je7"
      }
    ]
  },
  "rootHash": "3hzQFdEaAJ5YzXuZvy95QMKwg1hyV1YCiBwceX7YUHc1",
  "txn": {
    "data": {
      "data": {
        "attr_names": [
          "생년월일",
          "학년",
          "입학년월일",
          "성명",
          "학교이름",
          "과정명"
        ],
        "name": "대학제증명",
        "version": "1.0"
      }
    },
    "metadata": {
      "digest": "6c4347222b1fb71c6e39a455da3736d5c6d1da90def063d7cc8903fad2bdf60a",
      "from": "9c74RUPtMwtiSXq8tVDqxp",
      "payloadDigest": "92e54af94075cac70d1fae5fe2c869ed82c277d2d06cf9da93c504ed83730945",
      "reqId": 1600849682020794600
    },
    "protocolVersion": 2,
    "type": "101"
  },
  "txnMetadata": {
    "seqNo": 5108,
    "txnId": "9c74RUPtMwtiSXq8tVDqxp:2:대학제증명:1.0",
    "txnTime": 1600849682
  },
  "ver": "1"
}
```
    
<br><br>


### STEP 2. Faber : Create Credential Definition & Revocation Registry.

* Method and Resource

    `POST` `/credential-definitions` Credential Definition을 Ledger에 생성하고 credential definition ID를 return 한다. 
    동시에 `/revocation/create-registry` 싫행하여 REVOC_REG_DEF와 REVOC_REG_ENTRY를 등록하고 
    `/revocation/registry/{rev_reg_id}` 자동 실행하여 tail file URI 까지 등록한다. 
    이후 `/revocation/registry/{rev_reg_id}/publish` 자동 실행하여 완료.

* Parameter

 Name | Description 
 --- | ---
 body | Credential Definition String 
 schema_id | Credential 발급할 Schema ID. 타인이 생성한 Schema ID도 사용 가능.
 tag | Credential Definition을 구분하기 위한 tag.
 support_revocation | Revocation(폐기) 기능 사용 여부. true로 세팅해야 지원.
 revocation_registry_size | revocation을 위해서 생성할 registry의 크기. 해당 크기 단위로 ledger에 REVOC_REG_DEF가 새로 등록된다. 
<p></p>

* Example

    * input <br>
    `schema_id` : `Th7MpTaRZVRYnPiabds81Y:2:degree_schema:1.0`<br>
    `tag` : `faber_college`<br>
    `support_revocation` : `true`<br>
    `revocation_registry_size` : `100`<br>


    * body
```json
{
    "revocation_registry_size": 100,
    "schema_id": "9c74RUPtMwtiSXq8tVDqxp:2:대학제증명:1.0",
    "support_revocation": true,
    "tag": "SK대학교"
}
```

<p></p>
 
   * Response body
```json
{
    "credential_definition_id": "9c74RUPtMwtiSXq8tVDqxp:3:CL:5108:SK대학교"
}
```

<p></p>

* Next Step
    
<p></p>

* Ledger Transactions (Indy Node)
아래 4개의 Transaction 이 자동 실행 됨 
<br><br> Type: CRED_DEF
<br><br> Type: REVOC_REG_DEF
<br><br> Type: REVOC_REG_ENTRY
<br><br> Type: REVOC_REG_DEF



<br><br>

