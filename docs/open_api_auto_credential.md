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

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 VC발급 예제
![issue_diagram](img/issue_diagram.png)
[PlantUML](http://www.plantuml.com/plantuml/uml/TLD1Rn916BtFh_ZIqsx3zWSqG6gywCruw7qBYsxSxgwxWqblY9XKsqID9QLr8QX1Ww792eWSwX_Q-VWFVimCMuMGS61dtlVUlFTc3c9kHxn-x377vou_WHTEvDPS9m8xXfr3_R6psgt7UWUR8xmUOk-zyI3mghHuBmpD7ER6SLq37yg_EuonKWd2o8cSvtKtTha3fH9Z6bYqoefw7og1xpiLxWQ-WPTfDavDmAkM-7ek2Hv5GHZ4jcS4uH9YIX8vIGoQR1z4ccJpDu3z1BiT8zGp9lC3alMS6cUx2g8HArOJH7DAjdQPbVi3CPbZbtvJmEyliV-1s3zbapxWsGox3JYyBmQtiD9WbY9UnsnR9tLOj6_4Zo7HljFoyk2pX5dAfEr1Da_5sm4cj-9p0lYgAtg_84iRjBR7h68jy3dbvWLHwSYhE-NYKTdrNUxIe1s699GV3vIjHRk3boEuyuZN5pY-8TsME1ljKOjLM6QrgiVwDzprAKCvCs5nrQQWP4r1hKPOuzZsgpOFeXFLKqudoruhgdrgo2dTKkG-UPhNzDz-tf6wU9JWHj60lTDbMCCfPB_duhonZ8sYntOBnCT4V1jbFrFHx1IOQW2_l1Q3JY5lOjB8ndyAEBkWZ7NETvb8Wu_f5ZinNxUeh-A6GTshIcZRVTLnHehQL4Bo-QofRClHaElc9Z_s6zWRg6Tas9LdJjLa1sI8dlrV)

issue_credential State

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

<br><br>

### STEP 0. Alice(Holder) --> Faber(발급자) : Issuer에게 Credential 발행 proposal

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP0는 Holder(initial app)에서 Credential Proposal을 먼저 진행 한다</p>
</div>

Holder가 credential proposal을 요청하면, Webhook을 통해 아래와 같은 event가 전달 된다.
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
Issuer는 아래 정보를 확인 해야 함.
<br>1. `"topic": "issue_credential"`, `"state": "proposal_received"` (사용자가 발급 요청을 했음을 확인) 
<br>2. `"cred_def_id":"CB1f9WKGAJDwUKCT2XEx7o:3:CL:1617870264:9f714e9d-4dfb-4d9f-8c8f-60281c729745"` (사용자가 요청한 VC 정보)
<br>3. `"connection_id":"0844ebf0-c88f-49cf-9ed0-d0b57cfd9ec8"` (사용자와 연결 정보)
<br>4. `"credential_exchange_id": "148b673e-d506-431a-8063-a70aebdaadfe"` (VC발급 session id)
<br><br>

### STEP 1-1. Faber(발급자) --> Alice(Holder) : Proposal의 credential_exchange_id 기반 Credential 발행 

* Method and Resource

    `POST` `/issue-credential​/send-offer` credential preview를 보내면서 offer.  

* Parameter

 Name | Description 
 --- | --- 
 body | Schema String 
 connection_id | Holder와 연결을 위해 사용한 ID. STEP0의 Webhook Event를 통해 확인 가능하다. 
 cred_def_id | 발급할 Credential ID. STEP0의 Webhook Event를 통해 확인 가능하다.
 credential_preview | Holder에 발급할 VC의 정보를 기입한다. 
 counter_proposal | 사용자의 proposal에 대한 응답임을 명시
 credential_proposal | 
 auto_issue | Alice가 credential request를 하면 자동으로 issue 완료<br>`/issue-credential/records/{cred_ex_id}/issue` 자동 처리
 auto_remove | Credential issue 완료되면 cred_ex_id record를 자동 삭제 <br> `/issue-credential/records/{cred_ex_id}/remove` 자동 처리 
 
<p></p>

* Example

    * input <br>
    `connection_id` : `string`<br>
    `attribute` : `list string`<br>
    `cred_def_id` : `string`<br>

    * body
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
    }
  }
}
```

cURL Request Example
```
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/issue-credential/records/553b95d3-ab6e-41d8-83db-fbd68aadce40/send-offer' \
--header 'Authorization: Bearer 2ca4dd8a-22b6-421c-bf2b-c5fb0286f2cc' \
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
      ]
    }
  }
}'
```

<p></p>


<p></p>

* issue_credential State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | issue_credential state | **`offer_sent`** | N/A |
<p></p>


* Next Step
<br> Push Notification/Webhook등으로 Alice에게 전달. 
<br> Alice의 `/issue-credential/records/{cred_ex_id}/send-request` send request를 waiting.
<p></p>


    
<br><br>

### STEP 1-1. Faber(발급자) --> Alice(Holder) : Proposal의 credential_exchange_id 기반 Credential 발행

* Method and Resource

  `POST` `/issue-credential​/send-offer` credential preview를 보내면서 offer.

* Parameter

Name | Description
 --- | --- 
body | Schema String
connection_id | Holder와 연결을 위해 사용한 ID. STEP0의 Webhook Event를 통해 확인 가능하다.
cred_def_id | 발급할 Credential ID. STEP0의 Webhook Event를 통해 확인 가능하다.
credential_preview | Holder에 발급할 VC의 정보를 기입한다.
auto_issue | Alice가 credential request를 하면 자동으로 issue 완료<br>`/issue-credential/records/{cred_ex_id}/issue` 자동 처리
auto_remove | Credential issue 완료되면 cred_ex_id record를 자동 삭제 <br> `/issue-credential/records/{cred_ex_id}/remove` 자동 처리

<p></p>

* Example

    * input <br>
      `connection_id` : `string`<br>
      `attribute` : `list string`<br>
      `cred_def_id` : `string`<br>

    * body
```json
{
  "connection_id": "2a6ed506-9577-46ee-a60c-726241f89aec",
  "cred_def_id": "DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd",
  "credential_preview": {
    "attributes": [
      {"name": "학년", "value": "2"},
      {"name": "성명", "value": "Alice"},
      {"name": "과정명", "value": "CS"},
      {"name": "생년월일", "value": "19990101"},
      {"name": "학교이름", "value": "SK대학교"},
      {"name": "입학년월일", "value": "20190301"}
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
        "attributes": [
            {"name": "학년", "value": "2"}, 
            {"name": "성명", "value": "Alice"},
            {"name": "과정명", "value": "CS"},
            {"name": "생년월일", "value": "19990101"},
            {"name": "학교이름", "value": "SK대학교"},
            {"name": "입학년월일", "value": "20190301"}
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

* Response body
```json
{
    "credential_offer": {
        "schema_id": "9c74RUPtMwtiSXq8tVDqxp:2:대학제증명:1.0",
        "cred_def_id": "9c74RUPtMwtiSXq8tVDqxp:3:CL:5108:SK대학교",
        "key_correctness_proof": {
            "c": "3574860104439614441631755614593377391996482033957826376030782602429297358144",
            "xz_cap": "82662898944207905145486057550555262994060493972118983793053791602839408589021262850031583029775050151361560461018756623364210432600668181816548534962509278185503324377253107157926401434547507800912111886812062871322881346095731188765602241200630329990488783751648592549984088398360677744515620695232390090386944608056639007386642119194236445571625701614202341158950378955901750443961851876156037093724166085561079382495813066133053910652612609361935344969440640120574219638602339372960664418509071084219000740783039073459550355637844282126302073670169221844887131872820490710879505110848388167479981267418562516174015567739336917177123219566506461874838388867089009844976618795924267804348154",
            "xr_cap": [
                [
                    "master_secret",
                    "62001042206898037330759247590667274366673690816328831544074740476676759957101023625578114049380390467511715349769345558532249596023043637696943512685456201567375841881315290463087331849678280539332025690150155223172541794020118709898697060046335676460943036938786089536039539901689465527736561535871543890415281786119849798314450026073033155220191418982866767974032674222387905093500346826962149144412673494569607381205477325157684753805425266663513376679643563006265437892069450943528340517270248531185569815252228988288282793934050144997060200635271473433606154646313075227430066992924580017492322582114024906965793135140549460614489623456833607434959636808526093824481227390849027206187183"
                ],
                [
                    "성명",
                    "7546002179926178749639584262520532948685328435895845047204194702496150055913353492787148650243698924817730478971177242188970093543812341021572067604583692960171816907601392329064660156088475908140494541097264437148496150155240514678668721805334053962894938367796880945136013345814930654181874252236937784909189035994122767750194061957768967235321928536727838075442877815980073566017437315129055431068171029582359618016841424349091836768646199936123426633226020447853286040023370317512791439194838242523048014892528484244898103037648186427871470024362026710067427600478467091768740784492994203228720421236774418644942410146884184441663965682229968713245774576287587804651697958763235515128351"
                ],
                [
                    "과정명",
                    "34749723426337900276709315829199845443889801450190894106750453844963884150904161418698403772752567930275221170203843538188849851133117344584018625004545059399294445681739040090968089200461057061256433237480602541804086398732924289742323658342987846329375048167945819186900532396385415920707676529324835843031770102895627437049733775905730966268418227992805375449263179243833664783151922848768601778773469137170577842582319785610355125870398574842618188887579231474570520901217729378881670400299581053655054237729554240350925342199945645574862900599139043034146735398303281842365595778050438859827968792227820381718644284194229147836484342059670953467115760060418760029945823365899766325423069"
                ],
                [
                    "입학년월일",
                    "36217319999302314348475667377736086934523824774962339922352752645733147728548082344796325832763939153299003943223854114688594656504046548183927473122732425408631913201756683381489034512210535238113797813109499361348837706206488318552715047667906172585065974689889706421192025693701422344763475413426878893651018447475811462761766371768279580367382968869964582233000493317409113229563577670415094852540716675278547988060765346012847492454123263663441349396258845112110126362843979687172073001055990140861002845986736644447121210528968171862604529730796982093833186521997002195523630066402798899419791583885618606504698895373791911397531767889267367961847025652726593709744887037990771523357558"
                ],
                [
                    "학교이름",
                    "21160063921536232174796388861266854042223249907675286543402336613834391636321746024711456043354073017064917552374178336692945025489853979378573718283837277630089040797625138381009406012420777860022965286134726201454876067404501933897989457506104028287902489991167474395484378932710596895712733805872384387135881574751978243970837534351584412083737825779217407102986540490043084529496415801802040184745957803087561227306401368821386300801228784925550981658147443581915815293321787323777788583931241986380020394800699424812373109992066017677350034319880109444837278313713615674107040860165890191830654731329471534949847894917264923931029833052245560704017120801395570040993002000342073587587378"
                ],
                [
                    "생년월일",
                    "67986171164147549269552058788831514271799603257001677807354068099185070377889539643367638623091086203286977687688687755600566140538021210662083004307909196226844347299330524848014883121449354982645447878173570424986062882255214953244657514144174487081540391029005769652806133721594849897172480882908840081944811337928026119946668098465031167584087844629345650138374244485242504868965834615156634606356526161886095055470486456938264028142739265505415156546238081706603898983276728169085470046845369733207940413744918420639290143922170007366502629785976780291269002094937705892663892395755080952226621061959916651721119183910777489615121110160211130743591796098772944335263909722743873458580872"
                ],
                [
                    "학년",
                    "33775276995444246089730537509007075323795637320283126373617135528270137829559496942113789393948493291561176972030070523579283494604397544070409207993679246895788457880931179655217711292592354804825295122834076335947210077320412180991394600827462237189253404734369766321582979131172011433820228545589982476918079526983276208891324013620591228005516841773977185125473332830325062080270182906310786621695182458802978942614376853568343973455682398545331795707509004253850025579764348966727105957687580117068304532052893800423954545670985639485022190199029146897901943156210065256097009017149688065803231521258294379718322895118591501135675339559660692710024274329032209289131125963150017588946312"
                ]
            ]
        },
        "nonce": "88916117075781627654640"
    },
    "initiator": "self",
    "credential_proposal_dict": {
        "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
        "@id": "32e8be8f-0187-47fc-a6cc-59ebb16341a7",
        "comment": "SK대학교 Alice 제증명",
        "cred_def_id": "9c74RUPtMwtiSXq8tVDqxp:3:CL:5108:SK대학교",
        "credential_proposal": {
            "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
            "attributes": [
                {
                    "name": "학년",
                    "value": "2"
                },
                {
                    "name": "성명",
                    "value": "Alice"
                },
                {
                    "name": "과정명",
                    "value": "CS"
                },
                {
                    "name": "생년월일",
                    "value": "19990101"
                },
                {
                    "name": "학교이름",
                    "value": "SK대학교"
                },
                {
                    "name": "입학년월일",
                    "value": "20190301"
                }
            ]
        }
    },
    "connection_id": "15a3e02b-e1bb-4f10-a1e6-ac0968b71fa7",
    "credential_exchange_id": "59583fae-eea9-47f9-9194-448ac473d542",
    "auto_issue": true,
    "credential_offer_dict": {
        "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/offer-credential",
        "@id": "30fd78d6-3fd4-40b6-b3c8-51b7af822ccb",
        "~thread": {},
        "comment": "SK대학교 Alice 제증명",
        "credential_preview": {
            "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
            "attributes": [
                {
                    "name": "학년",
                    "value": "2"
                },
                {
                    "name": "성명",
                    "value": "Alice"
                },
                {
                    "name": "과정명",
                    "value": "CS"
                },
                {
                    "name": "생년월일",
                    "value": "19990101"
                },
                {
                    "name": "학교이름",
                    "value": "SK대학교"
                },
                {
                    "name": "입학년월일",
                    "value": "20190301"
                }
            ]
        },
        "offers~attach": [
            {
                "@id": "libindy-cred-offer-0",
                "mime-type": "application/json",
                "data": {
                    "base64": "eyJzY2hlbWFfaWQiOiAiOWM3NFJVUHRNd3RpU1hxOHRWRHF4cDoyOlx1YjMwMFx1ZDU1OVx1YzgxY1x1Yzk5ZFx1YmE4NToxLjAiLCAiY3JlZF9kZWZfaWQiOiAiOWM3NFJVUHRNd3RpU1hxOHRWRHF4cDozOkNMOjUxMDg6U0tcdWIzMDBcdWQ1NTlcdWFkNTAiLCAia2V5X2NvcnJlY3RuZXNzX3Byb29mIjogeyJjIjogIjM1NzQ4NjAxMDQ0Mzk2MTQ0NDE2MzE3NTU2MTQ1OTMzNzczOTE5OTY0ODIwMzM5NTc4MjYzNzYwMzA3ODI2MDI0MjkyOTczNTgxNDQiLCAieHpfY2FwIjogIjgyNjYyODk4OTQ0MjA3OTA1MTQ1NDg2MDU3NTUwNTU1MjYyOTk0MDYwNDkzOTcyMTE4OTgzNzkzMDUzNzkxNjAyODM5NDA4NTg5MDIxMjYyODUwMDMxNTgzMDI5Nzc1MDUwMTUxMzYxNTYwNDYxMDE4NzU2NjIzMzY0MjEwNDMyNjAwNjY4MTgxODE2NTQ4NTM0OTYyNTA5Mjc4MTg1NTAzMzI0Mzc3MjUzMTA3MTU3OTI2NDAxNDM0NTQ3NTA3ODAwOTEyMTExODg2ODEyMDYyODcxMzIyODgxMzQ2MDk1NzMxMTg4NzY1NjAyMjQxMjAwNjMwMzI5OTkwNDg4NzgzNzUxNjQ4NTkyNTQ5OTg0MDg4Mzk4MzYwNjc3NzQ0NTE1NjIwNjk1MjMyMzkwMDkwMzg2OTQ0NjA4MDU2NjM5MDA3Mzg2NjQyMTE5MTk0MjM2NDQ1NTcxNjI1NzAxNjE0MjAyMzQxMTU4OTUwMzc4OTU1OTAxNzUwNDQzOTYxODUxODc2MTU2MDM3MDkzNzI0MTY2MDg1NTYxMDc5MzgyNDk1ODEzMDY2MTMzMDUzOTEwNjUyNjEyNjA5MzYxOTM1MzQ0OTY5NDQwNjQwMTIwNTc0MjE5NjM4NjAyMzM5MzcyOTYwNjY0NDE4NTA5MDcxMDg0MjE5MDAwNzQwNzgzMDM5MDczNDU5NTUwMzU1NjM3ODQ0MjgyMTI2MzAyMDczNjcwMTY5MjIxODQ0ODg3MTMxODcyODIwNDkwNzEwODc5NTA1MTEwODQ4Mzg4MTY3NDc5OTgxMjY3NDE4NTYyNTE2MTc0MDE1NTY3NzM5MzM2OTE3MTc3MTIzMjE5NTY2NTA2NDYxODc0ODM4Mzg4ODY3MDg5MDA5ODQ0OTc2NjE4Nzk1OTI0MjY3ODA0MzQ4MTU0IiwgInhyX2NhcCI6IFtbIm1hc3Rlcl9zZWNyZXQiLCAiNjIwMDEwNDIyMDY4OTgwMzczMzA3NTkyNDc1OTA2NjcyNzQzNjY2NzM2OTA4MTYzMjg4MzE1NDQwNzQ3NDA0NzY2NzY3NTk5NTcxMDEwMjM2MjU1NzgxMTQwNDkzODAzOTA0Njc1MTE3MTUzNDk3NjkzNDU1NTg1MzIyNDk1OTYwMjMwNDM2Mzc2OTY5NDM1MTI2ODU0NTYyMDE1NjczNzU4NDE4ODEzMTUyOTA0NjMwODczMzE4NDk2NzgyODA1MzkzMzIwMjU2OTAxNTAxNTUyMjMxNzI1NDE3OTQwMjAxMTg3MDk4OTg2OTcwNjAwNDYzMzU2NzY0NjA5NDMwMzY5Mzg3ODYwODk1MzYwMzk1Mzk5MDE2ODk0NjU1Mjc3MzY1NjE1MzU4NzE1NDM4OTA0MTUyODE3ODYxMTk4NDk3OTgzMTQ0NTAwMjYwNzMwMzMxNTUyMjAxOTE0MTg5ODI4NjY3Njc5NzQwMzI2NzQyMjIzODc5MDUwOTM1MDAzNDY4MjY5NjIxNDkxNDQ0MTI2NzM0OTQ1Njk2MDczODEyMDU0NzczMjUxNTc2ODQ3NTM4MDU0MjUyNjY2NjM1MTMzNzY2Nzk2NDM1NjMwMDYyNjU0Mzc4OTIwNjk0NTA5NDM1MjgzNDA1MTcyNzAyNDg1MzExODU1Njk4MTUyNTIyMjg5ODgyODgyODI3OTM5MzQwNTAxNDQ5OTcwNjAyMDA2MzUyNzE0NzM0MzM2MDYxNTQ2NDYzMTMwNzUyMjc0MzAwNjY5OTI5MjQ1ODAwMTc0OTIzMjI1ODIxMTQwMjQ5MDY5NjU3OTMxMzUxNDA1NDk0NjA2MTQ0ODk2MjM0NTY4MzM2MDc0MzQ5NTk2MzY4MDg1MjYwOTM4MjQ0ODEyMjczOTA4NDkwMjcyMDYxODcxODMiXSwgWyJcdWMxMzFcdWJhODUiLCAiNzU0NjAwMjE3OTkyNjE3ODc0OTYzOTU4NDI2MjUyMDUzMjk0ODY4NTMyODQzNTg5NTg0NTA0NzIwNDE5NDcwMjQ5NjE1MDA1NTkxMzM1MzQ5Mjc4NzE0ODY1MDI0MzY5ODkyNDgxNzczMDQ3ODk3MTE3NzI0MjE4ODk3MDA5MzU0MzgxMjM0MTAyMTU3MjA2NzYwNDU4MzY5Mjk2MDE3MTgxNjkwNzYwMTM5MjMyOTA2NDY2MDE1NjA4ODQ3NTkwODE0MDQ5NDU0MTA5NzI2NDQzNzE0ODQ5NjE1MDE1NTI0MDUxNDY3ODY2ODcyMTgwNTMzNDA1Mzk2Mjg5NDkzODM2Nzc5Njg4MDk0NTEzNjAxMzM0NTgxNDkzMDY1NDE4MTg3NDI1MjIzNjkzNzc4NDkwOTE4OTAzNTk5NDEyMjc2Nzc1MDE5NDA2MTk1Nzc2ODk2NzIzNTMyMTkyODUzNjcyNzgzODA3NTQ0Mjg3NzgxNTk4MDA3MzU2NjAxNzQzNzMxNTEyOTA1NTQzMTA2ODE3MTAyOTU4MjM1OTYxODAxNjg0MTQyNDM0OTA5MTgzNjc2ODY0NjE5OTkzNjEyMzQyNjYzMzIyNjAyMDQ0Nzg1MzI4NjA0MDAyMzM3MDMxNzUxMjc5MTQzOTE5NDgzODI0MjUyMzA0ODAxNDg5MjUyODQ4NDI0NDg5ODEwMzAzNzY0ODE4NjQyNzg3MTQ3MDAyNDM2MjAyNjcxMDA2NzQyNzYwMDQ3ODQ2NzA5MTc2ODc0MDc4NDQ5Mjk5NDIwMzIyODcyMDQyMTIzNjc3NDQxODY0NDk0MjQxMDE0Njg4NDE4NDQ0MTY2Mzk2NTY4MjIyOTk2ODcxMzI0NTc3NDU3NjI4NzU4NzgwNDY1MTY5Nzk1ODc2MzIzNTUxNTEyODM1MSJdLCBbIlx1YWNmY1x1YzgxNVx1YmE4NSIsICIzNDc0OTcyMzQyNjMzNzkwMDI3NjcwOTMxNTgyOTE5OTg0NTQ0Mzg4OTgwMTQ1MDE5MDg5NDEwNjc1MDQ1Mzg0NDk2Mzg4NDE1MDkwNDE2MTQxODY5ODQwMzc3Mjc1MjU2NzkzMDI3NTIyMTE3MDIwMzg0MzUzODE4ODg0OTg1MTEzMzExNzM0NDU4NDAxODYyNTAwNDU0NTA1OTM5OTI5NDQ0NTY4MTczOTA0MDA5MDk2ODA4OTIwMDQ2MTA1NzA2MTI1NjQzMzIzNzQ4MDYwMjU0MTgwNDA4NjM5ODczMjkyNDI4OTc0MjMyMzY1ODM0Mjk4Nzg0NjMyOTM3NTA0ODE2Nzk0NTgxOTE4NjkwMDUzMjM5NjM4NTQxNTkyMDcwNzY3NjUyOTMyNDgzNTg0MzAzMTc3MDEwMjg5NTYyNzQzNzA0OTczMzc3NTkwNTczMDk2NjI2ODQxODIyNzk5MjgwNTM3NTQ0OTI2MzE3OTI0MzgzMzY2NDc4MzE1MTkyMjg0ODc2ODYwMTc3ODc3MzQ2OTEzNzE3MDU3Nzg0MjU4MjMxOTc4NTYxMDM1NTEyNTg3MDM5ODU3NDg0MjYxODE4ODg4NzU3OTIzMTQ3NDU3MDUyMDkwMTIxNzcyOTM3ODg4MTY3MDQwMDI5OTU4MTA1MzY1NTA1NDIzNzcyOTU1NDI0MDM1MDkyNTM0MjE5OTk0NTY0NTU3NDg2MjkwMDU5OTEzOTA0MzAzNDE0NjczNTM5ODMwMzI4MTg0MjM2NTU5NTc3ODA1MDQzODg1OTgyNzk2ODc5MjIyNzgyMDM4MTcxODY0NDI4NDE5NDIyOTE0NzgzNjQ4NDM0MjA1OTY3MDk1MzQ2NzExNTc2MDA2MDQxODc2MDAyOTk0NTgyMzM2NTg5OTc2NjMyNTQyMzA2OSJdLCBbIlx1Yzc4NVx1ZDU1OVx1YjE0NFx1YzZkNFx1Yzc3YyIsICIzNjIxNzMxOTk5OTMwMjMxNDM0ODQ3NTY2NzM3NzczNjA4NjkzNDUyMzgyNDc3NDk2MjMzOTkyMjM1Mjc1MjY0NTczMzE0NzcyODU0ODA4MjM0NDc5NjMyNTgzMjc2MzkzOTE1MzI5OTAwMzk0MzIyMzg1NDExNDY4ODU5NDY1NjUwNDA0NjU0ODE4MzkyNzQ3MzEyMjczMjQyNTQwODYzMTkxMzIwMTc1NjY4MzM4MTQ4OTAzNDUxMjIxMDUzNTIzODExMzc5NzgxMzEwOTQ5OTM2MTM0ODgzNzcwNjIwNjQ4ODMxODU1MjcxNTA0NzY2NzkwNjE3MjU4NTA2NTk3NDY4OTg4OTcwNjQyMTE5MjAyNTY5MzcwMTQyMjM0NDc2MzQ3NTQxMzQyNjg3ODg5MzY1MTAxODQ0NzQ3NTgxMTQ2Mjc2MTc2NjM3MTc2ODI3OTU4MDM2NzM4Mjk2ODg2OTk2NDU4MjIzMzAwMDQ5MzMxNzQwOTExMzIyOTU2MzU3NzY3MDQxNTA5NDg1MjU0MDcxNjY3NTI3ODU0Nzk4ODA2MDc2NTM0NjAxMjg0NzQ5MjQ1NDEyMzI2MzY2MzQ0MTM0OTM5NjI1ODg0NTExMjExMDEyNjM2Mjg0Mzk3OTY4NzE3MjA3MzAwMTA1NTk5MDE0MDg2MTAwMjg0NTk4NjczNjY0NDQ0NzEyMTIxMDUyODk2ODE3MTg2MjYwNDUyOTczMDc5Njk4MjA5MzgzMzE4NjUyMTk5NzAwMjE5NTUyMzYzMDA2NjQwMjc5ODg5OTQxOTc5MTU4Mzg4NTYxODYwNjUwNDY5ODg5NTM3Mzc5MTkxMTM5NzUzMTc2Nzg4OTI2NzM2Nzk2MTg0NzAyNTY1MjcyNjU5MzcwOTc0NDg4NzAzNzk5MDc3MTUyMzM1NzU1OCJdLCBbIlx1ZDU1OVx1YWQ1MFx1Yzc3NFx1Yjk4NCIsICIyMTE2MDA2MzkyMTUzNjIzMjE3NDc5NjM4ODg2MTI2Njg1NDA0MjIyMzI0OTkwNzY3NTI4NjU0MzQwMjMzNjYxMzgzNDM5MTYzNjMyMTc0NjAyNDcxMTQ1NjA0MzM1NDA3MzAxNzA2NDkxNzU1MjM3NDE3ODMzNjY5Mjk0NTAyNTQ4OTg1Mzk3OTM3ODU3MzcxODI4MzgzNzI3NzYzMDA4OTA0MDc5NzYyNTEzODM4MTAwOTQwNjAxMjQyMDc3Nzg2MDAyMjk2NTI4NjEzNDcyNjIwMTQ1NDg3NjA2NzQwNDUwMTkzMzg5Nzk4OTQ1NzUwNjEwNDAyODI4NzkwMjQ4OTk5MTE2NzQ3NDM5NTQ4NDM3ODkzMjcxMDU5Njg5NTcxMjczMzgwNTg3MjM4NDM4NzEzNTg4MTU3NDc1MTk3ODI0Mzk3MDgzNzUzNDM1MTU4NDQxMjA4MzczNzgyNTc3OTIxNzQwNzEwMjk4NjU0MDQ5MDA0MzA4NDUyOTQ5NjQxNTgwMTgwMjA0MDE4NDc0NTk1NzgwMzA4NzU2MTIyNzMwNjQwMTM2ODgyMTM4NjMwMDgwMTIyODc4NDkyNTU1MDk4MTY1ODE0NzQ0MzU4MTkxNTgxNTI5MzMyMTc4NzMyMzc3Nzc4ODU4MzkzMTI0MTk4NjM4MDAyMDM5NDgwMDY5OTQyNDgxMjM3MzEwOTk5MjA2NjAxNzY3NzM1MDAzNDMxOTg4MDEwOTQ0NDgzNzI3ODMxMzcxMzYxNTY3NDEwNzA0MDg2MDE2NTg5MDE5MTgzMDY1NDczMTMyOTQ3MTUzNDk0OTg0Nzg5NDkxNzI2NDkyMzkzMTAyOTgzMzA1MjI0NTU2MDcwNDAxNzEyMDgwMTM5NTU3MDA0MDk5MzAwMjAwMDM0MjA3MzU4NzU4NzM3OCJdLCBbIlx1YzBkZFx1YjE0NFx1YzZkNFx1Yzc3YyIsICI2Nzk4NjE3MTE2NDE0NzU0OTI2OTU1MjA1ODc4ODgzMTUxNDI3MTc5OTYwMzI1NzAwMTY3NzgwNzM1NDA2ODA5OTE4NTA3MDM3Nzg4OTUzOTY0MzM2NzYzODYyMzA5MTA4NjIwMzI4Njk3NzY4NzY4ODY4Nzc1NTYwMDU2NjE0MDUzODAyMTIxMDY2MjA4MzAwNDMwNzkwOTE5NjIyNjg0NDM0NzI5OTMzMDUyNDg0ODAxNDg4MzEyMTQ0OTM1NDk4MjY0NTQ0Nzg3ODE3MzU3MDQyNDk4NjA2Mjg4MjI1NTIxNDk1MzI0NDY1NzUxNDE0NDE3NDQ4NzA4MTU0MDM5MTAyOTAwNTc2OTY1MjgwNjEzMzcyMTU5NDg0OTg5NzE3MjQ4MDg4MjkwODg0MDA4MTk0NDgxMTMzNzkyODAyNjExOTk0NjY2ODA5ODQ2NTAzMTE2NzU4NDA4Nzg0NDYyOTM0NTY1MDEzODM3NDI0NDQ4NTI0MjUwNDg2ODk2NTgzNDYxNTE1NjYzNDYwNjM1NjUyNjE2MTg4NjA5NTA1NTQ3MDQ4NjQ1NjkzODI2NDAyODE0MjczOTI2NTUwNTQxNTE1NjU0NjIzODA4MTcwNjYwMzg5ODk4MzI3NjcyODE2OTA4NTQ3MDA0Njg0NTM2OTczMzIwNzk0MDQxMzc0NDkxODQyMDYzOTI5MDE0MzkyMjE3MDAwNzM2NjUwMjYyOTc4NTk3Njc4MDI5MTI2OTAwMjA5NDkzNzcwNTg5MjY2Mzg5MjM5NTc1NTA4MDk1MjIyNjYyMTA2MTk1OTkxNjY1MTcyMTExOTE4MzkxMDc3NzQ4OTYxNTEyMTExMDE2MDIxMTEzMDc0MzU5MTc5NjA5ODc3Mjk0NDMzNTI2MzkwOTcyMjc0Mzg3MzQ1ODU4MDg3MiJdLCBbIlx1ZDU1OVx1YjE0NCIsICIzMzc3NTI3Njk5NTQ0NDI0NjA4OTczMDUzNzUwOTAwNzA3NTMyMzc5NTYzNzMyMDI4MzEyNjM3MzYxNzEzNTUyODI3MDEzNzgyOTU1OTQ5Njk0MjExMzc4OTM5Mzk0ODQ5MzI5MTU2MTE3Njk3MjAzMDA3MDUyMzU3OTI4MzQ5NDYwNDM5NzU0NDA3MDQwOTIwNzk5MzY3OTI0Njg5NTc4ODQ1Nzg4MDkzMTE3OTY1NTIxNzcxMTI5MjU5MjM1NDgwNDgyNTI5NTEyMjgzNDA3NjMzNTk0NzIxMDA3NzMyMDQxMjE4MDk5MTM5NDYwMDgyNzQ2MjIzNzE4OTI1MzQwNDczNDM2OTc2NjMyMTU4Mjk3OTEzMTE3MjAxMTQzMzgyMDIyODU0NTU4OTk4MjQ3NjkxODA3OTUyNjk4MzI3NjIwODg5MTMyNDAxMzYyMDU5MTIyODAwNTUxNjg0MTc3Mzk3NzE4NTEyNTQ3MzMzMjgzMDMyNTA2MjA4MDI3MDE4MjkwNjMxMDc4NjYyMTY5NTE4MjQ1ODgwMjk3ODk0MjYxNDM3Njg1MzU2ODM0Mzk3MzQ1NTY4MjM5ODU0NTMzMTc5NTcwNzUwOTAwNDI1Mzg1MDAyNTU3OTc2NDM0ODk2NjcyNzEwNTk1NzY4NzU4MDExNzA2ODMwNDUzMjA1Mjg5MzgwMDQyMzk1NDU0NTY3MDk4NTYzOTQ4NTAyMjE5MDE5OTAyOTE0Njg5NzkwMTk0MzE1NjIxMDA2NTI1NjA5NzAwOTAxNzE0OTY4ODA2NTgwMzIzMTUyMTI1ODI5NDM3OTcxODMyMjg5NTExODU5MTUwMTEzNTY3NTMzOTU1OTY2MDY5MjcxMDAyNDI3NDMyOTAzMjIwOTI4OTEzMTEyNTk2MzE1MDAxNzU4ODk0NjMxMiJdXX0sICJub25jZSI6ICI4ODkxNjExNzA3NTc4MTYyNzY1NDY0MCJ9"
                }
            }
        ]
    },
    "credential_definition_id": "9c74RUPtMwtiSXq8tVDqxp:3:CL:5108:SK대학교",
    "thread_id": "30fd78d6-3fd4-40b6-b3c8-51b7af822ccb",
    "trace": false,
    "updated_at": "2020-09-23 08:49:53.241860Z",
    "state": "offer_sent",
    "auto_remove": false,
    "created_at": "2020-09-23 08:49:53.241860Z",
    "auto_offer": false,
    "schema_id": "9c74RUPtMwtiSXq8tVDqxp:2:대학제증명:1.0"
}
```

<p></p>

* issue_credential State check

  |  | Faber | Alice |
      | --- | --- | --- |
  | issue_credential state | **`offer_sent`** | N/A |
<p></p>


* Next Step
  <br> Push Notification/Webhook등으로 Alice에게 전달.
  <br> Alice의 `/issue-credential/records/{cred_ex_id}/send-request` send request를 waiting.
<p></p>



<br><br>


### STEP 2. Alice : Faber의 Credential offer를 확인. 

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 Holder가 initial app 일 경우 SDK에서 자동으로 처리됨 </p>
</div>

<br><br>

### STEP 3. Alice --> Faber : Faber에게 Credential Request를 요청한다. 

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP3는 Holder가 initial app 일 경우 SDK에서 자동으로 처리되고, 발급자는 Alice로 부터 Webhook Message를 전달 받고 자동 처림 됨 </p>
</div>

<br><br>

### STEP 4. Faber : Webhook을 확인 한다. 

받은 Webhook event의 `topic:issue_credential`, `state:credential_acked` 이면 정상으로 완료된 상태
<br>

Revocation 사용을 위해서는 아래 [Option] 내용을 확인하고 추가 구현 필요함

<br><br>


### [Option] 폐기(Revocation) 처리를 위한 정보 기록 

기한만료, 자격증명상실등의 이유로 증명서(VC)의 폐기할 경우를 대비하여 `topic:issue_credential`, `state:credential_acked` 으로 전달된 event 중 폐기에 필요한 필수 3가지 값을 기록해야 함. 
만약 김증명에게 VC를 발행했다면 아래 예시와 같이 기록 필요 
1. credential_exchange_id
2. rev_reg_id
3. revocation_id

* 발급내역 기록 예시

항목 | 예제 내용
 --- | ---
unique id | 김증명 CI 혹은 ID등 기관에서 구분값
rev_reg_id | 9c74RUPtMwtiSXq8tVDqxp:4:9c74RUPtMwtiSXq8tVDqxp:3:CL:5108:SK대학교:CL_ACCUM:8f8ff208-ad45-49a4-a606-1b523dcd5d5a
revocation_id | 2
credential_exchange_id | 27e5b826-8a48-4d4b-a63c-a405ab81debc

상세 폐기 방법은 Revocation page 참조
<br><br>