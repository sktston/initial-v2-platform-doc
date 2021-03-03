<p>initial Platform DID Agent API 이용 개발 안내</p>

![platform arch](img/initial_platform_architecture.png)

## 기관 참여자 구현/개발이 필요 항목 
위 그림을 참고하여 기관사용자 영역의 3개 항목에 대해서 구현 필요

1. 연결(Connection)요청 API
2. DID Agent API 요청 Controller 서버
3. Event 수신 Webhook 서버
4. 참여사 개인정보 수집 및 이용 동의서 전달 

### 1. 연결 요청 API 개발 안내
- 기관사용자는 initial App과 최초 연결(connection)을 위해 초대장(invitation)을 생성할 수 있는 API를 제공해야 한다.
- 각각의 연결요청 API는 initial App내의 증명서발행/검증 icon과 매칭되기 때문에, 연결요청은 발행양식/검증양식마다 각각 API 생성이 필요하다.
- 연결요청을 위한 기본 domain 주소를 플랫폼에 등록하면, 양식이 생성될때 마다 ```기관제공연결요청url``` + ```발행/검증양식ID``` 형식의 규격화된 url을 자동생성해서 제공한다. (예제 http://기관등록연결요청url/BsCP2xiQBjWmvWw1uHZRsP:3:CL:1602080133:SK대학교 )
- 기관 사용자는 플랫폼에서 제공하는 url으로 API server를 제공해야 한다.
- 개발 예제는 https://github.com/sktston/acapy-controller-java/blob/master/src/main/java/com/sktelecom/ston/controller/faber/GlobalController.java 참고

### 2. DID Agent Controller 개발 안내 
- 기관사용자는 DID Agent REST API를 사용하기 위해 controller 서버를 개발해야 한다.
- Webhook 서버를 통해 전달된 event에 따라 다음 action을 구현한다.
- 자세한 API 내용은 initial DID Agent API Guide 및 reference code 참조 

  
### 3. Webhook 서버 개발 안내 
- initial DID Platform의 DID Agent는 Event Driven(이벤트 구동형) 방식이기 때문에 기관 사용자는 Webhook 서버를 개발하고 url 등록해아 한다.

- initial DID Agent는 event driven 방식으로 REST API 요청에 대한 응답을 Webhook 서버로 전달합니다
- 처리결과를 받기 위한 기관 사용자의 Webhook URL은 기관정보에 필수 등록해야 합니다. e.g)https://domain.com/webhooks
- Webhook 서버의 API Key는 보안 강화를 위한 선택 사항입니다. https://domain.com/webhooks#org-api-key 와 같이 입력하시면 header의 x-api-key 항목에 값으로 "org-api-key"를 함께 전송합니다.
- 기관 사용자는 보안강화를 위한 IP기반 방화벽 설정 하세요 (initial DID platform의 IP대역 xxx.xxx.xxx.xxx/24는 별도로 안래 예정입니다)
- Webhook 서버 예제는 아래 reference code (DID 발행/검증 Demo)에서 확인할 수 있습니다. 
<br><br>
#### 3-1. Webhook 구현

- initial DID platform의 webhook은 topic, body로 전달된다. 
- 참고 1 : https://github.com/sktston/acapy-controller-java/blob/develop/src/main/java/com/sktelecom/ston/controller/faber/GlobalService.java
- 참고 2 : https://ldej.nl/post/aries-cloudagent-python-webhooks

item | description | example
--- | --- | ---
topic | event 응답의 종류 | topic:connections
body | response | body:{"my_did": "PJRSPwhWdnGJp7CvifXpkw", "their_role": "inviter", "rfc23_state": "request-sent", "accept": "auto", "their_label": "faber.24.73.48.label", "connection_id": "52b11e22-72c6-4bc1-8664-0872e05816e8", **"state": "request"**, "routing_state": "none", "updated_at": "2021-02-15 11:24:16.430351Z", "invitation_mode": "once", "request_id": "5aee8c7b-a728-4d15-96ba-295f982d695a", "created_at": "2021-02-15 11:24:16.390399Z"}

body의 `state`는 이후 다음 action을 위해 중요하게 활용 됨. 

#### 3-2. Webhook서버 Controller API 예시 

- @PostMapping("/webhooks/topic/{topic}")
- 등록한 webhook url 뒤에 전달되는 topic을 확인 후 다음 action을 진행하면 된다.

#### 3-3. Webhook topic의 종류 및 next action 
 
topic | state | description | next action
--- | --- | --- | ---
connection | invitation,request,response | 연결 중 | No Action. connection state `active` 까지 자동 진행 됨. 
connection | active | 연결 완료 | 개인정보수집 동의서를 보내거나, VC 발행 및 검증 요청을 진행한다. <br>e.g. send-message,send-offer,send-request 
basicmessages | received | Message 수신 완료 | message를 display 하거나, 정책에 따라 VC 발행이나 검증을 진행한다. <br>e.g. send-offer,send-request 
issue_credential | offer_sent, request_received, credential_issued | 발행 중 | No Action. VC발급 진행 중. issue_credential stat `credential_acked`까지 자동 진행 됨. 
issuer_cred_rev | issued | revocation registry update  | revocation 관련 정보 저장  |
issue_credential | credential_acked | VC 발행 완료 | 추가 VC 발행 하거나, 검증 요청할 수 있음. <br>e.g. send-message,send-offer,send-request  
present_proof | request_sent, presentation_received | 검증 중 | No Action. Verify 완료 시 까지 자동으로 진행 
present_proof | verified | Verify 완료 | 검증 결과를 확인하거나, 필요 시 저장 하면 됨.
revocation_registry | posted | RevocationRegistry record 생성 완료 | No Action
problem_report | | | 에러 확인 


### 4. 참여사 개인정보 수집 및 이용 동의서 조회 전달 구현 안내 
- 모바일 initial App에서 참여사에게 본인확인증명을 제출하기 전 개인정보 수집 및 이용 동의를 받기 위해, 동의서 전달이 필요하다. 
- 동의서 전달은 DID Agent의 Message 전달 API를 이용한다. 상세 내용은 Message API 참조.


