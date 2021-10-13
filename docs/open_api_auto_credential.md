Auto Credential Issue
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

* **기관**(Issuer/Verifier)과 **사용자**(Holder/Prover)의 VC발급 workflow
![issue_diagram](img/issue_diagram.png)
[PlantUML](http://www.plantuml.com/plantuml/uml/TLF1RX9H5Ds_hxXXDTCmVe01KkD6TyQ5ssRAF9A9qtdZp63I7I8crJPHOucq3WGrQ30q6G6HHVsXURV_uEMzWL882GjoS-uvSywvRp-AxJ2k7tcC-x4R7qCzuY7O4HImESBB8VO-68-4vz3mGH2OXINCZQ9w3doi_XOOO-Ko124F-Okw6xanXtAPiHnOiYgQ-ZvKXU_pQkmAty3FQJPEJS2Bbln-fWYUX28GaUqP8bX0J4MYDeb1an67J9Di_XQmds2tOmJvZibyGRBY9G-L6DlJk1osf3P1DgVaROzPAvCW9tFiqcyA-FCLzZ-In9TiqWSydM6t0SyUoi4rB8MOfOatCRlcf0utxIlvQqYqxtDvzVMpX5dQgUr1Da_bkm4cr_9h0lYwAtj_84iRDBl7h59D-365vucmVEZLUQLqM75zDtPfqGw2XT1TsMh8dGE7rmvSXwHQydH4vJcmY6ZPYhMUwMqMU7cEuoj3Up7Xvg9DqQXsHAr6MEF8zXqx5k6nhcT5fphUwATzOgYjzN8qVhsU5MhLZRuwIcwhPS3UoIAauHIoltDvrbZ4HTwntGBvAP4_HjdlL3OxHQQJnszlvA1JN3KmQMJZVqMSdLEsURwtoIYRJ-c4UHHl6TLtk6KpxrFdjEjOyr13JMrg8VLsDbHsfMcekRdDZ_q6zWRw3HbszJbtJBPFXkXL_mS0)

<p></p>

<br>

Issue Flow

![connection flow](img/webconsole_issue_flow.png)

<Br>


* 발급 과정 중 issue_credential State 

Topic | State | Description
--- | --- | ---
issue_credential | <font color=red>proposal_received<br><b>(Webhook event 전달) | (issuer)credential proposal 받은 상태
issue_credential | <font color=red>offer_sent<br><b>(Webhook event 전달) | (issuer)credential offer 한 상태
issue_credential | offer_received | (holder)offer를 받은 상태
issue_credential | request_sent | (holder) credential을 요청 한 상태
issue_credential | <font color=red>request_received<br><b>(Webhook event 전달) | (issuer) Credential 요청을 받은 상태 
issue_credential | <font color=red>credential_issued<br><b>(Webhook event 전달) | (issuer) Credential을 issue  
issue_credential | credential_received | (holder) Credential을 받은 상태 
issue_credential | <font color=red>credential_acked<br><b>(Webhook event 전달) | (holder/issuer) Credential 수취 완료

<br>

* Credential_exchange_id Data Model(Example)
    - issue_credential 관리를 위해 별도의 identifier인 credential_exchange_id를 제공한다.
    - credential_exchange_id는 아래와 같이 connection_id를 포함하여 발급 관련 모든 정보를 포함하고 있다

```json
        {
            "initiator": "external",
            "auto_issue": true,
            "trace": false,
            "state": "proposal_received",
            "credential_proposal_dict": {
                "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
                "@id": "33c8e5ff-ab21-45c1-b966-06cead6e0317",
                "cred_def_id": "A5EaF6CUiTquuMFysqykmY:3:CL:1614236895:tag.56.42.80"
            },
            "connection_id": "c9e32cc7-ad4e-4808-8d43-0a6dbadcfbb2",
            "thread_id": "33c8e5ff-ab21-45c1-b966-06cead6e0317",
            "auto_remove": false,
            "credential_exchange_id": "bbf3ec30-f01d-4879-b016-9abc4ce075cb",
            "role": "issuer",
            "updated_at": "2021-05-26 04:20:47.073303Z",
            "created_at": "2021-05-26 04:20:47.073303Z"
        }
```
<br><br>

### STEP 0. <font color=green>[Mandatory]</font> 사용자(Holder) → 기관(발급자/Issuer) : Issue proposal

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP0는 initial app에서 Issue Proposal을 먼저 진행 합니다. 기관사용자는 해당 요청을 Webhook을 통해서 받습니다.</p>
</div>

사용자(Holder)가 credential proposal을 요청하면, Webhook을 통해 아래와 같은 event가 전달 된다.
```json
{
  "initiator": "external",
  "credential_proposal_dict": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
    "@id": "03e19631-26a0-4c06-9a29-35c95766a692",
    "cred_def_id": "DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd"
  },
  "trace": false,
  "credential_exchange_id": "148b673e-d506-431a-8063-a70aebdaadfe",
  "auto_remove": false,
  "connection_id": "85e21c28-342b-406c-9303-69db21cb96d3",
  "role": "issuer",
  "created_at": "2021-05-18 10:10:43.082572Z",
  "auto_issue": true,
  "state": "proposal_received",
  "updated_at": "2021-05-18 10:10:43.082572Z",
  "thread_id": "03e19631-26a0-4c06-9a29-35c95766a692",
  "topic": "issue_credential"
}
```
* Issuer는 아래 정보를 확인 해야 함.
<p></p>
1. `"topic": "issue_credential"` >> VC 발행 요청
2. `"state": "proposal_received"` >> Proposal 상태
3. `"cred_def_id":"CB1f9WKGAJDwUKCT2XEx7o:3:CL:1617870264:9f714e9d-4dfb-4d9f-8c8f-60281c729745"` >> 사용자가 요청한 VC 정보
4. `"connection_id":"0844ebf0-c88f-49cf-9ed0-d0b57cfd9ec8"` >> 사용자 연결 정보
5. `"credential_exchange_id": "148b673e-d506-431a-8063-a70aebdaadfe"` >> VC발급 관련 모든 정보를 담고 있는 ID
<br><br>

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> 아래는 Holder 전용 Cloud Wallet 사용자 Guide 입니다. 기관 사용자는 Skip 하셔도 됩니다.</p>
</div>

#### Method and Resource

`POST` `/issue-credential/send-proposal` <font color=blue><b>[Holder 전용]</b></font> Issuer에게 VC 발급 요청
<p></p>

* Swagger Document
  
    [Link Click](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/issue-credential%20v1.0/post_issue_credential_send_proposal)
<p></p>

#### Parameters

* Query Parameters

     KEY | Value | Required | Description 
     --- | :---: | :---: | ---

<p></p>

* Path Variables

     KEY | Value | Required | Description 
     --- | --- | --- | ---

* Body 

    Parameter content type `application/json`

```json
{
  "auto_remove": true,
  "comment": "string",
  "connection_id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "cred_def_id": "WgWxqztrNooG92RXvxSTWv:3:CL:20:tag" // 발행받고자 하는 VC ID
}
```

#### Request Example 

* Curl

```
curl -L -X POST 'https://dev-console.myinitial.io/agent/api/issue-credential/send-proposal' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer 5dd1e041-xxxx-xxxx-xxxx-96616886ece4' \
--data-raw '{
  "auto_remove": true,
  "comment": "string",
  "connection_id": "6efb1145-3163-4db1-af8f-57a0acef8086",
  "cred_def_id": "WgWxqztrNooG92RXvxSTWv:3:CL:20:tag" 
}'
```
<p></p>

#### Response example

* Response body

```json
{
    "connection_id": "6efb1145-3163-4db1-af8f-57a0acef8086",
    "credential_exchange_id": "7ca8537e-e412-4dee-a37e-c444d4ae1724",
    "created_at": "2021-10-13 15:50:11.817413Z",
    "thread_id": "1d095f07-aa47-40ca-ab1d-655f3810f025",
    "role": "holder",
    "state": "proposal_sent",
    "credential_proposal_dict": {
        "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
        "@id": "1d095f07-aa47-40ca-ab1d-655f3810f025",
        "cred_def_id": "WgWxqztrNooG92RXvxSTWv:3:CL:20:tag",
        "comment": "string"
    },
    "auto_remove": true,
    "auto_issue": false,
    "updated_at": "2021-10-13 15:50:11.817413Z",
    "initiator": "self"
}
```
<p></p><p></p>

#### Webhook example

```json
{
  "initiator": "external",
  "credential_proposal_dict": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
    "@id": "03e19631-26a0-4c06-9a29-35c95766a692",
    "cred_def_id": "DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd"
  },
  "trace": false,
  "credential_exchange_id": "148b673e-d506-431a-8063-a70aebdaadfe",
  "auto_remove": false,
  "connection_id": "85e21c28-342b-406c-9303-69db21cb96d3",
  "role": "issuer",
  "created_at": "2021-05-18 10:10:43.082572Z",
  "auto_issue": true,
  "state": "proposal_received",
  "updated_at": "2021-05-18 10:10:43.082572Z",
  "thread_id": "03e19631-26a0-4c06-9a29-35c95766a692",
  "topic": "issue_credential"
}
```

1. <b>topic</b> : `issue_credential` >> VC 발행 요청 event
2. <b>state</b> : `proposal_received` >> Proposal 상태

<br><br>


### STEP 1. <font color=green>[Mandatory]</font> 기관(발급자) → 사용자(Holder) 발행

- VC 발급은 두가지 방법이 존재한다.

    - Step 1-1 : Step 0의 Holder로 부터 받은 Proposal의 credential_exchange_id를 그대로 활용하여 발행 요청
    - Step 1-2 : Step 0의 Holder로 부터 받은 Proposal의 credential_exchange_id와 상관없이 새로운 credential_exchange_id기반 발행 요청

    <p></p>STEP 1-1의 방법은 기존에 받은 proposal을 활용하는 방법으로 전체적인 발급관리에 있어 효율적임. 기본 사용으로 추천.


<br>

#### STEP 1-1. 기관(발급자) → 사용자(Holder) : Proposal의 credential_exchange_id 기반 Credential 발행 

* Method and Resource

    `POST` `/issue-credential/records/{cred_ex_id}/send-offer` credential preview를 보내면서 offer.  

* Parameter Description

 Name | Description 
 --- | --- 
 cred_def_id | 발급할 Credential ID. STEP0의 Webhook Event를 통해 확인 가능하다.
 counter_proposal | 사용자의 issue proposal에 대한 응답임을 명시
 credential_proposal | Proposal이 내용 부분
 auto_issue | 사용자가 credential request를 하면 자동으로 issue 완료<br>`/issue-credential/records/{cred_ex_id}/issue` 자동 처리
 auto_remove | Credential issue 완료되면 관련 record를 자동 삭제 <br> `/issue-credential/records/{cred_ex_id}/remove` 자동 처리 
 
<p></p>

* Example

    * Path Variables Parameter input <br>
    `cred_ex_id` : `string`<br>

    * body

    <div class="admonition warning ">
    <p class="admonition-title">important</p>
    <p> 모든 attribute의 값을 입력해야(null 가능) 오류가 발행하지 않음 </p>
    </div>

    
```json
{
  "counter_proposal":{
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "credential_proposal":{
      "attributes":[
        {
          "name":"date_of_birth",
          "value":"20000228"
        },
        {
          "name":"date_of_test",
          "value":"20180228'"
        },
        {
          "name":"english_name",
          "value":"Kim Initial"
        },
        {
          "name":"exp_date",
          "value":"20180228"
        },
        {
          "name":"korean_name",
          "value":"김증명"
        },
        {
          "name":"registration_number",
          "value":"123456789"
        },
        {
          "name":"score_of_listening",
          "value":"445"
        },
        {
          "name":"score_of_reading",
          "value":"445"
        },
        {
          "name":"score_of_total",
          "value":"990"
        }
      ]
    },
    "auto_issue": true,
    "auto_remove": false,
    "comment": "Counter Proposal Send offer Example",
    "trace": false
  }
}
```

- cURL Request Example

```
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/issue-credential/records/d6aa4f24-c082-4443-9f91-2dda87962257/send-offer' \
--header 'Authorization: Bearer 2ca4dd8a-1234-1234-1234-c5fb0286f2cc' \
--header 'Content-Type: application/json' \
--data-raw '{
  "counter_proposal":{
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "credential_proposal":{
      "attributes":[
        {
          "name":"date_of_birth",
          "value":"20000228"
        },
        {
          "name":"date_of_test",
          "value":"20180228"
        },
        {
          "name":"english_name",
          "value":"Kim Initial"
        },
        {
          "name":"exp_date",
          "value":"20180228"
        },
        {
          "name":"korean_name",
          "value":"김증명"
        },
        {
          "name":"registration_number",
          "value":"123456789"
        },
        {
          "name":"score_of_listening",
          "value":"445"
        },
        {
          "name":"score_of_reading",
          "value":"445"
        },
        {
          "name":"score_of_total",
          "value":""
        }
      ]
    },
    "auto_issue": true,
    "auto_remove": true,
    "comment": "Counter Proposal Send offer Example",
    "trace": false
  }
}'
```

<p></p>


* issue_credential State check 

    |  | Issuer | Holder |
    | --- | --- | --- |
    | issue_credential state | **`offer_sent`** | N/A |

<p></p>

<br>

#### STEP 1-2. 기관(발급자) → 사용자(Holder) : 신규 credential_exchange_id 기반 Credential 발행

* Method and Resource

  `POST` `/issue-credential​/send-offer` credential preview를 보내면서 offer.

* Parameter

Name | Description
 --- | --- 
connection_id | Holder와 연결을 위해 사용한 ID. STEP0의 Webhook Event를 통해 확인 가능하다.
cred_def_id | 발급할 Credential ID. STEP0의 Webhook Event를 통해 확인 가능하다.
credential_preview | Holder에 발급할 VC의 정보를 기입한다.
auto_issue | Alice가 credential request를 하면 자동으로 issue 완료<br>`/issue-credential/records/{cred_ex_id}/issue` 자동 처리
auto_remove | Credential issue 완료되면 cred_ex_id record를 자동 삭제 <br> `/issue-credential/records/{cred_ex_id}/remove` 자동 처리

<p></p>

* Example

    * body 

    <div class="admonition warning ">
    <p class="admonition-title">important</p>
    <p> 모든 attribute의 값을 입력해야(null 가능) 오류가 발행하지 않음 </p>
    </div>

```json
{
  "connection_id": "2a6ed506-9577-46ee-a60c-726241f89aec",
  "cred_def_id": "DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
  "credential_preview": {
    "attributes":[
      {
        "name":"date_of_birth",
        "value":"20000228"
      },
      {
        "name":"date_of_test",
        "value":"20180228'"
      },
      {
        "name":"english_name",
        "value":"Kim Initial"
      },
      {
        "name":"exp_date",
        "value":"20180228"
      },
      {
        "name":"korean_name",
        "value":"김증명"
      },
      {
        "name":"registration_number",
        "value":"123456789"
      },
      {
        "name":"score_of_listening",
        "value":"445"
      },
      {
        "name":"score_of_reading",
        "value":"445"
      },
      {
        "name":"score_of_total",
        "value":"990"
      }
    ],
    "@type": "issue-credential/1.0/credential-preview"
  },
  "auto_issue": true,
  "auto_remove": false,
  "comment": "consequat anim minim aliquip",
  "trace": false
}
```

cURL Request Example
```
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/issue-credential/send-offer' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer 2ca4dd8a-xxxx-xxxx-xxxx-c5fb0286f2cc' \
--data-raw '{
    "connection_id": "2a6ed506-9577-46ee-a60c-726241f89aec",
    "cred_def_id": "DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "credential_preview": {
       "attributes":[
        {
          "name":"date_of_birth",
          "value":"20000228"
        },
        {
          "name":"date_of_test",
          "value":"20180228'\''"
        },
        {
          "name":"english_name",
          "value":"Kim Initial"
        },
        {
          "name":"exp_date",
          "value":"20180228"
        },
        {
          "name":"korean_name",
          "value":"김증명"
        },
        {
          "name":"registration_number",
          "value":"123456789"
        },
        {
          "name":"score_of_listening",
          "value":"445"
        },
        {
          "name":"score_of_reading",
          "value":"445"
        },
        {
          "name":"score_of_total",
          "value":"990"
        }
      ],
        "@type": "issue-credential/1.0/credential-preview"
    },
    "auto_issue": true,
    "auto_remove": false,
    "comment": "consequat anim minim aliquip",
    "trace": false
}'
```

<p></p>

* issue_credential State check

  |  | Issuer | Holder |
        | --- | --- | --- |
  | issue_credential state | **`offer_sent`** | N/A |
<p></p>

##### STEP 1-1, 1-2 모두 아래와 같은 Event가 전달 됨.

* Webhook Event Example (state : `offer_sent`)

```json
{
  "updated_at": "2021-05-27 13:47:00.702727Z",
  "credential_offer_dict": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/offer-credential",
    "@id": "774db754-4182-4cb7-b02b-0af0e0e5c3ae",
    "~thread": {},
    "comment": "consequat anim minim aliquip",
    "credential_preview": {
      "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes": [
        {
          "name": "date_of_birth",
          "value": "20000228"
        },
        {
          "name": "date_of_test",
          "value": "20180228'"
        },
        {
          "name": "english_name",
          "value": "Kim Initial"
        },
        {
          "name": "exp_date",
          "value": "20180228"
        },
        {
          "name": "korean_name",
          "value": "김증명"
        },
        {
          "name": "registration_number",
          "value": "123456789"
        },
        {
          "name": "score_of_listening",
          "value": "445"
        },
        {
          "name": "score_of_reading",
          "value": "445"
        },
        {
          "name": "score_of_total",
          "value": "990"
        }
      ]
    },
    "offers~attach": [
      {
        "@id": "libindy-cred-offer-0",
        "mime-type": "application/json",
        "data": {
          "base64": "eyJzY2hlbWFfaWQiOiAiTjZyNG5Md0FrY1lVWDhjOEtiOFVmdToyOkNlcnRpZmljYXRlT2ZUT0VJQzo0LjAiLCAiY3JlZF9kZWZfaWQiOiAiRHJMYlhGU2FvNFZvOGdNZmp4UHhVMTozOkNMOjE2MTc2OTgyMzg6ODFkZjAwMTAtNjJiNC00NWIxLWJkMDAtOGQwYWQ3NDc2MmZkIiwgImtleV9jb3JyZWN0bmVzc19wcm9vZiI6IHsiYyI6ICIxMTUzMDkyMDc2NjU0Mjg0MDYxNzk1NDU5OTQzNzM5MTIxODEyODI4MzE1NDUxMTg1NDAwMTQyMjk5NjcyNzE3MDc4OTYyMjQ5MzE1NTAiLCAieHpfY2FwIjogIjEwNTU1NzI3Njk4NjI2NjM0NjM3MDk5MzQyMDQ2MDE5NTQwODA0ODM4OTg4MjYwMzA4Nzk1OTU5NzkwNzQ5NzEwMDQyMDY4NTkzOTM3ODI2NTU1OTkzMjQxNDA5MTM5MDI2NTM2NTAzOTg5Mzg5MDY2ODE2NDE5MjQzOTA3OTQ4MTI3Mzg4ODc4MTE4OTU1NDU3MTEwNjM1NjQ0ODExNzkxNzIzMzA4ODEwOTQ5NTI0Mzg5NTA4MzYzMjYzNDI4MzA4NDU5Njc5ODc4NTUyNjUxOTE3ODM1NjU2ODgxNjk5NzU4NjMzMjQ1NDU1NDUzMTU3ODE2NjkwMjQ1OTY5ODYyNTcxNDMwNjE1MjUyNjQ4OTY0NTg0ODY2MjU0MzEyODA3NjQ1NjY4NTgwMTAyNDg2MjYzNDY4MTE4MTgzMzI1NTMzNDg5OTQyNTUxMDk0MzAwNTM5NDA0NTIyMjIzMzAyOTY3MjEyNDQxMzAxMDkxMzk3Mzk5MDU4MTk2NzQxOTgxMjM4MjU0MjM2OTcwMTQxOTQ2NTY2MzAzOTU1Mjc1MzMwMzU1ODA5MTk5MzU3Mzc0OTk1ODk4MzE5NjY2NTkyNTA4NjM3OTgyNjE3NTk0MTY3NzkyMzY3NDc2NzQxMTY4OTk5OTE1NDg1MjA3ODMwMDc0MzIxODIyMDU2NDk3MDA1ODczNTg2OTUzMjIxMDU5MTUwNTE0ODM4MzAyNDYxMzU1MTgzODU1NTQyNjM1MzEzMzczODc2MjY3MDQ1ODc0MDU2Njk5Njg3NzE0MjE4ODAzMDY4NTAxNTYwNDg3NDg1NzM3MzA1NzU3MzUwMTYxNjA2MjI3MDY2MDU4ODUwOTM2NDQ3ODI1MzI2NDczNzM2NTkxOTY3ODQyNDU4MzI3MjMwODUyMTM0OTcwNDg5ODk1MjAiLCAieHJfY2FwIjogW1siZGF0ZV9vZl90ZXN0IiwgIjEwNzYyMzIxNTkyNDk5NjQ3MzQxMDk3MTg1ODk3NzcyNjc0NjIxMDk0ODQwNzA0NDA0MTU2OTE1MTE4MjY1MjE1ODk1MjgxOTU5NjAxMTcwMDY2MDE1OTY0OTk4MzM4ODExMDY3ODc1MzI5MDg1ODI3MzU2ODU5NDcxMjUzMDM2Mzc3Nzk3NDk4NjUzOTU5NzM3NDg3ODY4NDUzODExMjU2MTQyNzYxODU3NzYzMjU1MjEyODQ1MDc5NzkwMjIyMDI3NjUwMTU3MzQ1MzE2Njg0OTQ0Nzg2Mjc0MDQ2NDQ2NzQ5MjIxMTUzNTEzOTI4MTA5MzUzMzY1MjkxNzkxMDg0NDYzNjIxNTcxMjMzMTU1NjUyNjA3MjU0ODMwMzUxNTk1NDY5Nzc5ODI2MzYyNjgyNzM3NDExMzQ0NDc2MjU2NTY5MTMwODMwNDk0NzExNjM4NjMzMjU1ODA1NDU3NTEyODEyODE3MDcyMzkwMzAwMTE0OTE5MDUzNzU2NzM1MjE2MzU3OTA3MTczMDExODg3MTY5ODk0MTkyNDU1OTQ0MjU2Njc4OTczNzIxMzc4ODc2MzIwOTEyNTc2OTI5OTIwMDIyMDQzNTMxNzIxNDEwMzU1MzI2ODY4MTk5NTY2NTU0NjQyNzQ3Mjg4ODgxNjYyMzM4MDA3MzAyMDk2Mjc2MjczMzMxMTgzNzIxNzYxOTAxMDg1ODE4MTkwMjg5NjIzODM0ODc2MTkyOTE4NDUwMjA5OTQwODE5MTIyMDgxNDgwNjE0ODIyNDExNTA0OTE3OTg2MzAxMDU1NTk4NjA0NjQyOTIxMDA5ODc2NTA5MDY5NDk1NDgyMjgwMTI5MDgwOTkwMzYyMzQ5Njk5NzgwOTE1OTA1NzcwNTY4NTk4MTQyMDU1OTg4Njc2MzI5Mjc5NTA3MTUiXSwgWyJlbmdsaXNoX25hbWUiLCAiNjk2NDExMTMwOTM5NTgwNTMzNTEwNDM2NjY0MTk0NTI2ODE5MTU2MDk1NjI5Mjg3NDEyNDM0MDQ1MjM4MzUwODA0NzUwNDU4ODc1MjUyMDA5MTU5ODM3MjA4NjY3MDk2MzU3NzA4MjczODk4ODM2Nzc0NTk2NTY5NTcxODI2NDIxNDE2MzczNjc1NzU4MTUzNDM3MjI0OTYxMzQ3ODAzMDAzNTg3MzI4Nzc0MjM4NjQ1Mjc5NzE4MDExMzA1MjM0NDMwNTEwOTE0MzM5MDMwNzM5NzcyMTQxMDM0OTM3ODIyNjEwNjAxODk0MTAwMDkyNjExNjk5MDUxNTg3MzY4NTcyNDc2Mzc4MDY2NTcxOTk1MTk3MTYwMzA4NzcwODMwMzMxNjUxNjg0MTU3NDIyMzIxNDk0MjEwNTY1NjIyMjU0OTM2MTc5MDUwMjU2MTk2Mzk1ODM5ODM2NDUwNzM5NzA4NjAzMTkzMjg1ODk2Nzg1ODAxMzQwMDY2MjMwNDgwMDQyMzQxOTU0NjY2NjIwNzAxMTIxOTA2NTkzNjMwNjU1OTk4ODY0MDA4NTk4MTk0NDk4MDUyODAzMjE1NTk4MjQ2OTIzNzE2OTYxODI3NzY1NDE1NDI3NDY3NzI1NjM5Nzc4Mzk5ODQzMTc4OTkxOTM0MzE1OTAxNzg1OTI3MjAzOTY0MTgzMjQ0NjE5MzA3MTU4MDIwNzMzODE3NTE2NDE5ODc1MDk5ODQ2OTI3MzU2ODAwOTI5ODA3MjA4NzEzMTAyOTQxNTk0MDQ5NTc3MzExODUxOTcwMjk2MjY1MzA1Mjc0MjM1NjQyMDU5ODAwOTkzOTM3ODA1NTkzNDQ0NjAyNjczNjIyMjYwMzYyMzYxNTUzMjAwMDEwMzI2NzQ0Njg0ODgwMTEzMzQzNzczMjI0ODU2Il0sIFsiZXhwX2RhdGUiLCAiMjQyMzY1MDEyMzE2NDkwNTUyOTM2MzYyMTEzNzgwOTcyMzE0NTMyNzg2NzU0Mjc1MzA0ODc2MDYxMDczMDY3NDI4NDc1NTczOTk3NjM1NjQ4NzQ2NjI2NzU3NzM5NzcxMzY2OTkyNzgyNDU1NTcxODA3ODY3NDcyOTMyOTgyMjEzNjYyMzUyODQ5NTAwNDkyMjA1Nzc0MTkzNTk4NDgzMjc2MjkzNzg4Mjk1Mzc5ODAwNzgzODMxNjIwNzk0NDUwODAwMDIyNDMzMTM4NTk5NjQ0MzI2OTU4MDc3MjQ5ODc2OTg2ODU2NTAwNzYxMDUwMzEwMzA4NjQ2NTMyMDkyMTA5NDY4MjI4MTYwOTYyODg2OTkwNjMzNDc0NjA3OTgzMjk2NzM1MDkwNzk4NjkyNTM4NTU5MTg2NzAxMDUyNzYwNzE3Nzc5NTI0OTQyMzM1ODU0NDk4MTg0Mjc3MjEwNjA1NjMwMDY1OTQ4NDMyMzk0NzE4OTA1Mjc1ODA5ODA1MjcxNzI5MTgyMTYyNTk0NTYyNTE5NjAzOTM2Mzg4ODU3ODMyNjk0MDM3MTIyOTY5OTY3MjM2MzIxOTI2NDUzNDg3MzgwMjg1MjQ3NjcxODczODM0NzMwMjkyOTk0MDQ3MDI5ODc4OTg3Njg4NjE5NjkxMDQ2NDM4MTU2ODIyMDAzMjUwMTE4MzQxNjM0OTg0ODE0Mzk4ODE3MzcyNzE1NTE5NDcyODcyMDg2NDIwNTE4OTc3NzI4MzMwNTM0OTg0NTUzMTI3MjQyMjgwNTIyODI1MjY4NzcyNzk5MjE4MjAyNjk4MjYxNDQ2MjIwMjY0NDg1NjI4MDI2NDAzNDQ1MDgxNDc4NjYwNDQ4MzI5NjA5MjA1NjY5OTk5MTU0NDc0OTA5NzcyODg2ODQ4Mzc0NDQ0NjMzMSJdLCBbInNjb3JlX29mX3JlYWRpbmciLCAiMjI2Mzg4NjAxNDE2MzY5NjU5MDg3NzcxODgxOTEwMzQ1NTE0MTgzOTc2NDEwNjgzMjc2ODU5Njk1OTQ4NDk3MjUyMjQ4OTU3NTAyNzAxNzg4ODM3Njk4MTU2ODEzMjY0MTkyMzM2Njg1MDY0ODI4Nzg0MDE1MzkxMjEyNDM2NzI3NTM3MTQ3NDc2OTc5MDUwMDQyMzUyNDYwNDkxOTU2NDIzODA5Mjg1NzIyNDI3ODMwMDUzNDAwMDg5NzYzNjgwMTg1MjcwNTgwOTk0MTA0NjU4MTc3NTU4OTc1MTg2MDUyMjIxNzQ5MDY0NTMwNjc1NzM5MTMyODMwMzMxOTk1NjA1NDExODM5NTk1MzExNDgzMTMxMDg0MjgwOTc0OTk3NTU5MTY2MDIxNTQ2NDEwNTU4MDkyNTEzODI2MDIzMzMyNDA1OTUyMDA1MjQ2Njk1MTQwMTAyODc1OTQ3MzY0NTk4MjE2ODE4ODE5MDE3NjIyODAwNDg1NDQ3NTk4NTk0MjAxNjMwOTk5Mjg3ODc2ODgwMTk1NDA1NzQ4Mzc0Njk0NTg3MzU3MDI5NTg4MjQ4ODQzMTAzMTE2NjEzNjgwODYyNDEzNDE1MzgyMDYzNDMwMTQzODIwMzcxNTIyODU0MTA2MTg0MzUyMDQ0OTgzNjYzMzE3NDY5OTM1NzY5MzUwNTk4NDg1MjU2NTQzNDA0MzEyOTQ4MjIyMDc0MTkwNDQ1NDA4MDA1MTMyODMzODE1MzAwMTE5NjY3MDk0ODk5MDg5NjU4ODMwNzE0NDQxODMzNDI2NTgzMTQ1OTUwMjg4OTkzOTY4MDMxMjUzNDg5ODQ3MjY0NTAwNDEwMzI4NTYzMjgyNzU2MDkyNzE3MTQwNDcyMDg5ODg1MTMyOTQxNzYwNTQ1MzYzNDgzMTQ1MjEwNTg4MCJdLCBbImtvcmVhbl9uYW1lIiwgIjE3ODg5MDUxMzAxMTEyNjU5MDQ0NTY1NzQ3NDAxMjQ5MjIzOTY1NjUyMTUzNTQyMDQzODA0NDk1Njc1NzUxOTk2MzMwNTMxMjcxNjAxNzQyMzQ3NDAzMzQxNjkxNjM1MzMyMzQwOTgxNDg0NjY3NDIyOTIzOTI2NzQ5ODQ0MTI5NTU5MjgyOTUwMTE4OTc5MTQzOTA4MjUzNjY4ODM2NzA3MDQ1NjIyNDQwMTk4MTIwNDE1MjMyMzc5OTA5MjY0NzExODkwNzgzNjU4NDg0ODg4MzMyNzAzMTE0MzUzOTQwMjYxODEyMzI4NjQ2OTgxMDgyMTE1Njg0OTQ0MTM4NTQzNTMzNTk1MDE1NDU1OTcyMjgzMjM4MDc2Njc2MDY3NTMwOTQ4Mjk5NTM2NjA3MzAwMjEyOTYwMjk2MTk1NjczNzAyMTk3MDE2ODI4Mjk2NTUzMTMxNTMwMTc1Mjc4MzgxMjQ0NTU3ODk3NjcwMzYyNTIxNzgzMDI2Mjk0NjU1MDc0MjY2MTAyODYzNjQxNjQ2ODE0OTUzNTMxNDUyNTExNjQ1NTcwODIzNzU5NTgxOTcxNjk3MzMyNTM2MzMzNzI3NDE0NDc0MzAyNTUzMjMwNDMyNzgyNDQyNjU0NjE4MjY4MDI2OTc2ODA1NjUxNjg1NjE1MTk3Nzk0MzA5OTA0NzA0NjM1OTU1ODYzOTA1MzEwNDE3Mzc3ODk5MTM0NzAyOTUwNTcxMDQ0NzYzNjU0NDEyOTM0OTMzODUxMjAzMzk1MDMxODQ4NDMzMDI1MjI0NTU2MjI4MDE5OTk2MzQzNjg0MjA1NTEzNDUyOTAyNzAxNzQyMzg3NTg0Mzc1ODY1ODIwMzQ5NzQ2NjI3MjEyOTU2NzUzMjg3NDI2NzYyNTU4NjM1MTgwMTcwMTIxMDI3NDE3NTQiXSwgWyJzY29yZV9vZl90b3RhbCIsICIxMTMxNDA4MjI5NjIxMTIwMzQxNDE5NzE0MzYyNTk2Mzk3MjU3NDM3MDEwMzU2NzYxOTcwODMxMTE3MTU5NDc5NDYyNjQ0OTA5MDkyNjA3MjYzMDgyNzI4NTAwMjI0NjYzMjg3NDMyNzkzNjg3MjA2OTI0NjU2OTM0NjIzMDA4NTQ5NDYxMjkyMTQzMzQ5OTk0OTY1MDE0OTk2MjIwNTQ5NjAwODUwMTcxMzQ3Mzk5NDI1OTk1NzMxODY5MjA2MjA5NzIzMTIxODM2MTQ2MjkxMzAzODUyNjM0MTE5OTkzNjU2NTMxMzc5OTEzOTQ5OTg1MzY3MjY4NDE4OTk2OTQxMjkyMDczNDM5NTkwNjI4ODY1MDQ5MjY0NTc1Mzc0MzY4MzMxNjg0MDU5NjM0MTE2MzA1ODMwODc4MDA3NTA1NDY0NDgxNTg5MDk3NTI0Mzc3NzMxMTA1NTkwNTI0MDk5NDYxMjE4OTc1NDI2Njc3MTI3MjM0NDA4NzAxMTU5NzIxODMwMzE2MTcwNzg5MzYxNDc3OTI1NTI1MDk3MDgwNzUzMTE0NTE2Nzg5NzY3ODEzMTI4MDQxODQ3Mzk3NDMwNTIzMTYzNzMzODcxMDA4MDA2MzE4MDAwMzk0MjUxNjIwNzA0MDA3MDIzMzExODg2Njg2NDg5MjEzODU1MzYyMjg3MDEyNTE5NTI0MzQ1OTE4OTcxNjA1MDIyMDYyNzU1MTUwODY5NTExMDM2MDI3Mzg1MTIzMjE3NjQ0NzU0NzQwNTE3MTI1MTQwNjE4ODc5Mjg2NjM3NzUyMzAzMjQ5Mzg2NjIxNDUwMzEzODQwODEwNjAxMzg5NTI2NDYwMTE0MzA3MjM5MjU0NTA5OTg3NzI1NTczNDAwNDQ4NDAwNDg5OTM3NjI4NzIwMzQzMjA5Mzk1MDg5Il0sIFsicmVnaXN0cmF0aW9uX251bWJlciIsICIyMjQ3MDAxNDE4MzQ3NTgyMjcwOTg4NzI5ODUzNzk4NDE2NjQ0MzE5NDczOTA3NDU2ODYyMjAwODEwOTIzMTQ4MDMyNTcwNzI0NDQ1OTg5MDQ5NDY3NDA4NzgzOTI5NTI1MzE5MDg4Nzk2NTI4ODQ2MDc0MzAwMjk4MTY5MDk1ODMyMDA4MDkwMzQ4NzY1MDcxODE5OTAzNTkyMjU5MDU0NTM5ODI0MTA3NTQ4Mzk2MDU0ODMxNjIwMTU2MDY1Mzk3MzUzNTY0MzE0MDc1ODMyNjA2MjEwMDAyOTc3NDE0MDMxODA3MTkwODAyMDM2NjE0NzExOTYyNzUzMDQ1NDU5MzAyMDU1OTk1NDgyNzExMjAyNDc2MjY0OTEzNzI5MDAxMDUzNzQyMzgxNTEzNjI3OTY3MjM3ODQ3NTY0ODc5MzEwNDUwMjk2NTIyNjU2MTUwOTMyMTkwNTg3MDg5NDk3NDA1NzcyNTE1NDYzMTQ2MjQyOTk0NDQ0Nzc0MTA0ODgyNTY4MTI2NzYwMjAxNDM4NjE1MDcwNDU1MTc4MDIxOTM5NjUyNjI5NTY3NzAwMzY5NzIwODMxNDAwOTQ1NjcwMTU1NjExNTU2MDc5ODc0MDIwMDYzMTkxNjQ4MzY1NzAzNTYxODI1NDkxODcyOTMzMDExMTQyNjU0MzI3ODE1MjU4NjU5MTE3MDUwMDk2NTg4MzgwODk4Njg2MDU4ODAwOTA4NzA3NjA3MDQ5MTM3MDA3MjM4NDUyNTg5NDM5MTI2Mjk0NTc3MzA2NTIzMzc3ODM1NTY3NjM2NzgzMjcwNDMzOTMxOTY5OTA3MTU1MjQzODgxNjUwMjEzNTUyOTcxNzAwMDQwNjM5MTAyMjY0Mjg5MDE4MTU3NjI4NjgxMDQ2NDAxNzUyOTU1OTg2Nzk3OTMzMjE4Il0sIFsiZGF0ZV9vZl9iaXJ0aCIsICIxNjIyMDM2MzYxNTMxNjAxOTEyMjUxMDg0ODIwNDE4NzAyODkxNjAxMDQ3NzgyNjM3NjI1MTc1MTM4ODI1NTcyNjA1NzMzMzkwNjU3MzcwNDI1OTA4Mjc2ODQ3NzAxNTM3NTk4OTgwMjczNjA0MzgyMDcwNDU2NTg3ODQ1MTgyMjAwOTExNTUwNDEwNDk2MDIxMTEyOTM4MTE2ODE3NDY5MzI4MTIyNzQwMzQzNDY1ODQyMTQ1NTE3NTIzMDY4NDI2MzMwNzk3MTAyODU3MTc0NjgwMjE3NzMwNzQyNjM2MjgwOTYyNzAyNjc4MjE0MDEyMjEyMDU1MTc5NzgxMDU4OTM1MjUzNTg5OTczMDU3OTkzMTgyMjk0MzI1Nzc3Mzc3MjI0MTgzOTkyNjAzOTcwNDA4NzM3ODQ4MjY1NjA4NTU3ODE5Nzc3NjU5NTI5MTQ2MDI5OTE3Mzg0MDMyODQ0NTMxNDA4NjQ3Mzg5NjQzODI2NTA2Nzk3ODk2OTg2MDQ2NjYxNDU3NTUyMTc1MzM5MTEzNTMyMTc2OTM4MzI4NDY0MzgzMjAxMDAwOTg3MzQzMjYzNDI5MDY5NzA1MDIzMDU5MTQxMzE5NTg1MjA1MzU0Mjc3NTcwMDc1MTgxNzMwMjUwODgzNzg2NDYyNzg2NDQ2Mzg1MTYwMjg1MTA2NzI2MzY3MzE4MzEyNTY5NjIzMzk0Mzc1MDg3NTA0NDAwODA4Mjc5ODg3OTE3NjAzMTE4ODAzODE5NDkxNDIyMzcyNTA4MTYzMzQ5NTcyMDAwMDIwNTc4MDI0NDk0MjQ0NTkyMzAzOTE4NzE0ODkwNzI2NjgyNjEwMDcyNzg4MDM4Mjg1Nzg4MDAzNTkwMTkxNjM5ODI3OTEzNDY0MDk1NjY5MTU5MjE5NzY4MjYwNjQyNDk0NzY3Il0sIFsic2NvcmVfb2ZfbGlzdGVuaW5nIiwgIjEyMDIwNDMxODY1NDEwMDAwNzcwNjczMTg4OTQ2NjU5NDQ0ODE0ODk2NjgxMzUwMjIxMzQzMTQ0NDM0MDQ0MzAzOTYyODY4OTk0NjA3MTI3MTYyOTY3NTY0MTAzMTQzNzk4MDkwMTI0MTY5NDEwNDkyODczMjgxNjMzNDY2MzE3NzM1OTYwMTIwMjk1NTUxNDIzNzg3ODQ1ODAxMTk1OTYzNDQ0NTUxMTg3MzU5MjY1MTE5MjM0MDc1MzU2MDU5NTI4ODUwMjAzMjU0NzU5NTYzNTE5MjU0NTMyNjQ3OTAyMjY1NTk2ODY0NjA3MjE0NDU2Mzk1MDk4MjQxNzYxNTEyOTUyNDE4NDQyNjE0MjQ4NjQ5MTY4OTQwMTAyNjIwODAzMTQ0MjM0NTMxNTY0OTY3NDA3NjE5MDE5NDgzMjM5OTQ3MzI2MTMyNzYwOTM3NTY0NzIzODMzNDgzMzMzMDA3ODI2Njg1OTU2ODU2OTc1MjU5NTY5NDI1MjIyNTcwMDYzMzc1NTQyMTA3NDMzMTk4NTA1MDY0MDcyMTU0MzI2MjY1NjAwMjcyNjc1NDgzNTI3Mzc1MzYwMTA3MDQyMjg5ODAwNzY5MjY3MTg1MTk1Njk2NDQ5MDA2MDAzODMxODkwNDk0Nzc2MjU4NjQ2ODcwMTE5MjY0NDA3NDYzMjA2MTg4MTA2NDQ3NTQyNDUxNTQ2NTA1MjAyMTgzNDkzNzYwNTgwMjU4MDU0NTU2NTMyMjAwMjM0NDM5MjM5MTUxNzAwMjgzMDg0OTE5MTU3MzA5NTQ4MzQ3NTE3MDYzMzQyNzk0ODE5NDczODcyODQ5NzA2NDMwNDk0Mzg2MjYzNzI2NTkwNjkyMDA2MjAxMzY5NjE2MjA4MDc3MzYzMjAyNjY1MDg5MzU2MDg3Nzg4ODExMjg0MDUiXSwgWyJtYXN0ZXJfc2VjcmV0IiwgIjc1MDU4NDYyMTM5ODU2NzA0MTI1NjU0Mzk3NDk0MjU0MTk5ODc0NTc3MTY4MTMzNTE3NTc4NTA0ODE0MDU0NTg4NzA1NDAzMzc2MTQ2NTMxMTgwNTE2MzA1MTExMDQ1NzQ4NTg2NjU3ODUwNjM5OTQ4NDYyODk4NTQ4MzA5NjI4NjgxMjI0NTQzNzg2NjcxMTg0NzY5MjU0MjM4OTM5NTgwNjUwNjIxMDM0MzYwMzY0MDk3OTc4NDY4NjM4OTYyNjgwMDA0NDQ4ODIxODIzMTc3NTI1NzE5NzE4NDk5NzY2ODg2NjQ4NzgzNDc4NTA2OTkxMzQ3Njg0NDczMDQwNzY0MTkyMzg2MDEwNTA0MjAzNTM5MTQ2MDE4NzMzNDk1OTg1MTIzMDc5OTc1NjUzNTg4MTI4OTg0MTI0ODUzNjcxODQ1MzMxMjU0NjcwNDc0NDk5MDY1MDEwMTk3MzA4NTMzNzI4MDQ3Nzc3MDkyMzU5NDUwNDgzMjYyNzYzNTQwNzIxOTc4Njk2MjgxNzA2NDQwMzYzNTM0MjE1NzI5NDM3MDQ1OTAwNDEzNDc4ODUwOTY4OTA2Nzg2ODE0MTAzODA4NTE5MDI1NzI5MDYwODg4MTAxMDgyNjcxMTQ0NjAxMzE5ODgyMDk4NDI1NDI3NTIzMDc2NDY3MDc2MzAxNzcwMDU5MjU1ODM0NTgxOTYyMTY0MDE1NzM4NDg4Nzg4NzI3MTIzMjU2Mjc4Mjg5MjQwNjk5NzA0NzE3OTc5ODYyMDM0MjY3NzU0OTM1MzUyNzA2MjM0NjE1MjAzMzM1OTc1NDQ0MTYyMTkwNjY1Nzg0MDk2ODUwNjg4NzA2ODU0NDMxMjA0NjIzMDU4MTc2ODg2Mzg1MzczNzMwNTkzMzY3NTYwNzEyNDE1ODUxMjkwODczMjEzIl1dfSwgIm5vbmNlIjogIjE5NTY5NTIyMjgxMDEwMjI3MTIyNjA3NyJ9"
        }
      }
    ]
  },
  "state": "offer_sent",
  "connection_id": "2a6ed506-9577-46ee-a60c-726241f89aec",
  "thread_id": "774db754-4182-4cb7-b02b-0af0e0e5c3ae",
  "initiator": "self",
  "role": "issuer",
  "created_at": "2021-05-27 13:47:00.702727Z",
  "auto_issue": true,
  "schema_id": "N6r4nLwAkcYUX8c8Kb8Ufu:2:CertificateOfTOEIC:4.0",
  "auto_offer": false,
  "credential_offer": {
    "schema_id": "N6r4nLwAkcYUX8c8Kb8Ufu:2:CertificateOfTOEIC:4.0",
    "cred_def_id": "DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "key_correctness_proof": {
      "c": "115309207665428406179545994373912181282831545118540014229967271707896224931550",
      "xz_cap": "1055572769862663463709934204601954080483898826030879595979074971004206859393782655599324140913902653650398938906681641924390794812738887811895545711063564481179172330881094952438950836326342830845967987855265191783565688169975863324545545315781669024596986257143061525264896458486625431280764566858010248626346811818332553348994255109430053940452222330296721244130109139739905819674198123825423697014194656630395527533035580919935737499589831966659250863798261759416779236747674116899991548520783007432182205649700587358695322105915051483830246135518385554263531337387626704587405669968771421880306850156048748573730575735016160622706605885093644782532647373659196784245832723085213497048989520",
      "xr_cap": [
        [
          "date_of_test",
          "1076232159249964734109718589777267462109484070440415691511826521589528195960117006601596499833881106787532908582735685947125303637779749865395973748786845381125614276185776325521284507979022202765015734531668494478627404644674922115351392810935336529179108446362157123315565260725483035159546977982636268273741134447625656913083049471163863325580545751281281707239030011491905375673521635790717301188716989419245594425667897372137887632091257692992002204353172141035532686819956655464274728888166233800730209627627333118372176190108581819028962383487619291845020994081912208148061482241150491798630105559860464292100987650906949548228012908099036234969978091590577056859814205598867632927950715"
        ],
        [
          "english_name",
          "696411130939580533510436664194526819156095629287412434045238350804750458875252009159837208667096357708273898836774596569571826421416373675758153437224961347803003587328774238645279718011305234430510914339030739772141034937822610601894100092611699051587368572476378066571995197160308770830331651684157422321494210565622254936179050256196395839836450739708603193285896785801340066230480042341954666620701121906593630655998864008598194498052803215598246923716961827765415427467725639778399843178991934315901785927203964183244619307158020733817516419875099846927356800929807208713102941594049577311851970296265305274235642059800993937805593444602673622260362361553200010326744684880113343773224856"
        ],
        [
          "exp_date",
          "2423650123164905529363621137809723145327867542753048760610730674284755739976356487466267577397713669927824555718078674729329822136623528495004922057741935984832762937882953798007838316207944508000224331385996443269580772498769868565007610503103086465320921094682281609628869906334746079832967350907986925385591867010527607177795249423358544981842772106056300659484323947189052758098052717291821625945625196039363888578326940371229699672363219264534873802852476718738347302929940470298789876886196910464381568220032501183416349848143988173727155194728720864205189777283305349845531272422805228252687727992182026982614462202644856280264034450814786604483296092056699991544749097728868483744446331"
        ],
        [
          "score_of_reading",
          "2263886014163696590877718819103455141839764106832768596959484972522489575027017888376981568132641923366850648287840153912124367275371474769790500423524604919564238092857224278300534000897636801852705809941046581775589751860522217490645306757391328303319956054118395953114831310842809749975591660215464105580925138260233324059520052466951401028759473645982168188190176228004854475985942016309992878768801954057483746945873570295882488431031166136808624134153820634301438203715228541061843520449836633174699357693505984852565434043129482220741904454080051328338153001196670948990896588307144418334265831459502889939680312534898472645004103285632827560927171404720898851329417605453634831452105880"
        ],
        [
          "korean_name",
          "1788905130111265904456574740124922396565215354204380449567575199633053127160174234740334169163533234098148466742292392674984412955928295011897914390825366883670704562244019812041523237990926471189078365848488833270311435394026181232864698108211568494413854353359501545597228323807667606753094829953660730021296029619567370219701682829655313153017527838124455789767036252178302629465507426610286364164681495353145251164557082375958197169733253633372741447430255323043278244265461826802697680565168561519779430990470463595586390531041737789913470295057104476365441293493385120339503184843302522455622801999634368420551345290270174238758437586582034974662721295675328742676255863518017012102741754"
        ],
        [
          "score_of_total",
          "1131408229621120341419714362596397257437010356761970831117159479462644909092607263082728500224663287432793687206924656934623008549461292143349994965014996220549600850171347399425995731869206209723121836146291303852634119993656531379913949985367268418996941292073439590628865049264575374368331684059634116305830878007505464481589097524377731105590524099461218975426677127234408701159721830316170789361477925525097080753114516789767813128041847397430523163733871008006318000394251620704007023311886686489213855362287012519524345918971605022062755150869511036027385123217644754740517125140618879286637752303249386621450313840810601389526460114307239254509987725573400448400489937628720343209395089"
        ],
        [
          "registration_number",
          "2247001418347582270988729853798416644319473907456862200810923148032570724445989049467408783929525319088796528846074300298169095832008090348765071819903592259054539824107548396054831620156065397353564314075832606210002977414031807190802036614711962753045459302055995482711202476264913729001053742381513627967237847564879310450296522656150932190587089497405772515463146242994444774104882568126760201438615070455178021939652629567700369720831400945670155611556079874020063191648365703561825491872933011142654327815258659117050096588380898686058800908707607049137007238452589439126294577306523377835567636783270433931969907155243881650213552971700040639102264289018157628681046401752955986797933218"
        ],
        [
          "date_of_birth",
          "1622036361531601912251084820418702891601047782637625175138825572605733390657370425908276847701537598980273604382070456587845182200911550410496021112938116817469328122740343465842145517523068426330797102857174680217730742636280962702678214012212055179781058935253589973057993182294325777377224183992603970408737848265608557819777659529146029917384032844531408647389643826506797896986046661457552175339113532176938328464383201000987343263429069705023059141319585205354277570075181730250883786462786446385160285106726367318312569623394375087504400808279887917603118803819491422372508163349572000020578024494244592303918714890726682610072788038285788003590191639827913464095669159219768260642494767"
        ],
        [
          "score_of_listening",
          "1202043186541000077067318894665944481489668135022134314443404430396286899460712716296756410314379809012416941049287328163346631773596012029555142378784580119596344455118735926511923407535605952885020325475956351925453264790226559686460721445639509824176151295241844261424864916894010262080314423453156496740761901948323994732613276093756472383348333300782668595685697525956942522257006337554210743319850506407215432626560027267548352737536010704228980076926718519569644900600383189049477625864687011926440746320618810644754245154650520218349376058025805455653220023443923915170028308491915730954834751706334279481947387284970643049438626372659069200620136961620807736320266508935608778881128405"
        ],
        [
          "master_secret",
          "75058462139856704125654397494254199874577168133517578504814054588705403376146531180516305111045748586657850639948462898548309628681224543786671184769254238939580650621034360364097978468638962680004448821823177525719718499766886648783478506991347684473040764192386010504203539146018733495985123079975653588128984124853671845331254670474499065010197308533728047777092359450483262763540721978696281706440363534215729437045900413478850968906786814103808519025729060888101082671144601319882098425427523076467076301770059255834581962164015738488788727123256278289240699704717979862034267754935352706234615203335975444162190665784096850688706854431204623058176886385373730593367560712415851290873213"
        ]
      ]
    },
    "nonce": "195695222810102271226077"
  },
  "credential_exchange_id": "08c6da17-8c09-4b03-bd53-c5cb197fd60c",
  "credential_proposal_dict": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
    "@id": "0118389d-7569-44c5-9bf9-a35bc07ac438",
    "comment": "consequat anim minim aliquip",
    "credential_proposal": {
      "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes": [
        {
          "name": "date_of_birth",
          "value": "20000228"
        },
        {
          "name": "date_of_test",
          "value": "20180228'"
        },
        {
          "name": "english_name",
          "value": "Kim Initial"
        },
        {
          "name": "exp_date",
          "value": "20180228"
        },
        {
          "name": "korean_name",
          "value": "김증명"
        },
        {
          "name": "registration_number",
          "value": "123456789"
        },
        {
          "name": "score_of_listening",
          "value": "445"
        },
        {
          "name": "score_of_reading",
          "value": "445"
        },
        {
          "name": "score_of_total",
          "value": "990"
        }
      ]
    },
    "cred_def_id": "DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd"
  },
  "auto_remove": false,
  "trace": false,
  "credential_definition_id": "DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd"
}
```
<p></p>


### STEP 2. 사용자 : Issuer(발급기관)의 Credential offer 확인. 

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 Holder가 initial app 일 경우 SDK에서 자동으로 처리됨. <br> initial App 사용자 화면에는 발급하기전 미리보기 화면이 출력  </p>
</div>

<br><br>

### STEP 3. 사용자 → 기관 : Issuer(발급기관)에게 Credential Request

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP3는 initial app SDK에서 자동으로 처리되고, 발급자는 Webhook Message를 전달 받고 자동 처림 됨 </p>
</div>

- 아래와 같은 Webhook Event가 전달 됨.(state : `request_received`) 

```json
{
  "schema_id":"N6r4nLwAkcYUX8c8Kb8Ufu:2:CertificateOfTOEIC:4.0",
  "auto_issue":true,
  "trace":false,
  "credential_offer_dict":{
    "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/offer-credential",
    "@id":"98b5ffd6-b7e4-4c56-a58d-68a11dc21f97",
    "~thread":{
      "thid":"217c6a09-eb41-4f0e-bad2-981be4622759"
    },
    "offers~attach":[
      {
        "@id":"libindy-cred-offer-0",
        "mime-type":"application/json",
        "data":{
          "base64":"eyJzY2hlbWFfaWQiOiAiTjZyNG5Md0FrY1lVWDhjOEtiOFVmdToyOkNlcnRpZmljYXRlT2ZUT0VJQzo0LjAiLCAiY3JlZF9kZWZfaWQiOiAiRHJMYlhGU2FvNFZvOGdNZmp4UHhVMTozOkNMOjE2MTc2OTgyMzg6ODFkZjAwMTAtNjJiNC00NWIxLWJkMDAtOGQwYWQ3NDc2MmZkIiwgImtleV9jb3JyZWN0bmVzc19wcm9vZiI6IHsiYyI6ICIxMTUzMDkyMDc2NjU0Mjg0MDYxNzk1NDU5OTQzNzM5MTIxODEyODI4MzE1NDUxMTg1NDAwMTQyMjk5NjcyNzE3MDc4OTYyMjQ5MzE1NTAiLCAieHpfY2FwIjogIjEwNTU1NzI3Njk4NjI2NjM0NjM3MDk5MzQyMDQ2MDE5NTQwODA0ODM4OTg4MjYwMzA4Nzk1OTU5NzkwNzQ5NzEwMDQyMDY4NTkzOTM3ODI2NTU1OTkzMjQxNDA5MTM5MDI2NTM2NTAzOTg5Mzg5MDY2ODE2NDE5MjQzOTA3OTQ4MTI3Mzg4ODc4MTE4OTU1NDU3MTEwNjM1NjQ0ODExNzkxNzIzMzA4ODEwOTQ5NTI0Mzg5NTA4MzYzMjYzNDI4MzA4NDU5Njc5ODc4NTUyNjUxOTE3ODM1NjU2ODgxNjk5NzU4NjMzMjQ1NDU1NDUzMTU3ODE2NjkwMjQ1OTY5ODYyNTcxNDMwNjE1MjUyNjQ4OTY0NTg0ODY2MjU0MzEyODA3NjQ1NjY4NTgwMTAyNDg2MjYzNDY4MTE4MTgzMzI1NTMzNDg5OTQyNTUxMDk0MzAwNTM5NDA0NTIyMjIzMzAyOTY3MjEyNDQxMzAxMDkxMzk3Mzk5MDU4MTk2NzQxOTgxMjM4MjU0MjM2OTcwMTQxOTQ2NTY2MzAzOTU1Mjc1MzMwMzU1ODA5MTk5MzU3Mzc0OTk1ODk4MzE5NjY2NTkyNTA4NjM3OTgyNjE3NTk0MTY3NzkyMzY3NDc2NzQxMTY4OTk5OTE1NDg1MjA3ODMwMDc0MzIxODIyMDU2NDk3MDA1ODczNTg2OTUzMjIxMDU5MTUwNTE0ODM4MzAyNDYxMzU1MTgzODU1NTQyNjM1MzEzMzczODc2MjY3MDQ1ODc0MDU2Njk5Njg3NzE0MjE4ODAzMDY4NTAxNTYwNDg3NDg1NzM3MzA1NzU3MzUwMTYxNjA2MjI3MDY2MDU4ODUwOTM2NDQ3ODI1MzI2NDczNzM2NTkxOTY3ODQyNDU4MzI3MjMwODUyMTM0OTcwNDg5ODk1MjAiLCAieHJfY2FwIjogW1siZGF0ZV9vZl90ZXN0IiwgIjEwNzYyMzIxNTkyNDk5NjQ3MzQxMDk3MTg1ODk3NzcyNjc0NjIxMDk0ODQwNzA0NDA0MTU2OTE1MTE4MjY1MjE1ODk1MjgxOTU5NjAxMTcwMDY2MDE1OTY0OTk4MzM4ODExMDY3ODc1MzI5MDg1ODI3MzU2ODU5NDcxMjUzMDM2Mzc3Nzk3NDk4NjUzOTU5NzM3NDg3ODY4NDUzODExMjU2MTQyNzYxODU3NzYzMjU1MjEyODQ1MDc5NzkwMjIyMDI3NjUwMTU3MzQ1MzE2Njg0OTQ0Nzg2Mjc0MDQ2NDQ2NzQ5MjIxMTUzNTEzOTI4MTA5MzUzMzY1MjkxNzkxMDg0NDYzNjIxNTcxMjMzMTU1NjUyNjA3MjU0ODMwMzUxNTk1NDY5Nzc5ODI2MzYyNjgyNzM3NDExMzQ0NDc2MjU2NTY5MTMwODMwNDk0NzExNjM4NjMzMjU1ODA1NDU3NTEyODEyODE3MDcyMzkwMzAwMTE0OTE5MDUzNzU2NzM1MjE2MzU3OTA3MTczMDExODg3MTY5ODk0MTkyNDU1OTQ0MjU2Njc4OTczNzIxMzc4ODc2MzIwOTEyNTc2OTI5OTIwMDIyMDQzNTMxNzIxNDEwMzU1MzI2ODY4MTk5NTY2NTU0NjQyNzQ3Mjg4ODgxNjYyMzM4MDA3MzAyMDk2Mjc2MjczMzMxMTgzNzIxNzYxOTAxMDg1ODE4MTkwMjg5NjIzODM0ODc2MTkyOTE4NDUwMjA5OTQwODE5MTIyMDgxNDgwNjE0ODIyNDExNTA0OTE3OTg2MzAxMDU1NTk4NjA0NjQyOTIxMDA5ODc2NTA5MDY5NDk1NDgyMjgwMTI5MDgwOTkwMzYyMzQ5Njk5NzgwOTE1OTA1NzcwNTY4NTk4MTQyMDU1OTg4Njc2MzI5Mjc5NTA3MTUiXSwgWyJlbmdsaXNoX25hbWUiLCAiNjk2NDExMTMwOTM5NTgwNTMzNTEwNDM2NjY0MTk0NTI2ODE5MTU2MDk1NjI5Mjg3NDEyNDM0MDQ1MjM4MzUwODA0NzUwNDU4ODc1MjUyMDA5MTU5ODM3MjA4NjY3MDk2MzU3NzA4MjczODk4ODM2Nzc0NTk2NTY5NTcxODI2NDIxNDE2MzczNjc1NzU4MTUzNDM3MjI0OTYxMzQ3ODAzMDAzNTg3MzI4Nzc0MjM4NjQ1Mjc5NzE4MDExMzA1MjM0NDMwNTEwOTE0MzM5MDMwNzM5NzcyMTQxMDM0OTM3ODIyNjEwNjAxODk0MTAwMDkyNjExNjk5MDUxNTg3MzY4NTcyNDc2Mzc4MDY2NTcxOTk1MTk3MTYwMzA4NzcwODMwMzMxNjUxNjg0MTU3NDIyMzIxNDk0MjEwNTY1NjIyMjU0OTM2MTc5MDUwMjU2MTk2Mzk1ODM5ODM2NDUwNzM5NzA4NjAzMTkzMjg1ODk2Nzg1ODAxMzQwMDY2MjMwNDgwMDQyMzQxOTU0NjY2NjIwNzAxMTIxOTA2NTkzNjMwNjU1OTk4ODY0MDA4NTk4MTk0NDk4MDUyODAzMjE1NTk4MjQ2OTIzNzE2OTYxODI3NzY1NDE1NDI3NDY3NzI1NjM5Nzc4Mzk5ODQzMTc4OTkxOTM0MzE1OTAxNzg1OTI3MjAzOTY0MTgzMjQ0NjE5MzA3MTU4MDIwNzMzODE3NTE2NDE5ODc1MDk5ODQ2OTI3MzU2ODAwOTI5ODA3MjA4NzEzMTAyOTQxNTk0MDQ5NTc3MzExODUxOTcwMjk2MjY1MzA1Mjc0MjM1NjQyMDU5ODAwOTkzOTM3ODA1NTkzNDQ0NjAyNjczNjIyMjYwMzYyMzYxNTUzMjAwMDEwMzI2NzQ0Njg0ODgwMTEzMzQzNzczMjI0ODU2Il0sIFsiZXhwX2RhdGUiLCAiMjQyMzY1MDEyMzE2NDkwNTUyOTM2MzYyMTEzNzgwOTcyMzE0NTMyNzg2NzU0Mjc1MzA0ODc2MDYxMDczMDY3NDI4NDc1NTczOTk3NjM1NjQ4NzQ2NjI2NzU3NzM5NzcxMzY2OTkyNzgyNDU1NTcxODA3ODY3NDcyOTMyOTgyMjEzNjYyMzUyODQ5NTAwNDkyMjA1Nzc0MTkzNTk4NDgzMjc2MjkzNzg4Mjk1Mzc5ODAwNzgzODMxNjIwNzk0NDUwODAwMDIyNDMzMTM4NTk5NjQ0MzI2OTU4MDc3MjQ5ODc2OTg2ODU2NTAwNzYxMDUwMzEwMzA4NjQ2NTMyMDkyMTA5NDY4MjI4MTYwOTYyODg2OTkwNjMzNDc0NjA3OTgzMjk2NzM1MDkwNzk4NjkyNTM4NTU5MTg2NzAxMDUyNzYwNzE3Nzc5NTI0OTQyMzM1ODU0NDk4MTg0Mjc3MjEwNjA1NjMwMDY1OTQ4NDMyMzk0NzE4OTA1Mjc1ODA5ODA1MjcxNzI5MTgyMTYyNTk0NTYyNTE5NjAzOTM2Mzg4ODU3ODMyNjk0MDM3MTIyOTY5OTY3MjM2MzIxOTI2NDUzNDg3MzgwMjg1MjQ3NjcxODczODM0NzMwMjkyOTk0MDQ3MDI5ODc4OTg3Njg4NjE5NjkxMDQ2NDM4MTU2ODIyMDAzMjUwMTE4MzQxNjM0OTg0ODE0Mzk4ODE3MzcyNzE1NTE5NDcyODcyMDg2NDIwNTE4OTc3NzI4MzMwNTM0OTg0NTUzMTI3MjQyMjgwNTIyODI1MjY4NzcyNzk5MjE4MjAyNjk4MjYxNDQ2MjIwMjY0NDg1NjI4MDI2NDAzNDQ1MDgxNDc4NjYwNDQ4MzI5NjA5MjA1NjY5OTk5MTU0NDc0OTA5NzcyODg2ODQ4Mzc0NDQ0NjMzMSJdLCBbInNjb3JlX29mX3JlYWRpbmciLCAiMjI2Mzg4NjAxNDE2MzY5NjU5MDg3NzcxODgxOTEwMzQ1NTE0MTgzOTc2NDEwNjgzMjc2ODU5Njk1OTQ4NDk3MjUyMjQ4OTU3NTAyNzAxNzg4ODM3Njk4MTU2ODEzMjY0MTkyMzM2Njg1MDY0ODI4Nzg0MDE1MzkxMjEyNDM2NzI3NTM3MTQ3NDc2OTc5MDUwMDQyMzUyNDYwNDkxOTU2NDIzODA5Mjg1NzIyNDI3ODMwMDUzNDAwMDg5NzYzNjgwMTg1MjcwNTgwOTk0MTA0NjU4MTc3NTU4OTc1MTg2MDUyMjIxNzQ5MDY0NTMwNjc1NzM5MTMyODMwMzMxOTk1NjA1NDExODM5NTk1MzExNDgzMTMxMDg0MjgwOTc0OTk3NTU5MTY2MDIxNTQ2NDEwNTU4MDkyNTEzODI2MDIzMzMyNDA1OTUyMDA1MjQ2Njk1MTQwMTAyODc1OTQ3MzY0NTk4MjE2ODE4ODE5MDE3NjIyODAwNDg1NDQ3NTk4NTk0MjAxNjMwOTk5Mjg3ODc2ODgwMTk1NDA1NzQ4Mzc0Njk0NTg3MzU3MDI5NTg4MjQ4ODQzMTAzMTE2NjEzNjgwODYyNDEzNDE1MzgyMDYzNDMwMTQzODIwMzcxNTIyODU0MTA2MTg0MzUyMDQ0OTgzNjYzMzE3NDY5OTM1NzY5MzUwNTk4NDg1MjU2NTQzNDA0MzEyOTQ4MjIyMDc0MTkwNDQ1NDA4MDA1MTMyODMzODE1MzAwMTE5NjY3MDk0ODk5MDg5NjU4ODMwNzE0NDQxODMzNDI2NTgzMTQ1OTUwMjg4OTkzOTY4MDMxMjUzNDg5ODQ3MjY0NTAwNDEwMzI4NTYzMjgyNzU2MDkyNzE3MTQwNDcyMDg5ODg1MTMyOTQxNzYwNTQ1MzYzNDgzMTQ1MjEwNTg4MCJdLCBbImtvcmVhbl9uYW1lIiwgIjE3ODg5MDUxMzAxMTEyNjU5MDQ0NTY1NzQ3NDAxMjQ5MjIzOTY1NjUyMTUzNTQyMDQzODA0NDk1Njc1NzUxOTk2MzMwNTMxMjcxNjAxNzQyMzQ3NDAzMzQxNjkxNjM1MzMyMzQwOTgxNDg0NjY3NDIyOTIzOTI2NzQ5ODQ0MTI5NTU5MjgyOTUwMTE4OTc5MTQzOTA4MjUzNjY4ODM2NzA3MDQ1NjIyNDQwMTk4MTIwNDE1MjMyMzc5OTA5MjY0NzExODkwNzgzNjU4NDg0ODg4MzMyNzAzMTE0MzUzOTQwMjYxODEyMzI4NjQ2OTgxMDgyMTE1Njg0OTQ0MTM4NTQzNTMzNTk1MDE1NDU1OTcyMjgzMjM4MDc2Njc2MDY3NTMwOTQ4Mjk5NTM2NjA3MzAwMjEyOTYwMjk2MTk1NjczNzAyMTk3MDE2ODI4Mjk2NTUzMTMxNTMwMTc1Mjc4MzgxMjQ0NTU3ODk3NjcwMzYyNTIxNzgzMDI2Mjk0NjU1MDc0MjY2MTAyODYzNjQxNjQ2ODE0OTUzNTMxNDUyNTExNjQ1NTcwODIzNzU5NTgxOTcxNjk3MzMyNTM2MzMzNzI3NDE0NDc0MzAyNTUzMjMwNDMyNzgyNDQyNjU0NjE4MjY4MDI2OTc2ODA1NjUxNjg1NjE1MTk3Nzk0MzA5OTA0NzA0NjM1OTU1ODYzOTA1MzEwNDE3Mzc3ODk5MTM0NzAyOTUwNTcxMDQ0NzYzNjU0NDEyOTM0OTMzODUxMjAzMzk1MDMxODQ4NDMzMDI1MjI0NTU2MjI4MDE5OTk2MzQzNjg0MjA1NTEzNDUyOTAyNzAxNzQyMzg3NTg0Mzc1ODY1ODIwMzQ5NzQ2NjI3MjEyOTU2NzUzMjg3NDI2NzYyNTU4NjM1MTgwMTcwMTIxMDI3NDE3NTQiXSwgWyJzY29yZV9vZl90b3RhbCIsICIxMTMxNDA4MjI5NjIxMTIwMzQxNDE5NzE0MzYyNTk2Mzk3MjU3NDM3MDEwMzU2NzYxOTcwODMxMTE3MTU5NDc5NDYyNjQ0OTA5MDkyNjA3MjYzMDgyNzI4NTAwMjI0NjYzMjg3NDMyNzkzNjg3MjA2OTI0NjU2OTM0NjIzMDA4NTQ5NDYxMjkyMTQzMzQ5OTk0OTY1MDE0OTk2MjIwNTQ5NjAwODUwMTcxMzQ3Mzk5NDI1OTk1NzMxODY5MjA2MjA5NzIzMTIxODM2MTQ2MjkxMzAzODUyNjM0MTE5OTkzNjU2NTMxMzc5OTEzOTQ5OTg1MzY3MjY4NDE4OTk2OTQxMjkyMDczNDM5NTkwNjI4ODY1MDQ5MjY0NTc1Mzc0MzY4MzMxNjg0MDU5NjM0MTE2MzA1ODMwODc4MDA3NTA1NDY0NDgxNTg5MDk3NTI0Mzc3NzMxMTA1NTkwNTI0MDk5NDYxMjE4OTc1NDI2Njc3MTI3MjM0NDA4NzAxMTU5NzIxODMwMzE2MTcwNzg5MzYxNDc3OTI1NTI1MDk3MDgwNzUzMTE0NTE2Nzg5NzY3ODEzMTI4MDQxODQ3Mzk3NDMwNTIzMTYzNzMzODcxMDA4MDA2MzE4MDAwMzk0MjUxNjIwNzA0MDA3MDIzMzExODg2Njg2NDg5MjEzODU1MzYyMjg3MDEyNTE5NTI0MzQ1OTE4OTcxNjA1MDIyMDYyNzU1MTUwODY5NTExMDM2MDI3Mzg1MTIzMjE3NjQ0NzU0NzQwNTE3MTI1MTQwNjE4ODc5Mjg2NjM3NzUyMzAzMjQ5Mzg2NjIxNDUwMzEzODQwODEwNjAxMzg5NTI2NDYwMTE0MzA3MjM5MjU0NTA5OTg3NzI1NTczNDAwNDQ4NDAwNDg5OTM3NjI4NzIwMzQzMjA5Mzk1MDg5Il0sIFsicmVnaXN0cmF0aW9uX251bWJlciIsICIyMjQ3MDAxNDE4MzQ3NTgyMjcwOTg4NzI5ODUzNzk4NDE2NjQ0MzE5NDczOTA3NDU2ODYyMjAwODEwOTIzMTQ4MDMyNTcwNzI0NDQ1OTg5MDQ5NDY3NDA4NzgzOTI5NTI1MzE5MDg4Nzk2NTI4ODQ2MDc0MzAwMjk4MTY5MDk1ODMyMDA4MDkwMzQ4NzY1MDcxODE5OTAzNTkyMjU5MDU0NTM5ODI0MTA3NTQ4Mzk2MDU0ODMxNjIwMTU2MDY1Mzk3MzUzNTY0MzE0MDc1ODMyNjA2MjEwMDAyOTc3NDE0MDMxODA3MTkwODAyMDM2NjE0NzExOTYyNzUzMDQ1NDU5MzAyMDU1OTk1NDgyNzExMjAyNDc2MjY0OTEzNzI5MDAxMDUzNzQyMzgxNTEzNjI3OTY3MjM3ODQ3NTY0ODc5MzEwNDUwMjk2NTIyNjU2MTUwOTMyMTkwNTg3MDg5NDk3NDA1NzcyNTE1NDYzMTQ2MjQyOTk0NDQ0Nzc0MTA0ODgyNTY4MTI2NzYwMjAxNDM4NjE1MDcwNDU1MTc4MDIxOTM5NjUyNjI5NTY3NzAwMzY5NzIwODMxNDAwOTQ1NjcwMTU1NjExNTU2MDc5ODc0MDIwMDYzMTkxNjQ4MzY1NzAzNTYxODI1NDkxODcyOTMzMDExMTQyNjU0MzI3ODE1MjU4NjU5MTE3MDUwMDk2NTg4MzgwODk4Njg2MDU4ODAwOTA4NzA3NjA3MDQ5MTM3MDA3MjM4NDUyNTg5NDM5MTI2Mjk0NTc3MzA2NTIzMzc3ODM1NTY3NjM2NzgzMjcwNDMzOTMxOTY5OTA3MTU1MjQzODgxNjUwMjEzNTUyOTcxNzAwMDQwNjM5MTAyMjY0Mjg5MDE4MTU3NjI4NjgxMDQ2NDAxNzUyOTU1OTg2Nzk3OTMzMjE4Il0sIFsiZGF0ZV9vZl9iaXJ0aCIsICIxNjIyMDM2MzYxNTMxNjAxOTEyMjUxMDg0ODIwNDE4NzAyODkxNjAxMDQ3NzgyNjM3NjI1MTc1MTM4ODI1NTcyNjA1NzMzMzkwNjU3MzcwNDI1OTA4Mjc2ODQ3NzAxNTM3NTk4OTgwMjczNjA0MzgyMDcwNDU2NTg3ODQ1MTgyMjAwOTExNTUwNDEwNDk2MDIxMTEyOTM4MTE2ODE3NDY5MzI4MTIyNzQwMzQzNDY1ODQyMTQ1NTE3NTIzMDY4NDI2MzMwNzk3MTAyODU3MTc0NjgwMjE3NzMwNzQyNjM2MjgwOTYyNzAyNjc4MjE0MDEyMjEyMDU1MTc5NzgxMDU4OTM1MjUzNTg5OTczMDU3OTkzMTgyMjk0MzI1Nzc3Mzc3MjI0MTgzOTkyNjAzOTcwNDA4NzM3ODQ4MjY1NjA4NTU3ODE5Nzc3NjU5NTI5MTQ2MDI5OTE3Mzg0MDMyODQ0NTMxNDA4NjQ3Mzg5NjQzODI2NTA2Nzk3ODk2OTg2MDQ2NjYxNDU3NTUyMTc1MzM5MTEzNTMyMTc2OTM4MzI4NDY0MzgzMjAxMDAwOTg3MzQzMjYzNDI5MDY5NzA1MDIzMDU5MTQxMzE5NTg1MjA1MzU0Mjc3NTcwMDc1MTgxNzMwMjUwODgzNzg2NDYyNzg2NDQ2Mzg1MTYwMjg1MTA2NzI2MzY3MzE4MzEyNTY5NjIzMzk0Mzc1MDg3NTA0NDAwODA4Mjc5ODg3OTE3NjAzMTE4ODAzODE5NDkxNDIyMzcyNTA4MTYzMzQ5NTcyMDAwMDIwNTc4MDI0NDk0MjQ0NTkyMzAzOTE4NzE0ODkwNzI2NjgyNjEwMDcyNzg4MDM4Mjg1Nzg4MDAzNTkwMTkxNjM5ODI3OTEzNDY0MDk1NjY5MTU5MjE5NzY4MjYwNjQyNDk0NzY3Il0sIFsic2NvcmVfb2ZfbGlzdGVuaW5nIiwgIjEyMDIwNDMxODY1NDEwMDAwNzcwNjczMTg4OTQ2NjU5NDQ0ODE0ODk2NjgxMzUwMjIxMzQzMTQ0NDM0MDQ0MzAzOTYyODY4OTk0NjA3MTI3MTYyOTY3NTY0MTAzMTQzNzk4MDkwMTI0MTY5NDEwNDkyODczMjgxNjMzNDY2MzE3NzM1OTYwMTIwMjk1NTUxNDIzNzg3ODQ1ODAxMTk1OTYzNDQ0NTUxMTg3MzU5MjY1MTE5MjM0MDc1MzU2MDU5NTI4ODUwMjAzMjU0NzU5NTYzNTE5MjU0NTMyNjQ3OTAyMjY1NTk2ODY0NjA3MjE0NDU2Mzk1MDk4MjQxNzYxNTEyOTUyNDE4NDQyNjE0MjQ4NjQ5MTY4OTQwMTAyNjIwODAzMTQ0MjM0NTMxNTY0OTY3NDA3NjE5MDE5NDgzMjM5OTQ3MzI2MTMyNzYwOTM3NTY0NzIzODMzNDgzMzMzMDA3ODI2Njg1OTU2ODU2OTc1MjU5NTY5NDI1MjIyNTcwMDYzMzc1NTQyMTA3NDMzMTk4NTA1MDY0MDcyMTU0MzI2MjY1NjAwMjcyNjc1NDgzNTI3Mzc1MzYwMTA3MDQyMjg5ODAwNzY5MjY3MTg1MTk1Njk2NDQ5MDA2MDAzODMxODkwNDk0Nzc2MjU4NjQ2ODcwMTE5MjY0NDA3NDYzMjA2MTg4MTA2NDQ3NTQyNDUxNTQ2NTA1MjAyMTgzNDkzNzYwNTgwMjU4MDU0NTU2NTMyMjAwMjM0NDM5MjM5MTUxNzAwMjgzMDg0OTE5MTU3MzA5NTQ4MzQ3NTE3MDYzMzQyNzk0ODE5NDczODcyODQ5NzA2NDMwNDk0Mzg2MjYzNzI2NTkwNjkyMDA2MjAxMzY5NjE2MjA4MDc3MzYzMjAyNjY1MDg5MzU2MDg3Nzg4ODExMjg0MDUiXSwgWyJtYXN0ZXJfc2VjcmV0IiwgIjc1MDU4NDYyMTM5ODU2NzA0MTI1NjU0Mzk3NDk0MjU0MTk5ODc0NTc3MTY4MTMzNTE3NTc4NTA0ODE0MDU0NTg4NzA1NDAzMzc2MTQ2NTMxMTgwNTE2MzA1MTExMDQ1NzQ4NTg2NjU3ODUwNjM5OTQ4NDYyODk4NTQ4MzA5NjI4NjgxMjI0NTQzNzg2NjcxMTg0NzY5MjU0MjM4OTM5NTgwNjUwNjIxMDM0MzYwMzY0MDk3OTc4NDY4NjM4OTYyNjgwMDA0NDQ4ODIxODIzMTc3NTI1NzE5NzE4NDk5NzY2ODg2NjQ4NzgzNDc4NTA2OTkxMzQ3Njg0NDczMDQwNzY0MTkyMzg2MDEwNTA0MjAzNTM5MTQ2MDE4NzMzNDk1OTg1MTIzMDc5OTc1NjUzNTg4MTI4OTg0MTI0ODUzNjcxODQ1MzMxMjU0NjcwNDc0NDk5MDY1MDEwMTk3MzA4NTMzNzI4MDQ3Nzc3MDkyMzU5NDUwNDgzMjYyNzYzNTQwNzIxOTc4Njk2MjgxNzA2NDQwMzYzNTM0MjE1NzI5NDM3MDQ1OTAwNDEzNDc4ODUwOTY4OTA2Nzg2ODE0MTAzODA4NTE5MDI1NzI5MDYwODg4MTAxMDgyNjcxMTQ0NjAxMzE5ODgyMDk4NDI1NDI3NTIzMDc2NDY3MDc2MzAxNzcwMDU5MjU1ODM0NTgxOTYyMTY0MDE1NzM4NDg4Nzg4NzI3MTIzMjU2Mjc4Mjg5MjQwNjk5NzA0NzE3OTc5ODYyMDM0MjY3NzU0OTM1MzUyNzA2MjM0NjE1MjAzMzM1OTc1NDQ0MTYyMTkwNjY1Nzg0MDk2ODUwNjg4NzA2ODU0NDMxMjA0NjIzMDU4MTc2ODg2Mzg1MzczNzMwNTkzMzY3NTYwNzEyNDE1ODUxMjkwODczMjEzIl1dfSwgIm5vbmNlIjogIjQwMzY5NjI4ODU0OTA3MzE0OTQ0NDMxNSJ9"
        }
      }
    ],
    "credential_preview":{
      "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes":[
        {
          "name":"date_of_birth",
          "value":"null"
        },
        {
          "name":"date_of_test",
          "value":"20190105"
        },
        {
          "name":"english_name",
          "value":""
        },
        {
          "name":"exp_date",
          "value":"20220228"
        },
        {
          "name":"korean_name",
          "value":"null"
        },
        {
          "name":"registration_number",
          "value":"null"
        },
        {
          "name":"score_of_listening",
          "value":"401"
        },
        {
          "name":"score_of_reading",
          "value":""
        },
        {
          "name":"score_of_total",
          "value":"990"
        }
      ]
    }
  },
  "credential_offer":{
    "schema_id":"N6r4nLwAkcYUX8c8Kb8Ufu:2:CertificateOfTOEIC:4.0",
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "nonce":"403696288549073149444315",
    "key_correctness_proof":{
      "c":"115309207665428406179545994373912181282831545118540014229967271707896224931550",
      "xz_cap":"1055572769862663463709934204601954080483898826030879595979074971004206859393782655599324140913902653650398938906681641924390794812738887811895545711063564481179172330881094952438950836326342830845967987855265191783565688169975863324545545315781669024596986257143061525264896458486625431280764566858010248626346811818332553348994255109430053940452222330296721244130109139739905819674198123825423697014194656630395527533035580919935737499589831966659250863798261759416779236747674116899991548520783007432182205649700587358695322105915051483830246135518385554263531337387626704587405669968771421880306850156048748573730575735016160622706605885093644782532647373659196784245832723085213497048989520",
      "xr_cap":[
        [
          "date_of_test",
          "1076232159249964734109718589777267462109484070440415691511826521589528195960117006601596499833881106787532908582735685947125303637779749865395973748786845381125614276185776325521284507979022202765015734531668494478627404644674922115351392810935336529179108446362157123315565260725483035159546977982636268273741134447625656913083049471163863325580545751281281707239030011491905375673521635790717301188716989419245594425667897372137887632091257692992002204353172141035532686819956655464274728888166233800730209627627333118372176190108581819028962383487619291845020994081912208148061482241150491798630105559860464292100987650906949548228012908099036234969978091590577056859814205598867632927950715"
        ],
        [
          "english_name",
          "696411130939580533510436664194526819156095629287412434045238350804750458875252009159837208667096357708273898836774596569571826421416373675758153437224961347803003587328774238645279718011305234430510914339030739772141034937822610601894100092611699051587368572476378066571995197160308770830331651684157422321494210565622254936179050256196395839836450739708603193285896785801340066230480042341954666620701121906593630655998864008598194498052803215598246923716961827765415427467725639778399843178991934315901785927203964183244619307158020733817516419875099846927356800929807208713102941594049577311851970296265305274235642059800993937805593444602673622260362361553200010326744684880113343773224856"
        ],
        [
          "exp_date",
          "2423650123164905529363621137809723145327867542753048760610730674284755739976356487466267577397713669927824555718078674729329822136623528495004922057741935984832762937882953798007838316207944508000224331385996443269580772498769868565007610503103086465320921094682281609628869906334746079832967350907986925385591867010527607177795249423358544981842772106056300659484323947189052758098052717291821625945625196039363888578326940371229699672363219264534873802852476718738347302929940470298789876886196910464381568220032501183416349848143988173727155194728720864205189777283305349845531272422805228252687727992182026982614462202644856280264034450814786604483296092056699991544749097728868483744446331"
        ],
        [
          "score_of_reading",
          "2263886014163696590877718819103455141839764106832768596959484972522489575027017888376981568132641923366850648287840153912124367275371474769790500423524604919564238092857224278300534000897636801852705809941046581775589751860522217490645306757391328303319956054118395953114831310842809749975591660215464105580925138260233324059520052466951401028759473645982168188190176228004854475985942016309992878768801954057483746945873570295882488431031166136808624134153820634301438203715228541061843520449836633174699357693505984852565434043129482220741904454080051328338153001196670948990896588307144418334265831459502889939680312534898472645004103285632827560927171404720898851329417605453634831452105880"
        ],
        [
          "korean_name",
          "1788905130111265904456574740124922396565215354204380449567575199633053127160174234740334169163533234098148466742292392674984412955928295011897914390825366883670704562244019812041523237990926471189078365848488833270311435394026181232864698108211568494413854353359501545597228323807667606753094829953660730021296029619567370219701682829655313153017527838124455789767036252178302629465507426610286364164681495353145251164557082375958197169733253633372741447430255323043278244265461826802697680565168561519779430990470463595586390531041737789913470295057104476365441293493385120339503184843302522455622801999634368420551345290270174238758437586582034974662721295675328742676255863518017012102741754"
        ],
        [
          "score_of_total",
          "1131408229621120341419714362596397257437010356761970831117159479462644909092607263082728500224663287432793687206924656934623008549461292143349994965014996220549600850171347399425995731869206209723121836146291303852634119993656531379913949985367268418996941292073439590628865049264575374368331684059634116305830878007505464481589097524377731105590524099461218975426677127234408701159721830316170789361477925525097080753114516789767813128041847397430523163733871008006318000394251620704007023311886686489213855362287012519524345918971605022062755150869511036027385123217644754740517125140618879286637752303249386621450313840810601389526460114307239254509987725573400448400489937628720343209395089"
        ],
        [
          "registration_number",
          "2247001418347582270988729853798416644319473907456862200810923148032570724445989049467408783929525319088796528846074300298169095832008090348765071819903592259054539824107548396054831620156065397353564314075832606210002977414031807190802036614711962753045459302055995482711202476264913729001053742381513627967237847564879310450296522656150932190587089497405772515463146242994444774104882568126760201438615070455178021939652629567700369720831400945670155611556079874020063191648365703561825491872933011142654327815258659117050096588380898686058800908707607049137007238452589439126294577306523377835567636783270433931969907155243881650213552971700040639102264289018157628681046401752955986797933218"
        ],
        [
          "date_of_birth",
          "1622036361531601912251084820418702891601047782637625175138825572605733390657370425908276847701537598980273604382070456587845182200911550410496021112938116817469328122740343465842145517523068426330797102857174680217730742636280962702678214012212055179781058935253589973057993182294325777377224183992603970408737848265608557819777659529146029917384032844531408647389643826506797896986046661457552175339113532176938328464383201000987343263429069705023059141319585205354277570075181730250883786462786446385160285106726367318312569623394375087504400808279887917603118803819491422372508163349572000020578024494244592303918714890726682610072788038285788003590191639827913464095669159219768260642494767"
        ],
        [
          "score_of_listening",
          "1202043186541000077067318894665944481489668135022134314443404430396286899460712716296756410314379809012416941049287328163346631773596012029555142378784580119596344455118735926511923407535605952885020325475956351925453264790226559686460721445639509824176151295241844261424864916894010262080314423453156496740761901948323994732613276093756472383348333300782668595685697525956942522257006337554210743319850506407215432626560027267548352737536010704228980076926718519569644900600383189049477625864687011926440746320618810644754245154650520218349376058025805455653220023443923915170028308491915730954834751706334279481947387284970643049438626372659069200620136961620807736320266508935608778881128405"
        ],
        [
          "master_secret",
          "75058462139856704125654397494254199874577168133517578504814054588705403376146531180516305111045748586657850639948462898548309628681224543786671184769254238939580650621034360364097978468638962680004448821823177525719718499766886648783478506991347684473040764192386010504203539146018733495985123079975653588128984124853671845331254670474499065010197308533728047777092359450483262763540721978696281706440363534215729437045900413478850968906786814103808519025729060888101082671144601319882098425427523076467076301770059255834581962164015738488788727123256278289240699704717979862034267754935352706234615203335975444162190665784096850688706854431204623058176886385373730593367560712415851290873213"
        ]
      ]
    }
  },
  "initiator":"external",
  "state":"request_received",
  "role":"issuer",
  "created_at":"2021-08-06 04:43:14.481031Z",
  "updated_at":"2021-08-06 04:43:36.253615Z",
  "connection_id":"d57cee53-6d64-444d-aa27-faec84d25c46",
  "auto_remove":true,
  "credential_proposal_dict":{
    "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
    "@id":"915f582a-1703-404d-976a-3a7fdd50ac1f",
    "comment":"JJ Test",
    "credential_proposal":{
      "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes":[
        {
          "name":"date_of_birth",
          "value":"null"
        },
        {
          "name":"date_of_test",
          "value":"20190105"
        },
        {
          "name":"english_name",
          "value":""
        },
        {
          "name":"exp_date",
          "value":"20220228"
        },
        {
          "name":"korean_name",
          "value":"null"
        },
        {
          "name":"registration_number",
          "value":"null"
        },
        {
          "name":"score_of_listening",
          "value":"401"
        },
        {
          "name":"score_of_reading",
          "value":""
        },
        {
          "name":"score_of_total",
          "value":"990"
        }
      ]
    },
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd"
  },
  "credential_request":{
    "prover_did":"97tE6e4v2K4vu1qjTWCFNg",
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "blinded_ms":{
      "u":"46831997328364301643560418398490601107404402418858671578137178928437446873320264586769819734807592006981843643797048636176545942127626113581637633992678985809986539645073812116060735925040113989648014245325647116102311574929207191431153445351031881214362589607267342496329275279987379019841630697479605548573417452012941350282137790586135125417975457165516639878476191673523942783822974022772301207764127613893393119636018290950386848847603439063968839433929801323062062302134399119778461191205998350172502435589826107416478327124481726806932655767216483793975040135290729197346256729889112751201367369575399155096029",
      "ur":"1 0F8566D0279976405786E75C4A6C55488578ADDAEA35B161CCA50A7C3BF45F76 1 12B89567472ECE510DF69F2E8208D4B32467740DC7942DA436BBA71E1AC385FE 2 095E45DDF417D05FB10933FFC63D474548B7FFFF7888802F07FFFFFF7D07A8A8",
      "hidden_attributes":[
        "master_secret"
      ],
      "committed_attributes":{

      }
    },
    "blinded_ms_correctness_proof":{
      "c":"79491694275957467808663629857140069543040376706162470853242122646197063169925",
      "v_dash_cap":"1435122002388533961027134840159500466316860333018095865228711993601047581332651750648078735798372784951671417597347488299580664488139988252974317356152922955508177537347153034916967960466880091097323588335739644113576341875606149325572872657807648450848862334571776241826695852478818185193649035809144532141522939442397385211676304172432050461183359300378609457138799743916183219947105371468310249097326871657166141245084029345180165327788167467034165019571462953242409072307876551061122242242516171023616917326212952490302476722937997150723212278225162787655593641181435922421638437076431287624156790626819428454807130945831358912551350425322605790927087848774628193538637712182142787574161794595598240100786038052508",
      "m_caps":{
        "master_secret":"2319623634283540032715827729851714852924001696855662216781048128776680239347185112688128861808689679482193826453156849849945078040235212522290996342499868425107046831682663417157"
      },
      "r_caps":{

      }
    },
    "nonce":"629297215437572416239898"
  },
  "thread_id":"217c6a09-eb41-4f0e-bad2-981be4622759",
  "credential_definition_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
  "credential_exchange_id":"2aa723e2-16f8-45ff-9c29-0b88ce6dbf09",
  "topic":"issue_credential"
}
```
<br><br>

### STEP 4. 기관 : Webhook을 확인 한다. 

아래와 같이 두번의 Webhook event가 차례 전달 됨

`topic:issue_credential`, `state:credential_issued` 

`topic:issue_credential`, `state:credential_acked` 이면 정상으로 완료된 상태


`state:credential_issued` Webhook Event Sample

```json
{
  "revoc_reg_id":"DrLbXFSao4Vo8gMfjxPxU1:4:DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd:CL_ACCUM:0fb34de2-af15-4a12-96b4-617df0604620",
  "schema_id":"N6r4nLwAkcYUX8c8Kb8Ufu:2:CertificateOfTOEIC:4.0",
  "auto_issue":true,
  "trace":false,
  "credential_offer_dict":{
    "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/offer-credential",
    "@id":"98b5ffd6-b7e4-4c56-a58d-68a11dc21f97",
    "~thread":{
      "thid":"217c6a09-eb41-4f0e-bad2-981be4622759"
    },
    "offers~attach":[
      {
        "@id":"libindy-cred-offer-0",
        "mime-type":"application/json",
        "data":{
          "base64":"eyJzY2hlbWFfaWQiOiAiTjZyNG5Md0FrY1lVWDhjOEtiOFVmdToyOkNlcnRpZmljYXRlT2ZUT0VJQzo0LjAiLCAiY3JlZF9kZWZfaWQiOiAiRHJMYlhGU2FvNFZvOGdNZmp4UHhVMTozOkNMOjE2MTc2OTgyMzg6ODFkZjAwMTAtNjJiNC00NWIxLWJkMDAtOGQwYWQ3NDc2MmZkIiwgImtleV9jb3JyZWN0bmVzc19wcm9vZiI6IHsiYyI6ICIxMTUzMDkyMDc2NjU0Mjg0MDYxNzk1NDU5OTQzNzM5MTIxODEyODI4MzE1NDUxMTg1NDAwMTQyMjk5NjcyNzE3MDc4OTYyMjQ5MzE1NTAiLCAieHpfY2FwIjogIjEwNTU1NzI3Njk4NjI2NjM0NjM3MDk5MzQyMDQ2MDE5NTQwODA0ODM4OTg4MjYwMzA4Nzk1OTU5NzkwNzQ5NzEwMDQyMDY4NTkzOTM3ODI2NTU1OTkzMjQxNDA5MTM5MDI2NTM2NTAzOTg5Mzg5MDY2ODE2NDE5MjQzOTA3OTQ4MTI3Mzg4ODc4MTE4OTU1NDU3MTEwNjM1NjQ0ODExNzkxNzIzMzA4ODEwOTQ5NTI0Mzg5NTA4MzYzMjYzNDI4MzA4NDU5Njc5ODc4NTUyNjUxOTE3ODM1NjU2ODgxNjk5NzU4NjMzMjQ1NDU1NDUzMTU3ODE2NjkwMjQ1OTY5ODYyNTcxNDMwNjE1MjUyNjQ4OTY0NTg0ODY2MjU0MzEyODA3NjQ1NjY4NTgwMTAyNDg2MjYzNDY4MTE4MTgzMzI1NTMzNDg5OTQyNTUxMDk0MzAwNTM5NDA0NTIyMjIzMzAyOTY3MjEyNDQxMzAxMDkxMzk3Mzk5MDU4MTk2NzQxOTgxMjM4MjU0MjM2OTcwMTQxOTQ2NTY2MzAzOTU1Mjc1MzMwMzU1ODA5MTk5MzU3Mzc0OTk1ODk4MzE5NjY2NTkyNTA4NjM3OTgyNjE3NTk0MTY3NzkyMzY3NDc2NzQxMTY4OTk5OTE1NDg1MjA3ODMwMDc0MzIxODIyMDU2NDk3MDA1ODczNTg2OTUzMjIxMDU5MTUwNTE0ODM4MzAyNDYxMzU1MTgzODU1NTQyNjM1MzEzMzczODc2MjY3MDQ1ODc0MDU2Njk5Njg3NzE0MjE4ODAzMDY4NTAxNTYwNDg3NDg1NzM3MzA1NzU3MzUwMTYxNjA2MjI3MDY2MDU4ODUwOTM2NDQ3ODI1MzI2NDczNzM2NTkxOTY3ODQyNDU4MzI3MjMwODUyMTM0OTcwNDg5ODk1MjAiLCAieHJfY2FwIjogW1siZGF0ZV9vZl90ZXN0IiwgIjEwNzYyMzIxNTkyNDk5NjQ3MzQxMDk3MTg1ODk3NzcyNjc0NjIxMDk0ODQwNzA0NDA0MTU2OTE1MTE4MjY1MjE1ODk1MjgxOTU5NjAxMTcwMDY2MDE1OTY0OTk4MzM4ODExMDY3ODc1MzI5MDg1ODI3MzU2ODU5NDcxMjUzMDM2Mzc3Nzk3NDk4NjUzOTU5NzM3NDg3ODY4NDUzODExMjU2MTQyNzYxODU3NzYzMjU1MjEyODQ1MDc5NzkwMjIyMDI3NjUwMTU3MzQ1MzE2Njg0OTQ0Nzg2Mjc0MDQ2NDQ2NzQ5MjIxMTUzNTEzOTI4MTA5MzUzMzY1MjkxNzkxMDg0NDYzNjIxNTcxMjMzMTU1NjUyNjA3MjU0ODMwMzUxNTk1NDY5Nzc5ODI2MzYyNjgyNzM3NDExMzQ0NDc2MjU2NTY5MTMwODMwNDk0NzExNjM4NjMzMjU1ODA1NDU3NTEyODEyODE3MDcyMzkwMzAwMTE0OTE5MDUzNzU2NzM1MjE2MzU3OTA3MTczMDExODg3MTY5ODk0MTkyNDU1OTQ0MjU2Njc4OTczNzIxMzc4ODc2MzIwOTEyNTc2OTI5OTIwMDIyMDQzNTMxNzIxNDEwMzU1MzI2ODY4MTk5NTY2NTU0NjQyNzQ3Mjg4ODgxNjYyMzM4MDA3MzAyMDk2Mjc2MjczMzMxMTgzNzIxNzYxOTAxMDg1ODE4MTkwMjg5NjIzODM0ODc2MTkyOTE4NDUwMjA5OTQwODE5MTIyMDgxNDgwNjE0ODIyNDExNTA0OTE3OTg2MzAxMDU1NTk4NjA0NjQyOTIxMDA5ODc2NTA5MDY5NDk1NDgyMjgwMTI5MDgwOTkwMzYyMzQ5Njk5NzgwOTE1OTA1NzcwNTY4NTk4MTQyMDU1OTg4Njc2MzI5Mjc5NTA3MTUiXSwgWyJlbmdsaXNoX25hbWUiLCAiNjk2NDExMTMwOTM5NTgwNTMzNTEwNDM2NjY0MTk0NTI2ODE5MTU2MDk1NjI5Mjg3NDEyNDM0MDQ1MjM4MzUwODA0NzUwNDU4ODc1MjUyMDA5MTU5ODM3MjA4NjY3MDk2MzU3NzA4MjczODk4ODM2Nzc0NTk2NTY5NTcxODI2NDIxNDE2MzczNjc1NzU4MTUzNDM3MjI0OTYxMzQ3ODAzMDAzNTg3MzI4Nzc0MjM4NjQ1Mjc5NzE4MDExMzA1MjM0NDMwNTEwOTE0MzM5MDMwNzM5NzcyMTQxMDM0OTM3ODIyNjEwNjAxODk0MTAwMDkyNjExNjk5MDUxNTg3MzY4NTcyNDc2Mzc4MDY2NTcxOTk1MTk3MTYwMzA4NzcwODMwMzMxNjUxNjg0MTU3NDIyMzIxNDk0MjEwNTY1NjIyMjU0OTM2MTc5MDUwMjU2MTk2Mzk1ODM5ODM2NDUwNzM5NzA4NjAzMTkzMjg1ODk2Nzg1ODAxMzQwMDY2MjMwNDgwMDQyMzQxOTU0NjY2NjIwNzAxMTIxOTA2NTkzNjMwNjU1OTk4ODY0MDA4NTk4MTk0NDk4MDUyODAzMjE1NTk4MjQ2OTIzNzE2OTYxODI3NzY1NDE1NDI3NDY3NzI1NjM5Nzc4Mzk5ODQzMTc4OTkxOTM0MzE1OTAxNzg1OTI3MjAzOTY0MTgzMjQ0NjE5MzA3MTU4MDIwNzMzODE3NTE2NDE5ODc1MDk5ODQ2OTI3MzU2ODAwOTI5ODA3MjA4NzEzMTAyOTQxNTk0MDQ5NTc3MzExODUxOTcwMjk2MjY1MzA1Mjc0MjM1NjQyMDU5ODAwOTkzOTM3ODA1NTkzNDQ0NjAyNjczNjIyMjYwMzYyMzYxNTUzMjAwMDEwMzI2NzQ0Njg0ODgwMTEzMzQzNzczMjI0ODU2Il0sIFsiZXhwX2RhdGUiLCAiMjQyMzY1MDEyMzE2NDkwNTUyOTM2MzYyMTEzNzgwOTcyMzE0NTMyNzg2NzU0Mjc1MzA0ODc2MDYxMDczMDY3NDI4NDc1NTczOTk3NjM1NjQ4NzQ2NjI2NzU3NzM5NzcxMzY2OTkyNzgyNDU1NTcxODA3ODY3NDcyOTMyOTgyMjEzNjYyMzUyODQ5NTAwNDkyMjA1Nzc0MTkzNTk4NDgzMjc2MjkzNzg4Mjk1Mzc5ODAwNzgzODMxNjIwNzk0NDUwODAwMDIyNDMzMTM4NTk5NjQ0MzI2OTU4MDc3MjQ5ODc2OTg2ODU2NTAwNzYxMDUwMzEwMzA4NjQ2NTMyMDkyMTA5NDY4MjI4MTYwOTYyODg2OTkwNjMzNDc0NjA3OTgzMjk2NzM1MDkwNzk4NjkyNTM4NTU5MTg2NzAxMDUyNzYwNzE3Nzc5NTI0OTQyMzM1ODU0NDk4MTg0Mjc3MjEwNjA1NjMwMDY1OTQ4NDMyMzk0NzE4OTA1Mjc1ODA5ODA1MjcxNzI5MTgyMTYyNTk0NTYyNTE5NjAzOTM2Mzg4ODU3ODMyNjk0MDM3MTIyOTY5OTY3MjM2MzIxOTI2NDUzNDg3MzgwMjg1MjQ3NjcxODczODM0NzMwMjkyOTk0MDQ3MDI5ODc4OTg3Njg4NjE5NjkxMDQ2NDM4MTU2ODIyMDAzMjUwMTE4MzQxNjM0OTg0ODE0Mzk4ODE3MzcyNzE1NTE5NDcyODcyMDg2NDIwNTE4OTc3NzI4MzMwNTM0OTg0NTUzMTI3MjQyMjgwNTIyODI1MjY4NzcyNzk5MjE4MjAyNjk4MjYxNDQ2MjIwMjY0NDg1NjI4MDI2NDAzNDQ1MDgxNDc4NjYwNDQ4MzI5NjA5MjA1NjY5OTk5MTU0NDc0OTA5NzcyODg2ODQ4Mzc0NDQ0NjMzMSJdLCBbInNjb3JlX29mX3JlYWRpbmciLCAiMjI2Mzg4NjAxNDE2MzY5NjU5MDg3NzcxODgxOTEwMzQ1NTE0MTgzOTc2NDEwNjgzMjc2ODU5Njk1OTQ4NDk3MjUyMjQ4OTU3NTAyNzAxNzg4ODM3Njk4MTU2ODEzMjY0MTkyMzM2Njg1MDY0ODI4Nzg0MDE1MzkxMjEyNDM2NzI3NTM3MTQ3NDc2OTc5MDUwMDQyMzUyNDYwNDkxOTU2NDIzODA5Mjg1NzIyNDI3ODMwMDUzNDAwMDg5NzYzNjgwMTg1MjcwNTgwOTk0MTA0NjU4MTc3NTU4OTc1MTg2MDUyMjIxNzQ5MDY0NTMwNjc1NzM5MTMyODMwMzMxOTk1NjA1NDExODM5NTk1MzExNDgzMTMxMDg0MjgwOTc0OTk3NTU5MTY2MDIxNTQ2NDEwNTU4MDkyNTEzODI2MDIzMzMyNDA1OTUyMDA1MjQ2Njk1MTQwMTAyODc1OTQ3MzY0NTk4MjE2ODE4ODE5MDE3NjIyODAwNDg1NDQ3NTk4NTk0MjAxNjMwOTk5Mjg3ODc2ODgwMTk1NDA1NzQ4Mzc0Njk0NTg3MzU3MDI5NTg4MjQ4ODQzMTAzMTE2NjEzNjgwODYyNDEzNDE1MzgyMDYzNDMwMTQzODIwMzcxNTIyODU0MTA2MTg0MzUyMDQ0OTgzNjYzMzE3NDY5OTM1NzY5MzUwNTk4NDg1MjU2NTQzNDA0MzEyOTQ4MjIyMDc0MTkwNDQ1NDA4MDA1MTMyODMzODE1MzAwMTE5NjY3MDk0ODk5MDg5NjU4ODMwNzE0NDQxODMzNDI2NTgzMTQ1OTUwMjg4OTkzOTY4MDMxMjUzNDg5ODQ3MjY0NTAwNDEwMzI4NTYzMjgyNzU2MDkyNzE3MTQwNDcyMDg5ODg1MTMyOTQxNzYwNTQ1MzYzNDgzMTQ1MjEwNTg4MCJdLCBbImtvcmVhbl9uYW1lIiwgIjE3ODg5MDUxMzAxMTEyNjU5MDQ0NTY1NzQ3NDAxMjQ5MjIzOTY1NjUyMTUzNTQyMDQzODA0NDk1Njc1NzUxOTk2MzMwNTMxMjcxNjAxNzQyMzQ3NDAzMzQxNjkxNjM1MzMyMzQwOTgxNDg0NjY3NDIyOTIzOTI2NzQ5ODQ0MTI5NTU5MjgyOTUwMTE4OTc5MTQzOTA4MjUzNjY4ODM2NzA3MDQ1NjIyNDQwMTk4MTIwNDE1MjMyMzc5OTA5MjY0NzExODkwNzgzNjU4NDg0ODg4MzMyNzAzMTE0MzUzOTQwMjYxODEyMzI4NjQ2OTgxMDgyMTE1Njg0OTQ0MTM4NTQzNTMzNTk1MDE1NDU1OTcyMjgzMjM4MDc2Njc2MDY3NTMwOTQ4Mjk5NTM2NjA3MzAwMjEyOTYwMjk2MTk1NjczNzAyMTk3MDE2ODI4Mjk2NTUzMTMxNTMwMTc1Mjc4MzgxMjQ0NTU3ODk3NjcwMzYyNTIxNzgzMDI2Mjk0NjU1MDc0MjY2MTAyODYzNjQxNjQ2ODE0OTUzNTMxNDUyNTExNjQ1NTcwODIzNzU5NTgxOTcxNjk3MzMyNTM2MzMzNzI3NDE0NDc0MzAyNTUzMjMwNDMyNzgyNDQyNjU0NjE4MjY4MDI2OTc2ODA1NjUxNjg1NjE1MTk3Nzk0MzA5OTA0NzA0NjM1OTU1ODYzOTA1MzEwNDE3Mzc3ODk5MTM0NzAyOTUwNTcxMDQ0NzYzNjU0NDEyOTM0OTMzODUxMjAzMzk1MDMxODQ4NDMzMDI1MjI0NTU2MjI4MDE5OTk2MzQzNjg0MjA1NTEzNDUyOTAyNzAxNzQyMzg3NTg0Mzc1ODY1ODIwMzQ5NzQ2NjI3MjEyOTU2NzUzMjg3NDI2NzYyNTU4NjM1MTgwMTcwMTIxMDI3NDE3NTQiXSwgWyJzY29yZV9vZl90b3RhbCIsICIxMTMxNDA4MjI5NjIxMTIwMzQxNDE5NzE0MzYyNTk2Mzk3MjU3NDM3MDEwMzU2NzYxOTcwODMxMTE3MTU5NDc5NDYyNjQ0OTA5MDkyNjA3MjYzMDgyNzI4NTAwMjI0NjYzMjg3NDMyNzkzNjg3MjA2OTI0NjU2OTM0NjIzMDA4NTQ5NDYxMjkyMTQzMzQ5OTk0OTY1MDE0OTk2MjIwNTQ5NjAwODUwMTcxMzQ3Mzk5NDI1OTk1NzMxODY5MjA2MjA5NzIzMTIxODM2MTQ2MjkxMzAzODUyNjM0MTE5OTkzNjU2NTMxMzc5OTEzOTQ5OTg1MzY3MjY4NDE4OTk2OTQxMjkyMDczNDM5NTkwNjI4ODY1MDQ5MjY0NTc1Mzc0MzY4MzMxNjg0MDU5NjM0MTE2MzA1ODMwODc4MDA3NTA1NDY0NDgxNTg5MDk3NTI0Mzc3NzMxMTA1NTkwNTI0MDk5NDYxMjE4OTc1NDI2Njc3MTI3MjM0NDA4NzAxMTU5NzIxODMwMzE2MTcwNzg5MzYxNDc3OTI1NTI1MDk3MDgwNzUzMTE0NTE2Nzg5NzY3ODEzMTI4MDQxODQ3Mzk3NDMwNTIzMTYzNzMzODcxMDA4MDA2MzE4MDAwMzk0MjUxNjIwNzA0MDA3MDIzMzExODg2Njg2NDg5MjEzODU1MzYyMjg3MDEyNTE5NTI0MzQ1OTE4OTcxNjA1MDIyMDYyNzU1MTUwODY5NTExMDM2MDI3Mzg1MTIzMjE3NjQ0NzU0NzQwNTE3MTI1MTQwNjE4ODc5Mjg2NjM3NzUyMzAzMjQ5Mzg2NjIxNDUwMzEzODQwODEwNjAxMzg5NTI2NDYwMTE0MzA3MjM5MjU0NTA5OTg3NzI1NTczNDAwNDQ4NDAwNDg5OTM3NjI4NzIwMzQzMjA5Mzk1MDg5Il0sIFsicmVnaXN0cmF0aW9uX251bWJlciIsICIyMjQ3MDAxNDE4MzQ3NTgyMjcwOTg4NzI5ODUzNzk4NDE2NjQ0MzE5NDczOTA3NDU2ODYyMjAwODEwOTIzMTQ4MDMyNTcwNzI0NDQ1OTg5MDQ5NDY3NDA4NzgzOTI5NTI1MzE5MDg4Nzk2NTI4ODQ2MDc0MzAwMjk4MTY5MDk1ODMyMDA4MDkwMzQ4NzY1MDcxODE5OTAzNTkyMjU5MDU0NTM5ODI0MTA3NTQ4Mzk2MDU0ODMxNjIwMTU2MDY1Mzk3MzUzNTY0MzE0MDc1ODMyNjA2MjEwMDAyOTc3NDE0MDMxODA3MTkwODAyMDM2NjE0NzExOTYyNzUzMDQ1NDU5MzAyMDU1OTk1NDgyNzExMjAyNDc2MjY0OTEzNzI5MDAxMDUzNzQyMzgxNTEzNjI3OTY3MjM3ODQ3NTY0ODc5MzEwNDUwMjk2NTIyNjU2MTUwOTMyMTkwNTg3MDg5NDk3NDA1NzcyNTE1NDYzMTQ2MjQyOTk0NDQ0Nzc0MTA0ODgyNTY4MTI2NzYwMjAxNDM4NjE1MDcwNDU1MTc4MDIxOTM5NjUyNjI5NTY3NzAwMzY5NzIwODMxNDAwOTQ1NjcwMTU1NjExNTU2MDc5ODc0MDIwMDYzMTkxNjQ4MzY1NzAzNTYxODI1NDkxODcyOTMzMDExMTQyNjU0MzI3ODE1MjU4NjU5MTE3MDUwMDk2NTg4MzgwODk4Njg2MDU4ODAwOTA4NzA3NjA3MDQ5MTM3MDA3MjM4NDUyNTg5NDM5MTI2Mjk0NTc3MzA2NTIzMzc3ODM1NTY3NjM2NzgzMjcwNDMzOTMxOTY5OTA3MTU1MjQzODgxNjUwMjEzNTUyOTcxNzAwMDQwNjM5MTAyMjY0Mjg5MDE4MTU3NjI4NjgxMDQ2NDAxNzUyOTU1OTg2Nzk3OTMzMjE4Il0sIFsiZGF0ZV9vZl9iaXJ0aCIsICIxNjIyMDM2MzYxNTMxNjAxOTEyMjUxMDg0ODIwNDE4NzAyODkxNjAxMDQ3NzgyNjM3NjI1MTc1MTM4ODI1NTcyNjA1NzMzMzkwNjU3MzcwNDI1OTA4Mjc2ODQ3NzAxNTM3NTk4OTgwMjczNjA0MzgyMDcwNDU2NTg3ODQ1MTgyMjAwOTExNTUwNDEwNDk2MDIxMTEyOTM4MTE2ODE3NDY5MzI4MTIyNzQwMzQzNDY1ODQyMTQ1NTE3NTIzMDY4NDI2MzMwNzk3MTAyODU3MTc0NjgwMjE3NzMwNzQyNjM2MjgwOTYyNzAyNjc4MjE0MDEyMjEyMDU1MTc5NzgxMDU4OTM1MjUzNTg5OTczMDU3OTkzMTgyMjk0MzI1Nzc3Mzc3MjI0MTgzOTkyNjAzOTcwNDA4NzM3ODQ4MjY1NjA4NTU3ODE5Nzc3NjU5NTI5MTQ2MDI5OTE3Mzg0MDMyODQ0NTMxNDA4NjQ3Mzg5NjQzODI2NTA2Nzk3ODk2OTg2MDQ2NjYxNDU3NTUyMTc1MzM5MTEzNTMyMTc2OTM4MzI4NDY0MzgzMjAxMDAwOTg3MzQzMjYzNDI5MDY5NzA1MDIzMDU5MTQxMzE5NTg1MjA1MzU0Mjc3NTcwMDc1MTgxNzMwMjUwODgzNzg2NDYyNzg2NDQ2Mzg1MTYwMjg1MTA2NzI2MzY3MzE4MzEyNTY5NjIzMzk0Mzc1MDg3NTA0NDAwODA4Mjc5ODg3OTE3NjAzMTE4ODAzODE5NDkxNDIyMzcyNTA4MTYzMzQ5NTcyMDAwMDIwNTc4MDI0NDk0MjQ0NTkyMzAzOTE4NzE0ODkwNzI2NjgyNjEwMDcyNzg4MDM4Mjg1Nzg4MDAzNTkwMTkxNjM5ODI3OTEzNDY0MDk1NjY5MTU5MjE5NzY4MjYwNjQyNDk0NzY3Il0sIFsic2NvcmVfb2ZfbGlzdGVuaW5nIiwgIjEyMDIwNDMxODY1NDEwMDAwNzcwNjczMTg4OTQ2NjU5NDQ0ODE0ODk2NjgxMzUwMjIxMzQzMTQ0NDM0MDQ0MzAzOTYyODY4OTk0NjA3MTI3MTYyOTY3NTY0MTAzMTQzNzk4MDkwMTI0MTY5NDEwNDkyODczMjgxNjMzNDY2MzE3NzM1OTYwMTIwMjk1NTUxNDIzNzg3ODQ1ODAxMTk1OTYzNDQ0NTUxMTg3MzU5MjY1MTE5MjM0MDc1MzU2MDU5NTI4ODUwMjAzMjU0NzU5NTYzNTE5MjU0NTMyNjQ3OTAyMjY1NTk2ODY0NjA3MjE0NDU2Mzk1MDk4MjQxNzYxNTEyOTUyNDE4NDQyNjE0MjQ4NjQ5MTY4OTQwMTAyNjIwODAzMTQ0MjM0NTMxNTY0OTY3NDA3NjE5MDE5NDgzMjM5OTQ3MzI2MTMyNzYwOTM3NTY0NzIzODMzNDgzMzMzMDA3ODI2Njg1OTU2ODU2OTc1MjU5NTY5NDI1MjIyNTcwMDYzMzc1NTQyMTA3NDMzMTk4NTA1MDY0MDcyMTU0MzI2MjY1NjAwMjcyNjc1NDgzNTI3Mzc1MzYwMTA3MDQyMjg5ODAwNzY5MjY3MTg1MTk1Njk2NDQ5MDA2MDAzODMxODkwNDk0Nzc2MjU4NjQ2ODcwMTE5MjY0NDA3NDYzMjA2MTg4MTA2NDQ3NTQyNDUxNTQ2NTA1MjAyMTgzNDkzNzYwNTgwMjU4MDU0NTU2NTMyMjAwMjM0NDM5MjM5MTUxNzAwMjgzMDg0OTE5MTU3MzA5NTQ4MzQ3NTE3MDYzMzQyNzk0ODE5NDczODcyODQ5NzA2NDMwNDk0Mzg2MjYzNzI2NTkwNjkyMDA2MjAxMzY5NjE2MjA4MDc3MzYzMjAyNjY1MDg5MzU2MDg3Nzg4ODExMjg0MDUiXSwgWyJtYXN0ZXJfc2VjcmV0IiwgIjc1MDU4NDYyMTM5ODU2NzA0MTI1NjU0Mzk3NDk0MjU0MTk5ODc0NTc3MTY4MTMzNTE3NTc4NTA0ODE0MDU0NTg4NzA1NDAzMzc2MTQ2NTMxMTgwNTE2MzA1MTExMDQ1NzQ4NTg2NjU3ODUwNjM5OTQ4NDYyODk4NTQ4MzA5NjI4NjgxMjI0NTQzNzg2NjcxMTg0NzY5MjU0MjM4OTM5NTgwNjUwNjIxMDM0MzYwMzY0MDk3OTc4NDY4NjM4OTYyNjgwMDA0NDQ4ODIxODIzMTc3NTI1NzE5NzE4NDk5NzY2ODg2NjQ4NzgzNDc4NTA2OTkxMzQ3Njg0NDczMDQwNzY0MTkyMzg2MDEwNTA0MjAzNTM5MTQ2MDE4NzMzNDk1OTg1MTIzMDc5OTc1NjUzNTg4MTI4OTg0MTI0ODUzNjcxODQ1MzMxMjU0NjcwNDc0NDk5MDY1MDEwMTk3MzA4NTMzNzI4MDQ3Nzc3MDkyMzU5NDUwNDgzMjYyNzYzNTQwNzIxOTc4Njk2MjgxNzA2NDQwMzYzNTM0MjE1NzI5NDM3MDQ1OTAwNDEzNDc4ODUwOTY4OTA2Nzg2ODE0MTAzODA4NTE5MDI1NzI5MDYwODg4MTAxMDgyNjcxMTQ0NjAxMzE5ODgyMDk4NDI1NDI3NTIzMDc2NDY3MDc2MzAxNzcwMDU5MjU1ODM0NTgxOTYyMTY0MDE1NzM4NDg4Nzg4NzI3MTIzMjU2Mjc4Mjg5MjQwNjk5NzA0NzE3OTc5ODYyMDM0MjY3NzU0OTM1MzUyNzA2MjM0NjE1MjAzMzM1OTc1NDQ0MTYyMTkwNjY1Nzg0MDk2ODUwNjg4NzA2ODU0NDMxMjA0NjIzMDU4MTc2ODg2Mzg1MzczNzMwNTkzMzY3NTYwNzEyNDE1ODUxMjkwODczMjEzIl1dfSwgIm5vbmNlIjogIjQwMzY5NjI4ODU0OTA3MzE0OTQ0NDMxNSJ9"
        }
      }
    ],
    "credential_preview":{
      "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes":[
        {
          "name":"date_of_birth",
          "value":"null"
        },
        {
          "name":"date_of_test",
          "value":"20190105"
        },
        {
          "name":"english_name",
          "value":""
        },
        {
          "name":"exp_date",
          "value":"20220228"
        },
        {
          "name":"korean_name",
          "value":"null"
        },
        {
          "name":"registration_number",
          "value":"null"
        },
        {
          "name":"score_of_listening",
          "value":"401"
        },
        {
          "name":"score_of_reading",
          "value":""
        },
        {
          "name":"score_of_total",
          "value":"990"
        }
      ]
    }
  },
  "credential_offer":{
    "schema_id":"N6r4nLwAkcYUX8c8Kb8Ufu:2:CertificateOfTOEIC:4.0",
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "nonce":"403696288549073149444315",
    "key_correctness_proof":{
      "c":"115309207665428406179545994373912181282831545118540014229967271707896224931550",
      "xz_cap":"1055572769862663463709934204601954080483898826030879595979074971004206859393782655599324140913902653650398938906681641924390794812738887811895545711063564481179172330881094952438950836326342830845967987855265191783565688169975863324545545315781669024596986257143061525264896458486625431280764566858010248626346811818332553348994255109430053940452222330296721244130109139739905819674198123825423697014194656630395527533035580919935737499589831966659250863798261759416779236747674116899991548520783007432182205649700587358695322105915051483830246135518385554263531337387626704587405669968771421880306850156048748573730575735016160622706605885093644782532647373659196784245832723085213497048989520",
      "xr_cap":[
        [
          "date_of_test",
          "1076232159249964734109718589777267462109484070440415691511826521589528195960117006601596499833881106787532908582735685947125303637779749865395973748786845381125614276185776325521284507979022202765015734531668494478627404644674922115351392810935336529179108446362157123315565260725483035159546977982636268273741134447625656913083049471163863325580545751281281707239030011491905375673521635790717301188716989419245594425667897372137887632091257692992002204353172141035532686819956655464274728888166233800730209627627333118372176190108581819028962383487619291845020994081912208148061482241150491798630105559860464292100987650906949548228012908099036234969978091590577056859814205598867632927950715"
        ],
        [
          "english_name",
          "696411130939580533510436664194526819156095629287412434045238350804750458875252009159837208667096357708273898836774596569571826421416373675758153437224961347803003587328774238645279718011305234430510914339030739772141034937822610601894100092611699051587368572476378066571995197160308770830331651684157422321494210565622254936179050256196395839836450739708603193285896785801340066230480042341954666620701121906593630655998864008598194498052803215598246923716961827765415427467725639778399843178991934315901785927203964183244619307158020733817516419875099846927356800929807208713102941594049577311851970296265305274235642059800993937805593444602673622260362361553200010326744684880113343773224856"
        ],
        [
          "exp_date",
          "2423650123164905529363621137809723145327867542753048760610730674284755739976356487466267577397713669927824555718078674729329822136623528495004922057741935984832762937882953798007838316207944508000224331385996443269580772498769868565007610503103086465320921094682281609628869906334746079832967350907986925385591867010527607177795249423358544981842772106056300659484323947189052758098052717291821625945625196039363888578326940371229699672363219264534873802852476718738347302929940470298789876886196910464381568220032501183416349848143988173727155194728720864205189777283305349845531272422805228252687727992182026982614462202644856280264034450814786604483296092056699991544749097728868483744446331"
        ],
        [
          "score_of_reading",
          "2263886014163696590877718819103455141839764106832768596959484972522489575027017888376981568132641923366850648287840153912124367275371474769790500423524604919564238092857224278300534000897636801852705809941046581775589751860522217490645306757391328303319956054118395953114831310842809749975591660215464105580925138260233324059520052466951401028759473645982168188190176228004854475985942016309992878768801954057483746945873570295882488431031166136808624134153820634301438203715228541061843520449836633174699357693505984852565434043129482220741904454080051328338153001196670948990896588307144418334265831459502889939680312534898472645004103285632827560927171404720898851329417605453634831452105880"
        ],
        [
          "korean_name",
          "1788905130111265904456574740124922396565215354204380449567575199633053127160174234740334169163533234098148466742292392674984412955928295011897914390825366883670704562244019812041523237990926471189078365848488833270311435394026181232864698108211568494413854353359501545597228323807667606753094829953660730021296029619567370219701682829655313153017527838124455789767036252178302629465507426610286364164681495353145251164557082375958197169733253633372741447430255323043278244265461826802697680565168561519779430990470463595586390531041737789913470295057104476365441293493385120339503184843302522455622801999634368420551345290270174238758437586582034974662721295675328742676255863518017012102741754"
        ],
        [
          "score_of_total",
          "1131408229621120341419714362596397257437010356761970831117159479462644909092607263082728500224663287432793687206924656934623008549461292143349994965014996220549600850171347399425995731869206209723121836146291303852634119993656531379913949985367268418996941292073439590628865049264575374368331684059634116305830878007505464481589097524377731105590524099461218975426677127234408701159721830316170789361477925525097080753114516789767813128041847397430523163733871008006318000394251620704007023311886686489213855362287012519524345918971605022062755150869511036027385123217644754740517125140618879286637752303249386621450313840810601389526460114307239254509987725573400448400489937628720343209395089"
        ],
        [
          "registration_number",
          "2247001418347582270988729853798416644319473907456862200810923148032570724445989049467408783929525319088796528846074300298169095832008090348765071819903592259054539824107548396054831620156065397353564314075832606210002977414031807190802036614711962753045459302055995482711202476264913729001053742381513627967237847564879310450296522656150932190587089497405772515463146242994444774104882568126760201438615070455178021939652629567700369720831400945670155611556079874020063191648365703561825491872933011142654327815258659117050096588380898686058800908707607049137007238452589439126294577306523377835567636783270433931969907155243881650213552971700040639102264289018157628681046401752955986797933218"
        ],
        [
          "date_of_birth",
          "1622036361531601912251084820418702891601047782637625175138825572605733390657370425908276847701537598980273604382070456587845182200911550410496021112938116817469328122740343465842145517523068426330797102857174680217730742636280962702678214012212055179781058935253589973057993182294325777377224183992603970408737848265608557819777659529146029917384032844531408647389643826506797896986046661457552175339113532176938328464383201000987343263429069705023059141319585205354277570075181730250883786462786446385160285106726367318312569623394375087504400808279887917603118803819491422372508163349572000020578024494244592303918714890726682610072788038285788003590191639827913464095669159219768260642494767"
        ],
        [
          "score_of_listening",
          "1202043186541000077067318894665944481489668135022134314443404430396286899460712716296756410314379809012416941049287328163346631773596012029555142378784580119596344455118735926511923407535605952885020325475956351925453264790226559686460721445639509824176151295241844261424864916894010262080314423453156496740761901948323994732613276093756472383348333300782668595685697525956942522257006337554210743319850506407215432626560027267548352737536010704228980076926718519569644900600383189049477625864687011926440746320618810644754245154650520218349376058025805455653220023443923915170028308491915730954834751706334279481947387284970643049438626372659069200620136961620807736320266508935608778881128405"
        ],
        [
          "master_secret",
          "75058462139856704125654397494254199874577168133517578504814054588705403376146531180516305111045748586657850639948462898548309628681224543786671184769254238939580650621034360364097978468638962680004448821823177525719718499766886648783478506991347684473040764192386010504203539146018733495985123079975653588128984124853671845331254670474499065010197308533728047777092359450483262763540721978696281706440363534215729437045900413478850968906786814103808519025729060888101082671144601319882098425427523076467076301770059255834581962164015738488788727123256278289240699704717979862034267754935352706234615203335975444162190665784096850688706854431204623058176886385373730593367560712415851290873213"
        ]
      ]
    }
  },
  "initiator":"external",
  "state":"credential_issued",
  "role":"issuer",
  "created_at":"2021-08-06 04:43:14.481031Z",
  "updated_at":"2021-08-06 04:43:36.413531Z",
  "connection_id":"d57cee53-6d64-444d-aa27-faec84d25c46",
  "auto_remove":true,
  "revocation_id":"13",
  "credential_proposal_dict":{
    "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
    "@id":"915f582a-1703-404d-976a-3a7fdd50ac1f",
    "comment":"JJ Test",
    "credential_proposal":{
      "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes":[
        {
          "name":"date_of_birth",
          "value":"null"
        },
        {
          "name":"date_of_test",
          "value":"20190105"
        },
        {
          "name":"english_name",
          "value":""
        },
        {
          "name":"exp_date",
          "value":"20220228"
        },
        {
          "name":"korean_name",
          "value":"null"
        },
        {
          "name":"registration_number",
          "value":"null"
        },
        {
          "name":"score_of_listening",
          "value":"401"
        },
        {
          "name":"score_of_reading",
          "value":""
        },
        {
          "name":"score_of_total",
          "value":"990"
        }
      ]
    },
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd"
  },
  "credential":{
    "schema_id":"N6r4nLwAkcYUX8c8Kb8Ufu:2:CertificateOfTOEIC:4.0",
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "rev_reg_id":"DrLbXFSao4Vo8gMfjxPxU1:4:DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd:CL_ACCUM:0fb34de2-af15-4a12-96b4-617df0604620"
  },
  "credential_request":{
    "prover_did":"97tE6e4v2K4vu1qjTWCFNg",
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "blinded_ms":{
      "u":"46831997328364301643560418398490601107404402418858671578137178928437446873320264586769819734807592006981843643797048636176545942127626113581637633992678985809986539645073812116060735925040113989648014245325647116102311574929207191431153445351031881214362589607267342496329275279987379019841630697479605548573417452012941350282137790586135125417975457165516639878476191673523942783822974022772301207764127613893393119636018290950386848847603439063968839433929801323062062302134399119778461191205998350172502435589826107416478327124481726806932655767216483793975040135290729197346256729889112751201367369575399155096029",
      "ur":"1 0F8566D0279976405786E75C4A6C55488578ADDAEA35B161CCA50A7C3BF45F76 1 12B89567472ECE510DF69F2E8208D4B32467740DC7942DA436BBA71E1AC385FE 2 095E45DDF417D05FB10933FFC63D474548B7FFFF7888802F07FFFFFF7D07A8A8",
      "hidden_attributes":[
        "master_secret"
      ],
      "committed_attributes":{

      }
    },
    "blinded_ms_correctness_proof":{
      "c":"79491694275957467808663629857140069543040376706162470853242122646197063169925",
      "v_dash_cap":"1435122002388533961027134840159500466316860333018095865228711993601047581332651750648078735798372784951671417597347488299580664488139988252974317356152922955508177537347153034916967960466880091097323588335739644113576341875606149325572872657807648450848862334571776241826695852478818185193649035809144532141522939442397385211676304172432050461183359300378609457138799743916183219947105371468310249097326871657166141245084029345180165327788167467034165019571462953242409072307876551061122242242516171023616917326212952490302476722937997150723212278225162787655593641181435922421638437076431287624156790626819428454807130945831358912551350425322605790927087848774628193538637712182142787574161794595598240100786038052508",
      "m_caps":{
        "master_secret":"2319623634283540032715827729851714852924001696855662216781048128776680239347185112688128861808689679482193826453156849849945078040235212522290996342499868425107046831682663417157"
      },
      "r_caps":{

      }
    },
    "nonce":"629297215437572416239898"
  },
  "thread_id":"217c6a09-eb41-4f0e-bad2-981be4622759",
  "credential_definition_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
  "credential_exchange_id":"2aa723e2-16f8-45ff-9c29-0b88ce6dbf09",
  "topic":"issue_credential"
}
```


`state:credential_acked` Webhook Event Sample
```json
{
  "revocation_id":"13",
  "credential_request":{
    "prover_did":"97tE6e4v2K4vu1qjTWCFNg",
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "blinded_ms":{
      "u":"46831997328364301643560418398490601107404402418858671578137178928437446873320264586769819734807592006981843643797048636176545942127626113581637633992678985809986539645073812116060735925040113989648014245325647116102311574929207191431153445351031881214362589607267342496329275279987379019841630697479605548573417452012941350282137790586135125417975457165516639878476191673523942783822974022772301207764127613893393119636018290950386848847603439063968839433929801323062062302134399119778461191205998350172502435589826107416478327124481726806932655767216483793975040135290729197346256729889112751201367369575399155096029",
      "ur":"1 0F8566D0279976405786E75C4A6C55488578ADDAEA35B161CCA50A7C3BF45F76 1 12B89567472ECE510DF69F2E8208D4B32467740DC7942DA436BBA71E1AC385FE 2 095E45DDF417D05FB10933FFC63D474548B7FFFF7888802F07FFFFFF7D07A8A8",
      "hidden_attributes":[
        "master_secret"
      ],
      "committed_attributes":{
        
      }
    },
    "blinded_ms_correctness_proof":{
      "c":"79491694275957467808663629857140069543040376706162470853242122646197063169925",
      "v_dash_cap":"1435122002388533961027134840159500466316860333018095865228711993601047581332651750648078735798372784951671417597347488299580664488139988252974317356152922955508177537347153034916967960466880091097323588335739644113576341875606149325572872657807648450848862334571776241826695852478818185193649035809144532141522939442397385211676304172432050461183359300378609457138799743916183219947105371468310249097326871657166141245084029345180165327788167467034165019571462953242409072307876551061122242242516171023616917326212952490302476722937997150723212278225162787655593641181435922421638437076431287624156790626819428454807130945831358912551350425322605790927087848774628193538637712182142787574161794595598240100786038052508",
      "m_caps":{
        "master_secret":"2319623634283540032715827729851714852924001696855662216781048128776680239347185112688128861808689679482193826453156849849945078040235212522290996342499868425107046831682663417157"
      },
      "r_caps":{
        
      }
    },
    "nonce":"629297215437572416239898"
  },
  "credential":{
    "schema_id":"N6r4nLwAkcYUX8c8Kb8Ufu:2:CertificateOfTOEIC:4.0",
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "rev_reg_id":"DrLbXFSao4Vo8gMfjxPxU1:4:DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd:CL_ACCUM:0fb34de2-af15-4a12-96b4-617df0604620"
  },
  "credential_offer_dict":{
    "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/offer-credential",
    "@id":"98b5ffd6-b7e4-4c56-a58d-68a11dc21f97",
    "~thread":{
      "thid":"217c6a09-eb41-4f0e-bad2-981be4622759"
    },
    "credential_preview":{
      "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes":[
        {
          "name":"date_of_birth",
          "value":"null"
        },
        {
          "name":"date_of_test",
          "value":"20190105"
        },
        {
          "name":"english_name",
          "value":""
        },
        {
          "name":"exp_date",
          "value":"20220228"
        },
        {
          "name":"korean_name",
          "value":"null"
        },
        {
          "name":"registration_number",
          "value":"null"
        },
        {
          "name":"score_of_listening",
          "value":"401"
        },
        {
          "name":"score_of_reading",
          "value":""
        },
        {
          "name":"score_of_total",
          "value":"990"
        }
      ]
    },
    "offers~attach":[
      {
        "@id":"libindy-cred-offer-0",
        "mime-type":"application/json",
        "data":{
          "base64":"eyJzY2hlbWFfaWQiOiAiTjZyNG5Md0FrY1lVWDhjOEtiOFVmdToyOkNlcnRpZmljYXRlT2ZUT0VJQzo0LjAiLCAiY3JlZF9kZWZfaWQiOiAiRHJMYlhGU2FvNFZvOGdNZmp4UHhVMTozOkNMOjE2MTc2OTgyMzg6ODFkZjAwMTAtNjJiNC00NWIxLWJkMDAtOGQwYWQ3NDc2MmZkIiwgImtleV9jb3JyZWN0bmVzc19wcm9vZiI6IHsiYyI6ICIxMTUzMDkyMDc2NjU0Mjg0MDYxNzk1NDU5OTQzNzM5MTIxODEyODI4MzE1NDUxMTg1NDAwMTQyMjk5NjcyNzE3MDc4OTYyMjQ5MzE1NTAiLCAieHpfY2FwIjogIjEwNTU1NzI3Njk4NjI2NjM0NjM3MDk5MzQyMDQ2MDE5NTQwODA0ODM4OTg4MjYwMzA4Nzk1OTU5NzkwNzQ5NzEwMDQyMDY4NTkzOTM3ODI2NTU1OTkzMjQxNDA5MTM5MDI2NTM2NTAzOTg5Mzg5MDY2ODE2NDE5MjQzOTA3OTQ4MTI3Mzg4ODc4MTE4OTU1NDU3MTEwNjM1NjQ0ODExNzkxNzIzMzA4ODEwOTQ5NTI0Mzg5NTA4MzYzMjYzNDI4MzA4NDU5Njc5ODc4NTUyNjUxOTE3ODM1NjU2ODgxNjk5NzU4NjMzMjQ1NDU1NDUzMTU3ODE2NjkwMjQ1OTY5ODYyNTcxNDMwNjE1MjUyNjQ4OTY0NTg0ODY2MjU0MzEyODA3NjQ1NjY4NTgwMTAyNDg2MjYzNDY4MTE4MTgzMzI1NTMzNDg5OTQyNTUxMDk0MzAwNTM5NDA0NTIyMjIzMzAyOTY3MjEyNDQxMzAxMDkxMzk3Mzk5MDU4MTk2NzQxOTgxMjM4MjU0MjM2OTcwMTQxOTQ2NTY2MzAzOTU1Mjc1MzMwMzU1ODA5MTk5MzU3Mzc0OTk1ODk4MzE5NjY2NTkyNTA4NjM3OTgyNjE3NTk0MTY3NzkyMzY3NDc2NzQxMTY4OTk5OTE1NDg1MjA3ODMwMDc0MzIxODIyMDU2NDk3MDA1ODczNTg2OTUzMjIxMDU5MTUwNTE0ODM4MzAyNDYxMzU1MTgzODU1NTQyNjM1MzEzMzczODc2MjY3MDQ1ODc0MDU2Njk5Njg3NzE0MjE4ODAzMDY4NTAxNTYwNDg3NDg1NzM3MzA1NzU3MzUwMTYxNjA2MjI3MDY2MDU4ODUwOTM2NDQ3ODI1MzI2NDczNzM2NTkxOTY3ODQyNDU4MzI3MjMwODUyMTM0OTcwNDg5ODk1MjAiLCAieHJfY2FwIjogW1siZGF0ZV9vZl90ZXN0IiwgIjEwNzYyMzIxNTkyNDk5NjQ3MzQxMDk3MTg1ODk3NzcyNjc0NjIxMDk0ODQwNzA0NDA0MTU2OTE1MTE4MjY1MjE1ODk1MjgxOTU5NjAxMTcwMDY2MDE1OTY0OTk4MzM4ODExMDY3ODc1MzI5MDg1ODI3MzU2ODU5NDcxMjUzMDM2Mzc3Nzk3NDk4NjUzOTU5NzM3NDg3ODY4NDUzODExMjU2MTQyNzYxODU3NzYzMjU1MjEyODQ1MDc5NzkwMjIyMDI3NjUwMTU3MzQ1MzE2Njg0OTQ0Nzg2Mjc0MDQ2NDQ2NzQ5MjIxMTUzNTEzOTI4MTA5MzUzMzY1MjkxNzkxMDg0NDYzNjIxNTcxMjMzMTU1NjUyNjA3MjU0ODMwMzUxNTk1NDY5Nzc5ODI2MzYyNjgyNzM3NDExMzQ0NDc2MjU2NTY5MTMwODMwNDk0NzExNjM4NjMzMjU1ODA1NDU3NTEyODEyODE3MDcyMzkwMzAwMTE0OTE5MDUzNzU2NzM1MjE2MzU3OTA3MTczMDExODg3MTY5ODk0MTkyNDU1OTQ0MjU2Njc4OTczNzIxMzc4ODc2MzIwOTEyNTc2OTI5OTIwMDIyMDQzNTMxNzIxNDEwMzU1MzI2ODY4MTk5NTY2NTU0NjQyNzQ3Mjg4ODgxNjYyMzM4MDA3MzAyMDk2Mjc2MjczMzMxMTgzNzIxNzYxOTAxMDg1ODE4MTkwMjg5NjIzODM0ODc2MTkyOTE4NDUwMjA5OTQwODE5MTIyMDgxNDgwNjE0ODIyNDExNTA0OTE3OTg2MzAxMDU1NTk4NjA0NjQyOTIxMDA5ODc2NTA5MDY5NDk1NDgyMjgwMTI5MDgwOTkwMzYyMzQ5Njk5NzgwOTE1OTA1NzcwNTY4NTk4MTQyMDU1OTg4Njc2MzI5Mjc5NTA3MTUiXSwgWyJlbmdsaXNoX25hbWUiLCAiNjk2NDExMTMwOTM5NTgwNTMzNTEwNDM2NjY0MTk0NTI2ODE5MTU2MDk1NjI5Mjg3NDEyNDM0MDQ1MjM4MzUwODA0NzUwNDU4ODc1MjUyMDA5MTU5ODM3MjA4NjY3MDk2MzU3NzA4MjczODk4ODM2Nzc0NTk2NTY5NTcxODI2NDIxNDE2MzczNjc1NzU4MTUzNDM3MjI0OTYxMzQ3ODAzMDAzNTg3MzI4Nzc0MjM4NjQ1Mjc5NzE4MDExMzA1MjM0NDMwNTEwOTE0MzM5MDMwNzM5NzcyMTQxMDM0OTM3ODIyNjEwNjAxODk0MTAwMDkyNjExNjk5MDUxNTg3MzY4NTcyNDc2Mzc4MDY2NTcxOTk1MTk3MTYwMzA4NzcwODMwMzMxNjUxNjg0MTU3NDIyMzIxNDk0MjEwNTY1NjIyMjU0OTM2MTc5MDUwMjU2MTk2Mzk1ODM5ODM2NDUwNzM5NzA4NjAzMTkzMjg1ODk2Nzg1ODAxMzQwMDY2MjMwNDgwMDQyMzQxOTU0NjY2NjIwNzAxMTIxOTA2NTkzNjMwNjU1OTk4ODY0MDA4NTk4MTk0NDk4MDUyODAzMjE1NTk4MjQ2OTIzNzE2OTYxODI3NzY1NDE1NDI3NDY3NzI1NjM5Nzc4Mzk5ODQzMTc4OTkxOTM0MzE1OTAxNzg1OTI3MjAzOTY0MTgzMjQ0NjE5MzA3MTU4MDIwNzMzODE3NTE2NDE5ODc1MDk5ODQ2OTI3MzU2ODAwOTI5ODA3MjA4NzEzMTAyOTQxNTk0MDQ5NTc3MzExODUxOTcwMjk2MjY1MzA1Mjc0MjM1NjQyMDU5ODAwOTkzOTM3ODA1NTkzNDQ0NjAyNjczNjIyMjYwMzYyMzYxNTUzMjAwMDEwMzI2NzQ0Njg0ODgwMTEzMzQzNzczMjI0ODU2Il0sIFsiZXhwX2RhdGUiLCAiMjQyMzY1MDEyMzE2NDkwNTUyOTM2MzYyMTEzNzgwOTcyMzE0NTMyNzg2NzU0Mjc1MzA0ODc2MDYxMDczMDY3NDI4NDc1NTczOTk3NjM1NjQ4NzQ2NjI2NzU3NzM5NzcxMzY2OTkyNzgyNDU1NTcxODA3ODY3NDcyOTMyOTgyMjEzNjYyMzUyODQ5NTAwNDkyMjA1Nzc0MTkzNTk4NDgzMjc2MjkzNzg4Mjk1Mzc5ODAwNzgzODMxNjIwNzk0NDUwODAwMDIyNDMzMTM4NTk5NjQ0MzI2OTU4MDc3MjQ5ODc2OTg2ODU2NTAwNzYxMDUwMzEwMzA4NjQ2NTMyMDkyMTA5NDY4MjI4MTYwOTYyODg2OTkwNjMzNDc0NjA3OTgzMjk2NzM1MDkwNzk4NjkyNTM4NTU5MTg2NzAxMDUyNzYwNzE3Nzc5NTI0OTQyMzM1ODU0NDk4MTg0Mjc3MjEwNjA1NjMwMDY1OTQ4NDMyMzk0NzE4OTA1Mjc1ODA5ODA1MjcxNzI5MTgyMTYyNTk0NTYyNTE5NjAzOTM2Mzg4ODU3ODMyNjk0MDM3MTIyOTY5OTY3MjM2MzIxOTI2NDUzNDg3MzgwMjg1MjQ3NjcxODczODM0NzMwMjkyOTk0MDQ3MDI5ODc4OTg3Njg4NjE5NjkxMDQ2NDM4MTU2ODIyMDAzMjUwMTE4MzQxNjM0OTg0ODE0Mzk4ODE3MzcyNzE1NTE5NDcyODcyMDg2NDIwNTE4OTc3NzI4MzMwNTM0OTg0NTUzMTI3MjQyMjgwNTIyODI1MjY4NzcyNzk5MjE4MjAyNjk4MjYxNDQ2MjIwMjY0NDg1NjI4MDI2NDAzNDQ1MDgxNDc4NjYwNDQ4MzI5NjA5MjA1NjY5OTk5MTU0NDc0OTA5NzcyODg2ODQ4Mzc0NDQ0NjMzMSJdLCBbInNjb3JlX29mX3JlYWRpbmciLCAiMjI2Mzg4NjAxNDE2MzY5NjU5MDg3NzcxODgxOTEwMzQ1NTE0MTgzOTc2NDEwNjgzMjc2ODU5Njk1OTQ4NDk3MjUyMjQ4OTU3NTAyNzAxNzg4ODM3Njk4MTU2ODEzMjY0MTkyMzM2Njg1MDY0ODI4Nzg0MDE1MzkxMjEyNDM2NzI3NTM3MTQ3NDc2OTc5MDUwMDQyMzUyNDYwNDkxOTU2NDIzODA5Mjg1NzIyNDI3ODMwMDUzNDAwMDg5NzYzNjgwMTg1MjcwNTgwOTk0MTA0NjU4MTc3NTU4OTc1MTg2MDUyMjIxNzQ5MDY0NTMwNjc1NzM5MTMyODMwMzMxOTk1NjA1NDExODM5NTk1MzExNDgzMTMxMDg0MjgwOTc0OTk3NTU5MTY2MDIxNTQ2NDEwNTU4MDkyNTEzODI2MDIzMzMyNDA1OTUyMDA1MjQ2Njk1MTQwMTAyODc1OTQ3MzY0NTk4MjE2ODE4ODE5MDE3NjIyODAwNDg1NDQ3NTk4NTk0MjAxNjMwOTk5Mjg3ODc2ODgwMTk1NDA1NzQ4Mzc0Njk0NTg3MzU3MDI5NTg4MjQ4ODQzMTAzMTE2NjEzNjgwODYyNDEzNDE1MzgyMDYzNDMwMTQzODIwMzcxNTIyODU0MTA2MTg0MzUyMDQ0OTgzNjYzMzE3NDY5OTM1NzY5MzUwNTk4NDg1MjU2NTQzNDA0MzEyOTQ4MjIyMDc0MTkwNDQ1NDA4MDA1MTMyODMzODE1MzAwMTE5NjY3MDk0ODk5MDg5NjU4ODMwNzE0NDQxODMzNDI2NTgzMTQ1OTUwMjg4OTkzOTY4MDMxMjUzNDg5ODQ3MjY0NTAwNDEwMzI4NTYzMjgyNzU2MDkyNzE3MTQwNDcyMDg5ODg1MTMyOTQxNzYwNTQ1MzYzNDgzMTQ1MjEwNTg4MCJdLCBbImtvcmVhbl9uYW1lIiwgIjE3ODg5MDUxMzAxMTEyNjU5MDQ0NTY1NzQ3NDAxMjQ5MjIzOTY1NjUyMTUzNTQyMDQzODA0NDk1Njc1NzUxOTk2MzMwNTMxMjcxNjAxNzQyMzQ3NDAzMzQxNjkxNjM1MzMyMzQwOTgxNDg0NjY3NDIyOTIzOTI2NzQ5ODQ0MTI5NTU5MjgyOTUwMTE4OTc5MTQzOTA4MjUzNjY4ODM2NzA3MDQ1NjIyNDQwMTk4MTIwNDE1MjMyMzc5OTA5MjY0NzExODkwNzgzNjU4NDg0ODg4MzMyNzAzMTE0MzUzOTQwMjYxODEyMzI4NjQ2OTgxMDgyMTE1Njg0OTQ0MTM4NTQzNTMzNTk1MDE1NDU1OTcyMjgzMjM4MDc2Njc2MDY3NTMwOTQ4Mjk5NTM2NjA3MzAwMjEyOTYwMjk2MTk1NjczNzAyMTk3MDE2ODI4Mjk2NTUzMTMxNTMwMTc1Mjc4MzgxMjQ0NTU3ODk3NjcwMzYyNTIxNzgzMDI2Mjk0NjU1MDc0MjY2MTAyODYzNjQxNjQ2ODE0OTUzNTMxNDUyNTExNjQ1NTcwODIzNzU5NTgxOTcxNjk3MzMyNTM2MzMzNzI3NDE0NDc0MzAyNTUzMjMwNDMyNzgyNDQyNjU0NjE4MjY4MDI2OTc2ODA1NjUxNjg1NjE1MTk3Nzk0MzA5OTA0NzA0NjM1OTU1ODYzOTA1MzEwNDE3Mzc3ODk5MTM0NzAyOTUwNTcxMDQ0NzYzNjU0NDEyOTM0OTMzODUxMjAzMzk1MDMxODQ4NDMzMDI1MjI0NTU2MjI4MDE5OTk2MzQzNjg0MjA1NTEzNDUyOTAyNzAxNzQyMzg3NTg0Mzc1ODY1ODIwMzQ5NzQ2NjI3MjEyOTU2NzUzMjg3NDI2NzYyNTU4NjM1MTgwMTcwMTIxMDI3NDE3NTQiXSwgWyJzY29yZV9vZl90b3RhbCIsICIxMTMxNDA4MjI5NjIxMTIwMzQxNDE5NzE0MzYyNTk2Mzk3MjU3NDM3MDEwMzU2NzYxOTcwODMxMTE3MTU5NDc5NDYyNjQ0OTA5MDkyNjA3MjYzMDgyNzI4NTAwMjI0NjYzMjg3NDMyNzkzNjg3MjA2OTI0NjU2OTM0NjIzMDA4NTQ5NDYxMjkyMTQzMzQ5OTk0OTY1MDE0OTk2MjIwNTQ5NjAwODUwMTcxMzQ3Mzk5NDI1OTk1NzMxODY5MjA2MjA5NzIzMTIxODM2MTQ2MjkxMzAzODUyNjM0MTE5OTkzNjU2NTMxMzc5OTEzOTQ5OTg1MzY3MjY4NDE4OTk2OTQxMjkyMDczNDM5NTkwNjI4ODY1MDQ5MjY0NTc1Mzc0MzY4MzMxNjg0MDU5NjM0MTE2MzA1ODMwODc4MDA3NTA1NDY0NDgxNTg5MDk3NTI0Mzc3NzMxMTA1NTkwNTI0MDk5NDYxMjE4OTc1NDI2Njc3MTI3MjM0NDA4NzAxMTU5NzIxODMwMzE2MTcwNzg5MzYxNDc3OTI1NTI1MDk3MDgwNzUzMTE0NTE2Nzg5NzY3ODEzMTI4MDQxODQ3Mzk3NDMwNTIzMTYzNzMzODcxMDA4MDA2MzE4MDAwMzk0MjUxNjIwNzA0MDA3MDIzMzExODg2Njg2NDg5MjEzODU1MzYyMjg3MDEyNTE5NTI0MzQ1OTE4OTcxNjA1MDIyMDYyNzU1MTUwODY5NTExMDM2MDI3Mzg1MTIzMjE3NjQ0NzU0NzQwNTE3MTI1MTQwNjE4ODc5Mjg2NjM3NzUyMzAzMjQ5Mzg2NjIxNDUwMzEzODQwODEwNjAxMzg5NTI2NDYwMTE0MzA3MjM5MjU0NTA5OTg3NzI1NTczNDAwNDQ4NDAwNDg5OTM3NjI4NzIwMzQzMjA5Mzk1MDg5Il0sIFsicmVnaXN0cmF0aW9uX251bWJlciIsICIyMjQ3MDAxNDE4MzQ3NTgyMjcwOTg4NzI5ODUzNzk4NDE2NjQ0MzE5NDczOTA3NDU2ODYyMjAwODEwOTIzMTQ4MDMyNTcwNzI0NDQ1OTg5MDQ5NDY3NDA4NzgzOTI5NTI1MzE5MDg4Nzk2NTI4ODQ2MDc0MzAwMjk4MTY5MDk1ODMyMDA4MDkwMzQ4NzY1MDcxODE5OTAzNTkyMjU5MDU0NTM5ODI0MTA3NTQ4Mzk2MDU0ODMxNjIwMTU2MDY1Mzk3MzUzNTY0MzE0MDc1ODMyNjA2MjEwMDAyOTc3NDE0MDMxODA3MTkwODAyMDM2NjE0NzExOTYyNzUzMDQ1NDU5MzAyMDU1OTk1NDgyNzExMjAyNDc2MjY0OTEzNzI5MDAxMDUzNzQyMzgxNTEzNjI3OTY3MjM3ODQ3NTY0ODc5MzEwNDUwMjk2NTIyNjU2MTUwOTMyMTkwNTg3MDg5NDk3NDA1NzcyNTE1NDYzMTQ2MjQyOTk0NDQ0Nzc0MTA0ODgyNTY4MTI2NzYwMjAxNDM4NjE1MDcwNDU1MTc4MDIxOTM5NjUyNjI5NTY3NzAwMzY5NzIwODMxNDAwOTQ1NjcwMTU1NjExNTU2MDc5ODc0MDIwMDYzMTkxNjQ4MzY1NzAzNTYxODI1NDkxODcyOTMzMDExMTQyNjU0MzI3ODE1MjU4NjU5MTE3MDUwMDk2NTg4MzgwODk4Njg2MDU4ODAwOTA4NzA3NjA3MDQ5MTM3MDA3MjM4NDUyNTg5NDM5MTI2Mjk0NTc3MzA2NTIzMzc3ODM1NTY3NjM2NzgzMjcwNDMzOTMxOTY5OTA3MTU1MjQzODgxNjUwMjEzNTUyOTcxNzAwMDQwNjM5MTAyMjY0Mjg5MDE4MTU3NjI4NjgxMDQ2NDAxNzUyOTU1OTg2Nzk3OTMzMjE4Il0sIFsiZGF0ZV9vZl9iaXJ0aCIsICIxNjIyMDM2MzYxNTMxNjAxOTEyMjUxMDg0ODIwNDE4NzAyODkxNjAxMDQ3NzgyNjM3NjI1MTc1MTM4ODI1NTcyNjA1NzMzMzkwNjU3MzcwNDI1OTA4Mjc2ODQ3NzAxNTM3NTk4OTgwMjczNjA0MzgyMDcwNDU2NTg3ODQ1MTgyMjAwOTExNTUwNDEwNDk2MDIxMTEyOTM4MTE2ODE3NDY5MzI4MTIyNzQwMzQzNDY1ODQyMTQ1NTE3NTIzMDY4NDI2MzMwNzk3MTAyODU3MTc0NjgwMjE3NzMwNzQyNjM2MjgwOTYyNzAyNjc4MjE0MDEyMjEyMDU1MTc5NzgxMDU4OTM1MjUzNTg5OTczMDU3OTkzMTgyMjk0MzI1Nzc3Mzc3MjI0MTgzOTkyNjAzOTcwNDA4NzM3ODQ4MjY1NjA4NTU3ODE5Nzc3NjU5NTI5MTQ2MDI5OTE3Mzg0MDMyODQ0NTMxNDA4NjQ3Mzg5NjQzODI2NTA2Nzk3ODk2OTg2MDQ2NjYxNDU3NTUyMTc1MzM5MTEzNTMyMTc2OTM4MzI4NDY0MzgzMjAxMDAwOTg3MzQzMjYzNDI5MDY5NzA1MDIzMDU5MTQxMzE5NTg1MjA1MzU0Mjc3NTcwMDc1MTgxNzMwMjUwODgzNzg2NDYyNzg2NDQ2Mzg1MTYwMjg1MTA2NzI2MzY3MzE4MzEyNTY5NjIzMzk0Mzc1MDg3NTA0NDAwODA4Mjc5ODg3OTE3NjAzMTE4ODAzODE5NDkxNDIyMzcyNTA4MTYzMzQ5NTcyMDAwMDIwNTc4MDI0NDk0MjQ0NTkyMzAzOTE4NzE0ODkwNzI2NjgyNjEwMDcyNzg4MDM4Mjg1Nzg4MDAzNTkwMTkxNjM5ODI3OTEzNDY0MDk1NjY5MTU5MjE5NzY4MjYwNjQyNDk0NzY3Il0sIFsic2NvcmVfb2ZfbGlzdGVuaW5nIiwgIjEyMDIwNDMxODY1NDEwMDAwNzcwNjczMTg4OTQ2NjU5NDQ0ODE0ODk2NjgxMzUwMjIxMzQzMTQ0NDM0MDQ0MzAzOTYyODY4OTk0NjA3MTI3MTYyOTY3NTY0MTAzMTQzNzk4MDkwMTI0MTY5NDEwNDkyODczMjgxNjMzNDY2MzE3NzM1OTYwMTIwMjk1NTUxNDIzNzg3ODQ1ODAxMTk1OTYzNDQ0NTUxMTg3MzU5MjY1MTE5MjM0MDc1MzU2MDU5NTI4ODUwMjAzMjU0NzU5NTYzNTE5MjU0NTMyNjQ3OTAyMjY1NTk2ODY0NjA3MjE0NDU2Mzk1MDk4MjQxNzYxNTEyOTUyNDE4NDQyNjE0MjQ4NjQ5MTY4OTQwMTAyNjIwODAzMTQ0MjM0NTMxNTY0OTY3NDA3NjE5MDE5NDgzMjM5OTQ3MzI2MTMyNzYwOTM3NTY0NzIzODMzNDgzMzMzMDA3ODI2Njg1OTU2ODU2OTc1MjU5NTY5NDI1MjIyNTcwMDYzMzc1NTQyMTA3NDMzMTk4NTA1MDY0MDcyMTU0MzI2MjY1NjAwMjcyNjc1NDgzNTI3Mzc1MzYwMTA3MDQyMjg5ODAwNzY5MjY3MTg1MTk1Njk2NDQ5MDA2MDAzODMxODkwNDk0Nzc2MjU4NjQ2ODcwMTE5MjY0NDA3NDYzMjA2MTg4MTA2NDQ3NTQyNDUxNTQ2NTA1MjAyMTgzNDkzNzYwNTgwMjU4MDU0NTU2NTMyMjAwMjM0NDM5MjM5MTUxNzAwMjgzMDg0OTE5MTU3MzA5NTQ4MzQ3NTE3MDYzMzQyNzk0ODE5NDczODcyODQ5NzA2NDMwNDk0Mzg2MjYzNzI2NTkwNjkyMDA2MjAxMzY5NjE2MjA4MDc3MzYzMjAyNjY1MDg5MzU2MDg3Nzg4ODExMjg0MDUiXSwgWyJtYXN0ZXJfc2VjcmV0IiwgIjc1MDU4NDYyMTM5ODU2NzA0MTI1NjU0Mzk3NDk0MjU0MTk5ODc0NTc3MTY4MTMzNTE3NTc4NTA0ODE0MDU0NTg4NzA1NDAzMzc2MTQ2NTMxMTgwNTE2MzA1MTExMDQ1NzQ4NTg2NjU3ODUwNjM5OTQ4NDYyODk4NTQ4MzA5NjI4NjgxMjI0NTQzNzg2NjcxMTg0NzY5MjU0MjM4OTM5NTgwNjUwNjIxMDM0MzYwMzY0MDk3OTc4NDY4NjM4OTYyNjgwMDA0NDQ4ODIxODIzMTc3NTI1NzE5NzE4NDk5NzY2ODg2NjQ4NzgzNDc4NTA2OTkxMzQ3Njg0NDczMDQwNzY0MTkyMzg2MDEwNTA0MjAzNTM5MTQ2MDE4NzMzNDk1OTg1MTIzMDc5OTc1NjUzNTg4MTI4OTg0MTI0ODUzNjcxODQ1MzMxMjU0NjcwNDc0NDk5MDY1MDEwMTk3MzA4NTMzNzI4MDQ3Nzc3MDkyMzU5NDUwNDgzMjYyNzYzNTQwNzIxOTc4Njk2MjgxNzA2NDQwMzYzNTM0MjE1NzI5NDM3MDQ1OTAwNDEzNDc4ODUwOTY4OTA2Nzg2ODE0MTAzODA4NTE5MDI1NzI5MDYwODg4MTAxMDgyNjcxMTQ0NjAxMzE5ODgyMDk4NDI1NDI3NTIzMDc2NDY3MDc2MzAxNzcwMDU5MjU1ODM0NTgxOTYyMTY0MDE1NzM4NDg4Nzg4NzI3MTIzMjU2Mjc4Mjg5MjQwNjk5NzA0NzE3OTc5ODYyMDM0MjY3NzU0OTM1MzUyNzA2MjM0NjE1MjAzMzM1OTc1NDQ0MTYyMTkwNjY1Nzg0MDk2ODUwNjg4NzA2ODU0NDMxMjA0NjIzMDU4MTc2ODg2Mzg1MzczNzMwNTkzMzY3NTYwNzEyNDE1ODUxMjkwODczMjEzIl1dfSwgIm5vbmNlIjogIjQwMzY5NjI4ODU0OTA3MzE0OTQ0NDMxNSJ9"
        }
      }
    ]
  },
  "schema_id":"N6r4nLwAkcYUX8c8Kb8Ufu:2:CertificateOfTOEIC:4.0",
  "credential_definition_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
  "revoc_reg_id":"DrLbXFSao4Vo8gMfjxPxU1:4:DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd:CL_ACCUM:0fb34de2-af15-4a12-96b4-617df0604620",
  "connection_id":"d57cee53-6d64-444d-aa27-faec84d25c46",
  "credential_exchange_id":"2aa723e2-16f8-45ff-9c29-0b88ce6dbf09",
  "created_at":"2021-08-06 04:43:14.481031Z",
  "thread_id":"217c6a09-eb41-4f0e-bad2-981be4622759",
  "role":"issuer",
  "state":"credential_acked",
  "credential_proposal_dict":{
    "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
    "@id":"915f582a-1703-404d-976a-3a7fdd50ac1f",
    "credential_proposal":{
      "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
      "attributes":[
        {
          "name":"date_of_birth",
          "value":"null"
        },
        {
          "name":"date_of_test",
          "value":"20190105"
        },
        {
          "name":"english_name",
          "value":""
        },
        {
          "name":"exp_date",
          "value":"20220228"
        },
        {
          "name":"korean_name",
          "value":"null"
        },
        {
          "name":"registration_number",
          "value":"null"
        },
        {
          "name":"score_of_listening",
          "value":"401"
        },
        {
          "name":"score_of_reading",
          "value":""
        },
        {
          "name":"score_of_total",
          "value":"990"
        }
      ]
    },
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "comment":"JJ Test"
  },
  "trace":false,
  "auto_remove":true,
  "auto_issue":true,
  "updated_at":"2021-08-06 04:43:43.961489Z",
  "initiator":"external",
  "credential_offer":{
    "schema_id":"N6r4nLwAkcYUX8c8Kb8Ufu:2:CertificateOfTOEIC:4.0",
    "cred_def_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
    "nonce":"403696288549073149444315",
    "key_correctness_proof":{
      "c":"115309207665428406179545994373912181282831545118540014229967271707896224931550",
      "xz_cap":"1055572769862663463709934204601954080483898826030879595979074971004206859393782655599324140913902653650398938906681641924390794812738887811895545711063564481179172330881094952438950836326342830845967987855265191783565688169975863324545545315781669024596986257143061525264896458486625431280764566858010248626346811818332553348994255109430053940452222330296721244130109139739905819674198123825423697014194656630395527533035580919935737499589831966659250863798261759416779236747674116899991548520783007432182205649700587358695322105915051483830246135518385554263531337387626704587405669968771421880306850156048748573730575735016160622706605885093644782532647373659196784245832723085213497048989520",
      "xr_cap":[
        [
          "date_of_test",
          "1076232159249964734109718589777267462109484070440415691511826521589528195960117006601596499833881106787532908582735685947125303637779749865395973748786845381125614276185776325521284507979022202765015734531668494478627404644674922115351392810935336529179108446362157123315565260725483035159546977982636268273741134447625656913083049471163863325580545751281281707239030011491905375673521635790717301188716989419245594425667897372137887632091257692992002204353172141035532686819956655464274728888166233800730209627627333118372176190108581819028962383487619291845020994081912208148061482241150491798630105559860464292100987650906949548228012908099036234969978091590577056859814205598867632927950715"
        ],
        [
          "english_name",
          "696411130939580533510436664194526819156095629287412434045238350804750458875252009159837208667096357708273898836774596569571826421416373675758153437224961347803003587328774238645279718011305234430510914339030739772141034937822610601894100092611699051587368572476378066571995197160308770830331651684157422321494210565622254936179050256196395839836450739708603193285896785801340066230480042341954666620701121906593630655998864008598194498052803215598246923716961827765415427467725639778399843178991934315901785927203964183244619307158020733817516419875099846927356800929807208713102941594049577311851970296265305274235642059800993937805593444602673622260362361553200010326744684880113343773224856"
        ],
        [
          "exp_date",
          "2423650123164905529363621137809723145327867542753048760610730674284755739976356487466267577397713669927824555718078674729329822136623528495004922057741935984832762937882953798007838316207944508000224331385996443269580772498769868565007610503103086465320921094682281609628869906334746079832967350907986925385591867010527607177795249423358544981842772106056300659484323947189052758098052717291821625945625196039363888578326940371229699672363219264534873802852476718738347302929940470298789876886196910464381568220032501183416349848143988173727155194728720864205189777283305349845531272422805228252687727992182026982614462202644856280264034450814786604483296092056699991544749097728868483744446331"
        ],
        [
          "score_of_reading",
          "2263886014163696590877718819103455141839764106832768596959484972522489575027017888376981568132641923366850648287840153912124367275371474769790500423524604919564238092857224278300534000897636801852705809941046581775589751860522217490645306757391328303319956054118395953114831310842809749975591660215464105580925138260233324059520052466951401028759473645982168188190176228004854475985942016309992878768801954057483746945873570295882488431031166136808624134153820634301438203715228541061843520449836633174699357693505984852565434043129482220741904454080051328338153001196670948990896588307144418334265831459502889939680312534898472645004103285632827560927171404720898851329417605453634831452105880"
        ],
        [
          "korean_name",
          "1788905130111265904456574740124922396565215354204380449567575199633053127160174234740334169163533234098148466742292392674984412955928295011897914390825366883670704562244019812041523237990926471189078365848488833270311435394026181232864698108211568494413854353359501545597228323807667606753094829953660730021296029619567370219701682829655313153017527838124455789767036252178302629465507426610286364164681495353145251164557082375958197169733253633372741447430255323043278244265461826802697680565168561519779430990470463595586390531041737789913470295057104476365441293493385120339503184843302522455622801999634368420551345290270174238758437586582034974662721295675328742676255863518017012102741754"
        ],
        [
          "score_of_total",
          "1131408229621120341419714362596397257437010356761970831117159479462644909092607263082728500224663287432793687206924656934623008549461292143349994965014996220549600850171347399425995731869206209723121836146291303852634119993656531379913949985367268418996941292073439590628865049264575374368331684059634116305830878007505464481589097524377731105590524099461218975426677127234408701159721830316170789361477925525097080753114516789767813128041847397430523163733871008006318000394251620704007023311886686489213855362287012519524345918971605022062755150869511036027385123217644754740517125140618879286637752303249386621450313840810601389526460114307239254509987725573400448400489937628720343209395089"
        ],
        [
          "registration_number",
          "2247001418347582270988729853798416644319473907456862200810923148032570724445989049467408783929525319088796528846074300298169095832008090348765071819903592259054539824107548396054831620156065397353564314075832606210002977414031807190802036614711962753045459302055995482711202476264913729001053742381513627967237847564879310450296522656150932190587089497405772515463146242994444774104882568126760201438615070455178021939652629567700369720831400945670155611556079874020063191648365703561825491872933011142654327815258659117050096588380898686058800908707607049137007238452589439126294577306523377835567636783270433931969907155243881650213552971700040639102264289018157628681046401752955986797933218"
        ],
        [
          "date_of_birth",
          "1622036361531601912251084820418702891601047782637625175138825572605733390657370425908276847701537598980273604382070456587845182200911550410496021112938116817469328122740343465842145517523068426330797102857174680217730742636280962702678214012212055179781058935253589973057993182294325777377224183992603970408737848265608557819777659529146029917384032844531408647389643826506797896986046661457552175339113532176938328464383201000987343263429069705023059141319585205354277570075181730250883786462786446385160285106726367318312569623394375087504400808279887917603118803819491422372508163349572000020578024494244592303918714890726682610072788038285788003590191639827913464095669159219768260642494767"
        ],
        [
          "score_of_listening",
          "1202043186541000077067318894665944481489668135022134314443404430396286899460712716296756410314379809012416941049287328163346631773596012029555142378784580119596344455118735926511923407535605952885020325475956351925453264790226559686460721445639509824176151295241844261424864916894010262080314423453156496740761901948323994732613276093756472383348333300782668595685697525956942522257006337554210743319850506407215432626560027267548352737536010704228980076926718519569644900600383189049477625864687011926440746320618810644754245154650520218349376058025805455653220023443923915170028308491915730954834751706334279481947387284970643049438626372659069200620136961620807736320266508935608778881128405"
        ],
        [
          "master_secret",
          "75058462139856704125654397494254199874577168133517578504814054588705403376146531180516305111045748586657850639948462898548309628681224543786671184769254238939580650621034360364097978468638962680004448821823177525719718499766886648783478506991347684473040764192386010504203539146018733495985123079975653588128984124853671845331254670474499065010197308533728047777092359450483262763540721978696281706440363534215729437045900413478850968906786814103808519025729060888101082671144601319882098425427523076467076301770059255834581962164015738488788727123256278289240699704717979862034267754935352706234615203335975444162190665784096850688706854431204623058176886385373730593367560712415851290873213"
        ]
      ]
    }
  },
  "topic":"issue_credential"
}
```


<br>

Revocation 사용을 위해서는 아래 [Option] 내용을 확인하고 추가 구현 필요함

<br><br>


### [Option] 폐기(Revocation) 처리를 위한 정보 기록 

기한만료, 자격증명상실등의 이유로 증명서(VC)의 폐기할 경우를 대비하여 `topic:issue_credential`, `state:credential_acked` 으로 전달된 event 중 폐기에 필요한 필수 3가지 값을 기록해야 함. 
만약 김증명에게 VC를 발행했다면 아래 예시와 같이 기록 필요

1. credential_exchange_id
2. rev_reg_id
3. revocation_id

<p></p>

* 발급내역 기록 예시

    항목 | 예제 내용
     --- | ---
    기관 unique id | 김증명 CI 혹은 ID등 기관에서 구분값
    rev_reg_id | DrLbXFSao4Vo8gMfjxPxU1:4:DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd:CL_ACCUM:0fb34de2-af15-4a12-96b4-617df0604620
    revocation_id | 13
    credential_exchange_id | 2aa723e2-16f8-45ff-9c29-0b88ce6dbf09

    <br>상세 폐기 방법은 Revocation page 참조
<br><br>
