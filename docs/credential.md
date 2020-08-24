Cloud Agent REST APIs Manual
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 연결 및 VC발급/검증 예제를 통한 API 설명
<br>

## Manual Connection

### STEP 1. Faber --> Alice : create invitation & send invitation.


* Method and Resource

    `POST` `/connections​/create-invitation` 새로운 초대장 생성
<p></p>

* Parameter

     Name | Description 
     --- | --- 
     alias | Alice에게 전달할 초대장 별칭 (e.g A대학제증명발급처)
     auto_accept | Alice가 초대장 수락 시 자동 connection 설정
     multi_use | 초대장을 일회성/다회성 사용여부. <br>QR코드등 인쇄시 `true` 설정 필요
     public | Public DID를 기반으로 초대장 생성 (현재 미지원)

<p></p>

* Example 

    * input <br>
    `auto_accept` : `false`<br>
    `multi_use` : `false`<br>
    `public` : `false`<br>
<br>
    * Response body (생성된 초대장)
```json
{
  "connection_id": "44bf3739-0e9e-43aa-b702-ed5059e3b450",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "ca2cb9c2-31a2-44c9-abc3-1b9abe22ab35",
    "label": "faber.agent",
    "recipientKeys": [
      "8x3USjfSd1Ynkj3ydh9mAv1AoWWDdctXqCURH6faqHk1"
    ],
    "serviceEndpoint": "http://host.docker.internal:8020"
  },
  "invitation_url": "http://host.docker.internal:8020?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiY2EyY2I5YzItMzFhMi00NGM5LWFiYzMtMWI5YWJlMjJhYjM1IiwgImxhYmVsIjogImZhYmVyLmFnZW50IiwgInJlY2lwaWVudEtleXMiOiBbIjh4M1VTamZTZDFZbmtqM3lkaDltQXYxQW9XV0RkY3RYcUNVUkg2ZmFxSGsxIl0sICJzZXJ2aWNlRW5kcG9pbnQiOiAiaHR0cDovL2hvc3QuZG9ja2VyLmludGVybmFsOjgwMjAifQ==",
  "alias": "create_invitation"
}
```

   * Connection State check : `get` `/connections​/{conn_id}`
   
|  | Faber | Alice |
| --- | --- | --- |
| connection state | **`invitation`** | N/A |

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> Alice의 연결요청에 대한 자동 연결을 위해 `auto_accept` parameter를 `true`로 설정하면 STEP4 Accept Connection Request 생략 가능 </p>
</div>

<p></p>  

* Next Step
    
    **invitation**내용이나 **invitation_url** 혹은 url의 **QR code**를 Alice에게 전달하여 초대.


<br>
<br>

### STEP 2. Alice --> Faber : receive invitation & request connection.

* Method and Resource

    `POST` `/connections/receive-invitation` 초대장을 받음

* Parameter

 Name | Description 
 --- | --- 
 body | Invitation 내용
 alias | 별칭 (e.g A대학제증명발급처) 
 auto_accept | Faber 초대장 수락 시 connection이 자동 active.
 
 <p></p>

* Example

    * input <br>
    `auto_accept` : `false`<br>
    
    * body - STEP1의 Faber가 생성한 invitation json을 입력

    * Response body

```json
{
  "accept": "manual",
  "alias": "receive_invitation",
  "state": "invitation",
  "invitation_key": "8x3USjfSd1Ynkj3ydh9mAv1AoWWDdctXqCURH6faqHk1",
  "routing_state": "none",
  "invitation_mode": "once",
  "initiator": "external",
  "their_label": "faber.agent",
  "created_at": "2020-08-19 04:30:28.542990Z",
  "updated_at": "2020-08-19 04:30:28.542990Z",
  "connection_id": "971f932a-1204-409f-9a9a-ebf2da53d05a"
}
```

<p></p>
* Connection State check : `get` `/connections​/{conn_id}`

     |  | Faber | Alice |
     | --- | --- | --- |
     | connection state | **`invitation`** | **`invitation`** |
  
    <div class="admonition Note">
    <p class="admonition-title">Note</p>
    <p> `auto_accept` parameter를 `true`로 설정하면 이 단계에서 Alice의 state는 자동으로 `active` 변경됨 STEP3 Accept Invitation 생략 가능 </p>
    </div>    

<p></p>
* Next Step

    초대장 받기 완료 후 Accept Invitation 진행 
    
<br>
<br>

### STEP 3. Alice --> Faber : Accept invitation & request connection.

* Method and Resource

    `POST` `/connections/{conn_id}/accept-invitation` 초대를 수락

    <div class="admonition warning">
    <p class="admonition-title">Warning</p>
    <p> STEP2에서 `auto_accept` parameter를 `true`로 설정하였다면, 이 단계를 진행하면 안됨</p>
    </div>

* Parameter

 Name | Description 
 --- | --- 
 conn_id | Connection identifier
 my_endpoint | My URL endpoint
 my_label | Label for connection

<p></p>

* Example

    * input <br>
    `conn_id` : `ae354d32-64d8-464b-b00a-2b8041f141e1`<br>

    * Response (초대를 Accept를 하기 위해 생성한 정보)
```json
{
  "request_id": "c9195df7-1c2b-4f39-855d-baec4c5d926b",
  "accept": "manual",
  "alias": "receive_invitation",
  "state": "request",
  "invitation_key": "8x3USjfSd1Ynkj3ydh9mAv1AoWWDdctXqCURH6faqHk1",
  "routing_state": "none",
  "invitation_mode": "once",
  "initiator": "external",
  "my_did": "5TEwzfwHJeTukFEsYd2nys",
  "their_label": "faber.agent",
  "created_at": "2020-08-19 04:30:28.542990Z",
  "updated_at": "2020-08-19 04:31:19.274806Z",
  "connection_id": "971f932a-1204-409f-9a9a-ebf2da53d05a"
}
```
<p></p>
* Connection State check : `get` `/connections​/{conn_id}`

     |  | Faber | Alice |
     | --- | --- | --- |
     | connection state | **`request`** | **`request`** |
     
<p></p>  

* Next Step
    Faber의 초대를 수락하고, Connection의 Request 진행. Faber는 최종 Connection Request를 Accept 해야 함.


<br>
<br>

### STEP 4. Faber --> Alice : accept connection request.

* Method and Resource
    `POST' '/connections/{conn_id}/accept-request` Alice의 연결 요쳥 수락

    <div class="admonition warning">
    <p class="admonition-title">Warning</p>
    <p> STEP1에서 `auto_accept` parameter를 `true`로 설정하였다면, 이미 `active` state 이기 때문에 error 발생 </p>
    </div>
    
* Parameter

     Name | Description 
     --- | --- 
     conn_id | Connection identifier
     my_endpoint | My URL endpoint

<p></p>
* Example

    * input <br>
    `conn_id` : `e56d0930-8bf6-4ceb-a1e5-8749f65bf553`<br>
    
    * Response (초대를 Accept 하기 위해 생성한 정보)
