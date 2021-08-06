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
present_proof | <font color=red>proposal_received<br><b>(Webhook event 전달) | (verifier) proof 제안을 받은 상태
present_proof | request_sent | (verifier)proof presentation 요청한 상태 
present_proof | request_received | (holder)proof presentation 요청을 받은 상태
present_proof | presentation_sent | (holder) proof를 presentation 한 상태
present_proof | <font color=red>presentation_received<br><b>(Webhook event 전달) | (verifier) Proof를 받은 상태 
present_proof | <font color=red>verified<br><b>(Webhook event 전달) | (verifier) Proof verification 완료 한 상태  
present_proof | presentation_acked | (holder) Proof verified 응답을 받은 상태 

<br><br>

### STEP 0. <font color=green>[Mandatory]</font> Holder(사용자) → Verifier(검증기관) : Proof Proposal

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP0는 initial app(Holder)에서 요청합니다. 발행기관의 경우 STEP0이 필요 없습니다.</p>
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

* Webhook Event Example

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

* Verifier 위 Webhook Event 아래 정보를 확인 해야 함.

<p></p>

1. `"topic": "present_proof"` >> VC 검증 요청
2. `"state": "proposal_received"` >> Proposal 상태
3. `presentation_proposal_dict.presentation_proposal.attributes.cred_def_id` >> 검증 제안 VC (추후 변경 가능)

Verifiers는 3번의 cred_def_id를 확인 후 STEP1의 proof 요청에 활용한다.

<br><br>

### STEP 1. <font color=green>[Mandatory]</font> Verifier → Holder : Verification Request

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

### STEP 2. Holder :  Verification Request 및 약관/동의 내용 확인. 

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 initial app(Holder) SDK에서 자동으로 처리됩니다. </p>
</div>

![동의서](./img/agreement.png)

<br><br>


### STEP 3. Holder → Verifier(검증기관) : send presentation 

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2에거 사용자가 `약관동의 및 증명서 제출` 클릭 시 initial app SDK에서 자동으로 처리됩니다.</p>
</div>

Verifier는 아래 Webhook Event를 받게 됩니다. 검증의 중간 과정으로 다른 Action은 필요 없습니다.

