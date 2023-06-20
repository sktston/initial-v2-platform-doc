# **Web Console 사용 Guide**

* initial Web Console 사용을 위해서는 회원가입을 통한 자격을 취득 해야합니다.

    - 참고 : 2021년 2월 현재 사업자등록번호를 가진 기관사용자만 이용 가능 (추후 개인 사용자 확대 예정)  


## **1. 회원 가입**

 - E-mail기반 회원 가입 진행.
 - 회원가입 후 개인 email에서 인증을 거쳐야만 로그인 가능합니다.
  
![webconsole 1](img/web_console_signup.png)


<br><br>


## **2. Master 신청**
Master는 기관을 생성하고 증명서 양식을 생성하거나 검증할 수 있는 권한이 부여 되고, Member를 기관에 초대할 수 있음. 

Master 권한이 필요없는 일반 User는 초대를 통해서 console 사용 가능(읽기권한만 부여)

회원가입 후 기관 사용자는 Master 신청을 진행
* Master 신청 시 사업자번호 등 회사 정보 등록
* 심사를 통해 Master 자격 승인 부여

![webconsole 1](img/web_console_master_application.png)

<br><br>

## **3. 기관생성하기** 

* Master는 '기관 생성하기' 버튼을 클릭하여 기관(Organization)을 여러개 생성 가능
    * 2021.02 현재 최대 5개까지 기관 생성 허용
* 하나의 기관으로 여러 서비스를 진행 하거나, 각 서비스마다 각각의 기관 생성 가능 합니다. 사용자의 혼돈을 최소화 하기 위해서는 비슷한 서비스는 하나의 기관을 생성하는 것을 추천 합니다.

* 기관마다 독립된 Public DID와 Private/Public Key/API Access Token이 생성됩니다.
* 기관 생성을 위해서는 아래와 같은 정보가 필요합니다.
    * 기관 명 : 예시) SK텔레콤 모바일사원증
    * 기관 설명 : 예시) 구성원 모바일사원증 발급을 위한 기관
    * 도메인 URL : 예시) https://www.skt.com
    * Webhook URL : Cloud Agent의 처리 결과를 받기 위한 주소
    * Invitation URL : initial App에 초대장을 전송하기 위한 주소
    * 기관구분 : Issuer | Verifier 중 택1
    * App Type : Android | iOS 선택
    * App 노출 : initial App 사용 여부 (현재 지원 안함)
    * 기관 공개 : Web Console에 기관 공개. 다른 기관에서 증명서 검색 가능

![webconsole 1](img/web_console_org_create.png)

<br>

### ***3-1. 기관정보 확인 하기***

* 기관 생성후 아래의 Access Token은 기관에서 안전하게 관리해야 한다. 
해당 Token이 노출되면 악용되거나, 추가적인 과금이 발생할 수 있다.
* Webhook URL 및 invitation URL 개발이 완료되고 정보가 입력되면 '기관공개'를 진행할 수 있다.

![webconsole 1](img/web_console_org_info.png)

<br><br>

### ***3-2. 암호화 송수신 사용 하기***

