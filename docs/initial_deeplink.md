initial App2App Deeplink Spec
================

<div class="admonition note">
<p class="admonition-title">note</p>
<p> initial은 App to App 호출을 위한 Deeplink 규격을 제공합니다. </p>
</div>

<br>

### Deeplink 연동 Spec

initial의 deeplink는 등록된 기관 및 증명서에 한해서 동작합니다.<br>
아래와 같은 scheme으로 동작합니다.

- Scheme : initial://reqService?**<span style="color:red">{{Parameter}}</span>**

#### Deeplink Parameters

  Parameter | M / O | Type | Value |  Description
  --- | :---: | :---: | :---: | ---
  process | 필수 | string | I <br> V <br> E <br> O <br> F | I - Issue, Credential 발급<br> V - Verify, Credential 제출/검증<br> E - 행안부 전자문서 제출 <br> O - OCR scan 문서 제출 <br> F - 추가서류 제출
  ynCloud | 필수 | String | Y <br>N | Cloud Agent 기관 여부 (Y/N)
  orgName | 옵션 | string | 기관명 | 등록된 기관명
  svcPublicDID | 필수 | String | did:ssw:{{did}}| 기관의 PublicDID
  credDefId | 옵션 | String | cred_def_id | 증명서 ID
  seq | 필수 | String | 고객구분자 | 고객구분자 / 신청번호
  govDocs | 옵션 | String | 코드 | 전자정부 문서 제출 목록 <br> process=E일 경우 필수
  govWalletAdd | 옵션 | String | 지갑 주소 | 제출할 곳의 전자정부 지갑 주소 <br> process=E일 경우 필수
  masking | 옵션 | String | Y <br>N | 마스킹 처리 여부
  submitUrl | 옵션 | url | URL | 제출 완료를 위한 URL
  invitationUrl | 필수 | URL | URL | 등록된 invitation url
  invitation | 필수 | string | invitation json | create-invitation으로 생성된 json
  callback | 옵션 | String | URL |제출완료 후 복귀할 deeplink URL


  - sample : 발급요청 / Cloud Agent 기관 / Public DID / 발행할 Cree_Def_ID / invitation


`initial://reqService?process=I&ynCloud=Y&svcPublicDID=did:ssw:DrLbXFSao4Vo8gMfjxPxU1&credDefId=DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd&invitation=https://dev-console.myinitial.io/agent/endpoint?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiNWQ5NDI5MTgtMDNjNC00ZTQyLTljMDgtMzZiNGM1YTY0ZDMxIiwgImRpZCI6ICJkaWQ6c3N3OkRyTGJYRlNhbzRWbzhnTWZqeFB4VTEiLCAiaW1hZ2VVcmwiOiAiaHR0cHM6Ly9rci5vYmplY3QubmNsb3Vkc3RvcmFnZS5jb20vZGV2LWltYWdlLWZpbGUvZDQxZDhjZDlfYTMyODYxZTdfMTYyNzg2NjUzMiIsICJsYWJlbCI6ICIoXHVjMGQ4XHVkNTBjKSBTS1QgXHVkMWEwXHVjNzc1XHVjMTMxXHVjODAxIFx1Yzk5ZFx1YmE4NVx1YzExYyJ9`


#### Parameter 상세 설명 및 예시 

##### process 

- process=VE : 연결 완료되면 Verify를 요청하고, 완료되면 행안부 전자문서를 제출 요청
- process=I : 연결 완료 후 발행 요청

##### ynCloud

- ynCloud=Y : Cloud Agent 사용 기관임을 명시
- ynCloud=N : Cloud Agent 사용 기관과 다른 방식으로 동작

##### orgName

- orgName=skt : 회사명

##### svcPublicDID

해당 기관의 public DID를 did:ssw method를 포함해서 입력

- svcPublicDID=did:ssw:DrLbXFSao4Vo8gMfjxPxU1

##### credDefId

발급/검증을 위한 target cred_def_id를 입력

- credDefId=DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd

##### seq

- seq=1038555586867

##### govDocs

전자정부 문서 제출 목록. 1개 이상 제출 시 "_"로 구분

 - govDocs=30000200076_30000100010

문서 코드표 (update 예정)

##### govWalletAdd

기관의 수취용 전자정부 지갑 주소

- govWalletAdd=2V3mcLB7ieZ1Y7XcdBW3rYPcvu6sfEupULzqKaN


##### govWalletAdd

민감정보 마스킹

- masking=Y

##### submitUrl(작성 중)

제출 완료 되면 아래와 같은 message를 webhook으로 전달 한다.

Key | M / O | Type | Value |  Description
--- | :---: | :---: | :---: | ---

##### invitationUrl

connection에 사용하기 위한 invitation-url을 전달한다.

invitationUrl=https://issue.sktelecom.com/invitation-url

##### invitation

invitation-url통해서 생성된 값을 전달한다. invitation-url public open이 불가능한 기관에서 사용할 수 있다.

`invitationUrl=https://dev-console.myinitial.io/agent/endpoint?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiNWQ5NDI5MTgtMDNjNC00ZTQyLTljMDgtMzZiNGM1YTY0ZDMxIiwgImRpZCI6ICJkaWQ6c3N3OkRyTGJYRlNhbzRWbzhnTWZqeFB4VTEiLCAiaW1hZ2VVcmwiOiAiaHR0cHM6Ly9rci5vYmplY3QubmNsb3Vkc3RvcmFnZS5jb20vZGV2LWltYWdlLWZpbGUvZDQxZDhjZDlfYTMyODYxZTdfMTYyNzg2NjUzMiIsICJsYWJlbCI6ICIoXHVjMGQ4XHVkNTBjKSBTS1QgXHVkMWEwXHVjNzc1XHVjMTMxXHVjODAxIFx1Yzk5ZFx1YmE4NVx1YzExYyJ9`

##### callback

발행/검증 완료 후 복귀한 url 주소이다.

- callback=initial://mainPage