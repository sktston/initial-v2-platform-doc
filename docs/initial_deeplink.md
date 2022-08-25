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

#### initial & 모바일지갑 공통
- Scheme : initial://reqService?**<span style="color:red">{{Parameter}}</span>**

#### 모바일지갑 전용
- Scheme : initial://mwpService?**<span style="color:red">{{Parameter}}</span>**

#### 1. Deeplink Parameters

  Parameter | M / O | Type | Value |  Description
  --- | :---: | :---: | :---: | ---
  process | 필수 | string | I <br> V <br> E <br> O <br> F | I - Issue, Credential 발급<br> V - Verify, Credential 제출/검증<br> S - Sign, 전자서명 <br> E - 행안부 전자문서 제출 <br> O - OCR scan 문서 제출 <br> F - 추가서류 제출 <br> K – 코스콤 전자서명 전용
  ynCloud | 필수 | String | Y <br>N | Cloud Agent 기관 여부 (Y/N)
  orgName | 필수 | string | 기관명 | 표시하기 원하는 기관명
  oUldUrl | process= 'O' or 'F'가 포함일 경우 필수 | String | http://127.0.0.1/initial/upload.do | OCR 촬영서류 및 기타서류를 제출하기 위한 URL
  seq | 옵션 | String | 고객구분자 | 고객구분자 / 신청번호
  svcPublicDID | process='V' or 'I' 포함일 경우 필수 | String | did:ssw:{{did}}| 기관의 PublicDID
  nonce | 옵션 | String | a123456789b | Issuer or verifier의 nonce
  credDefId | process= 'I'  포함일 경우 필수 | String | cred_def_id | 증명서 ID
  credName | ynCloud=Y 이며 process='V' or 'I' 포함일 경우 필수 | string | 기관명 | 표시하기 원하는 증명서명
  issueCredName | ynCloud=Y 이며 process='K' 일 경우 필수	 | string | 기관명 | 발급 증명서 명
  verifyCredName | ynCloud=Y 이며 process='K' 일 경우 필수	 | string | 기관명 | 검증 증명서 명
  invitationUrl | invitationUrl 혹은 invitation 둘중 하나 필수 | URL | URL | 등록된 invitation url
  invitation | invitationUrl 혹은 invitation 둘중 하나 필수 | string | invitation json | create-invitation으로 생성된 json. <br> `/connections​/create-invitation` 에서 `public=false` 로 생성한 경우 사용
  ocrDocs | process='O' 포함 일 경우 필수 | string | 90000000011 | OCR문서 목록 (별도 코드표 요청) <br> 1개이상 제출시 "_" 로 구분
  govDocs | process='E' 포함 일 경우 필수 | String | 90000000011 | 전자정부 문서 목록 (별도 코드표 요청) <br> 1개이상 제출시 "_" 로 구분
  govWalletAddr | process='E' 포함 일 경우 필수 | String | 지갑 주소 | 제출할 곳의 전자정부 지갑 주소
  etcDocs | process='F' 포함 일 경우 필수 | String | 지갑 주소 | 기타 서류 (카메라 촬영에 의한 서류 – 코드표 참조)<br> 1개이상 제출시 "_" 로 구분
  masking | 필수 | String | Y <br>N | 마스킹 처리 여부
  submitUrl | process= 'E' or 'O' or 'F'가 포함일 경우 필수 | url | http://127.0.0.1/initial/submit.do | 제출 완료를 위한 URL
  callback | 옵션 | String | URL | 제출완료 후 복귀할 deeplink URL
  govIssue | 필수 | String | Y <br>N | "Y"일 경우 발급 후 제출


  - sample : 검증요청 / Cloud Agent 기관 / Public DID / 발행할 Cree_Def_ID / invitation-url
    * 각 parameter value는 urlencoding 해야 함


```
initial://reqService?process=V&ynCloud=Y&orgName=SKT&credName=%ED%98%81%EC%8B%A0%EA%B3%B5%EC%9C%A0%EB%8C%80%ED%95%99&svcPublicDID=did:ssw:39twDfvgTg5ifaPzTQqUxQ&invitationUrl=https%3A%2F%2Fdev-console.myinitial.io%2Fivp%2Fsessions%2F1f288d76-8974-4620-9f5c-fc5f17755135%2Finvitation
```

<br>

#### Parameter 상세 설명 및 예시 

##### process 