* 민감 Data(개인정보등) 송수신을 위한 API 암호화 기능을 제공

     * 현재 암호화 제공하는 API는 아래와 같습니다.
     * 증명서 발행 : [/enc/issue-credential/records/{cred_ex_id}/send-offer](/open_api_auto_credential/#step-1)
     * 기관으로 전달하는 모든 Webhook data
     
* Console에서 Secrete key 및 IV 확인 가능 (AES256 사용)
* 암복호화 샘플 코드 : [https://github.com/sktston/initial-controller-java/blob/main/src/main/java/com/sktelecom/initial/controller/utils/Aes256Util.java](https://github.com/sktston/initial-controller-java/blob/main/src/main/java/com/sktelecom/initial/controller/utils/Aes256Util.java)

![webconsole enc](img/web_console_enc.png)

<br><br>


## **4. 발행양식 생성하기** 

* 증명서 발행을 위해서는 각 기관마다 발행양식(Credential Definition ID) 생성이 필요하다. 발행양식은 기본양식(Schema ID)기반으로 폐기지원여부, 증명서이름, 증명서발행개수등 설정값을 추가하여 생성이 가능하다. initial은 재직증명서, 재학증명서등 기본양식을 미리 제공한다.

    * example) initial회사에서 재직증명서 발행을 위해서는 initial재직증명서 발행양식을 생성한 후, initial app에 발행 가능하다.  

* 신규 기본양식(Schema ID) 생성이 필요하면 initial 관리자에게 별도 요청해야 한다.
* 생성된 발행양식은 해당 기관에서만 사용 가능하다.
 
![webconsole 1](img/web_console_issue.png)

<br>

### ***4-1. 발행양식 관리***

* 아래와 같이 발행양식 관리에서 생성된 증명서ID(Credential Definition ID) 확인 가능 합니다. 해당 증명서ID는 생성기관만 사용하여 발행 할 수 있습니다.

![webconsole 1](img/web_console_creddefid_complete.png)

 <br><br>

## **5. 발행하기**

VC발행을 위해서는 Open API를 사용하여 진행.

* Open API 사용하기 위해서는 기관의 API Access Token과 증명서ID(Credential Definition ID)가 필요합니다.
* 상세 내용은 Open API의 ['Issue'](/open_api_auto_credential/) Page 참고

<br><br>


## **6. 검증양식 생성하기** 

증명서 검증을 위해서는 제출 받을 증명서양식(Verification Template ID) 생성이 필요하다.<br>

1. 기본양식(Schema ID)기반으로 생성 : 해당 기본양식으로 발행된 모든 증명서 제출 가능 (SKT재직증명,삼성전자재직증명서,LG전자재직증명서등 모두 제출 가능)
2. 참여기관별 증명양식(Credential Definition ID)기반 생성  : 특정 증명서만 제출 가능 (SKT재직증명서만 제출 가능. 다른 재직증명서는 검증실패)
3. 고급 검증양식 : 2개 이상의 VC을 동시에 검증하거나, 특정 attribute의 값을 요청할때 사용 가능
4. 영지식증명 검증 양식 : 증명서의 내용을 공개하지 않고, True/False 검증 가능

initial에서 제공하는 증명서 양식 기반으로 제출 받을 내용을 선택하면 된다.<br>

    example) SKT임직원 행사를 위해 'SKT재직증명서'의 회사명과 이름을 제출하게 양식 생성 



 
![webconsole 1](img/web_console_verify_template.png)

<br>

![webconsole 1](img/web_console_verify_2.png)


<br><br>


## **7. 검증하기** 

