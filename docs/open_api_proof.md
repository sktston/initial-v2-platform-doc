(Verify) Proof Presentation
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

<br>

**Verifier**(검증자)와 **Holder**(사용자/initial App)의 VC 검증 시나리오
<br>

![verify sequence diagram](img/verify_diagram.png)
[PlantUML 상세보기](http://www.plantuml.com/plantuml/uml/TPH1JzjA4CVl-ob6d1o4UNT44EXTthjLFN2jJB9fBOpjsciaRW6CH0jIQKj4W3WAhG1H1IaDDCsX_KBUpNVeh3s9XLKa99BT_ypE_7ypMGs55OXeos7S5RROWMqUs0sR1s25CBTEN-Qcjr58DsglhoxkLFV4-CzpwdYuvlkLcSmEmuY4_-kFc82nQXNyW0VyRMI7jk1GhJ96GdCboxm4w__AGISTlmDr7iklnsnMpJ99O0bgdkloch0zrr1dWtGug90wU-95uFbUQ3c6vsj99Nic2lD7A9tEaWwRwYO1rUkef6tu55LXheUb1VRhDuBDvngIJ1DNGEuzOk_ppCplvU0oVUY1EXgff8aTC5D7Z3gJLll9EIQ1IUjUVhi35VSc-maAfow2V1Yh1FyUGTtkgjv7agZUBaopzIXQ9J6LpSoyISk1T3oGxwzLvvUywe3QJsJt1wI39fujCdEvuRa2VNEyeBhXH7nbUMF5TcrXOw3b-nW5KmCWBmbhLsTzAEfJ5wTgU8_lnlAeluYSww2TAW2PJ-qjHngvLnMOdBUmqGpLInm57WemjYotRWalsCbf5Ka9Ue6BaWLv9D5oU6C6goIOiSgdL2Ce4yyyxnvgw-uU8VqvbiTDRI9YLQqOvEUElEcdtmTohxt0Sehgou6yRYyG3sJNJ8U_5zJe51qdrncOU1shm_RJYdZzbIL4O6z4WeSLhLBjNNNRL4S9DL2MuyxKxPf5Sap_QJOYtGFPkmOZv4v3PytqcMZouGG70WdAKHCCrtFDW6zxctXRaADjuI7khAMdgCo1jcR6WCmdImdaywkLZotwNBnGtgvdP0KrIggvbT4Noq2LmEGofbrXHluVDBRH54cvztMTVAVKD3tvJ4_KStdgwDmWVRrem2mMWMhJ0FLn9yQnKLS4oH11nFzfM4x8YpB_jWThjKssYWBynVu3)

present_proof State 및 Webhook event 전달 항목 

Topic | State | Description
--- | --- | ---
present_proof | <font color=red>proposal_received<br><b>(Webhook event 전달) | (issuer) proof 제안을 받은 상태
present_proof | request_sent | (issuer)proof presentation 요청한 상태 
present_proof | request_received | (holder)proof presentation 요청을 받은 상태
present_proof | presentation_sent | (holder) proof를 presentation 한 상태
present_proof | <font color=red>presentation_received<br><b>(Webhook event 전달) | (issuer) Proof를 받은 상태 
present_proof | <font color=red>verified<br><b>(Webhook event 전달) | (issuer) Proof verification 완료 한 상태  
present_proof | presentation_acked | (holder) Proof verified 응답을 받은 상태 

<br><br>

### STEP 0. Holder(사용자) --> Verifier(검증자) : Proof Proposal 보낸다.

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP0는 Holder(initial app)에서 Proof Proposal을 먼저 진행할 경우 해당한다. <br> STEP0를 생략하고 Verifier가 STEP1 Verification Request를 먼저 보낼 수도 있다.</p>
</div>

* Method and Resource

  `POST` `/present-proof/send-proposal` proof 제출 요청.

* Body Parameter

Name | Description
 --- | ---
connection_id | 사용자와 connection 정보
presentation_proposal | proposal 내용

* Request Body Example

```json
{
    "connection_id": "83de4567-ec62-48cc-bfbe-426fbbc9a0b6",
    "presentation_proposal": {
        "attributes": [
            {
                "name": "favourite_drink",
                "cred_def_id": "WgWxqztrNooG92RXvxSTWv:3:CL:20:tag"
            }
        ],
        "predicates": [],
        "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/presentation-preview"
    },
    "auto_present": true,
    "comment": "검증 제안 Sample",
    "trace": false
}
```

<p></p>

* Response Body and Received Topic Example

```json
{
	"presentation_exchange_id": "cf11111c-f944-44c6-a9d0-fb401a5833f9",
	"presentation_proposal_dict": {
		"@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/propose-presentation",
		"@id": "60b43378-582e-450f-8d7a-69adfc55168b",
		"comment": "검증 제안 Sample",
		"presentation_proposal": {
			"@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/presentation-preview",
			"attributes": [{
				"name": "favourite_drink",
				"cred_def_id": "WgWxqztrNooG92RXvxSTWv:3:CL:20:tag"
			}],
			"predicates": []
		}
	},
	"connection_id": "b7ee2e6b-1bc3-4c23-93ac-416c857d4daa",
	"created_at": "2021-07-23 01:27:26.783346Z",
	"thread_id": "60b43378-582e-450f-8d7a-69adfc55168b",
	"role": "verifier",
	"auto_present": false,
	"updated_at": "2021-07-23 01:27:26.783346Z",
	"initiator": "external",
	"state": "proposal_received",
	"trace": false,
	"topic": "present_proof"
}
```
<p></p>

* Verifier 위 Topic에서 아래 정보를 확인 해야 함.

<p></p>

1. `"topic": "present_proof"` >> VC 검증 요청
2. `"state": "proposal_received"` >> Proposal 상태
3. `presentation_proposal_dict.presentation_proposal.attributes.cred_def_id` >> 검증 제안 VC (추후 변경 가능)

Verifiers는 3번의 cred_def_id를 확인 후 STEP1의 proof 요청한다.

<br><br>

### STEP 1. Verifier(검증자) --> Holder(사용자) : 사용자에게 Verification Request를 보낸다. 

* Method and Resource

    `POST` `/present-proof/send-verification-request` proof 제출 요청.  

* Body Parameter

 Name | Description 
 --- | ---
 connection_id | 사용자와 connection 정보
 verification_template_id | 사용하고자 하는 검증양식ID(verifTplId) 
 agreement | (String)개인정보 수집 및 이용 동의서 / 제3자 정보제공 동의서
 
<p></p>

검증양식ID는 아래 initial Console에서 생성 가능함.

![webconsole 1](img/web_console_create_verification_templete_id.png)

검증양식ID는 아래 initial Console에서 확인 가능함.

![webconsole 1](img/web_console_verification_templete_id.png)

* Request Body Example

```json
{
  "connection_id": "{{connectionId}}",
  "verification_template_id": "{{verifTplId}}",
  "agreement": "{{ 동의서 본문 }}"
}
```

- agreement에 들어갈 {{동의서 본문}} Sample

<div class="admonition warning">
<p class="admonition-title">important</p>
<p> 중요!! 검증기관은 사용자 개인정보를 취득하고, intial platform을 통한 정보 전달을 위해 아래와 같은 약관을 사용자에게 전달해야 한다. 동의서 본문은 아래와 같이 json 규격을 만족해야 한다. </p>
</div>


```json
{
  "type": "initial_agreement",
  "content": [{
    "sequence": 1,
    "title": "개인정보 수집 및 이용 동의서",
    "is_mandatory": "true",
    "terms_id": "person",
    "terms_ver": "1.0",
    "agreement": "initial서비스(이하“서비스”라 한다)와 관련하여, 본인은 동의 내용을 숙지하였으며, 이에 따라 본인의 개인정보를 귀사(SK텔레콤주식회사)가 수집 및 이용하는 것에 대해 동의 합니다. 본 동의는 서비스의 본질적 기능 제공을 위한 개인정보 수집/이용에 대한 동의로서, 동의를 하는 경우에만 서비스 이용이 가능합니다. 법령에 따른 개인정보의 수집/이용, 계약의 이행/편익 제공을 위한 개인정보 취급 위탁 및 개인정보 취급과 관련된 일반 사항은 서비스의 개인정보 처리 방침에 따릅니다.",
    "condition": [{
      "sub_title": "수집 항목",
      "target": "이름,생년월일"
    },
      {
        "sub_title": "수집 및 이용목적",
        "target": "서비스 이용에 따른 본인확인"
      },
      {
        "sub_title": "이용기간 및 보유/파기",
        "target": "1년"
      }
    ]
  },
    {
      "sequence": 2,
      "title": "위치정보 수집 및 이용 동의서",
      "is_mandatory": "true",
      "terms_id": "location",
      "terms_ver": "1.0",
      "agreement": "이 약관은 이니셜(SK텔레콤)(이하“회사”)가 제공하는 위치 정보사업 또는 위치기반 서비스 사업과 관련하여 회사와 개인 위치 정보주체와의 권리, 의무 및 책임사항, 기타 필요한 사항을 규정함을 목적으로 합니다.",
      "condition": [{
        "sub_title": "위치정보 수집 방법",
        "target": "GPS칩"
      },
        {
          "sub_title": "위치정보 이용/제공",
          "target": "이 약관에 명시되지 않은 사항은 위치정보의 보호 및 이용 등에 관한 법률, 정보통신망 이용촉진 및 정보보호 등에 관한 법률, 전기통신기본법, 전기통신사업법 등 관계법령과 회사의 이용약관 및 개인정보취급방침, 회사가 별도로 정한 지침 등에 의합니다."
        },
        {
          "sub_title": "수집목적",
          "target": "현재의 위치를 기반으로 하여 주변 매장의 위치 등의 정보를 제공하는 서비스"
        },
        {
          "sub_title": "위치정보 보유기간",
          "target": "1년"
        }
      ]
    },
    {
      "sequence": 3,
      "title": "제3자 정보제공 동의서",
      "is_mandatory": "true",
      "terms_id": "3rdparty",
      "terms_ver": "1.0",
      "agreement": "initial서비스(이하“서비스”라 한다)와 관련하여, 본인은 동의 내용을 숙지하였으며, 이에 따라 본인의 개인정보를 귀사(이슈어)가 수집한 개인정보를 아래와 같이 제3자에게 제공하는 것에 대해 동의 합니다. 고객은 개인정보의 제3자 제공에 대한 동의를 거부할 권리가 있으며, 동의를 거부할 시 받는 별도의 불이익은 없습니다. 단, 서비스 이용이 불가능하거나, 서비스 이용 목적에 따른 서비스 제공에 제한이 따르게 됩니다.",
      "condition": [{
        "sub_title": "제공하는 자",
        "target": "발급기관"
      },
        {
          "sub_title": "제공받는 자",
          "target": "이니셜(SK텔레콤)"
        },
        {
          "sub_title": "제공받는 항목",
          "target": "생년월일,시험일,성명(영문),만료일,성명(한글),수험번호,듣기점수,읽기점수,총점"
        },
        {
          "sub_title": "수집 및 이용목적",
          "target": "모바일 전자증명서 발급"
        },
        {
          "sub_title": "보유 및 이용기간",
          "target": "모바일 전자증명서 발급을 위해 서버에 임시 저장하였다가, 증명서 발행 후 즉시 삭제(단, 고객 단말기 내부 저장영역에 증명서 형태로 저장/보관)"
        }
      ]
    }
  ]

}
```

* initial app 동의 표시 화면

![동의서](./img/agreement.png)


- curl Request Example

```
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/present-proof/send-verification-request' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer 2ca4dd8a-1234-1234-1234-c5fb0286f2cc' \
--data-raw '{
	"connection_id": "e2a831c8-643a-4a50-bac9-fae620e5437e",
	"verification_template_id": "82c75511-4eb8-49db-b065-58cda32f8bb3",
	"agreement": {
		"type": "initial_agreement",
		"content": [{
			"sequence": 1,
			"title": "개인정보 수집 및 이용 동의서",
			"is_mandatory": "true",
			"terms_id": "person",
			"terms_ver": "1.0",
			"agreement": "initial서비스(이하\u201C서비스\u201D라 한다)와 관련하여, 본인은 동의 내용을  숙지하였으며, 이에 따라 본인의 개인정보를 귀사(SK텔레콤주식회사)가 수집 및 이용하는 것에 대해 동의 합니다. 본 동의는 서비스의 본질적 기능 제공을 위한 개인정보 수집/이용에 대한 동의로서, 동의를 하는 경우에만 서비스 이용이 가능합니다. 법령 에 따른 개인정보의 수집/이용, 계약의 이행/편익 제공을 위한 개인정보 취급 위탁 및 개인정보 취급과 관련된 일반 사항은 서비스의 개인정보 처리 방침에 따릅니다.",
			"condition": [{
				"sub_title": "수집 항목",
				"target": "이름,생년월일"
			}, {
				"sub_title": "수 집 및 이용목적",
				"target": "서비스 이용에 따른 본인확인"
			}, {
				"sub_title": "이용기간 및 보유/파기",
				"target": "1년"
			}]
		}, {
			"sequence": 2,
			"title": "위치정보 수집 및 이용 동의서",
			"is_mandatory": "true",
			"terms_id": "location",
			"terms_ver": "1.0",
			"agreement": "이 약관은 이니셜(SK텔레콤)(이하\u201C회사\u201D)가 제공하는 위치 정보사업 또는 위치기반 서비스 사업과 관련하여 회사와 개인 위치 정보주체와의 권리, 의무 및 책임사항, 기타 필요한 사항을 규정함을 목적으로 합니다.",
			"condition": [{
				"sub_title": "위치정보 수집 방법",
				"target": "GPS칩"
			}, {
				"sub_title": "위치정보 이용/제공",
				"target": "이 약관에 명시되지 않은 사항은 위치정보의 보호 및 이용 등에 관한 법률, 정보통신망 이용촉진 및 정보보호 등에 관한 법률, 전기통신기본법, 전기통신사업법 등 관계법령과 회사의 이용약관 및 개인정보취급방침, 회사가 별도로 정한 지침 등에 의합니다."
			}, {
				"sub_title": "수집목적",
				"target": "현재의 위치를 기반으로 하여 주변 매장의 위치 등의 정보를 제공하는 서비스"
			}, {
				"sub_title": "위치정보 보유기간",
				"target": "1년"
			}]
		}, {
			"sequence": 3,
			"title": "제3자 정보제공 동의서",
			"is_mandatory": "true",
			"terms_id": "3rdparty",
			"terms_ver": "1.0",
			"agreement": "initial서비스(이하\u201C서비스\u201D라 한다)와 관련하여, 본인은 동의 내용을 숙지하였으며, 이에 따라 본인의  개인정보를 귀사(이슈어)가 수집한 개인정보를 아래와 같이 제3자에게 제공하는 것에 대해 동의 합니다. 고객은 개인정보의 제3자 제공에 대한 동의를 거부할 권리가 있으며, 동의를 거부할 시 받는 별도의 불이익은 없습니다. 단, 서비스 이용이 불가능하 거나, 서비스 이용 목적에 따른 서비스 제공에 제한이 따르게 됩니다.",
			"condition": [{
				"sub_title": "제공하는 자",
				"target": "발급기관"
			}, {
				"sub_title": "제공받는 자",
				"target": "이니셜(SK텔레콤)"
			}, {
				"sub_title": "제공받는 항목",
				"target": "생년월일,시험일,성명(영문),만료일,성명(한글),수험번호,듣기점수,읽기점수,총점"
			}, {
				"sub_title": "수집 및 이용목적",
				"target": "모바일 전자증명서 발급"
			}, {
				"sub_title": "보유 및 이용기간",
				"target": "모바일 전자증명서 발급을 위해 서버에 임시 저장하였다가, 증명서 발행 후 즉시 삭제(단, 고객 단말기 내부 저장영역에 증명서 형태로 저장/보관)"
			}]
		}]
	}
}'
```

<p></p>
 
   * Response body
```json
{
  "trace": false,
  "thread_id": "e9f29413-9fcb-43e6-90d1-51e078cad5c5",
  "presentation_request_dict": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/request-presentation",
    "@id": "e9f29413-9fcb-43e6-90d1-51e078cad5c5",
    "request_presentations~attach": [
      {
        "@id": "libindy-request-presentation-0",
        "mime-type": "application/json",
        "data": {
          "base64": "eyJuYW1lIjogIlx1ZDFhMFx1Yzc3NVx1YzEzMVx1YzgwMVx1Yzk5ZFx1YmE4NVx1YzExY1x1YmMxY1x1ZDU4OSBcdWJhYThcdWJjMTRcdWM3N2NcdWFjMDBcdWM3ODVcdWFjODBcdWM5OWQiLCAidmVyc2lvbiI6ICIxLjAiLCAicmVxdWVzdGVkX2F0dHJpYnV0ZXMiOiB7ImRhdGVfb2ZfYmlydGgiOiB7Im5hbWUiOiAiZGF0ZV9vZl9iaXJ0aCIsICJub25fcmV2b2tlZCI6IHsiZnJvbSI6IDAsICJ0byI6IDE2MjQwMDMwNDJ9LCAicmVzdHJpY3Rpb25zIjogW3sic2NoZW1hX2lkIjogImNVOHJFcmpnS2o4ZmduMWtURHJlbjoyOlBlcnNvbklkZW50aXR5Q3JlZGVudGlhbDoxLjAiLCAiY3JlZF9kZWZfaWQiOiAiVG1pc25FQUdCUGVWVkRqdEFYUGRZdDozOkNMOjA6djAxIn1dfSwgIm1vYmlsZV9udW0iOiB7Im5hbWUiOiAibW9iaWxlX251bSIsICJub25fcmV2b2tlZCI6IHsiZnJvbSI6IDAsICJ0byI6IDE2MjQwMDMwNDJ9LCAicmVzdHJpY3Rpb25zIjogW3sic2NoZW1hX2lkIjogImNVOHJFcmpnS2o4ZmduMWtURHJlbjoyOlBlcnNvbklkZW50aXR5Q3JlZGVudGlhbDoxLjAiLCAiY3JlZF9kZWZfaWQiOiAiVG1pc25FQUdCUGVWVkRqdEFYUGRZdDozOkNMOjA6djAxIn1dfSwgInBlcnNvbl9uYW1lIjogeyJuYW1lIjogInBlcnNvbl9uYW1lIiwgIm5vbl9yZXZva2VkIjogeyJmcm9tIjogMCwgInRvIjogMTYyNDAwMzA0Mn0sICJyZXN0cmljdGlvbnMiOiBbeyJzY2hlbWFfaWQiOiAiY1U4ckVyamdLajhmZ24xa1REcmVuOjI6UGVyc29uSWRlbnRpdHlDcmVkZW50aWFsOjEuMCIsICJjcmVkX2RlZl9pZCI6ICJUbWlzbkVBR0JQZVZWRGp0QVhQZFl0OjM6Q0w6MDp2MDEifV19fSwgInJlcXVlc3RlZF9wcmVkaWNhdGVzIjoge30sICJub25jZSI6ICI0MTEwNjg0OTkzODA0MDkwODk0NTI2MDMifQ=="
        }
      }
    ],
    "comment": "{\"verification_template_id\":\"82c75511-4eb8-49db-b065-58cda32f8bb3\",\"agreement\":{}}"
  },
  "role": "verifier",
  "updated_at": "2021-06-18 07:57:23.007573Z",
  "auto_present": false,
  "connection_id": "e2a831c8-643a-4a50-bac9-fae620e5437e",
  "presentation_exchange_id": "029090d5-70de-48b2-9b30-d7ae0c4866ee",
  "initiator": "self",
  "presentation_request": {
    "nonce": "411068499380409089452603",
    "name": "토익성적증명서발행 모바일가입검증",
    "version": "1.0",
    "requested_attributes": {
      "date_of_birth": {
        "name": "date_of_birth",
        "restrictions": [
          {
            "schema_id": "cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
            "cred_def_id": "TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"
          }
        ],
        "non_revoked": {
          "to": 1.624003042E9,
          "from": 0.0
        }
      },
      "mobile_num": {
        "name": "mobile_num",
        "restrictions": [
          {
            "schema_id": "cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
            "cred_def_id": "TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"
          }
        ],
        "non_revoked": {
          "to": 1.624003042E9,
          "from": 0.0
        }
      },
      "person_name": {
        "name": "person_name",
        "restrictions": [
          {
            "schema_id": "cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
            "cred_def_id": "TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"
          }
        ],
        "non_revoked": {
          "to": 1.624003042E9,
          "from": 0.0
        }
      }
    },
    "requested_predicates": {}
  },
  "created_at": "2021-06-18 07:57:23.007573Z",
  "state": "request_sent"
}
```

<p></p>

* present_proof State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | present_proof state | **`request_sent`** | N/A |
<p></p>


* Next Step


* Ledger Transactions (Indy Node)

    
<br><br>

### STEP 2. Holder : Verifier(검증자)의 presentation 요청 내용 확인. 

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 Holder가 initial app 일 경우 SDK에서 자동으로 처리됨 </p>
</div>

<br><br>


### STEP 3. Holder --> Verifier(검증자) : Verifier에게 presentation 보낸다. 

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP3는 Holder가 initial app 일 경우 SDK에서 자동으로 처리되고, 검증자는 Alice로 부터 Webhook Message를 전달 받고 자동 처림 됨 </p>
</div>

<br><br>

### STEP 4. Verifier : Presentation 검증 결과 확인.

Webhhok Message의 `requested_proof` 및 `verified` 정보 확인
```json
 requested proof:
{
  "revealed_attrs": {
    "college": {
      "sub_proof_index": 0,
      "raw": "공과대학",
      "encoded": "16610701639769159354469803737729974768559429331015161930470692015712320866163"
    },
    "studentid": {
      "sub_proof_index": 0,
      "raw": "11111111",
      "encoded": "11111111"
    },
    "name": {
      "sub_proof_index": 0,
      "raw": "고증명",
      "encoded": "9471975446415845898244366274733369196591763592071417247540506130163033264982"
    },
    "campuslocation": {
      "sub_proof_index": 0,
      "raw": "서울",
      "encoded": "91558467477632645889600363997818936103495823051310911993186983916648377535079"
    },
    "university": {
      "sub_proof_index": 0,
      "raw": "고려대학교",
      "encoded": "82015121962619472303362503621468631267043118886484178232578676624082121630145"
    },
    "department": {
      "sub_proof_index": 0,
      "raw": "AI연구",
      "encoded": "103758450965506634956372864113637628594188561957360798051123090877687816373760"
    }
  },
  "self_attested_attrs": {},
  "unrevealed_attrs": {},
  "predicates": {}
}
```

`"verified": "true"`

verified 결과 True/False 확인

* 참고 

Webhook message에서 사용자 data를 확인 하기 위해서는 아래 json 경로를 parsing 한다<br>
`presentation.requested_proof.revealed_attrs.{{requested_attribute}}.raw`
<br> `{{requested_attribute}}` 의 명칭은 검증양식 생성할때 사용한 `"검증명"` + `"Attribute 이름"` 이다.

  ![verify_webhook](img/verify_topic2.png)


### STEP 5. 고급 증명양식 검증(Verify)의 다양한 기법

##### Data Model

- comment [Option] : 검증/관리를 위한 목적등 설명 작성
- connection_id [Mandatory] : Verify 요청할 connection id
- name [Option] : 검증 제목
- version [Option]: 버전 관리
- requested_attributes [Option]: 공개 요청할 항목들
    - name [Mandatory]: 요청할 attribute 값(schema 확인 하여 정확하게 기입)
    - non_revoked [Option]: 증명서 유효 기간(unix time).
        - from [Option] : 시작 시간. 일반적으로 0. 
        - to [Option] : 종료 시간. 일반적으로 현재시간.
    - restrictions [Option] : 제출할 증명서 제한. 
      - cred_def_id [Option] : 특정 credential
      - issuer_did [Option] : 특정 issuer 발행 credential
      - schema_id [Option] : 특정 schema
- requested_predicates [Option]: 영지식증명으로 요청할 항목들
    - name : 요청할 attribute 값(schema 확인 하여 정확하게 기입)
    - non_revoked [Option]: 증명서 유효 기간(unix time).
        - from [Option] : 시작 시간. 일반적으로 0.
        - to [Option] : 종료 시간. 일반적으로 현재시간.
    - restrictions [Option] : 제출할 증명서 제한.
        - cred_def_id [Option] : 특정 credential
        - issuer_did [Option] : 특정 issuer 발행 credential
        - schema_id [Option] : 특정 schema


<br>

##### Example #1 
시나리오 : 모바일가입증명의 cred_def_id가 정확히 일치하는 증명서의 이름,생일,전화번호 요청

 - restriction 1 : 현재시간(unixtime : 1623124667) 기준 revocation이 되지 않은 VC
 - restriction 2 : SKT 모바일 가입증명 cred_def_id (3fZJD68zkyJwzsME3rDk7e:3:CL:0:v01) VC

```json
{
  "connection_id": "836b3c81-062e-4270-b4cc-03725802bf9c",
  "proof_request": {
    "name": "SKT 입사지원을 위한 검증",
    "version": "1.0",
    "requested_attributes": {
      "모바일가입증명 검증_생일":{
        "name":"date_of_birth",
        "non_revoked":{
          "from":0,
          "to":1623124667
        },
        "restrictions":[
          {
            "cred_def_id":"3fZJD68zkyJwzsME3rDk7e:3:CL:0:v01"
          }
        ]
      },
      "모바일가입증명 검증_전화번호":{
        "name":"mobile_num",
        "non_revoked":{
          "from":0,
          "to":1623124667
        },
        "restrictions":[
          {
            "cred_def_id":"3fZJD68zkyJwzsME3rDk7e:3:CL:0:v01"
          }
        ]
      },
      "모바일가입증명 검증_이름":{
        "name":"person_name",
        "non_revoked":{
          "from":0,
          "to":1623124667
        },
        "restrictions":[
          {
            "cred_def_id":"3fZJD68zkyJwzsME3rDk7e:3:CL:0:v01"
          }
        ]
      }
    }
  },
  "comment": "SKT 입사지원을 위한 검증을 위해 모바일 가입증명을 요청"
}
```
<br>


##### Example #2
시나리오 : 모바일가입증명의 스키마를 사용하고, SKT initial 기관에서 발급한 증명서의 이름,생일,전화번호 요청

- restriction 1 : 발급 기관의 DID(issuer_did)는 SKT initial(3fZJD68zkyJwzsME3rDk7e) VC
- restriction 2 : 모바일 가입증명의 스키마(schema_id) 4G5rcJdUxa5SbtfqNJxPXt:2:PersonIdentityCredential:3.0 를 사용한 VC
- 필요시 non_revoked 사용 가능

```json
{
  "connection_id": "836b3c81-062e-4270-b4cc-03725802bf9c",
  "proof_request": {
    "name": "SKT 입사지원을 위한 검증",
    "version": "1.0",
    "requested_attributes": {
      "모바일가입증명 검증_생일":{
        "name":"date_of_birth",
        "restrictions":[
          {
            "issuer_did":"3fZJD68zkyJwzsME3rDk7e",
            "schema_id":"4G5rcJdUxa5SbtfqNJxPXt:2:PersonIdentityCredential:3.0"
          }
        ]
      },
      "모바일가입증명 검증_전화번호":{
        "name":"mobile_num",
        "non_revoked":{
          "from":0,
          "to":1623124667
        },
        "restrictions":[
          {
            "issuer_did":"3fZJD68zkyJwzsME3rDk7e",
            "schema_id":"4G5rcJdUxa5SbtfqNJxPXt:2:PersonIdentityCredential:3.0"
          }
        ]
      },
      "모바일가입증명 검증_이름":{
        "name":"person_name",
        "restrictions":[
          {
            "cred_def_id":"3fZJD68zkyJwzsME3rDk7e:3:CL:0:v01"
          }
        ]
      }
    }
  }
}
```
<br>

##### Example #3
시나리오 :  모바일가입증명의 스키마를 사용하고, SKT initial 기관 혹은 B기관에서 발급한 증명서의 전화번호 요청

- restriction 1 : 발급 기관의 DID(issuer_did)는 SKT initial(3fZJD68zkyJwzsME3rDk7e) 혹은 B기관(DrLbXFSao4Vo8gMfjxPxU1) VC
- restriction 2 : 모바일 가입증명의 스키마(schema_id) 4G5rcJdUxa5SbtfqNJxPXt:2:PersonIdentityCredential:3.0 를 사용한 VC
- restriction 3 : 현재시간(unixtime : 1623124667) 기준 revocation이 되지 않은 VC

```json
{
  "connection_id": "836b3c81-062e-4270-b4cc-03725802bf9c",
  "proof_request": {
    "name": "SKT 입사지원을 위한 검증",
    "version": "1.0",
    "requested_attributes": {
      "모바일가입증명 검증_전화번호":{
        "name":"mobile_num",
        "non_revoked":{
          "from":0,
          "to":1623124667
        },
        "restrictions":[
          {
            "issuer_did":"3fZJD68zkyJwzsME3rDk7e",
            "schema_id":"4G5rcJdUxa5SbtfqNJxPXt:2:PersonIdentityCredential:3.0"
          },
          {
            "issuer_did":"DrLbXFSao4Vo8gMfjxPxU1",
            "schema_id":"4G5rcJdUxa5SbtfqNJxPXt:2:PersonIdentityCredential:3.0"
          }
        ]
      }
    }
  }
}
```

<br>

##### Example #4
시나리오 :  서로 다른 VC의 항목을 요청. 
- restriction 1 : 모바일가입증명 VC의 생일 
- restriction 2 : 대학 재학증명서 VC의 학번
- restriction 2 : non_revoked 

```json
{
  "connection_id": "836b3c81-062e-4270-b4cc-03725802bf9c",
  "proof_request": {
    "name": "SKT 입사지원을 위한 검증",
    "version": "1.0",
    "requested_attributes": {
      "모바일가입증명 검증_생일":{
        "name":"date_of_birth",
        "non_revoked":{
          "from":0,
          "to":1623124667
        },
        "restrictions":[
          {
            "cred_def_id":"3fZJD68zkyJwzsME3rDk7e:3:CL:0:v01"
          }
        ]
      },
      "대학재학증명서 검증_학번":{
        "name":"student_id",
        "non_revoked":{
          "from":0,
          "to":1623124667
        },
        "restrictions":[
          {
            "cred_def_id":"QjuxXDs69RLKbSsCccehnQ:3:CL:1605674669:UniCertificateOfEnrollmentKorRev"
          }
        ]
      }
    }
  }
}
```
<br>


##### Example #5
시나리오 : 나이를 공개하지 않고, 영지식증명을 이용하여 성인 인증 
- restriction 1 : 모바일가입증명 VC의 생일
- restriction 2 : 대학 재학증명서 VC의 학번
- restriction 2 : non_revoked

* 지원가능 Range Predicate(p_type) : "<", "<=", ">=", ">"
* 현재 숫자만 가능

```json
{
  "comment": "2000년 1월 1일 이전 출생자인 성인인증을 통한 클럽 출입",
  "connection_id": "836b3c81-062e-4270-b4cc-03725802bf9c",
  "proof_request": {
    "name": "성인인증",
    "version": "1.0",
    "requested_predicates": {
      "모바일가입증명 검증_나이": {
        "name": "date_of_birth",
        "p_type": "<=",
        "p_value": 20000101,
        "restrictions": [
          {
            "cred_def_id": "3fZJD68zkyJwzsME3rDk7e:3:CL:0:v01"
          }
        ]
      }
    }
  }
}
```
<br>

##### Example #6
시나리오 : 복합 

```json
{
  "comment": "SKT 입사지원을 위한 검증",
  "connection_id": "2612699e-3b88-412c-a435-b9824b48983c",
  "proof_request": {
    "name": "모바일가입증명서/졸업증명서/성적증명서 검증",
    "version": "1.0",
    "requested_attributes": {
      "소제목:모바일가입증명서": {
        "name": "date_of_birth",
        "non_revoked": {
          "from": 0,
          "to": 1622436299
        },
        "restrictions": [
          {
            "issuer_did": "NoLL1sbRSGPB19TuqHPWqY",
            "schema_id": "N6r4nLwAkcYUX8c8Kb8Ufu:2:UniCertificateOfGraduationKor:2.0"
          }
        ]
      },
      "소제목2:대학졸업증명서": {
        "name": "college",
        "restrictions": [
          {
            "cred_def_id": "GoW6ww2bbRGauHx3CSicLM:3:CL:101:UniCertificateOfGraduationKorRevTest5"
          }
        ]
      },
      "소제목3:대학성적증명서": {
        "names": "degree",
        "restrictions": [
          {
            "issuer_did": "NoLL1sbRSGPB19TuqHPWqY",
            "schema_id": "N6r4nLwAkcYUX8c8Kb8Ufu:2:UniCertificateOfGraduationKor:2.0"          }
        ]
      }
    },
    "requested_predicates": {
      "성인 여부 영지식증명 검증": {
        "name": "age",
        "p_type": ">=",
        "p_value": 20,
        "restrictions": [
          {
            "issuer_did": "NoLL1sbRSGPB19TuqHPWqY",
            "cred_def_id": "GoW6ww2bbRGauHx3CSicLM:3:CL:101:UniCertificateOfGraduationKorRevTest5",
            "schema_id": "N6r4nLwAkcYUX8c8Kb8Ufu:2:UniCertificateOfGraduationKor:2.0"          }
        ]
      }
    }
  }
}
```
<br>

##### Example #7 (지원 예정)
시나리오 : 사용자가 값을 직접 입력 

- self_attested_attributes 이용하여, 사용자가 직접 값을 입력할 수 있게 할 수 있음.

```json
{
  "connection_id": "836b3c81-062e-4270-b4cc-03725802bf9c",
  "proof_request": {
    "name": "SKT 입사지원을 위한 검증",
    "version": "1.0",
    "self_attested_attributes": {
                  "address": "내가직접 입력"
    }
  }
}
```