- process=VE : 연결 완료되면 Verify를 요청하고, 완료되면 행안부 전자문서를 제출 요청
- process=I : 연결 완료 후 발행 요청

##### ynCloud

- ynCloud=Y : Cloud Agent 사용 기관임을 명시

##### orgName

- orgName=SKT : 회사명

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

##### submitUrl

제출 완료 되면 message를 webhook으로 전달 한다. 상세 문서는 아래 참조
- https://initial-v2-platform.readthedocs.io/ko/master/initial_deeplink/#4

##### invitationUrl

connection에 사용하기 위한 invitation-url을 전달한다.

invitationUrl=https://issue.sktelecom.com/invitation-url

실제 Deeplink 생성시에는 URL-encoded format으로 생성해야 한다

```invitationUrl=https%3A%2F%2Fissue.sktelecom.com%2Finvitation-url```

##### invitation

`/connections​/create-invitation` 에서 `public=false` 로 생성한 경우 사용한다
```
{
  "connection_id": "30cbd096-0fc9-4d12-b668-ad045345485e",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "8f215dba-072b-4ccc-b908-0e95ac4ce98c",
    "recipientKeys": [
      "EjD1ieKnuetMQ8E76pXczHKZYfBbmrXrZUTSAP3nmn9F"
    ],
    "serviceEndpoint": "https://dev-console.myinitial.io/agent/endpoint",
    "imageUrl": "https://kr.object.ncloudstorage.com/dev-image-file/d41d8cd9_cdf0a7c0_1624540317",
    "label": "SKT_Issuer_Demo"
  },
  "alias": "a123456789b", //기관이 특정 사용자를 지칭하기 위해 사용하는 key 값
  "invitation_url": "https://dev-console.myinitial.io/agent/endpoint?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiOGYyMTVkYmEtMDcyYi00Y2NjLWI5MDgtMGU5NWFjNGNlOThjIiwgInJlY2lwaWVudEtleXMiOiBbIkVqRDFpZUtudWV0TVE4RTc2cFhjekhLWllmQmJtclhyWlVUU0FQM25tbjlGIl0sICJzZXJ2aWNlRW5kcG9pbnQiOiAiaHR0cHM6Ly9kZXYtY29uc29sZS5teWluaXRpYWwuaW8vYWdlbnQvZW5kcG9pbnQiLCAiaW1hZ2VVcmwiOiAiaHR0cHM6Ly9rci5vYmplY3QubmNsb3Vkc3RvcmFnZS5jb20vZGV2LWltYWdlLWZpbGUvZDQxZDhjZDlfY2RmMGE3YzBfMTYyNDU0MDMxNyIsICJsYWJlbCI6ICJTS1RfSXNzdWVyX0RlbW8ifQ=="
}
```

위 response는 `public=false` 생성한 Sample이고, 위 `invitation-url` key의 value 값인 `https://dev-console.myini.........` 부분을 아래 invitation parameter를 통해서 전달한다. 

`invitation=https%3A%2F%2Fdev-console.myinitial.io%2Fagent%2Fendpoint%3Fc_i%3DeyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiOGYyMTVkYmEtMDcyYi00Y2NjLWI5MDgtMGU5NWFjNGNlOThjIiwgInJlY2lwaWVudEtleXMiOiBbIkVqRDFpZUtudWV0TVE4RTc2cFhjekhLWllmQmJtclhyWlVUU0FQM25tbjlGIl0sICJzZXJ2aWNlRW5kcG9pbnQiOiAiaHR0cHM6Ly9kZXYtY29uc29sZS5teWluaXRpYWwuaW8vYWdlbnQvZW5kcG9pbnQiLCAiaW1hZ2VVcmwiOiAiaHR0cHM6Ly9rci5vYmplY3QubmNsb3Vkc3RvcmFnZS5jb20vZGV2LWltYWdlLWZpbGUvZDQxZDhjZDlfY2RmMGE3YzBfMTYyNDU0MDMxNyIsICJsYWJlbCI6ICJTS1RfSXNzdWVyX0RlbW8ifQ%3D%3D`

##### callback

발행/검증 완료 후 복귀한 url 주소이다.(URL encoding 필요)

- callback=initial://mainPage



#### 2. 이미지 전송

OCR 인식 정보 및 촬영 된 이미지 전송은 multipart 형식으로 파일을 전송합니다.