```json
{
  "their_label": "alice.agent",
  "state": "response",
  "routing_state": "none",
  "accept": "manual",
  "their_did": "DYersndQVodwUaJQfvNNkx",
  "initiator": "self",
  "invitation_key": "Fzz8mQSYUzmvRyjQkVwXpZTJE1RDZR6SbkEsEKeCryLd",
  "updated_at": "2020-08-19 03:41:25.873966Z",
  "alias": "create_invitation",
  "connection_id": "e56d0930-8bf6-4ceb-a1e5-8749f65bf553",
  "my_did": "HdLX7RWEvcH2Es7ssnq4xv",
  "created_at": "2020-08-18 05:24:06.092740Z",
  "invitation_mode": "once"
}
```
* Connection State check : `get` `/connections​/{conn_id}`

     |  | Faber | Alice |
     | --- | --- | --- |
     | connection state | `response` --> **`active`** | **`active`** |

<p></p>
* Next Step

    Alice와 Faber 연결이 완료. ping을 통한 연결 확인.<br>
    Schema 생성 및 VC 발행 진행.
<br>
<br>
<br>
<br>

## Auto Connection

### STEP 1. Faber --> Alice : create invitation & send invitation.


* Method and Resource

    `POST` `/connections​/create-invitation` 새로운 초대장 생성
<p></p>

* Parameter

     Name | Description 
     --- | --- 
     alias | Alice에게 전달할 초대장 별칭 (e.g A대학제증명발급처)
     auto_accept | Alice가 초대장 수락 시 자동 connection 설정
     multi_use | 초대장을 일회성/다회성 사용여부. QR코드등 인쇄시 `true` 설정 필요
     public | Public DID를 기반으로 초대장 생성 (현재 미지원)

<p></p>

* Example 

    * input <br>
    `auto_accept` : `true`<br>
    `multi_use` : `false`<br>
    `public` : `false`<br>
<br>
    * Response body (생성된 초대장)
```json
{
  "connection_id": "eb180f76-0081-41db-a12c-454fe36b3295",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "66e7239f-93fe-47f2-939d-ddbc31f929d6",
    "label": "faber.agent",
    "serviceEndpoint": "http://host.docker.internal:8020",
    "recipientKeys": [
      "9RGU3g5Mm4Pziza3E3im12pPjFxPhaFDkZWQQvqjw9Sm"
    ]
  },
  "invitation_url": "http://host.docker.internal:8020?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiNjZlNzIzOWYtOTNmZS00N2YyLTkzOWQtZGRiYzMxZjkyOWQ2IiwgImxhYmVsIjogImZhYmVyLmFnZW50IiwgInNlcnZpY2VFbmRwb2ludCI6ICJodHRwOi8vaG9zdC5kb2NrZXIuaW50ZXJuYWw6ODAyMCIsICJyZWNpcGllbnRLZXlzIjogWyI5UkdVM2c1TW00UHppemEzRTNpbTEycFBqRnhQaGFGRGtaV1FRdnFqdzlTbSJdfQ==",
  "alias": "create_invitation"
}
```
    
   * Connection State check : `get` `/connections​/{conn_id}`
   
    |  | Faber | Alice |
    | --- | --- | --- |
    | connection state | **`invitation`** | N/A |
  

<p></p>
* Next Step
    
    **invitation**내용이나 **invitation_url** 혹은 url의 **QR code**를 Alice에게 전달하여 초대.


<br>
<br>

### STEP 2. Alice --> Faber : receive invitation & request connection.

* Method and Resource

    `POST` `/connections/receive-invitation` 초대장을 받음

* Parameter

 Name | Description 
 --- | --- 
 body | Invitation 내용
 alias | 별칭 (e.g A대학제증명발급처) 
 auto_accept | Faber 초대장 수락 시 connection이 자동 active.
 

* Example

    * input <br>
    `auto_accept` : `true`<br>
    
    * body - STEP1의 Faber가 생성한 invitation json을 입력

    * Response body

```json
{
  "updated_at": "2020-08-19 04:54:05.847426Z",
  "my_did": "4jaLRZTfHA9MVf2a4QaJGE",
  "state": "request",
  "their_label": "faber.agent",
  "invitation_mode": "once",
  "routing_state": "none",
  "initiator": "external",
  "alias": "receive_invitation",
  "connection_id": "2f139bd4-835a-4a3f-aae7-ddf9936373a6",
  "created_at": "2020-08-19 04:54:05.833098Z",
  "invitation_key": "9RGU3g5Mm4Pziza3E3im12pPjFxPhaFDkZWQQvqjw9Sm",
  "accept": "auto",
  "request_id": "33f861dc-29fa-4025-ae95-b951cd35748b"
}
```


* Connection State check : `get` `/connections​/{conn_id}`

     |  | Faber | Alice |
     | --- | --- | --- |
     | connection state | **`active`** | `request` --> **`active`** |
  
<p></p>
* Next Step

    연결 완료. Schema 생성 및 VC 발행 진행.


<br>
<br>
    
## Schema & CredDef	

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


## Credential Issue

### STEP 1. Faber --> Alice : Alice에게 Credential offer를 요청한다. 

* Method and Resource

    `POST` `/issue-credential​/send-offer` credential offer.  

* Parameter

 Name | Description 
 --- | --- 
 body | Schema String 
 
<p></p>

* Example

    * input <br>
    `connection_id` : `string`<br>
    `attribute` : `list string`<br>
    `cred_def_id` : `string`<br>

    * body
```json
{
  "connection_id": "ce737f4c-3065-4da5-a490-98568fc3a773",
  "trace": false,
  "credential_preview": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
    "attributes": [
      {"name": "name", "value": "alice"}, 
      {"name": "date", "value": "08-2018"},
      {"name": "degree", "value": "maths"},
      {"name": "age", "value": "25"}
    ]
  },
  "auto_issue": true,
  "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college",
  "auto_remove": true,
  "comment": "string"
}
```

<p></p>
 
   * Response body
