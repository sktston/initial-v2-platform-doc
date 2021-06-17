Message
================

**기관**(Issuer/Verifier)와 **사용자**(Holder/Prover)의 SMS 문자와 메신저와 같은 Basic Message 송/수신 예제.

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
### STEP 1. 기관이 사용자에게 Message를 전달. 

* Method and Resource

    `POST` `/connections/{conn_id}/send-message` message 전송.   


* Parameter

    Path Variables
    
    Name | Description
    --- | ---
    conn_id | 사용자와 연결된 connection_id 정보

* Body
```json
{"contents": "{{본문}}"}
```

<div class="admonition warning">
<p class="admonition-title">important</p>
<p>  중요!! 본문이 json일 경우 string으로 변환하여 {{본문}} 입력 한다  </p>
</div>

* Request Sample

```
curl -X 'POST' \
  'https://dev-console.myinitial.io/agent/api/connections/91026652-18dc-4119-b8b9-9957c8430d86/send-message' \
  -H 'accept: application/json' \
  -H 'Authorization: bearer 4dd1f97a-1234-1234-1234-9ed8cd2cfb6d' \
  -H 'Content-Type: application/json' \
  -d '{
  "content": "String"
}'
```

* Response body
```json
{ }
```


* Basicmessages State

Topic | State | Description
--- | --- | ---
basicmessages | received | message를 받은 상태


<br><br>

### [참고] initial Message Spec

Basic Message의 Type은 아래와 같이 정의 (2021.06.03)

type | from | to | Definition
--- | ---| ---| ---
initial_agreement |	issuer	| initial app | **<span style="color:red">(Issuer Only)</span>** 개인정보 이용 동의/약관 요청
initial_agreement_decision	| initial app |	issuer	| **<span style="color:red">(Issuer Only)</span>** 개인정보동의/약관 요청 결과 전송
initial_web_view | issuer/verifier	| initial app |	web_view url 표시 요청

<p></p>

<div class="admonition warning">
<p class="admonition-title">important</p>
<p>  중요!!  Verify(검증)이 포함된 기관은 Verify API에서 이용약관 및 동의서 보냄 </p>
</div>

<br>

#### 1. initial_agreement (기관 --> 사용자)

개인정보 수집 및 이용 동의서 전달을 위해 사용. 


Field name | Description
--- | ---
type | Message의 종류
title | 대제목
is_mandatory | 필수 여부
terms_id | 동의서 구분 값
terms_ver | 버전
agreement | 동의서 내용 (각 기관 법무팀 검토 필요)
condition | 각 기관 부제목 및 값을 표시. `sub_title` 및 `target` 을 key 값으로 list 형태 

<br>

* 동의서 {{본문}} Sample

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


<div class="admonition warning">
<p class="admonition-title">warning</p>
<p> Verify가 포함된 기관의 동의서 전달은 Message API가 아닌 Verify API로 진행 한다 </p>
</div>


* Request Sample

cURL Request Example