현재 파일의 용량은 최대 10MB로 제한합니다. 검증 서버 성능에 따라서 최대 용량은 더 낮아 질 수 있습니다.

##### 암호화(클라이언트)

- AES Symmetric Encryption 사용. 암호화 Key는 Holder의 Pairwise DID를 사용. Encrypted Data와 함께 기관의 Pairwise DID를 제공함.

##### 복호화(서버)

- AES Library 함수를 이용 복호화. 복호화 Key는 Cloud Agent의 API를 통해서 확인.

![encryption](img/cloud_agent_encryption.png)

##### 암호화/복호화 Sample Code

```java
@SneakyThrows
public static byte[] encrypt(ContentAlgorithm alg, String content, byte[] key, byte[] iv) {
        byte[] encoded = Base64.encode(content.getBytes(), Base64.NO_WRAP);
        switch (alg) {
            case AES256GCM:
                return encrypt(ContentAlgorithm.AES256GCM, encoded, key, iv);
            default:
                throw new IllegalArgumentException("this algorithm type hasn't defined.");
        }
    }

@SneakyThrows
public static byte[] encrypt(ContentAlgorithm alg, byte[] content, byte[] key, byte[] iv) {
    if (key == null || iv == null) {
        throw new IllegalArgumentException("key or iv is null.");
    }
    switch (alg) {
        case AES256GCM:
            Key secretKey = new SecretKeySpec(key, "AES");
            AlgorithmParameterSpec ivSpec = new IvParameterSpec(iv);
            Cipher cipher = Cipher.getInstance("AES_256/GCM/NoPadding");
            cipher.init(Cipher.ENCRYPT_MODE, secretKey, ivSpec);
            return cipher.doFinal(content);
        default:
            throw new IllegalArgumentException("this algorithm type hasn't defined.");
    }
}

@SneakyThrows
public static String decrypt(ContentAlgorithm alg, String encryptedContent, byte[] key, byte[] iv) {
    if (key == null || iv == null) {
        throw new IllegalArgumentException("key or iv is null.");
    }
    switch (alg) {
        case AES256GCM:
            Key secretKey = new SecretKeySpec(key, "AES");
            AlgorithmParameterSpec ivSpec = new IvParameterSpec(iv);
            Cipher cipher = Cipher.getInstance("AES_256/GCM/NoPadding");
            cipher.init(Cipher.DECRYPT_MODE, secretKey, ivSpec);
            return new String(Base64.decode(cipher.doFinal(Base64.decode(encryptedContent, Base64.NO_WRAP)), Base64.NO_WRAP));
       default:
            throw new IllegalArgumentException("this algorithm type hasn't defined.");
    }
}
```

##### Request (multipart)

POST { oUldUrl }

```
Content-Type: multipart/form-data; boundary=documents
-- documents
Content-Disposition: form-data; name="docinfo"
{
    "rcvPairwiseDid": "didString",
    "encType": "AES256/GCM",
    "iv" : initial vector를 Base64인코딩한 문자열
    "encData" : "<암호화 대상 Object>를 String 으로 변환 후 Base64 인코딩 후 암호화 적용된 byte[] 를 Hex String으로 변환"
}
-- documents
Content-Disposition:form-data; name="file" filename={fileName} Content-Type: image/jpeg
{{ 이미지의 byte[] 에 암호화 적용된 byte[] }}
-- documents --
```

##### Document Info

###### Body Parameter

Field name | Value | Description
--- | --- | --- 
rcvPairwiseDid	| String |	기관의 Pairwise DID. 암호화 Key를 찾기 위해 사용
iv |	String	| "iv" : initial vector를 Base64인코딩한 문자열
encType | String | 암호화 Type. 기본 AES256/GCM
encData | String | 하기의 <암호화 대상 Object>를 String 으로 변환 후 Base64 인코딩 후 암호화 적용된 byte[] 를 Hex String으로 변환 (Verify, Issue와 동일 로직)

###### 암호화 대상 Object

Field name | Value | Description
--- | --- | --- 
seq	| String |	고객구분자 or 신청서번호
reqDocId |	String	| 이니셜 앱 요청 문서코드
docId | String | 이니셜 앱 전송 코드 (코드표 참조)
ocrInfo | String |	OCR 인식 정보 (OCR 전달 파라메터 참조) <br> 인식된 OCR 이미지의 정보가 없을 경우 (단순 마스킹) null

