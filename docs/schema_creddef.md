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
  "schema_name": "degree_schema",
  "schema_version": "24.8.1",
  "attributes": [
    "name",
    "date",
    "degree",
    "age"
  ]
}
```

<p></p>
 
   * Response body
```json
{
  "schema_id": "Th7MpTaRZVRYnPiabds81Y:2:degree_schema:24.8.1",
  "schema": {
    "ver": "1.0",
    "id": "Th7MpTaRZVRYnPiabds81Y:2:degree_schema:24.8.1",
    "name": "degree_schema",
    "version": "24.8.1",
    "attrNames": [
      "date",
      "name",
      "degree",
      "age"
    ],
    "seqNo": 2143
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
    "4v6SmDv5LGEqpQLizK8c2vuAM7qZXdqPSGP3Y1nDEJ4o",
    "9LG5nxrXVsAiweKwgSuSsZXmBk2gsWRYswSqiJ2Ldobr",
    "APvykYw8itGpbUJNQdhxp6orhFzBtBSbTM5NG6d1Km2H",
    "H8KodGkMj1ZM3NvLSJzqWsTfxrx1CNFX26uAJSdRK4yU",
    "FB4rDkp2cZVVqTGw91z8VkQTj1eFX5Nh5w53oCt4WfQv",
    "6Lai2Ns9nXVpXqikoXkrorcneXYoXwbCHnWKoaRADGhH"
  ],
  "ledgerSize": 2143,
  "reqSignature": {
    "type": "ED25519",
    "values": [
      {
        "from": "Th7MpTaRZVRYnPiabds81Y",
        "value": "WqDXwhXwSYWatsjeN7cyrQEMsafYfoar2GBP6YNpaDNkkx1yuwaLurnUot6B9tEdXPs69fMre9Ja9yQdFwPsn2B"
      }
    ]
  },
  "rootHash": "CBqvBbNfNGy1r7vpst9m5yKVXcxPtm4DxnTskNKkfRJi",
  "txn": {
    "data": {
      "data": {
        "attr_names": [
          "date",
          "age",
          "degree",
          "name"
        ],
        "name": "degree_schema",
        "version": "24.8.1"
      }
    },
    "metadata": {
      "digest": "a548f28a3ed4ac0e370065dd5990ba287fafcf77fbdf5985b0ee4e92fc7d4030",
      "from": "Th7MpTaRZVRYnPiabds81Y",
      "payloadDigest": "dc67ec3cd071149f7d5b5049fb47f72a416a19d55ceb80bbcd515a1396a5ba2f",
      "reqId": 1598247980728272000
    },
    "protocolVersion": 2,
    "type": "101"
  },
  "txnMetadata": {
    "seqNo": 2143,
    "txnId": "Th7MpTaRZVRYnPiabds81Y:2:degree_schema:24.8.1",
    "txnTime": 1598247980
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
 
<p></p>

* Example

    * input <br>
    `schema_id` : `Th7MpTaRZVRYnPiabds81Y:2:degree_schema:1.0`<br>
    `tag` : `faber_college`<br>
    `support_revocation` : `false`<br>
    `revocation_registry_size` : `0`<br>


    * body
```json
{
  "schema_id": "Th7MpTaRZVRYnPiabds81Y:2:degree_schema:24.8.1",
  "tag": "faber_college",
  "support_revocation": true,
  "revocation_registry_size": 100
}
```

<p></p>
 
   * Response body
```json
{
  "credential_definition_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college"
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