```
curl -X 'POST' \
  'https://dev-console.myinitial.io/agent/api/connections/91026652-18dc-4119-b8b9-9957c8430d86/send-message' \
  -H 'accept: application/json' \
  -H 'Authorization: bearer 4dd1f97a-1234-1234-1234-9ed8cd2cfb6d' \
  -H 'Content-Type: application/json' \
  -d '{"content":"{\"type\":\"initial_agreement\",\"content\":[{\"sequence\":1,\"title\":\"개인정보 수집 및 이용 동의서\",\"is_mandatory\":\"true\",\"terms_id\":\"person\",\"terms_ver\":\"1.0\",\"agreement\":\"Initial 서비스(이하 \u201C서비스\u201D라한다)와관련하여,본인은동의내용을숙지하였으며, 이에따라본인의개인정보를귀사(SK텔레콤주식회사)가수집및이용하는것에대해동의합니다.본동의는서비스의본질적기능제공을위한개인정보수집/이용에대한동의로서, 동의를하는경우에만서비스이용이가능합니다.법령에따른개인정보의수집/이용, 계약의이행/편익제공을위한개인정보취급위탁및개인정보취급과관련된일반사항은서비스의개인정보처리방침에따릅니다.\",\"condition\":[{\"sub_title\":\"수집 항목\",\"target\":\"이름,생년월일\"},{\"sub_title\":\"수집및이용목적\",\"target\":\"서비스이용에따른본인확인\"},{\"sub_title\":\"이용기간및보유/파기\",\"target\":\"1년\"}]},{\"sequence\":2,\"title\":\"위치정보 수집 및 이용 동의서\",\"is_mandatory\":\"true\",\"terms_id\":\"location\",\"terms_ver\":\"1.0\",\"agreement\":\"이 약관은 이니셜(SK텔레콤)(이하 \u201C회사\u201D)가 제공하는 위치정보사업 또는 위치기반서비스사업과 관련하여 회사와 개인위치정보주체와의 권리, 의무 및 책임사항, 기타 필요한 사항을 규정함을 목적으로 합니다.\",\"condition\":[{\"sub_title\":\"위치정보 수집 방법\",\"target\":\"GPS칩\"},{\"sub_title\":\"위치정보 이용/제공\",\"target\":\"이 약관에 명시되지 않은 사항은 위치정보의 보호 및 이용 등에 관한 법률, 정보통신망 이용촉진 및 정보보호 등에 관한 법률, 전기통신기본법, 전기통신사업법 등 관계법령과 회사의 이용약관 및 개인정보취급방침, 회사가 별도로 정한 지침 등에 의합니다.\"},{\"sub_title\":\"수집목적\",\"target\":\"현재의 위치를 기반으로 하여 주변 매장의 위치 등의 정보를 제공하는 서비스\"},{\"sub_title\":\"위치정보 보유기간\",\"target\":\"1년\"}]},{\"sequence\":3,\"title\":\"제3자 정보제공 동의서\",\"is_mandatory\":\"true\",\"terms_id\":\"3rdparty\",\"terms_ver\":\"1.0\",\"agreement\":\" initial 서비스(이하 \u201C서비스\u201D라한다)와관련하여, 본인은동의내용을숙지하였으며, 이에따라본인의개인정보를귀사(이슈어)가수집한개인정보를아래와같이제3자에게제공하는것에대해동의합니다. 고객은개인정보의제3자제공에대한동의를거부할권리가있으며, 동의를거부할받는별도의불이익은없습니다. 단, 서비스이용불가능하거나, 서비스이용목적에따른서비스제공에제한이따르게됩니다.\",\"condition\":[{\"sub_title\":\"제공하는자\",\"target\":\"발급기관\"},{\"sub_title\":\"제공받는자\",\"target\":\"이니셜(SK텔레콤)\"},{\"sub_title\":\"제공받는 항목\",\"target\":\"제공항목(생년월일,시험일,성명(영문),만료일,성명(한글),수험번호,듣기점수,읽기점수,총점)\"},{\"sub_title\":\"수집 및 이용목적\",\"target\":\"모바일전자증명서발행\"},{\"sub_title\":\"보유 및 이용기간\",\"target\":\"모바일 전자증명서 발급을 위해 서버에 임시 저장하였다가, 증명서 발행 후 즉시 삭제(단, 고객 단말기 내부 저장영역에 증명서 형태로 저장/보관)\"}]}]}"}'
```


<p></p>




#### 2. initial_agreement_decision : 사용자 --> 기관 

동의서 결과 전송 받기.

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 사용자가 initial app에서 동의 내용을 확인 후 약관 동의를 하면 기관에게 결과 전달 </p>
</div>

Field name | Description
--- | ---
type | Message의 종류
agree_yn | 동의 여부 
signature | 사용자 서명 (사용안함)

<p></p>
- 기관은 Webhook Message를 확인 하여 동의서 결과를 전송 받는다. 
  
기관에게 전달되는 `initial_agreement_decision` example

```json
{
  "type":"initial_agreement_decision",
  "content":{
    "agree_yn":"Y",
    "signature":""
  }
}
```

* Next Step

    - 기관 관리자는 동의 결과를 사용자 정보(connection_id, CI, unique id등)와 mapping하여 정보를 관리한다. 

<br>
#### 3. initial_web_view (기관 --> 사용자)

- 기관은 Web View를 요청할 수 있다. initial app은 해당 web-view 화면을 출력 한다
- Web View는 사용자로 부터 추가 정보 입력이 필요할때 사용한다 
Web_view 요청 content sample

```json
{
  "type":"initial_web_view",
  "content":{
    "web_view_url":"https://www.sktelecom.com/test.php?their_did=UtArAzrfSaTF77mNJVcCrA"
  }
}
```


Web_view 요청 Request Sample
```
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/connections/b62bfc68-8762-4e9d-af4a-221502508944/send-message' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer 2ca4dd8a-xxxx-xxxx-xxxx-c5fb0286f2cc' \
--data-raw '{"content":" {\"type\" : \"initial_web_view\" , \"content\" = { \"web_view_url\":\"https://www.sktelecom.com/test.php?their_did=UtArAzrfSaTF77mNJVcCrA\" }}"}'
```

<br>

- 취소 버튼 Java Script Guide
    - web_view의 취소 버튼 삽입 시 아래 java script code를 적용하면, 사용자앱은 전체 진행을 취소.
    - sample code : <https://github.com/sktston/initial-controller-java/blob/cloud_demo/src/main/resources/static/web-view/form.html>

```javascript
<script>
    function cancle(){
        //initial app에서 web_view 화면을 즉시 종료 하는 code
        var userAgent = navigator.userAgent.toLowerCase();
        if (userAgent.search("android") > -1) {
            Android.failVC ();
        }
        else {
            webkit.messageHandlers.callbackHandler.postMessage("failVC");
        }
    }
</script>
```