* VC발행을 위해서는 Open API를 사용하여 진행.
* Open API 사용하기 위해서는 기관의 API Access Token과 Verification Template ID 필요함
* 상세 내용은 Open API의 ['Verify'](https://initial-v2-platform.readthedocs.io/open_api_proof/) Page 참고
<br><br>

## **8. initial App 연동하기**

<div class="admonition warning">
<p class="admonition-title">warning</p>
<p> initial App 연동은 사용자들에게 직접 영향을 주기 때문에 주의가 필요 </p>
</div>

* 기관 관리자는 Web Console을 통해 initial App의 기관 표시를 컨트롤 할 수 있다.
* 실수를 방지하기 위해 아래 두단계를 활성화 해야 한다
<p></p>

* <b>기관관리</b>-<b>기관정보</b>에서 아래 두 항목을 활성화 해야 한다. webhook url과 invitation url 모두 개발 되어야 활성화 된다.
    - <b>기관 공개</b> : `공개`로 설정. '비공개'로 설정하면 'App표시'는 자동으로 `미사용` 된다.
    - <b>App표시</b> : '사용'으로 설정.

![webconsole 1](img/initial_app_display_1.png)

* <b>발행관리</b>-<b>발행양식관리</b>-<b>증명서상세보기</b>에서 아래 항목을 활성화 해야 한다
    - <b>증명서 발행 설정</b> : '증명서공개'로 설정. 해당 증명서만 영향을 준다.
![webconsole 1](img/initial_app_display_2.png)

<br><br>

* 개발 진행 중에는 기관공개를 `비공개`로 하거나, App표시를 `미사용`으로 해야 한다.  


## 9. 통계분석하기

![webconsole 1](img/console_statistics.png)

* 발행/검증 통계를 제공한다.

##### - Web 화면 items

| 항목(화면)     | 설명                                       |
|------------|------------------------------------------|
| No.        | 번호                                       |
| From       | 통계 Data를 보낸 주체. Issuer 혹은 Verifier 만 표시됨 |
| Txn ID     | 통계 정보를 기록한 블록체인의 트랜잭션 ID                 |
| Org did    | 발행/검증을 진행한 기관의 Public DID                |
| Holder did | 증명서 발행/검증한 사용자의 Pairwised DID            |
| TxReulst   | 검증/발행의 성공/실패 여부                          |
| VCs        | 검증에 사용한 증명서의 개수                          |
| credDefId  | 발행/검증에 사용한 증명서ID                         |
| claims     | 발행/검증에 사용한 attribute 항목들                 |
| Time stamp | 이벤트 시간. 엑셀에서는 unixtime으로 제공된다.           |

##### - Excel file items

| 항목(화면)       | 설명                                                |
|--------------|---------------------------------------------------|
| txnType      | 통계 Data를 보낸 주체. Issuer(0) 혹은 Verifier(1) 만 표시됨    |
| txnId        | 통계 정보를 기록한 블록체인의 트랜잭션 ID                          |
| result       | 검증/발행의 성공/실패 여부                                   |
| sPublicDid   | 발행/검증을 진행한 기관의 Public DID                         |
| sPairwiseDid | 기관에서 생성한 Pairwised DID                            |
| cPairwiseDid | 증명서 발행/검증한 사용자(client)의 Pairwised DID(Holder DID) |
| verifTplId   | 검증양식 ID                                           |
| credentials  | 발행/검증에 사용한 증명서의 종류와(credDefId)오 항목들(claims)       |
| timestamp    | 이벤트 시간. 엑셀에서는 unixtime으로 제공된다.                    |

*** Excel unixtime to datatime 으로 변경하기 ***

예제) 엑셀 J열의 timestamp ```1652943167393``` 변환하는 예시


- Step 1 unixtime을 두부분으로 나눈 후 Seoul Time (9시간 추가) 변경하여 시간 변경 가능한 형태로 재계산 

```=((((LEFT(J2,10) & "." & RIGHT(J2,3))+9*60*60)/60)/60)/24+DATE(1970,1,1)```


- step 2 : date time 형식으로 cell 속성 변경

```yyyy/m/d h:mm:ss.000```



*** Holder did(사용자) 확인 방법 ***

<https://initial-v2-platform.readthedocs.io/open_api_auto_connection/#option-connection>

기관에서 실제 사용자와 매핑하여 보관하고 있는 ```connection_id```로 위 API를 호출하면 sPairwiseDid, cPairwiseDid 를 확인할 수 있다.

Sample curl Request

```
curl --location --request GET 'https://dev-console.myinitial.io/agent/api/connections/3fa85f64-5717-4562-b3fc-2c963f66afa6' \
--header 'Authorization: Bearer 2caxxd8a-xxxx-xxxx-xxxx-c5fbxx86f2cc'
```

Sample Response

- sPairwiseDid = my_did
- cPairwiseDid = their_did

```json
{
  "created_at":"2021-06-02 06:31:57.255177Z",
  "routing_state":"none",
  "my_did":"66ZaFYLPorqgtQpo8AcpQY",
  "their_label":"agency",
  "rfc23_state":"completed",
  "connection_id":"7d670c2a-ef85-4289-81c3-24bb8e4f045d",
  "their_role":"inviter",
  "accept":"auto",
  "updated_at":"2021-06-02 06:32:01.252322Z",
  "invitation_key":"81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P",
  "invitation_mode":"once",
  "their_did":"78bCMv53bKrusuxyrrbgWM",
  "state":"active",
  "topic":"connections"
}
```