```json
{
  "credential_definition_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college",
  "trace": false,
  "schema_id": "Th7MpTaRZVRYnPiabds81Y:2:degree_schema:24.8.1",
  "thread_id": "5a5eb7f2-efec-4319-9265-1a304f62fc1c",
  "credential_proposal_dict": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
    "@id": "44b6a5b8-a590-4bcc-9440-b5023c9556f7",
    "credential_proposal": {
      "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes": [
        {
          "name": "name",
          "value": "alice"
        },
        {
          "name": "date",
          "value": "08-2018"
        },
        {
          "name": "degree",
          "value": "maths"
        },
        {
          "name": "age",
          "value": "25"
        }
      ]
    },
    "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college",
    "comment": "string"
  },
  "updated_at": "2020-08-24 07:08:56.034090Z",
  "state": "offer_sent",
  "connection_id": "ce737f4c-3065-4da5-a490-98568fc3a773",
  "auto_issue": true,
  "auto_remove": true,
  "initiator": "self",
  "created_at": "2020-08-24 07:08:56.034090Z",
  "auto_offer": false,
  "credential_offer": {
    "schema_id": "Th7MpTaRZVRYnPiabds81Y:2:degree_schema:24.8.1",
    "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college",
    "key_correctness_proof": {
      "c": "1211877137352975463375244041700762417669628883094972980895062156226939594402",
      "xz_cap": "8115961514951812893019986223806866549524800875222674020819102495317582554707787440890847512164570050499140328724066359393524473039011310822575988360212058249011303025364927630306619639110090525647691013216404933458972973757324041910370972393348643439014381883537062076109476281533684079455056302022851203835166723040738610436456284853031488359745157917893874501698898620950357501750167791549863227035443558972787057489897135694495443263261231745249189026514606484118086509953367288902932736010808047145837780042232749595367491270654192839550655436662552365992872857372110965151191864165689047380962401251959398562959598596362667071587105859657891413523696717336765379185330665654916650216821",
      "xr_cap": [
        [
          "date",
          "33707859903081596188568324249725258543758458986207800463376651085096562554922818421232375486850302551555596664182852645285121968565909363837536111960227440017926587517104148607126442832105626813228504702527033626905478442067885586189706946533205293763702112054063046969349575823588577663075128920478993457405091963398088428490107167750576986934713442091205260529153597081471329551174363260912629846034195234008690789516396618444419611169470644668334218238518205064185487105177831198589625036929423822532554912318768192851904186578918402071091714900014042486279852690495876883908105724334215542859581004573900466741396523850791747351248163990721139768028556530617653324410890820711424830252340"
        ],
        [
          "master_secret",
          "21316669012664687319369248784598613730811052868099133401118752407582680150321161172633069205928479940145205890110674749991811847910687389520806101718665836012747969692113139643354501142700040489753326389921743858095616658979003572830343489665883575039480966878967569668891071229892991265881773195459999847621870782573588569960415929964902537869700232595160110730740225031770266573841343083758300276561438322842955503195778194030527744135691421625837828697234509007680111495549692336776653968890253564735921992281463127269130234247691344838644718116563844401840096408607320963729816150141205369777132798240918560151156620692042233842747168319561367835465734043635278535596645170273266651067354"
        ],
        [
          "degree",
          "32808350207909552000663769297428169906618718163752958299644864084198452324903653796103113267876821046310308274799929948822418741373992201343897949446061310789850587971301658151163692409244117594395244580844562766845849162535907880576337873622850357285846651115223417058267736144392508911367400339474812142768155878946867599302163956510663073145158154154471677185496324498384600910952528680219720874849255800023338669375164764501697507912320094017869912244417481642697674701469444157089813313232458745050745264124936932369322262874558153766798785790164688390965478024061533952490595314500169665208987515361044381127816263258281539988702671420428334413060199609960859059343694577591630948587557"
        ],
        [
          "name",
          "20394974789044082970541152955606572103461567728315829879381808717600421177744248635001385287010990031623026194822353092019613141430116596863878694023954099289187492902390535929653703097419404579137774631859241008672464329349626872154122310133280890862920531608780818629839689154706863780304112909252628756488092736234521949044509862165338438153103693977957134371048419499875347525353826666174889746746555907532164909278718231299368326261765846445616641758988577740151666194588257659450372094731058922976059931011909679868933083490345969958081572447120711547715449228751861811720192505120436604210676732030927784341861241008138909147570234499026644058423983959063173524450034277367859431304074"
        ],
        [
          "age",
          "12978383263696483579058528130178872284099728061983709986093745341385478818028463441116127444391324282828038361793245505751234964066473209353926429859942340059659452746878796562145765306236576059897863703112351705209095966460901176231047488026736546869621099393390353067470258360965780961086876508404727152292034936453127347056311090706223633486982380930089009642249818235821148708681287215415353758665701921158903681394116087492563096728978179355692830437496770368817085503849637689427788431657412454146824974437263001626025074741468648880355602470058488204464186826891543902078600881565122082389091528945986689054728016751936614042206171653669126382022427118458116005224449432328568366024314"
        ]
      ]
    },
    "nonce": "91199357596350999096630"
  },
  "credential_offer_dict": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/offer-credential",
    "@id": "5a5eb7f2-efec-4319-9265-1a304f62fc1c",
    "~thread": {},
    "offers~attach": [
      {
        "@id": "libindy-cred-offer-0",
        "mime-type": "application/json",
        "data": {
          "base64": "eyJzY2hlbWFfaWQiOiAiVGg3TXBUYVJaVlJZblBpYWJkczgxWToyOmRlZ3JlZV9zY2hlbWE6MjQuOC4xIiwgImNyZWRfZGVmX2lkIjogIlRoN01wVGFSWlZSWW5QaWFiZHM4MVk6MzpDTDoyMTQzOmZhYmVyX2NvbGxlZ2UiLCAia2V5X2NvcnJlY3RuZXNzX3Byb29mIjogeyJjIjogIjEyMTE4NzcxMzczNTI5NzU0NjMzNzUyNDQwNDE3MDA3NjI0MTc2Njk2Mjg4ODMwOTQ5NzI5ODA4OTUwNjIxNTYyMjY5Mzk1OTQ0MDIiLCAieHpfY2FwIjogIjgxMTU5NjE1MTQ5NTE4MTI4OTMwMTk5ODYyMjM4MDY4NjY1NDk1MjQ4MDA4NzUyMjI2NzQwMjA4MTkxMDI0OTUzMTc1ODI1NTQ3MDc3ODc0NDA4OTA4NDc1MTIxNjQ1NzAwNTA0OTkxNDAzMjg3MjQwNjYzNTkzOTM1MjQ0NzMwMzkwMTEzMTA4MjI1NzU5ODgzNjAyMTIwNTgyNDkwMTEzMDMwMjUzNjQ5Mjc2MzAzMDY2MTk2MzkxMTAwOTA1MjU2NDc2OTEwMTMyMTY0MDQ5MzM0NTg5NzI5NzM3NTczMjQwNDE5MTAzNzA5NzIzOTMzNDg2NDM0MzkwMTQzODE4ODM1MzcwNjIwNzYxMDk0NzYyODE1MzM2ODQwNzk0NTUwNTYzMDIwMjI4NTEyMDM4MzUxNjY3MjMwNDA3Mzg2MTA0MzY0NTYyODQ4NTMwMzE0ODgzNTk3NDUxNTc5MTc4OTM4NzQ1MDE2OTg4OTg2MjA5NTAzNTc1MDE3NTAxNjc3OTE1NDk4NjMyMjcwMzU0NDM1NTg5NzI3ODcwNTc0ODk4OTcxMzU2OTQ0OTU0NDMyNjMyNjEyMzE3NDUyNDkxODkwMjY1MTQ2MDY0ODQxMTgwODY1MDk5NTMzNjcyODg5MDI5MzI3MzYwMTA4MDgwNDcxNDU4Mzc3ODAwNDIyMzI3NDk1OTUzNjc0OTEyNzA2NTQxOTI4Mzk1NTA2NTU0MzY2NjI1NTIzNjU5OTI4NzI4NTczNzIxMTA5NjUxNTExOTE4NjQxNjU2ODkwNDczODA5NjI0MDEyNTE5NTkzOTg1NjI5NTk1OTg1OTYzNjI2NjcwNzE1ODcxMDU4NTk2NTc4OTE0MTM1MjM2OTY3MTczMzY3NjUzNzkxODUzMzA2NjU2NTQ5MTY2NTAyMTY4MjEiLCAieHJfY2FwIjogW1siZGF0ZSIsICIzMzcwNzg1OTkwMzA4MTU5NjE4ODU2ODMyNDI0OTcyNTI1ODU0Mzc1ODQ1ODk4NjIwNzgwMDQ2MzM3NjY1MTA4NTA5NjU2MjU1NDkyMjgxODQyMTIzMjM3NTQ4Njg1MDMwMjU1MTU1NTU5NjY2NDE4Mjg1MjY0NTI4NTEyMTk2ODU2NTkwOTM2MzgzNzUzNjExMTk2MDIyNzQ0MDAxNzkyNjU4NzUxNzEwNDE0ODYwNzEyNjQ0MjgzMjEwNTYyNjgxMzIyODUwNDcwMjUyNzAzMzYyNjkwNTQ3ODQ0MjA2Nzg4NTU4NjE4OTcwNjk0NjUzMzIwNTI5Mzc2MzcwMjExMjA1NDA2MzA0Njk2OTM0OTU3NTgyMzU4ODU3NzY2MzA3NTEyODkyMDQ3ODk5MzQ1NzQwNTA5MTk2MzM5ODA4ODQyODQ5MDEwNzE2Nzc1MDU3Njk4NjkzNDcxMzQ0MjA5MTIwNTI2MDUyOTE1MzU5NzA4MTQ3MTMyOTU1MTE3NDM2MzI2MDkxMjYyOTg0NjAzNDE5NTIzNDAwODY5MDc4OTUxNjM5NjYxODQ0NDQxOTYxMTE2OTQ3MDY0NDY2ODMzNDIxODIzODUxODIwNTA2NDE4NTQ4NzEwNTE3NzgzMTE5ODU4OTYyNTAzNjkyOTQyMzgyMjUzMjU1NDkxMjMxODc2ODE5Mjg1MTkwNDE4NjU3ODkxODQwMjA3MTA5MTcxNDkwMDAxNDA0MjQ4NjI3OTg1MjY5MDQ5NTg3Njg4MzkwODEwNTcyNDMzNDIxNTU0Mjg1OTU4MTAwNDU3MzkwMDQ2Njc0MTM5NjUyMzg1MDc5MTc0NzM1MTI0ODE2Mzk5MDcyMTEzOTc2ODAyODU1NjUzMDYxNzY1MzMyNDQxMDg5MDgyMDcxMTQyNDgzMDI1MjM0MCJdLCBbIm1hc3Rlcl9zZWNyZXQiLCAiMjEzMTY2NjkwMTI2NjQ2ODczMTkzNjkyNDg3ODQ1OTg2MTM3MzA4MTEwNTI4NjgwOTkxMzM0MDExMTg3NTI0MDc1ODI2ODAxNTAzMjExNjExNzI2MzMwNjkyMDU5Mjg0Nzk5NDAxNDUyMDU4OTAxMTA2NzQ3NDk5OTE4MTE4NDc5MTA2ODczODk1MjA4MDYxMDE3MTg2NjU4MzYwMTI3NDc5Njk2OTIxMTMxMzk2NDMzNTQ1MDExNDI3MDAwNDA0ODk3NTMzMjYzODk5MjE3NDM4NTgwOTU2MTY2NTg5NzkwMDM1NzI4MzAzNDM0ODk2NjU4ODM1NzUwMzk0ODA5NjY4Nzg5Njc1Njk2Njg4OTEwNzEyMjk4OTI5OTEyNjU4ODE3NzMxOTU0NTk5OTk4NDc2MjE4NzA3ODI1NzM1ODg1Njk5NjA0MTU5Mjk5NjQ5MDI1Mzc4Njk3MDAyMzI1OTUxNjAxMTA3MzA3NDAyMjUwMzE3NzAyNjY1NzM4NDEzNDMwODM3NTgzMDAyNzY1NjE0MzgzMjI4NDI5NTU1MDMxOTU3NzgxOTQwMzA1Mjc3NDQxMzU2OTE0MjE2MjU4Mzc4Mjg2OTcyMzQ1MDkwMDc2ODAxMTE0OTU1NDk2OTIzMzY3NzY2NTM5Njg4OTAyNTM1NjQ3MzU5MjE5OTIyODE0NjMxMjcyNjkxMzAyMzQyNDc2OTEzNDQ4Mzg2NDQ3MTgxMTY1NjM4NDQ0MDE4NDAwOTY0MDg2MDczMjA5NjM3Mjk4MTYxNTAxNDEyMDUzNjk3NzcxMzI3OTgyNDA5MTg1NjAxNTExNTY2MjA2OTIwNDIyMzM4NDI3NDcxNjgzMTk1NjEzNjc4MzU0NjU3MzQwNDM2MzUyNzg1MzU1OTY2NDUxNzAyNzMyNjY2NTEwNjczNTQiXSwgWyJkZWdyZWUiLCAiMzI4MDgzNTAyMDc5MDk1NTIwMDA2NjM3NjkyOTc0MjgxNjk5MDY2MTg3MTgxNjM3NTI5NTgyOTk2NDQ4NjQwODQxOTg0NTIzMjQ5MDM2NTM3OTYxMDMxMTMyNjc4NzY4MjEwNDYzMTAzMDgyNzQ3OTk5Mjk5NDg4MjI0MTg3NDEzNzM5OTIyMDEzNDM4OTc5NDk0NDYwNjEzMTA3ODk4NTA1ODc5NzEzMDE2NTgxNTExNjM2OTI0MDkyNDQxMTc1OTQzOTUyNDQ1ODA4NDQ1NjI3NjY4NDU4NDkxNjI1MzU5MDc4ODA1NzYzMzc4NzM2MjI4NTAzNTcyODU4NDY2NTExMTUyMjM0MTcwNTgyNjc3MzYxNDQzOTI1MDg5MTEzNjc0MDAzMzk0NzQ4MTIxNDI3NjgxNTU4Nzg5NDY4Njc1OTkzMDIxNjM5NTY1MTA2NjMwNzMxNDUxNTgxNTQxNTQ0NzE2NzcxODU0OTYzMjQ0OTgzODQ2MDA5MTA5NTI1Mjg2ODAyMTk3MjA4NzQ4NDkyNTU4MDAwMjMzMzg2NjkzNzUxNjQ3NjQ1MDE2OTc1MDc5MTIzMjAwOTQwMTc4Njk5MTIyNDQ0MTc0ODE2NDI2OTc2NzQ3MDE0Njk0NDQxNTcwODk4MTMzMTMyMzI0NTg3NDUwNTA3NDUyNjQxMjQ5MzY5MzIzNjkzMjIyNjI4NzQ1NTgxNTM3NjY3OTg3ODU3OTAxNjQ2ODgzOTA5NjU0NzgwMjQwNjE1MzM5NTI0OTA1OTUzMTQ1MDAxNjk2NjUyMDg5ODc1MTUzNjEwNDQzODExMjc4MTYyNjMyNTgyODE1Mzk5ODg3MDI2NzE0MjA0MjgzMzQ0MTMwNjAxOTk2MDk5NjA4NTkwNTkzNDM2OTQ1Nzc1OTE2MzA5NDg1ODc1NTciXSwgWyJuYW1lIiwgIjIwMzk0OTc0Nzg5MDQ0MDgyOTcwNTQxMTUyOTU1NjA2NTcyMTAzNDYxNTY3NzI4MzE1ODI5ODc5MzgxODA4NzE3NjAwNDIxMTc3NzQ0MjQ4NjM1MDAxMzg1Mjg3MDEwOTkwMDMxNjIzMDI2MTk0ODIyMzUzMDkyMDE5NjEzMTQxNDMwMTE2NTk2ODYzODc4Njk0MDIzOTU0MDk5Mjg5MTg3NDkyOTAyMzkwNTM1OTI5NjUzNzAzMDk3NDE5NDA0NTc5MTM3Nzc0NjMxODU5MjQxMDA4NjcyNDY0MzI5MzQ5NjI2ODcyMTU0MTIyMzEwMTMzMjgwODkwODYyOTIwNTMxNjA4NzgwODE4NjI5ODM5Njg5MTU0NzA2ODYzNzgwMzA0MTEyOTA5MjUyNjI4NzU2NDg4MDkyNzM2MjM0NTIxOTQ5MDQ0NTA5ODYyMTY1MzM4NDM4MTUzMTAzNjkzOTc3OTU3MTM0MzcxMDQ4NDE5NDk5ODc1MzQ3NTI1MzUzODI2NjY2MTc0ODg5NzQ2NzQ2NTU1OTA3NTMyMTY0OTA5Mjc4NzE4MjMxMjk5MzY4MzI2MjYxNzY1ODQ2NDQ1NjE2NjQxNzU4OTg4NTc3NzQwMTUxNjY2MTk0NTg4MjU3NjU5NDUwMzcyMDk0NzMxMDU4OTIyOTc2MDU5OTMxMDExOTA5Njc5ODY4OTMzMDgzNDkwMzQ1OTY5OTU4MDgxNTcyNDQ3MTIwNzExNTQ3NzE1NDQ5MjI4NzUxODYxODExNzIwMTkyNTA1MTIwNDM2NjA0MjEwNjc2NzMyMDMwOTI3Nzg0MzQxODYxMjQxMDA4MTM4OTA5MTQ3NTcwMjM0NDk5MDI2NjQ0MDU4NDIzOTgzOTU5MDYzMTczNTI0NDUwMDM0Mjc3MzY3ODU5NDMxMzA0MDc0Il0sIFsiYWdlIiwgIjEyOTc4MzgzMjYzNjk2NDgzNTc5MDU4NTI4MTMwMTc4ODcyMjg0MDk5NzI4MDYxOTgzNzA5OTg2MDkzNzQ1MzQxMzg1NDc4ODE4MDI4NDYzNDQxMTE2MTI3NDQ0MzkxMzI0MjgyODI4MDM4MzYxNzkzMjQ1NTA1NzUxMjM0OTY0MDY2NDczMjA5MzUzOTI2NDI5ODU5OTQyMzQwMDU5NjU5NDUyNzQ2ODc4Nzk2NTYyMTQ1NzY1MzA2MjM2NTc2MDU5ODk3ODYzNzAzMTEyMzUxNzA1MjA5MDk1OTY2NDYwOTAxMTc2MjMxMDQ3NDg4MDI2NzM2NTQ2ODY5NjIxMDk5MzkzMzkwMzUzMDY3NDcwMjU4MzYwOTY1NzgwOTYxMDg2ODc2NTA4NDA0NzI3MTUyMjkyMDM0OTM2NDUzMTI3MzQ3MDU2MzExMDkwNzA2MjIzNjMzNDg2OTgyMzgwOTMwMDg5MDA5NjQyMjQ5ODE4MjM1ODIxMTQ4NzA4NjgxMjg3MjE1NDE1MzUzNzU4NjY1NzAxOTIxMTU4OTAzNjgxMzk0MTE2MDg3NDkyNTYzMDk2NzI4OTc4MTc5MzU1NjkyODMwNDM3NDk2NzcwMzY4ODE3MDg1NTAzODQ5NjM3Njg5NDI3Nzg4NDMxNjU3NDEyNDU0MTQ2ODI0OTc0NDM3MjYzMDAxNjI2MDI1MDc0NzQxNDY4NjQ4ODgwMzU1NjAyNDcwMDU4NDg4MjA0NDY0MTg2ODI2ODkxNTQzOTAyMDc4NjAwODgxNTY1MTIyMDgyMzg5MDkxNTI4OTQ1OTg2Njg5MDU0NzI4MDE2NzUxOTM2NjE0MDQyMjA2MTcxNjUzNjY5MTI2MzgyMDIyNDI3MTE4NDU4MTE2MDA1MjI0NDQ5NDMyMzI4NTY4MzY2MDI0MzE0Il1dfSwgIm5vbmNlIjogIjkxMTk5MzU3NTk2MzUwOTk5MDk2NjMwIn0="
        }
      }
    ],
    "credential_preview": {
      "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes": [
        {
          "name": "name",
          "value": "alice"
        },
        {
          "name": "date",
          "value": "08-2018"
        },
        {
          "name": "degree",
          "value": "maths"
        },
        {
          "name": "age",
          "value": "25"
        }
      ]
    },
    "comment": "string"
  },
  "credential_exchange_id": "412de146-5833-4fcc-be92-dbf5d50ba8ab"
}
```

