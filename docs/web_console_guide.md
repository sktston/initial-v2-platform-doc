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
- 개발 참고 JAVA 예제 :  [Create Invitation Url java code](https://github.com/sktston/initial-controller-java/blob/master/src/main/java/com/sktelecom/initial/controller/issuer/GlobalService.java#L181)


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


invitation-url API http sample code
```
POST /agent/api/connections/create-invitation?alias=description&public=true HTTP/1.1
Host: dev-console.myinitial.io
Content-Type: application/json
Authorization: Bearer dxxxxxxx-xxxx-4a49-xxxx-1234567890as
```

invitation-url API cURL sample code
```url
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/connections/create-invitation?alias=description&public=true' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer dxxxxxxx-xxxx-4a49-xxxx-1234567890as' \
--data-raw ''
```


##### invitation-url API Response는 아래와 같은 양식으로 제공해야 한다 

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
- 처리결과를 받기 위한 기관 사용자의 Webhook URL은 기관정보에 필수 등록해야 합니다. e.g)https://domain.com/webhooks
- Webhook 서버의 API Key는 보안 강화를 위한 선택 사항입니다. https://domain.com/webhooks#org-api-key 와 같이 입력하시면 header의 x-api-key 항목에 값으로 "org-api-key"를 함께 전송합니다.
- Webhook 서버 예제는 아래 reference code (DID 발행/검증 Demo)에서 확인할 수 있습니다. 
<br><br>
  
#### 3-1. Webhook API 구현

- initial DID platform에서 처리 결과를 webhook으로 전달다.
- 기관은 Webhook으로 event를 받을 수 있는 API를 제공해야 한다.
- 개발 코드 참고 : https://github.com/sktston/initial-controller-java/blob/main/src/main/java/com/sktelecom/initial/controller/issuer/GlobalService.java#L643
- Topic에 관한 참고 : https://ldej.nl/post/aries-cloudagent-python-webhooks

```java
    @PostMapping(value = "/webhooks")
public ResponseEntity webhooksTopicHandler(@RequestBody String body) {
        globalService.handleEvent(body);
        return ResponseEntity.ok().build();
        }
```

실제 Webhook으로 전달되는 Connection Event Sample 

```json
body: {
"invitation_key": "81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P",
"accept": "auto",
"their_role": "inviter",
"updated_at": "2021-05-14 01:19:09.319499Z",
"routing_state": "none",
"created_at": "2021-05-14 01:19:03.726010Z",
"invitation_mode": "once",
"rfc23_state": "completed",
"my_did": "Av7wYofnBi321vdgFjyLk6",
"connection_id": "e112b31d-e6b4-4b80-8a32-bcdc07f5a095",
"their_label": "agency",
"state": "active",
"their_did": "L15dj3bW7pemZg78NTM4MU",
"topic": "connections"
}
```
body의 `topic`의 `state`는 event 수신 후 다음 action을 위해 중요하게 활용. 

#### 3-2. Webhook Controller 개발 

- webhook으로 전달된 event에 따른 biz logic 처리를 위해 controller를 개발해야 한다.

##### Webhook으로 전달되는 topic의 종류 및 biz logic 예시 

