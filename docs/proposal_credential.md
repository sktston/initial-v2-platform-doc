Proposal Credential & Issue
================

**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 연결 및 VC발급/검증 예제를 통한 API 설명
<br>
이 예제는 Alice가 본인이 원하는 Credential을 Faber에게 Proposal 하는 내용.
기술적으로는 가능하지만, 일반적인 시나리오는 아님.

issue_credential State


Topic | State | Description
--- | --- | ---
issue_credential | offer_sent | (faber)credential offer 한 상태
issue_credential | offer_received | (alice)offer를 받은 상태
issue_credential | request_sent | (alice) credential을 요청 한 상태
issue_credential | request_received | (faber) Credential 요청을 받은 상태 
issue_credential | credential_issued | (faber) Credential을 issue  
issue_credential | credential_received | (alice) Credential을 받은 상태 
issue_credential | credential_acked | (alice/faber) Credential 수취 완


### STEP 1. Alice --> Faber : Alice가 Faber에게 Credential Proposal 요청한다. 

* Method and Resource

    `POST` `/issue-credential/send-proposal` credential proposal.  

* Parameter

 Name | Description 
 --- | --- 
 body | Schema String 
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
  "comment": "string",
  "trace": false,
  "credential_proposal": {
    "@type": "issue-credential/1.0/credential-preview",
    "attributes": [
      {
        "name": "성명",
        "value": "Alice"
      },
      {
        "name": "과정명",
        "value": "Math"
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
      },
      {
        "name": "학년",
        "value": "2"
      }
    ]
  },
  "cred_def_id": "{{credId}}",
  "connection_id": "{{faber}}"
}
```

<p></p>
 
   * Response body
```json
{
    "initiator": "self",
    "updated_at": "2020-10-07 15:19:26.248489Z",
    "credential_proposal_dict": {
        "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
        "@id": "af5acbcf-b986-462f-b91a-f93c81f16f61",
        "comment": "string",
        "cred_def_id": "BsCP2xiQBjWmvWw1uHZRsP:3:CL:1602080133:SK대학교",
        "credential_proposal": {
            "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
            "attributes": [
                {
                    "name": "성명",
                    "value": "Alice"
                },
                {
                    "name": "과정명",
                    "value": "Math"
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
                },
                {
                    "name": "학년",
                    "value": "2"
                }
            ]
        }
    },
    "state": "proposal_sent",
    "trace": false,
    "auto_issue": false,
    "created_at": "2020-10-07 15:19:26.248489Z",
    "auto_remove": false,
    "credential_exchange_id": "4b9d46cb-7ad7-4643-b2ed-d4bcf6da1450",
    "connection_id": "db161274-e733-4c93-bb96-e105c707a58b",
    "role": "holder",
    "thread_id": "af5acbcf-b986-462f-b91a-f93c81f16f61"
}
```

<p></p>

* issue_credential State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | issue_credential state | N/A | **`proposal_sent`** |
<p></p>


* Next Step
<br> Push Notification/Webhook등으로 Alice에게 전달. 
<br> Alice의 `/issue-credential/records/{cred_ex_id}/send-request` send request를 waiting.
<p></p>

* Ledger Transactions (Indy Node)

    
<br><br>

### STEP 2. Faber : Alice의 Credential Proposal 확인. 

* Method and Resource

    `GET` `/issue-credential/records` cred_ex_id를 모를 경우. 모든 credential exchange records 가져와서 state가 `proposal_received` 항목을 확인.   
    `GET` `/issue-credential/records/{cred_ex_id}` webhook 혹은 push를 통해 cred_ex_id를 알고 있을 경우.

* Parameter

 Name | Description 
 --- | --- 
 cred_ex_id | Faber의 Credential offer로 부터 받은 `credential_exchange_id`
 
<p></p>

* Example

    * input <br>
    `credential_exchange_id`: "4ac81ce3-c30a-44a7-8258-258fcacc76a5"

<p></p>
 
   * Response body
```json