<p></p>

* Next Step
<br> Push Notification등으로 Alice에게 전달. Alice의 `/issue-credential/records/{cred_ex_id}/send-request` send request
<p></p>

* Ledger Transactions (Indy Node)
\
    
<br><br>


### STEP 2. Alice --> Faber : Faber에게 Credential Request를 요청한다. 

* Method and Resource

    `POST` `/issue-credential/records/{cred_ex_id}/send-request` send request.  

* Parameter

 Name | Description 
 --- | --- 
 cred_ex_id | Faber의 Credential offer로 부터 받은 `credential_exchange_id`
 
<p></p>

* Example

    * input <br>
    `credential_exchange_id`: "412de146-5833-4fcc-be92-dbf5d50ba8ab"

<p></p>
 
   * Response body
```json
{
  "credential_definition_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college",
  "trace": false,
  "schema_id": "Th7MpTaRZVRYnPiabds81Y:2:degree_schema:24.8.1",
  "thread_id": "5a5eb7f2-efec-4319-9265-1a304f62fc1c",
  "credential_proposal_dict": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
    "@id": "44b6a5b8-a590-4bcc-9440-b5023c9556f7",
    "credential_proposal": {
      "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes": [
        {
          "name": "name",
          "value": "alice"
        },
        {
          "name": "date",
          "value": "08-2018"
        },
        {
          "name": "degree",
          "value": "maths"
        },
        {
          "name": "age",
          "value": "25"
        }
      ]
    },
    "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college",
    "comment": "string"
  },
  "updated_at": "2020-08-24 07:08:56.034090Z",
  "state": "offer_sent",
  "connection_id": "ce737f4c-3065-4da5-a490-98568fc3a773",
  "auto_issue": true,
  "auto_remove": true,
  "initiator": "self",
  "created_at": "2020-08-24 07:08:56.034090Z",
  "auto_offer": false,
  "credential_offer": {
    "schema_id": "Th7MpTaRZVRYnPiabds81Y:2:degree_schema:24.8.1",
    "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college",
    "key_correctness_proof": {
      "c": "1211877137352975463375244041700762417669628883094972980895062156226939594402",
      "xz_cap": "8115961514951812893019986223806866549524800875222674020819102495317582554707787440890847512164570050499140328724066359393524473039011310822575988360212058249011303025364927630306619639110090525647691013216404933458972973757324041910370972393348643439014381883537062076109476281533684079455056302022851203835166723040738610436456284853031488359745157917893874501698898620950357501750167791549863227035443558972787057489897135694495443263261231745249189026514606484118086509953367288902932736010808047145837780042232749595367491270654192839550655436662552365992872857372110965151191864165689047380962401251959398562959598596362667071587105859657891413523696717336765379185330665654916650216821",
      "xr_cap": [
        [
          "date",
          "33707859903081596188568324249725258543758458986207800463376651085096562554922818421232375486850302551555596664182852645285121968565909363837536111960227440017926587517104148607126442832105626813228504702527033626905478442067885586189706946533205293763702112054063046969349575823588577663075128920478993457405091963398088428490107167750576986934713442091205260529153597081471329551174363260912629846034195234008690789516396618444419611169470644668334218238518205064185487105177831198589625036929423822532554912318768192851904186578918402071091714900014042486279852690495876883908105724334215542859581004573900466741396523850791747351248163990721139768028556530617653324410890820711424830252340"
        ],
        [
          "master_secret",
          "21316669012664687319369248784598613730811052868099133401118752407582680150321161172633069205928479940145205890110674749991811847910687389520806101718665836012747969692113139643354501142700040489753326389921743858095616658979003572830343489665883575039480966878967569668891071229892991265881773195459999847621870782573588569960415929964902537869700232595160110730740225031770266573841343083758300276561438322842955503195778194030527744135691421625837828697234509007680111495549692336776653968890253564735921992281463127269130234247691344838644718116563844401840096408607320963729816150141205369777132798240918560151156620692042233842747168319561367835465734043635278535596645170273266651067354"
        ],
        [
          "degree",
          "32808350207909552000663769297428169906618718163752958299644864084198452324903653796103113267876821046310308274799929948822418741373992201343897949446061310789850587971301658151163692409244117594395244580844562766845849162535907880576337873622850357285846651115223417058267736144392508911367400339474812142768155878946867599302163956510663073145158154154471677185496324498384600910952528680219720874849255800023338669375164764501697507912320094017869912244417481642697674701469444157089813313232458745050745264124936932369322262874558153766798785790164688390965478024061533952490595314500169665208987515361044381127816263258281539988702671420428334413060199609960859059343694577591630948587557"
        ],
        [
          "name",
          "20394974789044082970541152955606572103461567728315829879381808717600421177744248635001385287010990031623026194822353092019613141430116596863878694023954099289187492902390535929653703097419404579137774631859241008672464329349626872154122310133280890862920531608780818629839689154706863780304112909252628756488092736234521949044509862165338438153103693977957134371048419499875347525353826666174889746746555907532164909278718231299368326261765846445616641758988577740151666194588257659450372094731058922976059931011909679868933083490345969958081572447120711547715449228751861811720192505120436604210676732030927784341861241008138909147570234499026644058423983959063173524450034277367859431304074"
        ],
        [
          "age",
          "12978383263696483579058528130178872284099728061983709986093745341385478818028463441116127444391324282828038361793245505751234964066473209353926429859942340059659452746878796562145765306236576059897863703112351705209095966460901176231047488026736546869621099393390353067470258360965780961086876508404727152292034936453127347056311090706223633486982380930089009642249818235821148708681287215415353758665701921158903681394116087492563096728978179355692830437496770368817085503849637689427788431657412454146824974437263001626025074741468648880355602470058488204464186826891543902078600881565122082389091528945986689054728016751936614042206171653669126382022427118458116005224449432328568366024314"
        ]
      ]
    },
    "nonce": "91199357596350999096630"
  },
  "credential_offer_dict": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/offer-credential",
    "@id": "5a5eb7f2-efec-4319-9265-1a304f62fc1c",
    "~thread": {},
    "offers~attach": [
      {
        "@id": "libindy-cred-offer-0",
        "mime-type": "application/json",
        "data": {
          "base64": "eyJzY2hlbWFfaWQiOiAiVGg3TXBUYVJaVlJZblBpYWJkczgxWToyOmRlZ3JlZV9zY2hlbWE6MjQuOC4xIiwgImNyZWRfZGVmX2lkIjogIlRoN01wVGFSWlZSWW5QaWFiZHM4MVk6MzpDTDoyMTQzOmZhYmVyX2NvbGxlZ2UiLCAia2V5X2NvcnJlY3RuZXNzX3Byb29mIjogeyJjIjogIjEyMTE4NzcxMzczNTI5NzU0NjMzNzUyNDQwNDE3MDA3NjI0MTc2Njk2Mjg4ODMwOTQ5NzI5ODA4OTUwNjIxNTYyMjY5Mzk1OTQ0MDIiLCAieHpfY2FwIjogIjgxMTU5NjE1MTQ5NTE4MTI4OTMwMTk5ODYyMjM4MDY4NjY1NDk1MjQ4MDA4NzUyMjI2NzQwMjA4MTkxMDI0OTUzMTc1ODI1NTQ3MDc3ODc0NDA4OTA4NDc1MTIxNjQ1NzAwNTA0OTkxNDAzMjg3MjQwNjYzNTkzOTM1MjQ0NzMwMzkwMTEzMTA4MjI1NzU5ODgzNjAyMTIwNTgyNDkwMTEzMDMwMjUzNjQ5Mjc2MzAzMDY2MTk2MzkxMTAwOTA1MjU2NDc2OTEwMTMyMTY0MDQ5MzM0NTg5NzI5NzM3NTczMjQwNDE5MTAzNzA5NzIzOTMzNDg2NDM0MzkwMTQzODE4ODM1MzcwNjIwNzYxMDk0NzYyODE1MzM2ODQwNzk0NTUwNTYzMDIwMjI4NTEyMDM4MzUxNjY3MjMwNDA3Mzg2MTA0MzY0NTYyODQ4NTMwMzE0ODgzNTk3NDUxNTc5MTc4OTM4NzQ1MDE2OTg4OTg2MjA5NTAzNTc1MDE3NTAxNjc3OTE1NDk4NjMyMjcwMzU0NDM1NTg5NzI3ODcwNTc0ODk4OTcxMzU2OTQ0OTU0NDMyNjMyNjEyMzE3NDUyNDkxODkwMjY1MTQ2MDY0ODQxMTgwODY1MDk5NTMzNjcyODg5MDI5MzI3MzYwMTA4MDgwNDcxNDU4Mzc3ODAwNDIyMzI3NDk1OTUzNjc0OTEyNzA2NTQxOTI4Mzk1NTA2NTU0MzY2NjI1NTIzNjU5OTI4NzI4NTczNzIxMTA5NjUxNTExOTE4NjQxNjU2ODkwNDczODA5NjI0MDEyNTE5NTkzOTg1NjI5NTk1OTg1OTYzNjI2NjcwNzE1ODcxMDU4NTk2NTc4OTE0MTM1MjM2OTY3MTczMzY3NjUzNzkxODUzMzA2NjU2NTQ5MTY2NTAyMTY4MjEiLCAieHJfY2FwIjogW1siZGF0ZSIsICIzMzcwNzg1OTkwMzA4MTU5NjE4ODU2ODMyNDI0OTcyNTI1ODU0Mzc1ODQ1ODk4NjIwNzgwMDQ2MzM3NjY1MTA4NTA5NjU2MjU1NDkyMjgxODQyMTIzMjM3NTQ4Njg1MDMwMjU1MTU1NTU5NjY2NDE4Mjg1MjY0NTI4NTEyMTk2ODU2NTkwOTM2MzgzNzUzNjExMTk2MDIyNzQ0MDAxNzkyNjU4NzUxNzEwNDE0ODYwNzEyNjQ0MjgzMjEwNTYyNjgxMzIyODUwNDcwMjUyNzAzMzYyNjkwNTQ3ODQ0MjA2Nzg4NTU4NjE4OTcwNjk0NjUzMzIwNTI5Mzc2MzcwMjExMjA1NDA2MzA0Njk2OTM0OTU3NTgyMzU4ODU3NzY2MzA3NTEyODkyMDQ3ODk5MzQ1NzQwNTA5MTk2MzM5ODA4ODQyODQ5MDEwNzE2Nzc1MDU3Njk4NjkzNDcxMzQ0MjA5MTIwNTI2MDUyOTE1MzU5NzA4MTQ3MTMyOTU1MTE3NDM2MzI2MDkxMjYyOTg0NjAzNDE5NTIzNDAwODY5MDc4OTUxNjM5NjYxODQ0NDQxOTYxMTE2OTQ3MDY0NDY2ODMzNDIxODIzODUxODIwNTA2NDE4NTQ4NzEwNTE3NzgzMTE5ODU4OTYyNTAzNjkyOTQyMzgyMjUzMjU1NDkxMjMxODc2ODE5Mjg1MTkwNDE4NjU3ODkxODQwMjA3MTA5MTcxNDkwMDAxNDA0MjQ4NjI3OTg1MjY5MDQ5NTg3Njg4MzkwODEwNTcyNDMzNDIxNTU0Mjg1OTU4MTAwNDU3MzkwMDQ2Njc0MTM5NjUyMzg1MDc5MTc0NzM1MTI0ODE2Mzk5MDcyMTEzOTc2ODAyODU1NjUzMDYxNzY1MzMyNDQxMDg5MDgyMDcxMTQyNDgzMDI1MjM0MCJdLCBbIm1hc3Rlcl9zZWNyZXQiLCAiMjEzMTY2NjkwMTI2NjQ2ODczMTkzNjkyNDg3ODQ1OTg2MTM3MzA4MTEwNTI4NjgwOTkxMzM0MDExMTg3NTI0MDc1ODI2ODAxNTAzMjExNjExNzI2MzMwNjkyMDU5Mjg0Nzk5NDAxNDUyMDU4OTAxMTA2NzQ3NDk5OTE4MTE4NDc5MTA2ODczODk1MjA4MDYxMDE3MTg2NjU4MzYwMTI3NDc5Njk2OTIxMTMxMzk2NDMzNTQ1MDExNDI3MDAwNDA0ODk3NTMzMjYzODk5MjE3NDM4NTgwOTU2MTY2NTg5NzkwMDM1NzI4MzAzNDM0ODk2NjU4ODM1NzUwMzk0ODA5NjY4Nzg5Njc1Njk2Njg4OTEwNzEyMjk4OTI5OTEyNjU4ODE3NzMxOTU0NTk5OTk4NDc2MjE4NzA3ODI1NzM1ODg1Njk5NjA0MTU5Mjk5NjQ5MDI1Mzc4Njk3MDAyMzI1OTUxNjAxMTA3MzA3NDAyMjUwMzE3NzAyNjY1NzM4NDEzNDMwODM3NTgzMDAyNzY1NjE0MzgzMjI4NDI5NTU1MDMxOTU3NzgxOTQwMzA1Mjc3NDQxMzU2OTE0MjE2MjU4Mzc4Mjg2OTcyMzQ1MDkwMDc2ODAxMTE0OTU1NDk2OTIzMzY3NzY2NTM5Njg4OTAyNTM1NjQ3MzU5MjE5OTIyODE0NjMxMjcyNjkxMzAyMzQyNDc2OTEzNDQ4Mzg2NDQ3MTgxMTY1NjM4NDQ0MDE4NDAwOTY0MDg2MDczMjA5NjM3Mjk4MTYxNTAxNDEyMDUzNjk3NzcxMzI3OTgyNDA5MTg1NjAxNTExNTY2MjA2OTIwNDIyMzM4NDI3NDcxNjgzMTk1NjEzNjc4MzU0NjU3MzQwNDM2MzUyNzg1MzU1OTY2NDUxNzAyNzMyNjY2NTEwNjczNTQiXSwgWyJkZWdyZWUiLCAiMzI4MDgzNTAyMDc5MDk1NTIwMDA2NjM3NjkyOTc0MjgxNjk5MDY2MTg3MTgxNjM3NTI5NTgyOTk2NDQ4NjQwODQxOTg0NTIzMjQ5MDM2NTM3OTYxMDMxMTMyNjc4NzY4MjEwNDYzMTAzMDgyNzQ3OTk5Mjk5NDg4MjI0MTg3NDEzNzM5OTIyMDEzNDM4OTc5NDk0NDYwNjEzMTA3ODk4NTA1ODc5NzEzMDE2NTgxNTExNjM2OTI0MDkyNDQxMTc1OTQzOTUyNDQ1ODA4NDQ1NjI3NjY4NDU4NDkxNjI1MzU5MDc4ODA1NzYzMzc4NzM2MjI4NTAzNTcyODU4NDY2NTExMTUyMjM0MTcwNTgyNjc3MzYxNDQzOTI1MDg5MTEzNjc0MDAzMzk0NzQ4MTIxNDI3NjgxNTU4Nzg5NDY4Njc1OTkzMDIxNjM5NTY1MTA2NjMwNzMxNDUxNTgxNTQxNTQ0NzE2NzcxODU0OTYzMjQ0OTgzODQ2MDA5MTA5NTI1Mjg2ODAyMTk3MjA4NzQ4NDkyNTU4MDAwMjMzMzg2NjkzNzUxNjQ3NjQ1MDE2OTc1MDc5MTIzMjAwOTQwMTc4Njk5MTIyNDQ0MTc0ODE2NDI2OTc2NzQ3MDE0Njk0NDQxNTcwODk4MTMzMTMyMzI0NTg3NDUwNTA3NDUyNjQxMjQ5MzY5MzIzNjkzMjIyNjI4NzQ1NTgxNTM3NjY3OTg3ODU3OTAxNjQ2ODgzOTA5NjU0NzgwMjQwNjE1MzM5NTI0OTA1OTUzMTQ1MDAxNjk2NjUyMDg5ODc1MTUzNjEwNDQzODExMjc4MTYyNjMyNTgyODE1Mzk5ODg3MDI2NzE0MjA0MjgzMzQ0MTMwNjAxOTk2MDk5NjA4NTkwNTkzNDM2OTQ1Nzc1OTE2MzA5NDg1ODc1NTciXSwgWyJuYW1lIiwgIjIwMzk0OTc0Nzg5MDQ0MDgyOTcwNTQxMTUyOTU1NjA2NTcyMTAzNDYxNTY3NzI4MzE1ODI5ODc5MzgxODA4NzE3NjAwNDIxMTc3NzQ0MjQ4NjM1MDAxMzg1Mjg3MDEwOTkwMDMxNjIzMDI2MTk0ODIyMzUzMDkyMDE5NjEzMTQxNDMwMTE2NTk2ODYzODc4Njk0MDIzOTU0MDk5Mjg5MTg3NDkyOTAyMzkwNTM1OTI5NjUzNzAzMDk3NDE5NDA0NTc5MTM3Nzc0NjMxODU5MjQxMDA4NjcyNDY0MzI5MzQ5NjI2ODcyMTU0MTIyMzEwMTMzMjgwODkwODYyOTIwNTMxNjA4NzgwODE4NjI5ODM5Njg5MTU0NzA2ODYzNzgwMzA0MTEyOTA5MjUyNjI4NzU2NDg4MDkyNzM2MjM0NTIxOTQ5MDQ0NTA5ODYyMTY1MzM4NDM4MTUzMTAzNjkzOTc3OTU3MTM0MzcxMDQ4NDE5NDk5ODc1MzQ3NTI1MzUzODI2NjY2MTc0ODg5NzQ2NzQ2NTU1OTA3NTMyMTY0OTA5Mjc4NzE4MjMxMjk5MzY4MzI2MjYxNzY1ODQ2NDQ1NjE2NjQxNzU4OTg4NTc3NzQwMTUxNjY2MTk0NTg4MjU3NjU5NDUwMzcyMDk0NzMxMDU4OTIyOTc2MDU5OTMxMDExOTA5Njc5ODY4OTMzMDgzNDkwMzQ1OTY5OTU4MDgxNTcyNDQ3MTIwNzExNTQ3NzE1NDQ5MjI4NzUxODYxODExNzIwMTkyNTA1MTIwNDM2NjA0MjEwNjc2NzMyMDMwOTI3Nzg0MzQxODYxMjQxMDA4MTM4OTA5MTQ3NTcwMjM0NDk5MDI2NjQ0MDU4NDIzOTgzOTU5MDYzMTczNTI0NDUwMDM0Mjc3MzY3ODU5NDMxMzA0MDc0Il0sIFsiYWdlIiwgIjEyOTc4MzgzMjYzNjk2NDgzNTc5MDU4NTI4MTMwMTc4ODcyMjg0MDk5NzI4MDYxOTgzNzA5OTg2MDkzNzQ1MzQxMzg1NDc4ODE4MDI4NDYzNDQxMTE2MTI3NDQ0MzkxMzI0MjgyODI4MDM4MzYxNzkzMjQ1NTA1NzUxMjM0OTY0MDY2NDczMjA5MzUzOTI2NDI5ODU5OTQyMzQwMDU5NjU5NDUyNzQ2ODc4Nzk2NTYyMTQ1NzY1MzA2MjM2NTc2MDU5ODk3ODYzNzAzMTEyMzUxNzA1MjA5MDk1OTY2NDYwOTAxMTc2MjMxMDQ3NDg4MDI2NzM2NTQ2ODY5NjIxMDk5MzkzMzkwMzUzMDY3NDcwMjU4MzYwOTY1NzgwOTYxMDg2ODc2NTA4NDA0NzI3MTUyMjkyMDM0OTM2NDUzMTI3MzQ3MDU2MzExMDkwNzA2MjIzNjMzNDg2OTgyMzgwOTMwMDg5MDA5NjQyMjQ5ODE4MjM1ODIxMTQ4NzA4NjgxMjg3MjE1NDE1MzUzNzU4NjY1NzAxOTIxMTU4OTAzNjgxMzk0MTE2MDg3NDkyNTYzMDk2NzI4OTc4MTc5MzU1NjkyODMwNDM3NDk2NzcwMzY4ODE3MDg1NTAzODQ5NjM3Njg5NDI3Nzg4NDMxNjU3NDEyNDU0MTQ2ODI0OTc0NDM3MjYzMDAxNjI2MDI1MDc0NzQxNDY4NjQ4ODgwMzU1NjAyNDcwMDU4NDg4MjA0NDY0MTg2ODI2ODkxNTQzOTAyMDc4NjAwODgxNTY1MTIyMDgyMzg5MDkxNTI4OTQ1OTg2Njg5MDU0NzI4MDE2NzUxOTM2NjE0MDQyMjA2MTcxNjUzNjY5MTI2MzgyMDIyNDI3MTE4NDU4MTE2MDA1MjI0NDQ5NDMyMzI4NTY4MzY2MDI0MzE0Il1dfSwgIm5vbmNlIjogIjkxMTk5MzU3NTk2MzUwOTk5MDk2NjMwIn0="
        }
      }
    ],
    "credential_preview": {
      "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes": [
        {
          "name": "name",
          "value": "alice"
        },
        {
          "name": "date",
          "value": "08-2018"
        },
        {
          "name": "degree",
          "value": "maths"
        },
        {
          "name": "age",
          "value": "25"
        }
      ]
    },
    "comment": "string"
  },
  "credential_exchange_id": "412de146-5833-4fcc-be92-dbf5d50ba8ab"
}
```

<p></p>

* Next Step
<br> Alice의 `/issue-credential/records/{cred_ex_id}/send-request` send request
<p></p>

* Ledger Transactions (Indy Node)
\
    
<br><br>