```json
{
  "state":"presentation_received",
  "role":"verifier",
  "presentation":{
    "proof":{
      "proofs":[
        {
          "primary_proof":{
            "eq_proof":{
              "revealed_attrs":{
                "date_of_birth":"20000101",
                "mobile_num":"111444249212870126950404680205889709796474572836794180318995173744548268486974",
                "person_name":"114735663336402277097990206158879556599192671418456242825414219721487748472651"
              },
              "a_prime":"50956499984706665193211336384869312293099891550938279224448237983861359897039823387079226533024030740259073378796936026626985764650026850673290184935057871134628492224144748336841320494635519312950353720368830763806584821973884987693260351429687477113754247704315159232985149570405085437710350685391012002098358795717916756304952858535611017530040718441890767189570105581768338366107405575113710908002160982925106097520004503861578584388294360646025655575010365250078513492265251979581489699234926576049556572826834100749861228633973658087034851975762668708996137167985659808297239867678302872098917893577177526177453",
              "e":"137451067861865694689623528349803310054023418297273939188121806174253780118885912822133650374973010692437917374459757894716062304473854850",
              "v":"434390629198459379137064767464104052433063329898955932655851827261960018333480172932767427117358855849477285805454761989996743295558459309908880083279913871346987467084669509843703274149774785531255950881477323217486901852199967650671221546411824624682443972383485928662376603727535557818959752969381214725307143511994973375518082250995102701220759557665656767103436524783679903860623170542380113556706631575743929487730631832342098710475077872627515007662486095832163435216418667885452814078699986537929035270552909439008091310158368790556521817388771688487858798318799400640084770775002148448209849815518692221845195088906216961835437988293806880562918505878620151368699939378229233536873868873908844871052166133726632842023716225874148783431213186078088596250530872453507178955832735641721603072336465059606700671252812167432525664406369033001604920203100469681352661063022661452620843830081633829602215393457556119492",
              "m":{
                "telecom":"11908333078617188860100075573416589529116613315703368658262526978043470710976032734135075845161952294106465857486119541698960051745622245771458784912434493903881689873817518991545",
                "ci":"3070314167801282096943694778173206697181009314098943591352052983416786815600429780521097690356120321243133360003297190527327478274363668147793391160454995710363684714361445637473",
                "master_secret":"15392936264361168316669548237825982777797064567718616395714742145407417503846093072551565747091982241282983287282117575636670382017212030344085575439875205201205991112273729491799",
                "exp_date":"13043842987198671065369416829826037072679958535140747832667991584816537337327170993654837582531760547381566614864720857033361626654807375632012923929388472893295751103456878101407",
                "gender":"13687423294109759845269695723021996992478779830985760929474878199878724698003815340441834223379085980859684511822426810162063763939725095529443722342397470930978939609172960500290",
                "is_foreigner":"13682493121109188628231316988686501898754510514232069250701379638499815324583336822807783743399114182602815578905435044150550235486491768714031141520733608547936932278134050370058"
              },
              "m2":"7050228796353343765915578089962110792967950996338791992874892930819761843426362369422576422941879037289860271315371808157435966239566372224454231088331002892717163551427885287659"
            },
            "ge_proofs":[
              
            ]
          }
        }
      ],
      "aggregated_proof":{
        "c_hash":"41971952589089028556538507758666379125077274348802265882968501334901680800091",
        "c_list":[
          [
            1,
            173
          ]
        ]
      }
    },
    "requested_proof":{
      "revealed_attrs":{
        "mobile_num":{
          "encoded":"111444249212870126950404680205889709796474572836794180318995173744548268486974",
          "sub_proof_index":0,
          "raw":"01012345678"
        },
        "date_of_birth":{
          "encoded":"19780924",
          "sub_proof_index":0,
          "raw":"20000101"
        },
        "person_name":{
          "encoded":"114735663336402277097990206158879556599192671418456242825414219721487748472651",
          "sub_proof_index":0,
          "raw":"김증명"
        }
      },
      "self_attested_attrs":{
        
      },
      "unrevealed_attrs":{
        
      },
      "predicates":{
        
      }
    },
    "identifiers":[
      {
        "schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
        "cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"
      }
    ]
  },
  "created_at":"2021-08-06 03:47:20.722007Z",
  "thread_id":"f26c3468-e2c0-4575-9085-519b8e98f6c8",
  "presentation_exchange_id":"a0b47251-7ad8-4210-9643-cb608983e8fc",
  "presentation_request":{
    "nonce":"878752732301195434710634",
    "name":"토익성적증명서발행 모바일가입검증",
    "version":"1.0",
    "requested_attributes":{
      "date_of_birth":{
        "restrictions":[
          {
            "schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
            "cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"
          }
        ],
        "non_revoked":{
          "from":0,
          "to":1628221640
        },
        "name":"date_of_birth"
      },
      "mobile_num":{
        "restrictions":[
          {
            "schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
            "cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"
          }
        ],
        "non_revoked":{
          "from":0,
          "to":1628221640
        },
        "name":"mobile_num"
      },
      "person_name":{
        "restrictions":[
          {
            "schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
            "cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"
          }
        ],
        "non_revoked":{
          "from":0,
          "to":1628221640
        },
        "name":"person_name"
      }
    },
    "requested_predicates":{
      
    }
  },
  "connection_id":"a7247c17-d237-4a83-84a6-b2e641b5ab05",
  "trace":false,
  "presentation_request_dict":{
    "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/request-presentation",
    "@id":"f26c3468-e2c0-4575-9085-519b8e98f6c8",
    "request_presentations~attach":[
      {
        "@id":"libindy-request-presentation-0",
        "mime-type":"application/json",
        "data":{
          "base64":"eyJuYW1lIjogIlx1ZDFhMFx1Yzc3NVx1YzEzMVx1YzgwMVx1Yzk5ZFx1YmE4NVx1YzExY1x1YmMxY1x1ZDU4OSBcdWJhYThcdWJjMTRcdWM3N2NcdWFjMDBcdWM3ODVcdWFjODBcdWM5OWQiLCAidmVyc2lvbiI6ICIxLjAiLCAicmVxdWVzdGVkX2F0dHJpYnV0ZXMiOiB7ImRhdGVfb2ZfYmlydGgiOiB7Im5hbWUiOiAiZGF0ZV9vZl9iaXJ0aCIsICJub25fcmV2b2tlZCI6IHsiZnJvbSI6IDAsICJ0byI6IDE2MjgyMjE2NDB9LCAicmVzdHJpY3Rpb25zIjogW3sic2NoZW1hX2lkIjogImNVOHJFcmpnS2o4ZmduMWtURHJlbjoyOlBlcnNvbklkZW50aXR5Q3JlZGVudGlhbDoxLjAiLCAiY3JlZF9kZWZfaWQiOiAiVG1pc25FQUdCUGVWVkRqdEFYUGRZdDozOkNMOjA6djAxIn1dfSwgIm1vYmlsZV9udW0iOiB7Im5hbWUiOiAibW9iaWxlX251bSIsICJub25fcmV2b2tlZCI6IHsiZnJvbSI6IDAsICJ0byI6IDE2MjgyMjE2NDB9LCAicmVzdHJpY3Rpb25zIjogW3sic2NoZW1hX2lkIjogImNVOHJFcmpnS2o4ZmduMWtURHJlbjoyOlBlcnNvbklkZW50aXR5Q3JlZGVudGlhbDoxLjAiLCAiY3JlZF9kZWZfaWQiOiAiVG1pc25FQUdCUGVWVkRqdEFYUGRZdDozOkNMOjA6djAxIn1dfSwgInBlcnNvbl9uYW1lIjogeyJuYW1lIjogInBlcnNvbl9uYW1lIiwgIm5vbl9yZXZva2VkIjogeyJmcm9tIjogMCwgInRvIjogMTYyODIyMTY0MH0sICJyZXN0cmljdGlvbnMiOiBbeyJzY2hlbWFfaWQiOiAiY1U4ckVyamdLajhmZ24xa1REcmVuOjI6UGVyc29uSWRlbnRpdHlDcmVkZW50aWFsOjEuMCIsICJjcmVkX2RlZl9pZCI6ICJUbWlzbkVBR0JQZVZWRGp0QVhQZFl0OjM6Q0w6MDp2MDEifV19fSwgInJlcXVlc3RlZF9wcmVkaWNhdGVzIjoge30sICJub25jZSI6ICI4Nzg3NTI3MzIzMDExOTU0MzQ3MTA2MzQifQ=="
        }
      }
    ],
    "comment":"{\"verification_template_id\":\"82c75511-4eb8-49db-b065-58cda32f8bb3\",\"agreement\":{\"type\":\"initial_agreement\",\"content\":[{\"sequence\":1,\"title\":\"개인정보 수집 및 이용 동의서\",\"is_mandatory\":\"true\",\"terms_id\":\"person\",\"terms_ver\":\"1.0\",\"agreement\":\"Initial 서비스(이하 \u201C서비스\u201D라한다)와 관련하여, 본인은 동의내용을  숙지하였으며, 이에따라 본인의 개인정보를 귀사(SK텔레콤주식회사)가 수집 및 이용하는 것에 대해 동의합니다. 본동의는 서비스의 본질적 기능제공을 위한 개인정보 수집/이용에 대한 동의로서, 동의를 하는경우에만 서비스 이용이 가능합니다.법령에따른 개인정보의수집/이용, 계약의이행/편익제공을위한개인정보취급위탁및개인정보취급과관련된일반사항은서비스의개인정보처리방침에따릅니다.\",\"condition\":[{\"sub_title\":\"수집 항목\",\"target\":\"이름,생년월일\"},{\"sub_title\":\"수집및이용목적\",\"target\":\"서비스이용에따른본인확인\"},{\"sub_title\":\"이용기간및보유/파기\",\"target\":\"1년\"},{\"sub_title\":\"기타 정보\",\"target\":\"기타 내용\"}]},{\"sequence\":2,\"title\":\"위치정보 수집 및 이용 동의서\",\"is_mandatory\":\"true\",\"terms_id\":\"location\",\"terms_ver\":\"1.0\",\"agreement\":\"이 약관은 이니셜(SK텔레콤)(이하 \u201C회사\u201D)가 제공하는 위치정보사업 또는 위치기반서비스사업과 관련하여 회사와 개인위치정보주체와의 권리, 의무 및 책임사항, 기타 필요한 사항을 규정함을 목적으로 합니다.\",\"condition\":[{\"sub_title\":\"위치정보 수집 방법\",\"target\":\"GPS칩\"},{\"sub_title\":\"위치정보 이용/제공\",\"target\":\"이 약관에 명시되지 않은 사항은 위치정보의 보호 및 이용 등에 관한 법률,  정보통신망 이용촉진 및 정보보호 등에 관한 법률, 전기통신기본법, 전기통신사업법 등 관계법령과 회사의 이용약관 및 개인정보취급방침, 회사가 별도로 정한 지침 등에 의합니다.\"},{\"sub_title\":\"수집목적\",\"target\":\"현재의 위치를 기반으로 하여 주변 매장의 위치 등의 정보를 제공하는 서비스\"},{\"sub_title\":\"위치정보 보유기간\",\"target\":\"1년\"}]},{\"sequence\":3,\"title\":\"테스트 수집 및 이용 동의서\",\"is_mandatory\":\"true\",\"terms_id\":\"test\",\"terms_ver\":\"1.0\",\"agreement\":\"이 약관은 이니셜(SK텔레콤)(이하 \u201C회사\u201D)가 제공하는 위치정보사업 또는 위치기반서비스사업과 관련하여 회사와 개인위치정보주체와의 권리, 의무 및 책임사항, 기타 필요한 사항을 규정함을 목적으로 합니다.\",\"condition\":[{\"sub_title\":\"위치정보 수집 방법\",\"target\":\"GPS칩\"},{\"sub_title\":\"위치정보 이용/제공\",\"target\":\"이 약관에 명시되지 않은 사항은 위치정보의 보호 및 이용 등에 관한 법률, 정보통신망 이용촉진 및 정보보호 등에 관한 법률, 전기통신기본법, 전기통신사업법 등 관계법령과 회사의 이용약관 및 개인정보취급방침, 회사가 별도>로 정한 지침 등에 의합니다.\"},{\"sub_title\":\"수집목적\",\"target\":\"현재의 위치를 기반으로 하여 주변 매장의 위치 등의 정보를 제공하는 서비스\"},{\"sub_title\":\"위치정보 보유기간\",\"target\":\"1년\"}]},{\"sequence\":4,\"title\":\"제3자 정보제공 동의서\",\"is_mandatory\":\"true\",\"terms_id\":\"3rdparty\",\"terms_ver\":\"1.0\",\"agreement\":\" initial 서비스(이하 \u201C서비스\u201D라한다)와관련하여, 본인은동의내용을숙지하였으며, 이에따라본인의개인정보를귀사(이슈어)가수집한개인정보를아래와같이제3자에게제공하는것에대해동의합니다. 고객은개인정보의제3자제공에대한동의를거부할권리가있으며, 동의를거부할받는별도의불이익은없 습니다. 단, 서비스이용불가능하거나, 서비스이용목적에따른서비스제공에제한이따르게됩니다.\",\"condition\":[{\"sub_title\":\"제공하는자\",\"target\":\"발급기관\"},{\"sub_title\":\"제공받는자\",\"target\":\"이니셜(SK텔레콤)\"},{\"sub_title\":\"제공받는 항목\",\"target\":\"제공항목(생년월일,시험일,성명(영문),만료일,성명(한글),수험번호,듣기점수,읽기점수,총점)\"},{\"sub_title\":\"수집 및 이용목적\",\"target\":\"모바일전자증명서발행\"},{\"sub_title\":\"보유 및 이용기간\",\"target\":\"모바일 전자증명서 발급을 위해 서버에 임시 저장하였다가, 증명서 발행 후 즉시 삭제(단, 고객 단말기 내부 저장영역에 증명서 형태로 저장/보관)\"}]}]}}"
  },
  "auto_present":false,
  "updated_at":"2021-08-06 03:47:43.815054Z",
  "initiator":"self",
  "topic":"present_proof"
}
```
<br><br>

