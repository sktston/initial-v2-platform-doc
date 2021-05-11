<p>initial Platform DID Agent API 이용 개발 안내</p>

![platform arch](img/initial_platform_architecture.png)

## 기관 참여자 구현/개발이 필요 항목 
위 그림을 참고하여 기관사용자 영역의 3개 항목에 대해서 구현 필요

1. 연결(Connection)요청 API
2. DID Agent API 사용위한 Controller
3. Event 수신 Webhook API
4. 참여사 개인정보 수집 및 이용 동의서 전달 

### 1. 연결 요청 API 개발 안내
- 기관사용자는 initial App과 최초 연결(connection)을 위해 초대장(invitation)을 생성할 수 있는 API를 제공해야 한다.
- initial app의 기관 icon 클릭 시 해당 URL과 최초 통신 한다 
- 연결요청 Open API 참고 : [Connection](https://initial-v2-platform.readthedocs.io/ko/master/open_api_auto_connection/)
- 개발 참고 JAVA 예제 :  [Create Invitation Url java code](https://github.com/sktston/initial-controller-java/blob/master/src/main/java/com/sktelecom/initial/controller/issuer/GlobalService.java#L183)


invitation-url API JAVA sample code
```java
    public String createInvitationUrl() {
        String params = "?public=true";
        String response = client.requestPOST(agentApiUrl + "/connections/create-invitation" + params, accessToken, "{}");
        try {
            return JsonPath.read(response, "$.invitation_url");
        } catch (IllegalArgumentException e) {
            return null;
        }
    }
```


http sample
```
POST /agent/api/connections/create-invitation?alias=description&public=true HTTP/1.1
Host: dev-console.myinitial.io
Content-Type: application/json
Authorization: Bearer dxxxxxxx-xxxx-4a49-xxxx-1234567890as
```

cURL sample
```url
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/connections/create-invitation?alias=description&public=true' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer dxxxxxxx-xxxx-4a49-xxxx-1234567890as' \
--data-raw ''
```


##### invitation-url API Response는 아래와 같은 양식으 제공해야 한

Response sample
```
https://dev-console.myinitial.io/agent/endpoint?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiMmYyNWFhZTktNWQ4MS00MzFhLWE1NzItZDBiYzQ0Yzk5MzI2IiwgImxhYmVsIjogIlx1YjE3OFx1Yzc3OFx1Yzc3Y1x1Yzc5MFx1YjlhYyIsICJkaWQiOiAiZGlkOnNzdzpDQjFmOVdLR0FKRHdVS0NUMlhFeDdvIiwgImltYWdlVXJsIjogImh0dHBzOi8va3Iub2JqZWN0Lm5jbG91ZHN0b3JhZ2UuY29tL2Rldi1pbWFnZS1maWxlL2Q0MWQ4Y2Q5X2Q1YWQ4ZjliXzE2MTYxNDA3ODQifQ==
```

holder(initial app)은 위 response를 base64 decoding 하여 사용
```json
{
  "@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
  "@id":"2f25aae9-5d81-431a-a572-d0bc44c99326",
  "label":"\ub178\uc778\uc77c\uc790\ub9ac",
  "did":"did:ssw:CB1f9WKGAJDwUKCT2XEx7o",
  "imageUrl":"https://kr.object.ncloudstorage.com/dev-image-file/d41d8cd9_d5ad8f9b_1616140784"
}
```

### 2. DID Agent Controller 개발 안내 
- 기관사용자는 DID Agent REST API를 사용하기 위해 controller 서버를 개발해야 한다.
- Webhook 서버를 통해 전달된 event에 따라 다음 action을 구현한다.
- 자세한 API 내용은 initial DID Agent API Guide 및 reference code 참조
- Open API 참고 : [Open APIs](https://initial-v2-platform.readthedocs.io/ko/master/open_api_list/)
- 개발 참고 JAVA 예제 :  [Create Invitation Url java code](https://github.com/sktston/initial-controller-java/blob/master/src/main/java/com/sktelecom/initial/controller/issuer/GlobalService.java)


  
### 3. Webhook API 개발 안내 
- initial DID Platform의 DID Agent는 Event Driven(이벤트 구동형) 방식으로 기관 사용자는 Event를 받을 수 있는 Webhook API를 개발하고 url 등록해아 한다.
- initial DID Agent는 event driven 방식으로 REST API 요청에 대한 응답을 Webhook 서버로 전달합니다
- 처리결과를 받기 위한 기관 사용자의 Webhook URL은 기관정보에 필수 등록해야 합니다. e.g)https://domain.com/webhooks
- Webhook 서버의 API Key는 보안 강화를 위한 선택 사항입니다. https://domain.com/webhooks#org-api-key 와 같이 입력하시면 header의 x-api-key 항목에 값으로 "org-api-key"를 함께 전송합니다.
- 기관 사용자는 보안강화를 위한 IP기반 방화벽 설정 하세요 (initial DID platform의 IP대역 xxx.xxx.xxx.xxx/24는 별도로 안내 예정입니다)
- Webhook 서버 예제는 아래 reference code (DID 발행/검증 Demo)에서 확인할 수 있습니다. 
<br><br>
  
#### 3-1. Webhook API 구현

- initial DID platform에서 처리 결과를 webhook으로 topic, body를 전달된다.
- 기관은 Webhook으로 event를 받을 수 있는 API를 제공해야 한다.
- 개발 코드 참고 : https://github.com/sktston/initial-controller-java/blob/main/src/main/java/com/sktelecom/initial/controller/issuer/GlobalService.java#L64
- Topic에 관한 참고 : https://ldej.nl/post/aries-cloudagent-python-webhooks

```java
    @PostMapping(value = "/webhooks/topic/{topic}")
    public ResponseEntity webhooksTopicHandler(
            @PathVariable String topic,
            @RequestBody String body) {
        globalService.handleEvent(topic, body);
        return ResponseEntity.ok().build();
    }
```

실제 전달되는 Event Sample 

item | description | example
--- | --- | ---
topic | event 응답의 종류 | topic:connections
body | response | body:{"my_did": "PJRSPwhWdnGJp7CvifXpkw", "their_role": "inviter", "rfc23_state": "request-sent", "accept": "auto", "their_label": "faber.24.73.48.label", "connection_id": "52b11e22-72c6-4bc1-8664-0872e05816e8", **"state": "request"**, "routing_state": "none", "updated_at": "2021-02-15 11:24:16.430351Z", "invitation_mode": "once", "request_id": "5aee8c7b-a728-4d15-96ba-295f982d695a", "created_at": "2021-02-15 11:24:16.390399Z"}

body의 `state`는 event 수신 후 다음 action을 위해 중요하게 활용 됨. 

#### 3-2. Webhook Controller 개발 

- webhook으로 전달된 event에 따른 biz logic 처리를 위한 controller를 개발해야 한다.

##### Webhook으로 전달되는 topic의 종류 및 biz logic 예시 
 
topic | state 종류 | description | next action
--- | --- | --- | ---
connections | invitation,request,response,active | 연결 | connection state `active` 까지 자동 진행 됨. 기관 개발자는 connection_id를 저장하고, 발행기관은 Holder로 부터 발행 요청이 올때까지 대기한다. 검증기관은 개인정보수집을 위한 동의서를 요청을 진행한다.
basicmessages | received | Message 수신 완료 | message를 display 하거나, 정책에 따라 VC 발행이나 검증을 진행한다. <br>e.g. send-offer,send-request 
issue_credential | offer_sent, request_received, credential_issued, credential_acked | 발행 | issue_credential state `credential_acked`까지 자동 진행 됨. 
issuer_cred_rev | issued | revocation registry update  | revocation 관련 정보 저장  |
present_proof | request_sent, presentation_received,verified | 검증 | state:verified까지 자동으로 진행. 검증 결과를 확인하거나, 필요 시 저장 하면 됨. 
revocation_registry | posted | RevocationRegistry record 생성 완료 | 블록체인에 자동으로 생성 완료. 개발자는 별도로 진행할 필요 없음.
problem_report | | | 에러 확인 


##### Webhook Controller Sample code
```java
public void handleEvent(String topic, String body) {
String state = topic.equals("problem_report") ? null : JsonPath.read(body, "$.state");
log.info("handleEvent >>> topic:" + topic + ", state:" + state + ", body:" + body);

        switch(topic) {
            case "issue_credential":
                // 1. holder 가 credential 을 요청함 -> 개인정보이용 동의 요청
                if (state.equals("proposal_received")) {
                    log.info("- Case (topic:" + topic + ", state:" + state + ") -> isValidCredentialProposal && sendAgreement");
                    String credentialProposal = JsonPath.parse((LinkedHashMap)JsonPath.read(body, "$.credential_proposal_dict")).jsonString();
                    if(isValidCredentialProposal(credentialProposal)) {
                        sendAgreement(JsonPath.read(body, "$.connection_id"));
                    }
                }
                // 4. holder 가 증명서를 정상 저장하였음 -> 완료 (revocation 은 아래 코드 참조)
                else if (state.equals("credential_acked")) {
                    log.info("- Case (topic:" + topic + ", state:" + state + ") -> credential issued successfully");
                    // TODO: should store credential_exchange_id to revoke this credential later
                    // storeCredExRecord(body);
                    if (enableRevoke) {
                        revokeCredential(JsonPath.read(body, "$.credential_exchange_id"));
                    }
                }
                break;
            case "basicmessages":
                String content = JsonPath.read(body, "$.content");
                String type = getTypeFromBasicMessage(content);
                // 2. holder 가 개인정보이용 동의를 보냄 -> 모바일 가입증명 검증 요청
                if (type != null && type.equals("initial_agreement_decision")) {
                    if (isAgreementAgreed(content)) {
                        log.info("- Case (topic:" + topic + ", state:" + state + ", type:" + type + ") -> AgreementAgreed & sendPresentationRequest");
                        sendPresentationRequest(JsonPath.read(body, "$.connection_id"));
                    }
                }
                else
                    log.warn("- Warning: Unexpected type:" + type);
                break;
            case "present_proof":
                // 3. holder 가 보낸 모바일 가입증명 검증 완료
                if (state.equals("verified")) {
                    log.info("- Case (topic:" + topic + ", state:" + state + ") -> getPresentationResult");
                    String presRequest = JsonPath.parse((LinkedHashMap)JsonPath.read(body, "$.presentation_request")).jsonString();
                    String presentation = JsonPath.parse((LinkedHashMap)JsonPath.read(body, "$.presentation")).jsonString();
                    String attrs = getPresentationResult(JsonPath.read(body, "$.verified"), presRequest, presentation);
                    if (enableWebView) {
                        // 3-1. 검증 값 정보로 발행할 증명서가 한정되지 않는 경우 추가 정보 요구
                        log.info("Web View enabled -> sendWebView");
                        sendWebView(JsonPath.read(body, "$.connection_id"), attrs, body);
                    }
                    else {
                        // 3-2. 검증 값 정보 만으로 발행할 증명서가 한정되는 경우 증명서 바로 발행
                        log.info("Web View is not used -> sendCredentialOffer");
                        sendCredentialOffer(JsonPath.read(body, "$.connection_id"), attrs, null);
                    }
                }
                break;
            case "problem_report":
                log.warn("- Case (topic:" + topic + ") -> Print body");
                log.warn("  - body:" + prettyJson(body));
                break;
            case "connections":
            case "revocation_registry":
            case "issuer_cred_rev":
                break;
            default:
                log.warn("- Warning Unexpected topic:" + topic);
        }
    }
```


### 4. 참여사 개인정보 수집 및 이용 약관 조회 전달 구현 안내 
- 모바일 initial App에서 참여사에게 본인확인증명을 제출하기 전 개인정보 수집 및 이용 동의를 받기 위해, 동의서 전달이 필요하다.
- 동의서는 해당기관 법무팀의 검토를 받아야 한다.
- 약관/동의서 전달은 DID Agent의 Message 전달 API를 이용한다. 상세 내용은 Message API 참조.
- Open API 참고 : [Message](https://initial-v2-platform.readthedocs.io/ko/master/open_api_message/)
- 개발 참고 JAVA 예제 :  [약관 표시 java code](https://github.com/sktston/initial-controller-java/blob/main/src/main/java/com/sktelecom/initial/controller/issuer/GlobalService.java#L207)