암호화 대상 Object Sample 
```json
{
    "seq": "1038555586867",
    "reqDocId": "10000000001",
    "docId": "90000000011",
    "ocrInfo": {
        "name": "홍길동",
        "idNo": "111111-1",
        "issueDate": "20201117",
        "authority": "서울특별시 중구청장"
    }
}
```

###### {fileName} 파일명 : 명명 규칙
fileName = seq + "" + reqDocId + "" + docId + 확장자


#### 3. 기관 복호화 Key 획득 방법

기관은 Holder(사용자)가 보내온 암호화 data를 decryption  하기 위해 아래 API를 사용하여 key를 획득합니다.

Cloud Agent pairwise DID 조회 API

- 상세 API는 링크 참조 https://initial-v2-platform.readthedocs.io/ko/master/open_api_auto_connection/#option-connection


##### Sample Request

- parameter my_did에는 전달 받은 rcvPairwiseDid 입력

```curl
curl --location --request GET 'https://dev-console.myinitial.io/agent/api/connections?my_did={rcvPairwiseDid}&state=active' \
--header 'Authorization: Bearer 2ca4dd8a-xxx-421c-bf2b-c5fb0286f2cc'
```

##### Sample Response

아래 응답의 `their_did` 가 복호화 key 이다

```json
{
    "results": [
        {
            "created_at": "2021-05-13 01:28:01.013419Z",
            "rfc23_state": "completed",
            "accept": "auto",
            "state": "active",
            "their_label": "agency",
            "connection_id": "72d709d7-1a8c-4f26-a32c-b700b937e25b",
            "updated_at": "2021-05-13 01:28:05.039245Z",
            "their_role": "inviter",
            "routing_state": "none",
            "their_did": "D7rV3GxtbXuGgzKqUCbAk9", // 복호화 Key
            "invitation_mode": "once",
            "my_did": "QgbxVtztGEcUrGbx5aEpHZ",
            "invitation_key": "81Ebj8szfy9mKbhRtNVypb7NJ2YmTDN7cdm8Xg8wLW7P"
        }
    ]
}
```

- did는 22byte로 32byte를 만들기 위해 뒤에 0으로 padding 합니다.
- 복호화 key 샘플코드

```java
public static byte[] createKey(String did) {
    byte[] encodedDid = did.getBytes();
    byte[] key32Byte = new byte[32];
    if (encodedDid.length < 32) {
        System.arraycopy(encodedDid, 0, key32Byte,0, encodedDid.length);
    } else if (encodedDid.length > 32) {
        System.arraycopy(encodedDid, 0, key32Byte,0, key32Byte.length);
    } else {
        key32Byte = encodedDid;
    }
    return key32Byte;
}
```

#### 4. 문서 제출 완료 

사용자가 문서제출을 완료 했을 때 호출합니다.  (VC기반 Issue/Verify만 전달할 경우에는 호출 하지 않습니다.)

##### 결과 전송

문서 제출 완료 결과는 별도의 API를 호출하지 않고 Cloud Agent의 Message 전송 기능을 사용하여 전달 됩니다.

Sample

```json
{ 
    "seq":"1038555586867",
    "reqDocId":"10000000001",
    "docId":"90000000011",
    "govDocs":{
        "bundleId":"999388811455",
        "reqDocId":[
            "10000000001"
        ],
        "pinCode":"099998",
        "walletAddr":"1DF34115DA312141",
        "masking":"Y",
        "verify":"Y"
    },
    "ocrDocs":[
        {
            "seq":"1038555586867",
            "reqDocId":"10000000001",
            "docId":"90000000011",
            "fileName":"1038555586867_10000000001_189057378234.tiff",
            "masking":"Y",
            "verify":"Y",
            "ocrInfo":{
                "name":"홍길동",
                "idNo":"111111-1",
                "issueDate":"20201117",
                "authority":"서울특별시 중구청장"
            }
        },
        {
            "seq":"1038555586867",
            "reqDocId":"10000000001",
            "docId":"90000000022",
            "fileName":"1038555586867_10000000001_189057378235.tiff",
            "masking":"Y",
            "verify":"Y",
            "ocrInfo":{
                "name":"홍길동",
                "idNo":"111111-1",
                "issueDate":"20201117",
                "authority":"서울특별시 중구청장"
            }
        }
    ],
    "etcDocs":[
        {
            "seq":"1038555586867",
            "reqDocId":"10000000001",
            "masking":"N",
            "verify":"Y",
            "fileName":"1038555586867_10000000001_189057378245.jpeg",
            "docId":"90000000011"
        },
        {
            "seq":"1038555586867",
            "reqDocId":"10000000001",
            "docId":"90000000012",
            "masking":"N",
            "verify":"Y",
            "fileName":"1038555586867_10000000001_189057378255.jpeg",
        }
    ]
 
}
```