### STEP 4. <font color=green>[Mandatory]</font> Verifier : Presentation 검증 결과 확인.

Cloud Agent에서 검증이 완료되면 아래와 같은 Webhook Event가 전달 됩니다.


Webhook Event Example

```json
{
  "state":"verified",
  "role":"verifier",
  "presentation":{
    "proof":{
      "proofs":[
        {
          "primary_proof":{
            "eq_proof":{
              "revealed_attrs":{
                "date_of_birth":"19780924",
                "mobile_num":"111444249212870126950404680205889709796474572836794180318995173744548268486974",
                "person_name":"114735663336402277097990206158879556599192671418456242825414219721487748472651"
              },
              "a_prime":"50956499984706665193211336384869312293099891550938279224448237983861359897039823387079226533024030740259073378796936026626985764650026850673290184935057871134628492224144748336841320494635519312950353720368830763806584821973884987693260351429687477113754247704315159232985149570405085437710350685391012002098358795717916756304952858535611017530040718441890767189570105581768338366107405575113710908002160982925106097520004503861578584388294360646025655575010365250078513492265251979581489699234926576049556572826834100749861228633973658087034851975762668708996137167985659808297239867678302872098917893577177526177453",
              "e":"137451067861865694689623528349803310054023418297273939188121806174253780118885912822133650374973010692437917374459757894716062304473854850",
              "v":"434390629198459379137064767464104052433063329898955932655851827261960018333480172932767427117358855849477285805454761989996743295558459309908880083279913871346987467084669509843703274149774785531255950881477323217486901852199967650671221546411824624682443972383485928662376603727535557818959752969381214725307143511994973375518082250995102701220759557665656767103436524783679903860623170542380113556706631575743929487730631832342098710475077872627515007662486095832163435216418667885452814078699986537929035270552909439008091310158368790556521817388771688487858798318799400640084770775002148448209849815518692221845195088906216961835437988293806880562918505878620151368699939378229233536873868873908844871052166133726632842023716225874148783431213186078088596250530872453507178955832735641721603072336465059606700671252812167432525664406369033001604920203100469681352661063022661452620843830081633829602215393457556119492",
              "m":{
                "telecom":"11908333078617188860100075573416589529116613315703368658262526978043470710976032734135075845161952294106465857486119541698960051745622245771458784912434493903881689873817518991545",
                "ci":"3070314167801282096943694778173206697181009314098943591352052983416786815600429780521097690356120321243133360003297190527327478274363668147793391160454995710363684714361445637473",
                "master_secret":"15392936264361168316669548237825982777797064567718616395714742145407417503846093072551565747091982241282983287282117575636670382017212030344085575439875205201205991112273729491799",
                "exp_date":"13043842987198671065369416829826037072679958535140747832667991584816537337327170993654837582531760547381566614864720857033361626654807375632012923929388472893295751103456878101407",
                "gender":"13687423294109759845269695723021996992478779830985760929474878199878724698003815340441834223379085980859684511822426810162063763939725095529443722342397470930978939609172960500290",
                "is_foreigner":"13682493121109188628231316988686501898754510514232069250701379638499815324583336822807783743399114182602815578905435044150550235486491768714031141520733608547936932278134050370058"
              },
              "m2":"7050228796353343765915578089962110792967950996338791992874892930819761843426362369422576422941879037289860271315371808157435966239566372224454231088331002892717163551427885287659"
            },
            "ge_proofs":[
              
            ]
          }
        }
      ],
      "aggregated_proof":{
        "c_hash":"41971952589089028556538507758666379125077274348802265882968501334901680800091",
        "c_list":[
          [
            1,
            173
          ]
        ]
      }
    },
    "requested_proof":{
      "revealed_attrs":{
        "mobile_num":{
          "encoded":"111444249212870126950404680205889709796474572836794180318995173744548268486974",
          "sub_proof_index":0,
          "raw":"01012345678"
        },
        "date_of_birth":{
          "encoded":"20000101",
          "sub_proof_index":0,
          "raw":"20000101"
        },
        "person_name":{
          "encoded":"114735663336402277097990206158879556599192671418456242825414219721487748472651",
          "sub_proof_index":0,
          "raw":"김증명"
        }
      },
      "self_attested_attrs":{
        
      },
      "unrevealed_attrs":{
        
      },
      "predicates":{
        
      }
    },
    "identifiers":[
      {
        "schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
        "cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"
      }
    ]
  },
  "created_at":"2021-08-06 03:47:20.722007Z",
  "thread_id":"f26c3468-e2c0-4575-9085-519b8e98f6c8",
  "presentation_exchange_id":"a0b47251-7ad8-4210-9643-cb608983e8fc",
  "verified":"true",
  "presentation_request":{
    "nonce":"878752732301195434710634",
    "name":"토익성적증명서발행 모바일가입검증",
    "version":"1.0",
    "requested_attributes":{
      "date_of_birth":{
        "restrictions":[
          {
            "schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
            "cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"
          }
        ],
        "non_revoked":{
          "from":0,
          "to":1628221640
        },
        "name":"date_of_birth"
      },
      "mobile_num":{
        "restrictions":[
          {
            "schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
            "cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"
          }
        ],
        "non_revoked":{
          "from":0,
          "to":1628221640
        },
        "name":"mobile_num"
      },
      "person_name":{
        "restrictions":[
          {
            "schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
            "cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"
          }
        ],
        "non_revoked":{
          "from":0,
          "to":1628221640
        },
        "name":"person_name"
      }
    },
    "requested_predicates":{
      
    }
  },
  "connection_id":"a7247c17-d237-4a83-84a6-b2e641b5ab05",
  "trace":false,
  "presentation_request_dict":{
    "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/request-presentation",
    "@id":"f26c3468-e2c0-4575-9085-519b8e98f6c8",
    "request_presentations~attach":[
      {
        "@id":"libindy-request-presentation-0",
        "mime-type":"application/json",
        "data":{
          "base64":"eyJuYW1lIjogIlx1ZDFhMFx1Yzc3NVx1YzEzMVx1YzgwMVx1Yzk5ZFx1YmE4NVx1YzExY1x1YmMxY1x1ZDU4OSBcdWJhYThcdWJjMTRcdWM3N2NcdWFjMDBcdWM3ODVcdWFjODBcdWM5OWQiLCAidmVyc2lvbiI6ICIxLjAiLCAicmVxdWVzdGVkX2F0dHJpYnV0ZXMiOiB7ImRhdGVfb2ZfYmlydGgiOiB7Im5hbWUiOiAiZGF0ZV9vZl9iaXJ0aCIsICJub25fcmV2b2tlZCI6IHsiZnJvbSI6IDAsICJ0byI6IDE2MjgyMjE2NDB9LCAicmVzdHJpY3Rpb25zIjogW3sic2NoZW1hX2lkIjogImNVOHJFcmpnS2o4ZmduMWtURHJlbjoyOlBlcnNvbklkZW50aXR5Q3JlZGVudGlhbDoxLjAiLCAiY3JlZF9kZWZfaWQiOiAiVG1pc25FQUdCUGVWVkRqdEFYUGRZdDozOkNMOjA6djAxIn1dfSwgIm1vYmlsZV9udW0iOiB7Im5hbWUiOiAibW9iaWxlX251bSIsICJub25fcmV2b2tlZCI6IHsiZnJvbSI6IDAsICJ0byI6IDE2MjgyMjE2NDB9LCAicmVzdHJpY3Rpb25zIjogW3sic2NoZW1hX2lkIjogImNVOHJFcmpnS2o4ZmduMWtURHJlbjoyOlBlcnNvbklkZW50aXR5Q3JlZGVudGlhbDoxLjAiLCAiY3JlZF9kZWZfaWQiOiAiVG1pc25FQUdCUGVWVkRqdEFYUGRZdDozOkNMOjA6djAxIn1dfSwgInBlcnNvbl9uYW1lIjogeyJuYW1lIjogInBlcnNvbl9uYW1lIiwgIm5vbl9yZXZva2VkIjogeyJmcm9tIjogMCwgInRvIjogMTYyODIyMTY0MH0sICJyZXN0cmljdGlvbnMiOiBbeyJzY2hlbWFfaWQiOiAiY1U4ckVyamdLajhmZ24xa1REcmVuOjI6UGVyc29uSWRlbnRpdHlDcmVkZW50aWFsOjEuMCIsICJjcmVkX2RlZl9pZCI6ICJUbWlzbkVBR0JQZVZWRGp0QVhQZFl0OjM6Q0w6MDp2MDEifV19fSwgInJlcXVlc3RlZF9wcmVkaWNhdGVzIjoge30sICJub25jZSI6ICI4Nzg3NTI3MzIzMDExOTU0MzQ3MTA2MzQifQ=="
        }
      }
    ],
    "comment":"{\"verification_template_id\":\"82c75511-4eb8-49db-b065-58cda32f8bb3\",\"agreement\":{\"type\":\"initial_agreement\",\"content\":[{\"sequence\":1,\"title\":\"개인정보 수집 및 이용 동의서\",\"is_mandatory\":\"true\",\"terms_id\":\"person\",\"terms_ver\":\"1.0\",\"agreement\":\"Initial 서비스(이하 \u201C서비스\u201D라한다)와 관련하여, 본인은 동의내용을 숙지하였 으며, 이에따라 본인의 개인정보를 귀사(SK텔레콤주식회사)가 수집 및 이용하는 것에 대해 동의합니다. 본동의는 서비스의 본질적 기능제공을 위한 개인정보 수집/이용에 대한 동의로서, 동의를 하는경우에만 서비스 이용이 가능합니다.법령에따른개인정보 의수집/이용, 계약의이행/편익제공을위한개인정보취급위탁및개인정보취급과관련된일반사항은서비스의개인정보처리방침에따릅니다.\",\"condition\":[{\"sub_title\":\"수집 항목\",\"target\":\"이름,생년월일\"},{\"sub_title\":\"수집및이용목적\",\"target\":\"서비스이용에따른본인확인\"},{\"sub_title\":\"이용기간및보유/파기\",\"target\":\"1년\"},{\"sub_title\":\"기타 정보\",\"target\":\"기타 내용\"}]},{\"sequence\":2,\"title\":\"위치정보 수집 및 이용 동의서\",\"is_mandatory\":\"true\",\"terms_id\":\"location\",\"terms_ver\":\"1.0\",\"agreement\":\"이 약관은 이니셜(SK텔레콤)(이하 \u201C회사\u201D)가 제공하는 위치정보사업 또는 위치기반서비스사업과 관련하여 회사와 개인위치정보주체와의 권리, 의무 및 책임사항, 기타 필요한 사 항을 규정함을 목적으로 합니다.\",\"condition\":[{\"sub_title\":\"위치정보 수집 방법\",\"target\":\"GPS칩\"},{\"sub_title\":\"위치정보 이용/제공\",\"target\":\"이 약관에 명시되지 않은 사항은 위치정보의 보호 및 이용 등에 관한 법률, 정보통신망 이용촉진 및 정보보호 등에 관한 법률, 전기통신기본법, 전기통신사업법 등 관계법령과 회사의 이용약관 및 개인정보취급방침, 회사가 별도로 정한 지침 등에 의합니다.\"},{\"sub_title\":\"수집목적\",\"target\":\"현재의 위치를 기반으로 하여 주변 매장의 위치 등의 정보를 제공하는 서비스\"},{\"sub_title\":\"위치정보 보유기간\",\"target\":\"1년\"}]},{\"sequence\":3,\"title\":\"테스트 수집 및 이용 동의서\",\"is_mandatory\":\"true\",\"terms_id\":\"test\",\"terms_ver\":\"1.0\",\"agreement\":\"이 약관은 이니셜(SK텔레콤)(이하 \u201C회사\u201D)가 제공하는 위치정보사업 또는 위치기반서비스사업과 관련하여 회사와 개인위치정보주체와의 권리, 의무 및 책임사항, 기타 필요한 사항을 규정함을 목적으로 합니다.\",\"condition\":[{\"sub_title\":\"위치정보 수집 방법\",\"target\":\"GPS칩\"},{\"sub_title\":\"위치정보 이용/제공\",\"target\":\"이 약관에 명시되지 않은 사항은 위치정보의 보호 및 이용 등에 관한 법률, 정보통신망 이용촉진 및 정보보호 등에 관한 법률, 전기통신기본법, 전기통신사업법 등 관계법령과 회사의 이용약관 및 개인정보취급방침, 회사가 별도>로 정한 지침 등에 의합니다.\"},{\"sub_title\":\"수집목적\",\"target\":\"현재의 위치를 기반으로 하여 주변 매장의 위치 등의 정보를 제공하는 서비스\"},{\"sub_title\":\"위치정보 보유기간\",\"target\":\"1년\"}]},{\"sequence\":4,\"title\":\"제3자 정보제공 동의서\",\"is_mandatory\":\"true\",\"terms_id\":\"3rdparty\",\"terms_ver\":\"1.0\",\"agreement\":\" initial 서비스(이하 \u201C서비스\u201D라한다)와 관련하여, 본인은동의내용을숙지하였으며, 이에따라본인의개인정보를귀사(이슈어)가수집한개인정보를아래와같이제3자에게제공하는것에대해동의합니다. 고객은개인정보의제3자제공에대한동의를거부할권리가있으며, 동의를거부할받는별도의불이익은없습니다. 단, 서비스이용불가능하거나, 서비스이용목적에따른서비스제공에제한이따르게됩니다.\",\"condition\":[{\"sub_title\":\"제공하는자\",\"target\":\"발급기관\"},{\"sub_title\":\"제공받는자\",\"target\":\"이니셜(SK텔레콤)\"},{\"sub_title\":\"제공받는 항목\",\"target\":\"제공항목(생년월일,시험일,성명(영문),만료일,성명(한글),수험번호,듣기점수,읽기점수,총점)\"},{\"sub_title\":\"수집 및 이용목적\",\"target\":\"모바일전자증명서발행\"},{\"sub_title\":\"보유 및 이용기간\",\"target\":\"모 바일 전자증명서 발급을 위해 서버에 임시 저장하였다가, 증명서 발행 후 즉시 삭제(단, 고객 단말기 내부 저장영역에 증명서 형태로 저장/보관)\"}]}]}}"
  },
  "auto_present":false,
  "updated_at":"2021-08-06 03:47:43.952285Z",
  "initiator":"self",
  "topic":"present_proof"
}
```


- verified 결과 True/False 확인

`"verified": "true"`


- 검증 결과의 요청 정보 확인 하기 

Webhook message에서 사용자 data를 확인 하기 위해서는 아래 json 경로를 parsing 한다<br>
`presentation.requested_proof.revealed_attrs.{{requested_attribute}}.raw`
<br> `{{requested_attribute}}` 의 명칭은 검증양식 생성할때 사용한 `"검증명"` + `"Attribute 이름"` 이다.

  ![verify_webhook](img/verify_topic2.png)


### STEP 5. [Option] 고급 증명양식 검증(Verify)의 다양한 기법

아래 검증양식 예제는 검증에 대한 다양한 방법의 참고 자료로, 기관 사용자는 사용하지 않습니다.

##### Proof Request Data Model

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