현재 7가지 종류의 topic이 전달 됨. 

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
public void handleEvent(String body) {
String topic = JsonPath.read(body, "$.topic");
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

Webhook으로 전달되는 event의 서버 log 예제 
```
2021-05-14 10:19:02 [INFO ] [GlobalService.java]createInvitationUrl(185) : response: {"connection_id": null, "invitation": {"@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation", "@id": "b05616b9-cfa2-4714-b885-a95d85c24584", "imageUrl": "https://kr.object.ncloudstorage.com/dev-image-file/d41d8cd9_037d949c_1619417220", "label": "\ud14c\uc2a4\ud2b8\ud1a0\uc775\uc99d\uba85\uc11c", "did": "did:ssw:DrLbXFSao4Vo8gMfjxPxU1"}, "invitation_url": "https://dev-console.myinitial.io/agent/endpoint?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiYjA1NjE2YjktY2ZhMi00NzE0LWI4ODUtYTk1ZDg1YzI0NTg0IiwgImltYWdlVXJsIjogImh0dHBzOi8va3Iub2JqZWN0Lm5jbG91ZHN0b3JhZ2UuY29tL2Rldi1pbWFnZS1maWxlL2Q0MWQ4Y2Q5XzAzN2Q5NDljXzE2MTk0MTcyMjAiLCAibGFiZWwiOiAiXHVkMTRjXHVjMmE0XHVkMmI4XHVkMWEwXHVjNzc1XHVjOTlkXHViYTg1XHVjMTFjIiwgImRpZCI6ICJkaWQ6c3N3OkRyTGJYRlNhbzRWbzhnTWZqeFB4VTEifQ=="}
2021-05-14 10:19:03 [INFO ] [GlobalService.java]handleEvent(66) : handleEvent >>> topic:connections, state:request, body:{"updated_at":"2021-05-14 01:19:03.726010Z","their_did":"L15dj3bW7pemZg78NTM4MU","their_role":"inviter","rfc23_state":"request-sent","their_label":"agency","state":"request","created_at":"2021-05-14 01:19:03.726010Z","my_did":"Av7wYofnBi321vdgFjyLk6","accept":"auto","connection_id":"e112b31d-e6b4-4b80-8a32-bcdc07f5a095","invitation_key":"81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P","routing_state":"none","invitation_mode":"once","topic":"connections"}
2021-05-14 10:19:03 [INFO ] [GlobalService.java]handleEvent(66) : handleEvent >>> topic:connections, state:response, body:{"updated_at":"2021-05-14 01:19:03.796153Z","their_did":"L15dj3bW7pemZg78NTM4MU","their_role":"inviter","rfc23_state":"response-received","their_label":"agency","state":"response","created_at":"2021-05-14 01:19:03.726010Z","my_did":"Av7wYofnBi321vdgFjyLk6","accept":"auto","connection_id":"e112b31d-e6b4-4b80-8a32-bcdc07f5a095","invitation_key":"81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P","routing_state":"none","invitation_mode":"once","topic":"connections"}
2021-05-14 10:19:09 [INFO ] [GlobalService.java]handleEvent(66) : handleEvent >>> topic:connections, state:active, body:{"invitation_key":"81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P","accept":"auto","their_role":"inviter","updated_at":"2021-05-14 01:19:09.319499Z","routing_state":"none","created_at":"2021-05-14 01:19:03.726010Z","invitation_mode":"once","rfc23_state":"completed","my_did":"Av7wYofnBi321vdgFjyLk6","connection_id":"e112b31d-e6b4-4b80-8a32-bcdc07f5a095","their_label":"agency","state":"active","their_did":"L15dj3bW7pemZg78NTM4MU","topic":"connections"}
2021-05-14 10:19:14 [INFO ] [GlobalService.java]handleEvent(66) : handleEvent >>> topic:issue_credential, state:proposal_received, body:{"updated_at":"2021-05-14 01:19:14.137514Z","thread_id":"2eb92d5f-2a28-4cf0-bc09-a62f4abf1606","connection_id":"e112b31d-e6b4-4b80-8a32-bcdc07f5a095","credential_exchange_id":"c352e34b-88b9-4564-9e38-62d8f1e8f510","trace":false,"role":"issuer","state":"proposal_received","auto_issue":true,"created_at":"2021-05-14 01:19:14.137514Z","auto_remove":false,"initiator":"external","credential_proposal_dict":{"@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/propose-credential","@id":"2eb92d5f-2a28-4cf0-bc09-a62f4abf1606","cred_def_id":"A5EaF6CUiTquuMFysqykmY:3:CL:1614236895:tag.56.42.80"},"topic":"issue_credential"}
2021-05-14 10:19:14 [INFO ] [GlobalService.java]handleEvent(72) : - Case (topic:issue_credential, state:proposal_received) -> isValidCredentialProposal && sendAgreement
2021-05-14 10:19:14 [INFO ] [GlobalService.java]sendAgreement(219) : response: {}
2021-05-14 10:19:20 [INFO ] [GlobalService.java]handleEvent(66) : handleEvent >>> topic:basicmessages, state:received, body:{"connection_id":"e112b31d-e6b4-4b80-8a32-bcdc07f5a095","message_id":"cd555104-a28d-4743-859f-085e8ab4bb0e","content":"{\"type\":\"initial_agreement_decision\",\"content\":{\"agree_yn\":\"Y\"}}","state":"received","sent_time":"2021-05-14T10:19:20.123","locale":"en","topic":"basicmessages"}
2021-05-14 10:19:20 [INFO ] [GlobalService.java]isAgreementAgreed(225) : decisionContent: {"agree_yn":"Y"}
2021-05-14 10:19:20 [INFO ] [GlobalService.java]handleEvent(95) : - Case (topic:basicmessages, state:received, type:initial_agreement_decision) -> AgreementAgreed & sendPresentationRequest
2021-05-14 10:19:20 [INFO ] [GlobalService.java]handleEvent(66) : handleEvent >>> topic:present_proof, state:request_sent, body:{"initiator":"self","updated_at":"2021-05-14 01:19:20.377222Z","thread_id":"6224753f-272b-4a33-aa1a-229426fd0b7c","trace":false,"created_at":"2021-05-14 01:19:20.377222Z","presentation_request":{"name":"(샘플)SKT 모바일가입증명 검증","version":"1.0","requested_attributes":{"person_name":{"name":"person_name","restrictions":[{"schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0","cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"}]},"mobile_num":{"name":"mobile_num","restrictions":[{"schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0","cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"}]}},"requested_predicates":{},"nonce":"305818841731447723542848"},"presentation_exchange_id":"d7282a4c-7a1b-4c5b-a1dc-76c61d5cdfd6","presentation_request_dict":{"@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/request-presentation","@id":"6224753f-272b-4a33-aa1a-229426fd0b7c","request_presentations~attach":[{"@id":"libindy-request-presentation-0","mime-type":"application/json","data":{"base64":"eyJuYW1lIjogIihcdWMwZDhcdWQ1MGMpU0tUIFx1YmFhOFx1YmMxNFx1Yzc3Y1x1YWMwMFx1Yzc4NVx1Yzk5ZFx1YmE4NSBcdWFjODBcdWM5OWQiLCAidmVyc2lvbiI6ICIxLjAiLCAicmVxdWVzdGVkX2F0dHJpYnV0ZXMiOiB7InBlcnNvbl9uYW1lIjogeyJuYW1lIjogInBlcnNvbl9uYW1lIiwgInJlc3RyaWN0aW9ucyI6IFt7InNjaGVtYV9pZCI6ICJjVThyRXJqZ0tqOGZnbjFrVERyZW46MjpQZXJzb25JZGVudGl0eUNyZWRlbnRpYWw6MS4wIiwgImNyZWRfZGVmX2lkIjogIlRtaXNuRUFHQlBlVlZEanRBWFBkWXQ6MzpDTDowOnYwMSJ9XX0sICJtb2JpbGVfbnVtIjogeyJuYW1lIjogIm1vYmlsZV9udW0iLCAicmVzdHJpY3Rpb25zIjogW3sic2NoZW1hX2lkIjogImNVOHJFcmpnS2o4ZmduMWtURHJlbjoyOlBlcnNvbklkZW50aXR5Q3JlZGVudGlhbDoxLjAiLCAiY3JlZF9kZWZfaWQiOiAiVG1pc25FQUdCUGVWVkRqdEFYUGRZdDozOkNMOjA6djAxIn1dfX0sICJyZXF1ZXN0ZWRfcHJlZGljYXRlcyI6IHt9LCAibm9uY2UiOiAiMzA1ODE4ODQxNzMxNDQ3NzIzNTQyODQ4In0="}}],"comment":"{\"verification_template_id\":\"b43aba3a-0fb4-4a96-bfaa-9ee532b71cab\"}"},"role":"verifier","auto_present":false,"connection_id":"e112b31d-e6b4-4b80-8a32-bcdc07f5a095","state":"request_sent","topic":"present_proof"}
2021-05-14 10:19:20 [INFO ] [GlobalService.java]sendPresentationRequest(243) : response: {"thread_id":"6224753f-272b-4a33-aa1a-229426fd0b7c","trace":false,"presentation_request_dict":{"@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/request-presentation","@id":"6224753f-272b-4a33-aa1a-229426fd0b7c","request_presentations~attach":[{"@id":"libindy-request-presentation-0","mime-type":"application/json","data":{"base64":"eyJuYW1lIjogIihcdWMwZDhcdWQ1MGMpU0tUIFx1YmFhOFx1YmMxNFx1Yzc3Y1x1YWMwMFx1Yzc4NVx1Yzk5ZFx1YmE4NSBcdWFjODBcdWM5OWQiLCAidmVyc2lvbiI6ICIxLjAiLCAicmVxdWVzdGVkX2F0dHJpYnV0ZXMiOiB7InBlcnNvbl9uYW1lIjogeyJuYW1lIjogInBlcnNvbl9uYW1lIiwgInJlc3RyaWN0aW9ucyI6IFt7InNjaGVtYV9pZCI6ICJjVThyRXJqZ0tqOGZnbjFrVERyZW46MjpQZXJzb25JZGVudGl0eUNyZWRlbnRpYWw6MS4wIiwgImNyZWRfZGVmX2lkIjogIlRtaXNuRUFHQlBlVlZEanRBWFBkWXQ6MzpDTDowOnYwMSJ9XX0sICJtb2JpbGVfbnVtIjogeyJuYW1lIjogIm1vYmlsZV9udW0iLCAicmVzdHJpY3Rpb25zIjogW3sic2NoZW1hX2lkIjogImNVOHJFcmpnS2o4ZmduMWtURHJlbjoyOlBlcnNvbklkZW50aXR5Q3JlZGVudGlhbDoxLjAiLCAiY3JlZF9kZWZfaWQiOiAiVG1pc25FQUdCUGVWVkRqdEFYUGRZdDozOkNMOjA6djAxIn1dfX0sICJyZXF1ZXN0ZWRfcHJlZGljYXRlcyI6IHt9LCAibm9uY2UiOiAiMzA1ODE4ODQxNzMxNDQ3NzIzNTQyODQ4In0="}}],"comment":"{\"verification_template_id\":\"b43aba3a-0fb4-4a96-bfaa-9ee532b71cab\"}"},"role":"verifier","updated_at":"2021-05-14 01:19:20.377222Z","auto_present":false,"connection_id":"e112b31d-e6b4-4b80-8a32-bcdc07f5a095","presentation_exchange_id":"d7282a4c-7a1b-4c5b-a1dc-76c61d5cdfd6","initiator":"self","presentation_request":{"name":"(샘플)SKT 모바일가입증명 검증","version":"1.0","requested_attributes":{"person_name":{"name":"person_name","restrictions":[{"schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0","cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"}]},"mobile_num":{"name":"mobile_num","restrictions":[{"schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0","cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"}]}},"requested_predicates":{},"nonce":"305818841731447723542848"},"created_at":"2021-05-14 01:19:20.377222Z","state":"request_sent"}
2021-05-14 10:19:28 [INFO ] [GlobalService.java]handleEvent(66) : handleEvent >>> topic:present_proof, state:presentation_received, body:{"updated_at":"2021-05-14 01:19:28.021582Z","trace":false,"presentation_request_dict":{"@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/request-presentation","@id":"6224753f-272b-4a33-aa1a-229426fd0b7c","request_presentations~attach":[{"@id":"libindy-request-presentation-0","mime-type":"application/json","data":{"base64":"eyJuYW1lIjogIihcdWMwZDhcdWQ1MGMpU0tUIFx1YmFhOFx1YmMxNFx1Yzc3Y1x1YWMwMFx1Yzc4NVx1Yzk5ZFx1YmE4NSBcdWFjODBcdWM5OWQiLCAidmVyc2lvbiI6ICIxLjAiLCAicmVxdWVzdGVkX2F0dHJpYnV0ZXMiOiB7InBlcnNvbl9uYW1lIjogeyJuYW1lIjogInBlcnNvbl9uYW1lIiwgInJlc3RyaWN0aW9ucyI6IFt7InNjaGVtYV9pZCI6ICJjVThyRXJqZ0tqOGZnbjFrVERyZW46MjpQZXJzb25JZGVudGl0eUNyZWRlbnRpYWw6MS4wIiwgImNyZWRfZGVmX2lkIjogIlRtaXNuRUFHQlBlVlZEanRBWFBkWXQ6MzpDTDowOnYwMSJ9XX0sICJtb2JpbGVfbnVtIjogeyJuYW1lIjogIm1vYmlsZV9udW0iLCAicmVzdHJpY3Rpb25zIjogW3sic2NoZW1hX2lkIjogImNVOHJFcmpnS2o4ZmduMWtURHJlbjoyOlBlcnNvbklkZW50aXR5Q3JlZGVudGlhbDoxLjAiLCAiY3JlZF9kZWZfaWQiOiAiVG1pc25FQUdCUGVWVkRqdEFYUGRZdDozOkNMOjA6djAxIn1dfX0sICJyZXF1ZXN0ZWRfcHJlZGljYXRlcyI6IHt9LCAibm9uY2UiOiAiMzA1ODE4ODQxNzMxNDQ3NzIzNTQyODQ4In0="}}],"comment":"{\"verification_template_id\":\"b43aba3a-0fb4-4a96-bfaa-9ee532b71cab\"}"},"thread_id":"6224753f-272b-4a33-aa1a-229426fd0b7c","presentation_request":{"name":"(샘플)SKT 모바일가입증명 검증","version":"1.0","requested_attributes":{"person_name":{"name":"person_name","restrictions":[{"schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0","cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"}]},"mobile_num":{"name":"mobile_num","restrictions":[{"schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0","cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"}]}},"requested_predicates":{},"nonce":"305818841731447723542848"},"state":"presentation_received","created_at":"2021-05-14 01:19:20.377222Z","auto_present":false,"role":"verifier","connection_id":"e112b31d-e6b4-4b80-8a32-bcdc07f5a095","presentation":{"proof":{"proofs":[{"primary_proof":{"eq_proof":{"revealed_attrs":{"mobile_num":"111444249212870126950404680205889709796474572836794180318995173744548268486974","person_name":"114735663336402277097990206158879556599192671418456242825414219721487748472651"},"a_prime":"3683472764584092812681607912146632508389234460490438513872986938389359227457527571697912423415011481707290603026073183536504898959064321248534753652558890513872467501266404442973773097960455558828142952467443348868218124114508379384450046846166248380933322519079297551737485464782621714851535614504895181246470102894607480305713005157998584694704977182114810059359369103043717130492903342482329932290860644574613672883494555521967331053116760049391530196321702427627179489894351248606661352109283839773439571266140597534627427992424735132214222558140976368445959863967953841125816911001995392780511728054147653540982","e":"8586495162176078184732817735768615101338602897803640548856471086612666686122631965051289080267873718262859855628104382170994060642167176","v":"460071280200132829727786397384992656825560982577870579830357139939599479067746202159694823746885808170987010829761000084481148746805002737173212830453473856553326741198032975173614974317590016045922162683732861340260970502198195837099911503919283124858056837750156133887535108997773591728128189796408060630910586966407618323362290344275564183269782731736477806358072879926307356955472339411937831115586038595563705764075002815653028108271812250536306762046875359105177391038985677917718879617561725542382742073590423018862491012663093199986080757501927432138144428566308720597328103012310477695117644238253706944063735749355538469145410401526175273281405160857793184696496152410630392000434069034232160470506132651982734829621748017311775020065874155954011783450195202797102308348759634853879997091269651295232183388311823571417304771668738046402795201850954606712673486156172526429587264126444478799298777691719098120676","m":{"telecom":"8592732282160645814766892310687859889460456241557417720595899686955438417799359169859555016097566395080070553662164425316288145582263303124230994338197447420504305020940223766119","ci":"13687309894784095089727692885383602638502606850267005267648365569842435806305258457008768028085576223180064084456515199288804028377045829426555863850998747404810619635882104173259","gender":"1714315235322061957020875748916995907643035936929595676183889858116065731671841007010496764707222186110330154222728660825705192984934182010906609160450362736530701258710854133591","is_foreigner":"9843060089424133166510419058520950135589584851492014822507904218376888529122092200700018672631719827962441550165456336365980166162988771060194771443787953353687485009611452124012","date_of_birth":"4451117605775022699735946045635531565876798977727419567985107871156338350288249930990131023995425754087147779353680775300097668057939895868529904790673038202187891829049366001909","master_secret":"15390490185767226262145911924585243965633626937801106461389795563676624830510765051629506327173962592330419344346228270302149849265939892642322861611656467767827560204953239051991","exp_date":"5389874029799386357241629518298436809211133616892358837966542463956364161811439542722012391834218712616356362405900578996539431047629599958467892410004653191437322386949382897086"},"m2":"11344452923137401979908196864216849773287820275733420806004208573620225698385782927152226202121267049847496201075793089455435706736393229628295729754544564301406928373211842722403"},"ge_proofs":[]},"non_revoc_proof":null}],"aggregated_proof":{"c_hash":"104973293242122961475364758468403186572074139959074286600886021938733578743132","c_list":[[29,45,192,233,52,247,76,234,4,226,59,27,68,229,91,225,136,88,125,195,115,215,206,160,229,204,16,24,15,59,231,89,255,221,153,80,62,140,30,77,5,102,104,17,189,131,196,207,76,71,234,173,44,13,78,206,121,190,251,162,193,236,165,100,253,77,120,76,60,2,79,67,35,128,218,89,189,237,244,173,128,37,201,195,223,171,47,17,35,145,86,57,195,134,148,38,114,207,160,206,37,185,9,174,79,55,1,103,227,83,57,21,116,167,53,17,38,61,251,59,3,50,236,237,31,152,196,83,120,252,69,187,102,141,245,128,35,126,136,175,65,95,181,231,32,200,189,219,82,169,153,105,243,69,69,142,111,134,229,232,37,29,4,199,167,245,16,163,49,245,0,92,250,245,181,162,131,225,130,255,39,89,134,105,10,231,73,124,39,159,206,50,250,143,18,39,254,161,205,31,117,79,121,77,47,227,222,160,233,6,225,244,249,19,67,249,186,56,212,12,2,112,134,212,237,96,118,76,175,49,217,131,125,87,72,30,192,103,150,212,65,29,238,60,225,196,62,108,114,81,187,121,192,63,156,118]]}},"requested_proof":{"revealed_attrs":{"person_name":{"sub_proof_index":0,"raw":"김증명","encoded":"114735663336402277097990206158879556599192671418456242825414219721487748472651"},"mobile_num":{"sub_proof_index":0,"raw":"01012345678","encoded":"111444249212870126950404680205889709796474572836794180318995173744548268486974"}},"self_attested_attrs":{},"unrevealed_attrs":{},"predicates":{}},"identifiers":[{"schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0","cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01","rev_reg_id":null,"timestamp":null}]},"initiator":"self","presentation_exchange_id":"d7282a4c-7a1b-4c5b-a1dc-76c61d5cdfd6","topic":"present_proof"}
2021-05-14 10:19:28 [INFO ] [GlobalService.java]handleEvent(66) : handleEvent >>> topic:present_proof, state:verified, body:{"updated_at":"2021-05-14 01:19:28.158113Z","trace":false,"presentation_request_dict":{"@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/present-proof/1.0/request-presentation","@id":"6224753f-272b-4a33-aa1a-229426fd0b7c","request_presentations~attach":[{"@id":"libindy-request-presentation-0","mime-type":"application/json","data":{"base64":"eyJuYW1lIjogIihcdWMwZDhcdWQ1MGMpU0tUIFx1YmFhOFx1YmMxNFx1Yzc3Y1x1YWMwMFx1Yzc4NVx1Yzk5ZFx1YmE4NSBcdWFjODBcdWM5OWQiLCAidmVyc2lvbiI6ICIxLjAiLCAicmVxdWVzdGVkX2F0dHJpYnV0ZXMiOiB7InBlcnNvbl9uYW1lIjogeyJuYW1lIjogInBlcnNvbl9uYW1lIiwgInJlc3RyaWN0aW9ucyI6IFt7InNjaGVtYV9pZCI6ICJjVThyRXJqZ0tqOGZnbjFrVERyZW46MjpQZXJzb25JZGVudGl0eUNyZWRlbnRpYWw6MS4wIiwgImNyZWRfZGVmX2lkIjogIlRtaXNuRUFHQlBlVlZEanRBWFBkWXQ6MzpDTDowOnYwMSJ9XX0sICJtb2JpbGVfbnVtIjogeyJuYW1lIjogIm1vYmlsZV9udW0iLCAicmVzdHJpY3Rpb25zIjogW3sic2NoZW1hX2lkIjogImNVOHJFcmpnS2o4ZmduMWtURHJlbjoyOlBlcnNvbklkZW50aXR5Q3JlZGVudGlhbDoxLjAiLCAiY3JlZF9kZWZfaWQiOiAiVG1pc25FQUdCUGVWVkRqdEFYUGRZdDozOkNMOjA6djAxIn1dfX0sICJyZXF1ZXN0ZWRfcHJlZGljYXRlcyI6IHt9LCAibm9uY2UiOiAiMzA1ODE4ODQxNzMxNDQ3NzIzNTQyODQ4In0="}}],"comment":"{\"verification_template_id\":\"b43aba3a-0fb4-4a96-bfaa-9ee532b71cab\"}"},"verified":"false","thread_id":"6224753f-272b-4a33-aa1a-229426fd0b7c","presentation_request":{"name":"(샘플)SKT 모바일가입증명 검증","version":"1.0","requested_attributes":{"person_name":{"name":"person_name","restrictions":[{"schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0","cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"}]},"mobile_num":{"name":"mobile_num","restrictions":[{"schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0","cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01"}]}},"requested_predicates":{},"nonce":"305818841731447723542848"},"state":"verified","created_at":"2021-05-14 01:19:20.377222Z","auto_present":false,"role":"verifier","connection_id":"e112b31d-e6b4-4b80-8a32-bcdc07f5a095","presentation":{"proof":{"proofs":[{"primary_proof":{"eq_proof":{"revealed_attrs":{"mobile_num":"111444249212870126950404680205889709796474572836794180318995173744548268486974","person_name":"114735663336402277097990206158879556599192671418456242825414219721487748472651"},"a_prime":"3683472764584092812681607912146632508389234460490438513872986938389359227457527571697912423415011481707290603026073183536504898959064321248534753652558890513872467501266404442973773097960455558828142952467443348868218124114508379384450046846166248380933322519079297551737485464782621714851535614504895181246470102894607480305713005157998584694704977182114810059359369103043717130492903342482329932290860644574613672883494555521967331053116760049391530196321702427627179489894351248606661352109283839773439571266140597534627427992424735132214222558140976368445959863967953841125816911001995392780511728054147653540982","e":"8586495162176078184732817735768615101338602897803640548856471086612666686122631965051289080267873718262859855628104382170994060642167176","v":"460071280200132829727786397384992656825560982577870579830357139939599479067746202159694823746885808170987010829761000084481148746805002737173212830453473856553326741198032975173614974317590016045922162683732861340260970502198195837099911503919283124858056837750156133887535108997773591728128189796408060630910586966407618323362290344275564183269782731736477806358072879926307356955472339411937831115586038595563705764075002815653028108271812250536306762046875359105177391038985677917718879617561725542382742073590423018862491012663093199986080757501927432138144428566308720597328103012310477695117644238253706944063735749355538469145410401526175273281405160857793184696496152410630392000434069034232160470506132651982734829621748017311775020065874155954011783450195202797102308348759634853879997091269651295232183388311823571417304771668738046402795201850954606712673486156172526429587264126444478799298777691719098120676","m":{"telecom":"8592732282160645814766892310687859889460456241557417720595899686955438417799359169859555016097566395080070553662164425316288145582263303124230994338197447420504305020940223766119","ci":"13687309894784095089727692885383602638502606850267005267648365569842435806305258457008768028085576223180064084456515199288804028377045829426555863850998747404810619635882104173259","gender":"1714315235322061957020875748916995907643035936929595676183889858116065731671841007010496764707222186110330154222728660825705192984934182010906609160450362736530701258710854133591","is_foreigner":"9843060089424133166510419058520950135589584851492014822507904218376888529122092200700018672631719827962441550165456336365980166162988771060194771443787953353687485009611452124012","date_of_birth":"4451117605775022699735946045635531565876798977727419567985107871156338350288249930990131023995425754087147779353680775300097668057939895868529904790673038202187891829049366001909","master_secret":"15390490185767226262145911924585243965633626937801106461389795563676624830510765051629506327173962592330419344346228270302149849265939892642322861611656467767827560204953239051991","exp_date":"5389874029799386357241629518298436809211133616892358837966542463956364161811439542722012391834218712616356362405900578996539431047629599958467892410004653191437322386949382897086"},"m2":"11344452923137401979908196864216849773287820275733420806004208573620225698385782927152226202121267049847496201075793089455435706736393229628295729754544564301406928373211842722403"},"ge_proofs":[]},"non_revoc_proof":null}],"aggregated_proof":{"c_hash":"104973293242122961475364758468403186572074139959074286600886021938733578743132","c_list":[[29,45,192,233,52,247,76,234,4,226,59,27,68,229,91,225,136,88,125,195,115,215,206,160,229,204,16,24,15,59,231,89,255,221,153,80,62,140,30,77,5,102,104,17,189,131,196,207,76,71,234,173,44,13,78,206,121,190,251,162,193,236,165,100,253,77,120,76,60,2,79,67,35,128,218,89,189,237,244,173,128,37,201,195,223,171,47,17,35,145,86,57,195,134,148,38,114,207,160,206,37,185,9,174,79,55,1,103,227,83,57,21,116,167,53,17,38,61,251,59,3,50,236,237,31,152,196,83,120,252,69,187,102,141,245,128,35,126,136,175,65,95,181,231,32,200,189,219,82,169,153,105,243,69,69,142,111,134,229,232,37,29,4,199,167,245,16,163,49,245,0,92,250,245,181,162,131,225,130,255,39,89,134,105,10,231,73,124,39,159,206,50,250,143,18,39,254,161,205,31,117,79,121,77,47,227,222,160,233,6,225,244,249,19,67,249,186,56,212,12,2,112,134,212,237,96,118,76,175,49,217,131,125,87,72,30,192,103,150,212,65,29,238,60,225,196,62,108,114,81,187,121,192,63,156,118]]}},"requested_proof":{"revealed_attrs":{"person_name":{"sub_proof_index":0,"raw":"김증명","encoded":"114735663336402277097990206158879556599192671418456242825414219721487748472651"},"mobile_num":{"sub_proof_index":0,"raw":"01012345678","encoded":"111444249212870126950404680205889709796474572836794180318995173744548268486974"}},"self_attested_attrs":{},"unrevealed_attrs":{},"predicates":{}},"identifiers":[{"schema_id":"cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0","cred_def_id":"TmisnEAGBPeVVDjtAXPdYt:3:CL:0:v01","rev_reg_id":null,"timestamp":null}]},"initiator":"self","presentation_exchange_id":"d7282a4c-7a1b-4c5b-a1dc-76c61d5cdfd6","topic":"present_proof"}
2021-05-14 10:19:28 [INFO ] [GlobalService.java]handleEvent(105) : - Case (topic:present_proof, state:verified) -> getPresentationResult
2021-05-14 10:19:28 [INFO ] [GlobalService.java]getPresentationResult(248) : proof is not verified
2021-05-14 10:19:28 [INFO ] [GlobalService.java]handleEvent(116) : Web View is not used -> sendCredentialOffer
2021-05-14 10:19:28 [INFO ] [GlobalService.java]handleEvent(66) : handleEvent >>> topic:issue_credential, state:offer_sent, body:{"initiator":"self","credential_definition_id":"DrLbXFSao4Vo8gMfjxPxU1:3:CL:1617698238:81df0010-62b4-45b1-bd00-8d0ad74762fd","created_at":"2021-05-14 01:19:28.449637Z","credential_offer_dict":{"@type":"did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/issue-credential/1.0/offer-credential","@id":"14f835a6-9184-48b6-9957-400db54bb0d8","~thread":{},"offers~attach":[{"@id":"libindy-cred-offer-0","mime-type":"application/json","data":{"base64":"eyJzY2hlbWFfaWQiOiAiTjZyNG5Md0FrY1lVWDhjOEtiOFVmdToyOkNlcnRpZmljYXRlT2ZUT0VJQzo0LjAiLCAiY3JlZF9kZWZfaWQiOiAiRHJMYlhGU2FvNFZvOGdNZmp4UHhVMTozOkNMOjE2MTc2OTgyMzg6ODFkZjAwMTAtNjJiNC00NWIxLWJkMDAtOGQwYWQ3NDc2MmZkIiwgImtleV9jb3JyZWN0bmVzc19wcm9vZiI6IHsiYyI6ICIxMTUzMDkyMDc2NjU0Mjg0MDYxNzk1NDU5OTQzNzM5MTIxODEyODI4MzE1NDUxMTg1NDAwMTQyMjk5NjcyNzE3MDc4OTYyMjQ5MzE1NTAiLCAieHpfY2FwIjogIjEwNTU1NzI3Njk4NjI2NjM0NjM3MDk5MzQyMDQ2MDE5NTQwODA0ODM4OTg4MjYwMzA4Nzk1OTU5NzkwNzQ5NzEwMDQyMDY4NTkzOTM3ODI2NTU1OTkzMjQxNDA5MTM5MDI2NTM2NTAz
```
### 4. 참여사 개인정보 수집 및 이용 약관 조회 전달 구현 안내 
- 모바일 initial App에서 참여사에게 본인확인증명을 제출하기 전 개인정보 수집 및 이용 동의를 받기 위해, 동의서 전달이 필요하다.
- 동의서는 해당기관 법무팀의 검토를 받아야 한다.
- 약관/동의서 전달은 DID Agent의 Message 전달 API를 이용한다. 상세 내용은 Message API 참조.
- Open API 참고 : [Message](https://initial-v2-platform.readthedocs.io/ko/master/open_api_message/)
- 개발 참고 JAVA 예제 :  [약관 표시 java code](https://github.com/sktston/initial-controller-java/blob/main/src/main/java/com/sktelecom/initial/controller/issuer/GlobalService.java#L207)