Client에서 위의 결과값을 전달하면 기관은 아래와 같은 Event를 Webhook controller를 통해서 받게 됩니다. 아래 content의 string을 decoding 하면 됩니다.


```
{

   "connection_id":"9ac517e7-4381-44ba-8890-d2feacb484df",

   "message_id":"64635b43-cc6b-4dc0-a8ce-40cad4c5cd27",

   "content":"{\"type\":\"initial_summit_doc\",\"content\":{\"seq\":\"1038555586867\",\"reqDocId\":\"10000000001\",\"docId\":\"90000000011\",\"govDocs\":{\"bundleId\":\"999388811455\",\"reqDocId\":[10000000001],\"pinCode\":\"099998\",\"walletAddr\":\"1DF34115DA312141\",\"masking\":\"Y\",\"verify\":\"Y\"},\"ocrDocs\":[{\"seq\":\"1038555586867\",\"reqDocId\":\"10000000001\",\"docId\":\"90000000011\",\"fileName\":\"1038555586867_10000000001_189057378234.tiff\",\"masking\":\"Y\",\"verify\":\"Y\",\"ocrInfo\":{\"name\":\"\ud64d\uae38\ub3d9\",\"idNo\":\"111111-1\",\"issueDate\":\"20201117\",\"authority\":\"\uc11c\uc6b8\ud2b9\ubcc4\uc2dc \uc911\uad6c\uccad\uc7a5\"}},{\"seq\":\"1038555586867\",\"reqDocId\":\"10000000001\",\"docId\":\"90000000022\",\"fileName\":\"1038555586867_10000000001_189057378235.tiff\",\"masking\":\"Y\",\"verify\":\"Y\",\"ocrInfo\":{\"name\":\"\ud64d\uae38\ub3d9\",\"idNo\":\"111111-1\",\"issueDate\":\"20201117\",\"authority\":\"\uc11c\uc6b8\ud2b9\ubcc4\uc2dc \uc911\uad6c\uccad\uc7a5\"}}],\"etcDocs\":[{\"seq\":\"1038555586867\",\"reqDocId\":\"10000000001\",\"masking\":\"N\",\"verify\":\"Y\",\"fileName\":\"1038555586867_10000000001_189057378245.jpeg\",\"docId\":\"90000000011\"},{\"seq\":\"1038555586867\",\"reqDocId\":\"10000000001\",\"docId\":\"90000000012\",\"masking\":\"N\",\"verify\":\"Y\",\"fileName\":\"1038555586867_10000000001_189057378255.jpeg\"}]}}",

   "state":"received",

   "sent_time":"2021-11-05 02:09:02.602966Z"

}
```

#### 5. 문서코드

별도문서로 제공

#### 6. 신분증 OCR Parameter

별도문서로 제공

#### 7. 연동 오류코드 목록

Flag(결과코드) | Message(결과 Message)
--- | ---
0000 | 성공
0001 | 실패
1001 | 파라미터 값이 없습니다.
1002 | 최소값 미만입니다.
1003 | 최대값 초과입니다.
1004 | 패턴이 일치하지 않습니다.
1005 | 타입값이 트립니다.
1006 | 숫자타입이 아닙니다.
1007 | 이메일이 유효하지 않습니다.
2001 | 크레덴셜 DEF ID가 유효하지 않습니다.
2002 | 크레덴셜 어트리뷰트 조회 오류
2003 | DID SIGN이 유효하지 않습니다.
2004 | DID 복호화 오류
3001 | 필수약관에 동의하지 않았습니다.
3002 | 잘못된 약관정보입니다.
7004 | 만료된 access token입니다.
7005 | access token을 확인바랍니다.
9993 | 요청한 정보를 찾을수 없습니다.
9994 | 허용하지 않은 요청입니다.
9995 | 잘못된 요청입니다.
9996 | 허용되지 않는 HTTP 메소드 방식입니다.
9997 | 잘못된 URL로 요청되었습니다.
9998 | DB에러
9999 | 서버에러