```

<p></p>

* issue_credential State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | issue_credential state | **`proposal_received`** | **`proposal_sent`** |
<p></p>

* Next Step
<br> `/issue-credential/records/{cred_ex_id}/send-request` Faber에게 send request
<p></p>

* Ledger Transactions (Indy Node)

    
<br><br>


### STEP 3. Faber --> Alice : Faber send-offer 요청한다. 

* Method and Resource

    `POST` `/issue-credential/records/{cred_ex_id}/send-offer` send offer.  

* Parameter

 Name | Description 
 --- | --- 
 cred_ex_id | Alice Credential proposal 받은 `credential_exchange_id`
 
<p></p>

* Example

    * input <br>
    `credential_exchange_id`: "4ac81ce3-c30a-44a7-8258-258fcacc76a5"

<p></p>
 
   * Response body
```json
{
    "initiator": "external",
    "credential_offer": {
        "schema_id": "BsCP2xiQBjWmvWw1uHZRsP:2:대학제증명:1.0",
        "cred_def_id": "BsCP2xiQBjWmvWw1uHZRsP:3:CL:1602080133:SK대학교",
        "key_correctness_proof": {
            "c": "111899264163174730335361668755845578486002410750015300466299027729272119865342",
            "xz_cap": "2753706579565520305464947050431195309836302905734939315972846525761803784242882655336275334727101169919712993593111152486458501919490956338603682575178188303516841007590221540108902878629036163568621089016143019221176884080792051072881784750948316352759740217425467133218930037400190931032139316397563293220038566936762019004404133019658010578929035719928254093037386228747561008413615650712564500025383798851156979709831718774792792943678281231411396223065357722600591532179732400774349545912492159155632667941590712787231928472943314960921320049279926763580375948133087743153903318981538389326828580430355873541103018379456415426006703483095817752969374300171947813603518218843523785790652191",
            "xr_cap": [
                [
                    "입학년월일",
                    "1324732075013763824986843220381482786535839694267789311072917225774785785104728884042695130548560965420879231748867678255744833048065638098199187667460875290523749271999618738903732879424915462006381054427324051613187001932394713187644293692376713831453125419664363054887535934856548528265659585822840823464692236457915091986933398209737467512264484968755611192751449259758513050898933344004406356541413043663176636373691733423174018527513253679350755226345476986734239536005273130240837263571728186113148100639200983171367488184581638712024591213536307362082346785899119937963918983425349984272698303842259076267342257408133917102448881644338161409503146206493139157736216695421343799738027205"
                ],
                [
                    "생년월일",
                    "1145220988110716164239851930446503788997850637246469584453769635452719906052325397459711047455749656537126780062211232216027996427636848909156641856427801718306788235969309022816532087888750669832336329894836270358113790300441639817563442832867685044198918169025780312235323804825967458764809181691491614209126512576129420026300371808190139736955598089582998578942589490608688159608847821110017640559450202910572449620792149783982764832670972736513905821802571137543229265116475769539154542693894034240080238493139274503638436879369582254273277080458055707934122328152604242022703042648196981421897563182100828958235738296761733296680315634513583265708618912528260977618734803721527836582406803"
                ],
                [
                    "과정명",
                    "1904325954565415670482706787172176701622719733722172632420733912134495440198223561849939446015205755605320697483577651992689585913397667469326035550106314773026306587439346633176214196550612292010440830008809650094627958833104706951645958511200608136531326617196792287221020530664075350980078110201171930152418750426783243329842672674965366663108756714580394562535838603874006066641405836221774003301872849243242936622474152462457901234789929524936252113999819190419730737832467377062602206892698339796242519795236890229400689818971188851942176286413328886774013783110217113278398839891834950559195291812631036997839867493734492506317029936993375726296422381555917831007058978741019273157078548"
                ],
                [
                    "학교이름",
                    "1145868183205098847504829575599047155079768013561324132275923501921911083631225921771719061105234077814982439133349835251523649536823673896815878674061414632251430690694666556803968678340191942864273663128902140358861858998800738729875993425541193196774215864181493957916412193504880048011829054807455934700214204424529030937342829621556909996720022116345091765380766765074085222929382935231350114243973344291978290928494191142246163548345152425798161430362018085427907432854118717182020636905970493682275529045682395687661485690460112469625830230858605629072798631911695137460617282729998167919738459086723072432730616301138839200978077623431583357621181375897259656979169165963618662749264997"
                ],
                [
                    "학년",
                    "1263910623992846605197101632093807776338576939473417890011461511345920016457989966284363487070319706833194086516654228597668869168267688957438021906328680316018386015069562974200400930507174613632453667321543254209857655864413059092559466148452626614012996788562839422533844389465274184016075751713746107535053600326332391517871163380346852186225034534015047172182357947526417133539464138906429760006414121101903157115914097119070031290848203340426359804921902052317447955826768206205193697386535239219596566782068292082632893653356382958810789084288950527305693529532943704379001453913074513503702375073340958693811896920439290711921817333339499705166041594801868097532888924352998857124600789"
                ],
                [
                    "master_secret",
                    "2840190250432821038374223652041228871735407241025640817865611077181105549104255540924385329044208153280710613570315804969734560771097384047277946571455995029393749051386810705028416913402399539141622744564468128743286240837349055974133213592391865420135409067847548086656108014678641214625174964968763314257759875533542642719938427779918526397524848025462185365521430262518801058588836519417110743250083406716474723234693324385685367949799446850821853855801937551621933503131604390743679948671312185613474988642051373413271280726831026987102833935709081514070268727317410313419826397119935680150090405717719227552587949398083674812569100022686255919165650406275167083864129671693546687435316393"
                ],
                [
                    "성명",
                    "804911145001792285182011204644684847607575157940014425297984239427034272962466654486998389698582591638039899482844550249591315636867726565401831212638935925123320383840071996195026163788596801012248298057659253705251535629494240244487907958856795769227953008989353561301794890217639529966971898819594980354454403672093766566108864755737091858037870357909246455620131781361095785057363855334533827315440779306930687601731053285619575272111218346372100238104020844898999091825965643114780991224455824616744880473978038546756437406900180270890427311092351217195773018999463354111823488468870530459168305987294585420929550855017136361747227448830467293928569135866653027180269464742687892507807274"
                ]
            ]
        },
        "nonce": "89203415173086455134354"
    },
    "updated_at": "2020-10-07 15:22:39.008804Z",
    "credential_proposal_dict": {
        "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
        "@id": "af5acbcf-b986-462f-b91a-f93c81f16f61",
        "comment": "string",
        "cred_def_id": "BsCP2xiQBjWmvWw1uHZRsP:3:CL:1602080133:SK대학교",
        "credential_proposal": {
            "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
            "attributes": [
                {
                    "name": "성명",
                    "value": "Alice"
                },
                {
                    "name": "과정명",
                    "value": "Math"
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
                },
                {
                    "name": "학년",
                    "value": "2"
                }
            ]
        }
    },
    "state": "offer_sent",
    "trace": false,
    "credential_offer_dict": {
        "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/offer-credential",
        "@id": "5de900c8-852a-4ccd-bbc4-9d1f8f631578",
        "~thread": {
            "thid": "af5acbcf-b986-462f-b91a-f93c81f16f61"
        },
        "credential_preview": {
            "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
            "attributes": [
                {
                    "name": "성명",
                    "value": "Alice"
                },
                {
                    "name": "과정명",
                    "value": "Math"
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
                },
                {
                    "name": "학년",
                    "value": "2"
                }
            ]
        },
        "offers~attach": [
            {
                "@id": "libindy-cred-offer-0",
                "mime-type": "application/json",
                "data": {
                    "base64": "eyJzY2hlbWFfaWQiOiAiQnNDUDJ4aVFCaldtdld3MXVIWlJzUDoyOlx1YjMwMFx1ZDU1OVx1YzgxY1x1Yzk5ZFx1YmE4NToxLjAiLCAiY3JlZF9kZWZfaWQiOiAiQnNDUDJ4aVFCaldtdld3MXVIWlJzUDozOkNMOjE2MDIwODAxMzM6U0tcdWIzMDBcdWQ1NTlcdWFkNTAiLCAia2V5X2NvcnJlY3RuZXNzX3Byb29mIjogeyJjIjogIjExMTg5OTI2NDE2MzE3NDczMDMzNTM2MTY2ODc1NTg0NTU3ODQ4NjAwMjQxMDc1MDAxNTMwMDQ2NjI5OTAyNzcyOTI3MjExOTg2NTM0MiIsICJ4el9jYXAiOiAiMjc1MzcwNjU3OTU2NTUyMDMwNTQ2NDk0NzA1MDQzMTE5NTMwOTgzNjMwMjkwNTczNDkzOTMxNTk3Mjg0NjUyNTc2MTgwMzc4NDI0Mjg4MjY1NTMzNjI3NTMzNDcyNzEwMTE2OTkxOTcxMjk5MzU5MzExMTE1MjQ4NjQ1ODUwMTkxOTQ5MDk1NjMzODYwMzY4MjU3NTE3ODE4ODMwMzUxNjg0MTAwNzU5MDIyMTU0MDEwODkwMjg3ODYyOTAzNjE2MzU2ODYyMTA4OTAxNjE0MzAxOTIyMTE3Njg4NDA4MDc5MjA1MTA3Mjg4MTc4NDc1MDk0ODMxNjM1Mjc1OTc0MDIxNzQyNTQ2NzEzMzIxODkzMDAzNzQwMDE5MDkzMTAzMjEzOTMxNjM5NzU2MzI5MzIyMDAzODU2NjkzNjc2MjAxOTAwNDQwNDEzMzAxOTY1ODAxMDU3ODkyOTAzNTcxOTkyODI1NDA5MzAzNzM4NjIyODc0NzU2MTAwODQxMzYxNTY1MDcxMjU2NDUwMDAyNTM4Mzc5ODg1MTE1Njk3OTcwOTgzMTcxODc3NDc5Mjc5Mjk0MzY3ODI4MTIzMTQxMTM5NjIyMzA2NTM1NzcyMjYwMDU5MTUzMjE3OTczMjQwMDc3NDM0OTU0NTkxMjQ5MjE1OTE1NTYzMjY2Nzk0MTU5MDcxMjc4NzIzMTkyODQ3Mjk0MzMxNDk2MDkyMTMyMDA0OTI3OTkyNjc2MzU4MDM3NTk0ODEzMzA4Nzc0MzE1MzkwMzMxODk4MTUzODM4OTMyNjgyODU4MDQzMDM1NTg3MzU0MTEwMzAxODM3OTQ1NjQxNTQyNjAwNjcwMzQ4MzA5NTgxNzc1Mjk2OTM3NDMwMDE3MTk0NzgxMzYwMzUxODIxODg0MzUyMzc4NTc5MDY1MjE5MSIsICJ4cl9jYXAiOiBbWyJcdWM3ODVcdWQ1NTlcdWIxNDRcdWM2ZDRcdWM3N2MiLCAiMTMyNDczMjA3NTAxMzc2MzgyNDk4Njg0MzIyMDM4MTQ4Mjc4NjUzNTgzOTY5NDI2Nzc4OTMxMTA3MjkxNzIyNTc3NDc4NTc4NTEwNDcyODg4NDA0MjY5NTEzMDU0ODU2MDk2NTQyMDg3OTIzMTc0ODg2NzY3ODI1NTc0NDgzMzA0ODA2NTYzODA5ODE5OTE4NzY2NzQ2MDg3NTI5MDUyMzc0OTI3MTk5OTYxODczODkwMzczMjg3OTQyNDkxNTQ2MjAwNjM4MTA1NDQyNzMyNDA1MTYxMzE4NzAwMTkzMjM5NDcxMzE4NzY0NDI5MzY5MjM3NjcxMzgzMTQ1MzEyNTQxOTY2NDM2MzA1NDg4NzUzNTkzNDg1NjU0ODUyODI2NTY1OTU4NTgyMjg0MDgyMzQ2NDY5MjIzNjQ1NzkxNTA5MTk4NjkzMzM5ODIwOTczNzQ2NzUxMjI2NDQ4NDk2ODc1NTYxMTE5Mjc1MTQ0OTI1OTc1ODUxMzA1MDg5ODkzMzM0NDAwNDQwNjM1NjU0MTQxMzA0MzY2MzE3NjYzNjM3MzY5MTczMzQyMzE3NDAxODUyNzUxMzI1MzY3OTM1MDc1NTIyNjM0NTQ3Njk4NjczNDIzOTUzNjAwNTI3MzEzMDI0MDgzNzI2MzU3MTcyODE4NjExMzE0ODEwMDYzOTIwMDk4MzE3MTM2NzQ4ODE4NDU4MTYzODcxMjAyNDU5MTIxMzUzNjMwNzM2MjA4MjM0Njc4NTg5OTExOTkzNzk2MzkxODk4MzQyNTM0OTk4NDI3MjY5ODMwMzg0MjI1OTA3NjI2NzM0MjI1NzQwODEzMzkxNzEwMjQ0ODg4MTY0NDMzODE2MTQwOTUwMzE0NjIwNjQ5MzEzOTE1NzczNjIxNjY5NTQyMTM0Mzc5OTczODAyNzIwNSJdLCBbIlx1YzBkZFx1YjE0NFx1YzZkNFx1Yzc3YyIsICIxMTQ1MjIwOTg4MTEwNzE2MTY0MjM5ODUxOTMwNDQ2NTAzNzg4OTk3ODUwNjM3MjQ2NDY5NTg0NDUzNzY5NjM1NDUyNzE5OTA2MDUyMzI1Mzk3NDU5NzExMDQ3NDU1NzQ5NjU2NTM3MTI2NzgwMDYyMjExMjMyMjE2MDI3OTk2NDI3NjM2ODQ4OTA5MTU2NjQxODU2NDI3ODAxNzE4MzA2Nzg4MjM1OTY5MzA5MDIyODE2NTMyMDg3ODg4NzUwNjY5ODMyMzM2MzI5ODk0ODM2MjcwMzU4MTEzNzkwMzAwNDQxNjM5ODE3NTYzNDQyODMyODY3Njg1MDQ0MTk4OTE4MTY5MDI1NzgwMzEyMjM1MzIzODA0ODI1OTY3NDU4NzY0ODA5MTgxNjkxNDkxNjE0MjA5MTI2NTEyNTc2MTI5NDIwMDI2MzAwMzcxODA4MTkwMTM5NzM2OTU1NTk4MDg5NTgyOTk4NTc4OTQyNTg5NDkwNjA4Njg4MTU5NjA4ODQ3ODIxMTEwMDE3NjQwNTU5NDUwMjAyOTEwNTcyNDQ5NjIwNzkyMTQ5NzgzOTgyNzY0ODMyNjcwOTcyNzM2NTEzOTA1ODIxODAyNTcxMTM3NTQzMjI5MjY1MTE2NDc1NzY5NTM5MTU0NTQyNjkzODk0MDM0MjQwMDgwMjM4NDkzMTM5Mjc0NTAzNjM4NDM2ODc5MzY5NTgyMjU0MjczMjc3MDgwNDU4MDU1NzA3OTM0MTIyMzI4MTUyNjA0MjQyMDIyNzAzMDQyNjQ4MTk2OTgxNDIxODk3NTYzMTgyMTAwODI4OTU4MjM1NzM4Mjk2NzYxNzMzMjk2NjgwMzE1NjM0NTEzNTgzMjY1NzA4NjE4OTEyNTI4MjYwOTc3NjE4NzM0ODAzNzIxNTI3ODM2NTgyNDA2ODAzIl0sIFsiXHVhY2ZjXHVjODE1XHViYTg1IiwgIjE5MDQzMjU5NTQ1NjU0MTU2NzA0ODI3MDY3ODcxNzIxNzY3MDE2MjI3MTk3MzM3MjIxNzI2MzI0MjA3MzM5MTIxMzQ0OTU0NDAxOTgyMjM1NjE4NDk5Mzk0NDYwMTUyMDU3NTU2MDUzMjA2OTc0ODM1Nzc2NTE5OTI2ODk1ODU5MTMzOTc2Njc0NjkzMjYwMzU1NTAxMDYzMTQ3NzMwMjYzMDY1ODc0MzkzNDY2MzMxNzYyMTQxOTY1NTA2MTIyOTIwMTA0NDA4MzAwMDg4MDk2NTAwOTQ2Mjc5NTg4MzMxMDQ3MDY5NTE2NDU5NTg1MTEyMDA2MDgxMzY1MzEzMjY2MTcxOTY3OTIyODcyMjEwMjA1MzA2NjQwNzUzNTA5ODAwNzgxMTAyMDExNzE5MzAxNTI0MTg3NTA0MjY3ODMyNDMzMjk4NDI2NzI2NzQ5NjUzNjY2NjMxMDg3NTY3MTQ1ODAzOTQ1NjI1MzU4Mzg2MDM4NzQwMDYwNjY2NDE0MDU4MzYyMjE3NzQwMDMzMDE4NzI4NDkyNDMyNDI5MzY2MjI0NzQxNTI0NjI0NTc5MDEyMzQ3ODk5Mjk1MjQ5MzYyNTIxMTM5OTk4MTkxOTA0MTk3MzA3Mzc4MzI0NjczNzcwNjI2MDIyMDY4OTI2OTgzMzk3OTYyNDI1MTk3OTUyMzY4OTAyMjk0MDA2ODk4MTg5NzExODg4NTE5NDIxNzYyODY0MTMzMjg4ODY3NzQwMTM3ODMxMTAyMTcxMTMyNzgzOTg4Mzk4OTE4MzQ5NTA1NTkxOTUyOTE4MTI2MzEwMzY5OTc4Mzk4Njc0OTM3MzQ0OTI1MDYzMTcwMjk5MzY5OTMzNzU3MjYyOTY0MjIzODE1NTU5MTc4MzEwMDcwNTg5Nzg3NDEwMTkyNzMxNTcwNzg1NDgiXSwgWyJcdWQ1NTlcdWFkNTBcdWM3NzRcdWI5ODQiLCAiMTE0NTg2ODE4MzIwNTA5ODg0NzUwNDgyOTU3NTU5OTA0NzE1NTA3OTc2ODAxMzU2MTMyNDEzMjI3NTkyMzUwMTkyMTkxMTA4MzYzMTIyNTkyMTc3MTcxOTA2MTEwNTIzNDA3NzgxNDk4MjQzOTEzMzM0OTgzNTI1MTUyMzY0OTUzNjgyMzY3Mzg5NjgxNTg3ODY3NDA2MTQxNDYzMjI1MTQzMDY5MDY5NDY2NjU1NjgwMzk2ODY3ODM0MDE5MTk0Mjg2NDI3MzY2MzEyODkwMjE0MDM1ODg2MTg1ODk5ODgwMDczODcyOTg3NTk5MzQyNTU0MTE5MzE5Njc3NDIxNTg2NDE4MTQ5Mzk1NzkxNjQxMjE5MzUwNDg4MDA0ODAxMTgyOTA1NDgwNzQ1NTkzNDcwMDIxNDIwNDQyNDUyOTAzMDkzNzM0MjgyOTYyMTU1NjkwOTk5NjcyMDAyMjExNjM0NTA5MTc2NTM4MDc2Njc2NTA3NDA4NTIyMjkyOTM4MjkzNTIzMTM1MDExNDI0Mzk3MzM0NDI5MTk3ODI5MDkyODQ5NDE5MTE0MjI0NjE2MzU0ODM0NTE1MjQyNTc5ODE2MTQzMDM2MjAxODA4NTQyNzkwNzQzMjg1NDExODcxNzE4MjAyMDYzNjkwNTk3MDQ5MzY4MjI3NTUyOTA0NTY4MjM5NTY4NzY2MTQ4NTY5MDQ2MDExMjQ2OTYyNTgzMDIzMDg1ODYwNTYyOTA3Mjc5ODYzMTkxMTY5NTEzNzQ2MDYxNzI4MjcyOTk5ODE2NzkxOTczODQ1OTA4NjcyMzA3MjQzMjczMDYxNjMwMTEzODgzOTIwMDk3ODA3NzYyMzQzMTU4MzM1NzYyMTE4MTM3NTg5NzI1OTY1Njk3OTE2OTE2NTk2MzYxODY2Mjc0OTI2NDk5NyJdLCBbIlx1ZDU1OVx1YjE0NCIsICIxMjYzOTEwNjIzOTkyODQ2NjA1MTk3MTAxNjMyMDkzODA3Nzc2MzM4NTc2OTM5NDczNDE3ODkwMDExNDYxNTExMzQ1OTIwMDE2NDU3OTg5OTY2Mjg0MzYzNDg3MDcwMzE5NzA2ODMzMTk0MDg2NTE2NjU0MjI4NTk3NjY4ODY5MTY4MjY3Njg4OTU3NDM4MDIxOTA2MzI4NjgwMzE2MDE4Mzg2MDE1MDY5NTYyOTc0MjAwNDAwOTMwNTA3MTc0NjEzNjMyNDUzNjY3MzIxNTQzMjU0MjA5ODU3NjU1ODY0NDEzMDU5MDkyNTU5NDY2MTQ4NDUyNjI2NjE0MDEyOTk2Nzg4NTYyODM5NDIyNTMzODQ0Mzg5NDY1Mjc0MTg0MDE2MDc1NzUxNzEzNzQ2MTA3NTM1MDUzNjAwMzI2MzMyMzkxNTE3ODcxMTYzMzgwMzQ2ODUyMTg2MjI1MDM0NTM0MDE1MDQ3MTcyMTgyMzU3OTQ3NTI2NDE3MTMzNTM5NDY0MTM4OTA2NDI5NzYwMDA2NDE0MTIxMTAxOTAzMTU3MTE1OTE0MDk3MTE5MDcwMDMxMjkwODQ4MjAzMzQwNDI2MzU5ODA0OTIxOTAyMDUyMzE3NDQ3OTU1ODI2NzY4MjA2MjA1MTkzNjk3Mzg2NTM1MjM5MjE5NTk2NTY2NzgyMDY4MjkyMDgyNjMyODkzNjUzMzU2MzgyOTU4ODEwNzg5MDg0Mjg4OTUwNTI3MzA1NjkzNTI5NTMyOTQzNzA0Mzc5MDAxNDUzOTEzMDc0NTEzNTAzNzAyMzc1MDczMzQwOTU4NjkzODExODk2OTIwNDM5MjkwNzExOTIxODE3MzMzMzM5NDk5NzA1MTY2MDQxNTk0ODAxODY4MDk3NTMyODg4OTI0MzUyOTk4ODU3MTI0NjAwNzg5Il0sIFsibWFzdGVyX3NlY3JldCIsICIyODQwMTkwMjUwNDMyODIxMDM4Mzc0MjIzNjUyMDQxMjI4ODcxNzM1NDA3MjQxMDI1NjQwODE3ODY1NjExMDc3MTgxMTA1NTQ5MTA0MjU1NTQwOTI0Mzg1MzI5MDQ0MjA4MTUzMjgwNzEwNjEzNTcwMzE1ODA0OTY5NzM0NTYwNzcxMDk3Mzg0MDQ3Mjc3OTQ2NTcxNDU1OTk1MDI5MzkzNzQ5MDUxMzg2ODEwNzA1MDI4NDE2OTEzNDAyMzk5NTM5MTQxNjIyNzQ0NTY0NDY4MTI4NzQzMjg2MjQwODM3MzQ5MDU1OTc0MTMzMjEzNTkyMzkxODY1NDIwMTM1NDA5MDY3ODQ3NTQ4MDg2NjU2MTA4MDE0Njc4NjQxMjE0NjI1MTc0OTY0OTY4NzYzMzE0MjU3NzU5ODc1NTMzNTQyNjQyNzE5OTM4NDI3Nzc5OTE4NTI2Mzk3NTI0ODQ4MDI1NDYyMTg1MzY1NTIxNDMwMjYyNTE4ODAxMDU4NTg4ODM2NTE5NDE3MTEwNzQzMjUwMDgzNDA2NzE2NDc0NzIzMjM0NjkzMzI0Mzg1Njg1MzY3OTQ5Nzk5NDQ2ODUwODIxODUzODU1ODAxOTM3NTUxNjIxOTMzNTAzMTMxNjA0MzkwNzQzNjc5OTQ4NjcxMzEyMTg1NjEzNDc0OTg4NjQyMDUxMzczNDEzMjcxMjgwNzI2ODMxMDI2OTg3MTAyODMzOTM1NzA5MDgxNTE0MDcwMjY4NzI3MzE3NDEwMzEzNDE5ODI2Mzk3MTE5OTM1NjgwMTUwMDkwNDA1NzE3NzE5MjI3NTUyNTg3OTQ5Mzk4MDgzNjc0ODEyNTY5MTAwMDIyNjg2MjU1OTE5MTY1NjUwNDA2Mjc1MTY3MDgzODY0MTI5NjcxNjkzNTQ2Njg3NDM1MzE2MzkzIl0sIFsiXHVjMTMxXHViYTg1IiwgIjgwNDkxMTE0NTAwMTc5MjI4NTE4MjAxMTIwNDY0NDY4NDg0NzYwNzU3NTE1Nzk0MDAxNDQyNTI5Nzk4NDIzOTQyNzAzNDI3Mjk2MjQ2NjY1NDQ4Njk5ODM4OTY5ODU4MjU5MTYzODAzOTg5OTQ4Mjg0NDU1MDI0OTU5MTMxNTYzNjg2NzcyNjU2NTQwMTgzMTIxMjYzODkzNTkyNTEyMzMyMDM4Mzg0MDA3MTk5NjE5NTAyNjE2Mzc4ODU5NjgwMTAxMjI0ODI5ODA1NzY1OTI1MzcwNTI1MTUzNTYyOTQ5NDI0MDI0NDQ4NzkwNzk1ODg1Njc5NTc2OTIyNzk1MzAwODk4OTM1MzU2MTMwMTc5NDg5MDIxNzYzOTUyOTk2Njk3MTg5ODgxOTU5NDk4MDM1NDQ1NDQwMzY3MjA5Mzc2NjU2NjEwODg2NDc1NTczNzA5MTg1ODAzNzg3MDM1NzkwOTI0NjQ1NTYyMDEzMTc4MTM2MTA5NTc4NTA1NzM2Mzg1NTMzNDUzMzgyNzMxNTQ0MDc3OTMwNjkzMDY4NzYwMTczMTA1MzI4NTYxOTU3NTI3MjExMTIxODM0NjM3MjEwMDIzODEwNDAyMDg0NDg5ODk5OTA5MTgyNTk2NTY0MzExNDc4MDk5MTIyNDQ1NTgyNDYxNjc0NDg4MDQ3Mzk3ODAzODU0Njc1NjQzNzQwNjkwMDE4MDI3MDg5MDQyNzMxMTA5MjM1MTIxNzE5NTc3MzAxODk5OTQ2MzM1NDExMTgyMzQ4ODQ2ODg3MDUzMDQ1OTE2ODMwNTk4NzI5NDU4NTQyMDkyOTU1MDg1NTAxNzEzNjM2MTc0NzIyNzQ0ODgzMDQ2NzI5MzkyODU2OTEzNTg2NjY1MzAyNzE4MDI2OTQ2NDc0MjY4Nzg5MjUwNzgwNzI3NCJdXX0sICJub25jZSI6ICI4OTIwMzQxNTE3MzA4NjQ1NTEzNDM1NCJ9"
                }
            }
        ]
    },
    "credential_definition_id": "BsCP2xiQBjWmvWw1uHZRsP:3:CL:1602080133:SK대학교",
    "auto_issue": true,
    "created_at": "2020-10-07 15:19:26.317017Z",
    "auto_remove": true,
    "credential_exchange_id": "4ac81ce3-c30a-44a7-8258-258fcacc76a5",
    "schema_id": "BsCP2xiQBjWmvWw1uHZRsP:2:대학제증명:1.0",
    "connection_id": "93e673ae-1fc6-4c78-a068-d5f104456749",
    "role": "issuer",
    "thread_id": "af5acbcf-b986-462f-b91a-f93c81f16f61"
}
```

<p></p>

* issue_credential State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | issue_credential state | **`offer_sent`**  | **`offer_received`** |
<p></p>

* Next Step
<p></p>

* Ledger Transactions (Indy Node)

    
<br><br>

### STEP 4. Alice --> Faber : Faber에게 Credential Send Request. 

* Method and Resource

    `POST` `/issue-credential/records/{cred_ex_id}/send-request` send request.  

* Parameter

 Name | Description 
 --- | --- 
 body | String (Human-readable comment)
 cred_ex_id | Faber의 Credential offer로 부터 받은 `credential_exchange_id`
 
<p></p>

* Example

    * input <br>
    `credential_exchange_id`: "4b9d46cb-7ad7-4643-b2ed-d4bcf6da1450"

<p></p>
 
   * Response body
```json
{
    "initiator": "self",
    "credential_offer": {
        "schema_id": "BsCP2xiQBjWmvWw1uHZRsP:2:대학제증명:1.0",
        "cred_def_id": "BsCP2xiQBjWmvWw1uHZRsP:3:CL:1602080133:SK대학교",
        "key_correctness_proof": {
            "c": "111899264163174730335361668755845578486002410750015300466299027729272119865342",
            "xz_cap": "2753706579565520305464947050431195309836302905734939315972846525761803784242882655336275334727101169919712993593111152486458501919490956338603682575178188303516841007590221540108902878629036163568621089016143019221176884080792051072881784750948316352759740217425467133218930037400190931032139316397563293220038566936762019004404133019658010578929035719928254093037386228747561008413615650712564500025383798851156979709831718774792792943678281231411396223065357722600591532179732400774349545912492159155632667941590712787231928472943314960921320049279926763580375948133087743153903318981538389326828580430355873541103018379456415426006703483095817752969374300171947813603518218843523785790652191",
            "xr_cap": [
                [
                    "입학년월일",
                    "1324732075013763824986843220381482786535839694267789311072917225774785785104728884042695130548560965420879231748867678255744833048065638098199187667460875290523749271999618738903732879424915462006381054427324051613187001932394713187644293692376713831453125419664363054887535934856548528265659585822840823464692236457915091986933398209737467512264484968755611192751449259758513050898933344004406356541413043663176636373691733423174018527513253679350755226345476986734239536005273130240837263571728186113148100639200983171367488184581638712024591213536307362082346785899119937963918983425349984272698303842259076267342257408133917102448881644338161409503146206493139157736216695421343799738027205"
                ],
                [
                    "생년월일",
                    "1145220988110716164239851930446503788997850637246469584453769635452719906052325397459711047455749656537126780062211232216027996427636848909156641856427801718306788235969309022816532087888750669832336329894836270358113790300441639817563442832867685044198918169025780312235323804825967458764809181691491614209126512576129420026300371808190139736955598089582998578942589490608688159608847821110017640559450202910572449620792149783982764832670972736513905821802571137543229265116475769539154542693894034240080238493139274503638436879369582254273277080458055707934122328152604242022703042648196981421897563182100828958235738296761733296680315634513583265708618912528260977618734803721527836582406803"
                ],
                [
                    "과정명",
                    "1904325954565415670482706787172176701622719733722172632420733912134495440198223561849939446015205755605320697483577651992689585913397667469326035550106314773026306587439346633176214196550612292010440830008809650094627958833104706951645958511200608136531326617196792287221020530664075350980078110201171930152418750426783243329842672674965366663108756714580394562535838603874006066641405836221774003301872849243242936622474152462457901234789929524936252113999819190419730737832467377062602206892698339796242519795236890229400689818971188851942176286413328886774013783110217113278398839891834950559195291812631036997839867493734492506317029936993375726296422381555917831007058978741019273157078548"
                ],
                [
                    "학교이름",
                    "1145868183205098847504829575599047155079768013561324132275923501921911083631225921771719061105234077814982439133349835251523649536823673896815878674061414632251430690694666556803968678340191942864273663128902140358861858998800738729875993425541193196774215864181493957916412193504880048011829054807455934700214204424529030937342829621556909996720022116345091765380766765074085222929382935231350114243973344291978290928494191142246163548345152425798161430362018085427907432854118717182020636905970493682275529045682395687661485690460112469625830230858605629072798631911695137460617282729998167919738459086723072432730616301138839200978077623431583357621181375897259656979169165963618662749264997"
                ],
                [
                    "학년",
                    "1263910623992846605197101632093807776338576939473417890011461511345920016457989966284363487070319706833194086516654228597668869168267688957438021906328680316018386015069562974200400930507174613632453667321543254209857655864413059092559466148452626614012996788562839422533844389465274184016075751713746107535053600326332391517871163380346852186225034534015047172182357947526417133539464138906429760006414121101903157115914097119070031290848203340426359804921902052317447955826768206205193697386535239219596566782068292082632893653356382958810789084288950527305693529532943704379001453913074513503702375073340958693811896920439290711921817333339499705166041594801868097532888924352998857124600789"
                ],
                [
                    "master_secret",
                    "2840190250432821038374223652041228871735407241025640817865611077181105549104255540924385329044208153280710613570315804969734560771097384047277946571455995029393749051386810705028416913402399539141622744564468128743286240837349055974133213592391865420135409067847548086656108014678641214625174964968763314257759875533542642719938427779918526397524848025462185365521430262518801058588836519417110743250083406716474723234693324385685367949799446850821853855801937551621933503131604390743679948671312185613474988642051373413271280726831026987102833935709081514070268727317410313419826397119935680150090405717719227552587949398083674812569100022686255919165650406275167083864129671693546687435316393"
                ],
                [
                    "성명",
                    "804911145001792285182011204644684847607575157940014425297984239427034272962466654486998389698582591638039899482844550249591315636867726565401831212638935925123320383840071996195026163788596801012248298057659253705251535629494240244487907958856795769227953008989353561301794890217639529966971898819594980354454403672093766566108864755737091858037870357909246455620131781361095785057363855334533827315440779306930687601731053285619575272111218346372100238104020844898999091825965643114780991224455824616744880473978038546756437406900180270890427311092351217195773018999463354111823488468870530459168305987294585420929550855017136361747227448830467293928569135866653027180269464742687892507807274"
                ]
            ]
        },
        "nonce": "89203415173086455134354"
    },
    "updated_at": "2020-10-07 15:30:45.751517Z",
    "credential_proposal_dict": {
        "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential",
        "@id": "d98b6c32-1dc3-47e9-991f-b291864f04c5",
        "schema_id": "BsCP2xiQBjWmvWw1uHZRsP:2:대학제증명:1.0",
        "cred_def_id": "BsCP2xiQBjWmvWw1uHZRsP:3:CL:1602080133:SK대학교",
        "credential_proposal": {
            "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/credential-preview",
            "attributes": [
                {
                    "name": "성명",
                    "value": "Alice"
                },
                {
                    "name": "과정명",
                    "value": "Math"
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
                },
                {
                    "name": "학년",
                    "value": "2"
                }
            ]
        }
    },
    "credential_request_metadata": {
        "master_secret_blinding_data": {
            "v_prime": "12104592706800064758642536463862257185027404755376303788821121852820958321147522725662295272805750999884490196671236618350138769076762178218630538833650891064009940443778766496998946457040032561736242599726934901164370732179811130074091516590547275485805864779438622773472576619155960582460113680872478055405439123289814108781664007080582400201074908367578478643319578478225439763002214493258746832710343609073736576791664716822266224443594887789363979187827141499015032300351828190295846809433712607246875647510601321563121498900970459151260446286948203177361624740949300976601260974053973796274816627510319449512009387124957265391172180603",
            "vr_prime": "0018D2342B9C9379D098C1F47C1395498B9F86929BF01489868F876F19E14DAE"
        },
        "nonce": "681984843736653788293666",
        "master_secret_name": "jaehyeon.ju2@sk.com"
    },
    "state": "request_sent",
    "trace": false,
    "credential_definition_id": "BsCP2xiQBjWmvWw1uHZRsP:3:CL:1602080133:SK대학교",
    "credential_request": {
        "prover_did": "174U87fvsXyX9DQRtGdm2b",
        "cred_def_id": "BsCP2xiQBjWmvWw1uHZRsP:3:CL:1602080133:SK대학교",
        "blinded_ms": {
            "u": "32457883939273661726815935739589086819887478785343403974194537345161644976985262727659358689985294228726296763042318447743701338055133931927492425529637200394981765752700479989288575811128580599295851197266492052882019275607802433540633209763870334608536220797667928806156553221741573237101698182701325638178937863728089099381507105620728493094086698965608949308761269553722311784686983581250725730783993646191705690470067884229503367687477207128227799175719604235994394265998192015502528481771770066456640644842455681084016997822742346174250076465749736516840253732703023280022710475221763358101075001430010314646271",
            "ur": "1 145B7158CFC595924898230DDBB39522A5027702D1B5F82F27ABC1ECBBD399F0 1 14CBE71B3D433011856458F4B8D0674CFC14B93BFFA4DC2BE3A96FBAC4D606E6 2 095E45DDF417D05FB10933FFC63D474548B7FFFF7888802F07FFFFFF7D07A8A8",
            "hidden_attributes": [
                "master_secret"
            ],
            "committed_attributes": {}
        },
        "blinded_ms_correctness_proof": {
            "c": "70120970716539692440119525274304210172821335553467705844262059093021349512797",
            "v_dash_cap": "848785790729167272182262843599818349563700054572392611768386819036558277105252561638160427185037852129729105459333265672352290222339819962701750176780085736688563148805313323832559987437035433833247999087620924866127222624383186196523794674511265705260411629055818558634074002716178572614205083476559448467549382750978798969221261864306013004166462968544016878489610337025698477571385936617039221533150156797058516606210986133737279348158964510267367788753036376287372127305588350819194634811784577657450083527094427316951366173057667942457906834391386244446691065443197207364825764329234362785073013510796581377440788643553306872810091977476137860233746809497381235843829487297832061955822715183509860423069639480585",
            "m_caps": {
                "master_secret": "3474721245392013029738166516781312782479346119348819548256252527956993666797316475826300885352272359376288992698562206288660640656946224194349534695788179582551015950482550893265"
            },
            "r_caps": {}
        },
        "nonce": "681984843736653788293666"
    },
    "auto_issue": false,
    "created_at": "2020-10-07 15:19:26.248489Z",
    "auto_remove": false,
    "credential_exchange_id": "4b9d46cb-7ad7-4643-b2ed-d4bcf6da1450",
    "schema_id": "BsCP2xiQBjWmvWw1uHZRsP:2:대학제증명:1.0",
    "connection_id": "db161274-e733-4c93-bb96-e105c707a58b",
    "role": "holder",
    "thread_id": "af5acbcf-b986-462f-b91a-f93c81f16f61"
}
```

<p></p>

* issue_credential State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | issue_credential state | **`credential_issued`**  | **`request_sent`** |
<p></p>

* Next Step
<p></p>

* Ledger Transactions (Indy Node)

    
<br><br>

### STEP 5. Alice --> Faber : Alice의 Credential 저장 및 Ack 전달.

Cloud Agent의 아래 option이 `true`인 경우 

`--auto-respond-credential-request`: `true`

`--auto-store-credential` : `true`

Alice의 아래 작업은 자동으로 처리 완료.

`POST` `/issue-credential/records/{cred_ex_id}/store` : Store a received credential
`POST` `/issue-credential/records/{cred_ex_id}/remove` : Remove an existing credential exchange record

* issue_credential State check 

    |  | Faber | Alice |
    | --- | --- | --- |
    | issue_credential state | **`credential_acked`**  | **`credential_acked`** --> 삭제 |
<p></p>


Faber는 cred_ex_id를 직접 삭제 해야 함.

 `/issue-credential/records/{cred_ex_id}/remove` : Remove an existing credential exchange record


### STEP 6. Alice : Alice의 Credential 확인 

* Method and Resource

    `GET` `/credentials` wallet의 credential을 모두 가져옴.   
    `GET` `/credential/{credential_id}` wallet의 특정 credential id(`referent`) 가져옴.  
* Parameter

 Name | Description 
 --- | --- 
 cout | Maximum number to retrieve
 start | Start index
 wql | (JSON) WQL query

<p></p>

* Example
 
   * Response body
```json
{
  "results": [
    {
      "referent": "e483bb21-0469-4255-b0ac-46770a5a8552",
      "attrs": {
        "date": "08-2018",
        "age": "25",
        "degree": "maths",
        "name": "alice"
      },
      "schema_id": "Th7MpTaRZVRYnPiabds81Y:2:degree_schema:24.8.1",
      "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_2",
      "rev_reg_id": "Th7MpTaRZVRYnPiabds81Y:4:Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_2:CL_ACCUM:83584a6d-8d1e-4ba6-a091-b3b61f893465",
      "cred_rev_id": "1"
    },
    {
      "referent": "574bad94-f393-4182-9fc0-77c48853921f",
      "attrs": {
        "name": "alice",
        "age": "25",
        "date": "08-2018",
        "degree": "maths"
      },
      "schema_id": "Th7MpTaRZVRYnPiabds81Y:2:degree_schema:24.8.1",
      "cred_def_id": "Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_2",
      "rev_reg_id": "Th7MpTaRZVRYnPiabds81Y:4:Th7MpTaRZVRYnPiabds81Y:3:CL:2143:faber_college_2:CL_ACCUM:83584a6d-8d1e-4ba6-a091-b3b61f893465",
      "cred_rev_id": "2"
    }
  ]
}
```

<p></p>


* Next Step

* Ledger Transactions (Indy Node)

    
<br><br>