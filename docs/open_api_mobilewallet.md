Mobile Wallet Holder API
================

<div class="admonition note">
<p class="admonition-title">note</p>
<p> API 사용을 위해서는 User Access Token이 필요함 </p>
</div>

### STEP 1. User(Access) Token 발급

- 서버연동 Guide 5.1 참고

`POST` `/auth/oauth2/token`

Login(User) is designed for user-login based on the Oauth2 protocol (Type3).

- AUTHORIZATION
    - Basic Auth
    - Username <clientID>
    - Password <clientSecret>

- BODY (urlencoded)
    - username (Required): UID / CI
    - password (Optional) : Auto-filled if nil. Plz hash if filled.
    - grant_type (Required) : password 
    - scope (Required): all (Default timeout : 7200 seconds)


Request Sample 

```
curl --location --request POST 'https://dev.mobile-wallet.co.kr/auth/oauth2/token' \
--header 'Authorization: Basic MmFxxxxxZWU3MWNkM2E6MDRxxxxxTQtNGYxxxxxzVjLTlmYTMxxxxhjYzkzNDg=' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'username=3wxxxx7aQ1q3xxxxmD1tVH1xxxxvTTxxxxx94Txxxx9G' \
--data-urlencode 'password=' \
--data-urlencode 'grant_type=password' \
--data-urlencode 'scope=all'
```


Response Example

```
{
    "access_token": "eyJhbGciOxxxxxY2QzYSIsImV4cCI6MTYxxxxLamo5NFR4WU40OUcifQ.y_c9o2Mx_LihYWCV5xxxxToyQ5TKnEdvxHcUk3-0lTxxx_9OGwkD0HPkd7iw",
    "expires_in": 7200,
    "refresh_token": "MDZHXXXXXWJKXXXXXEXXXC2NDDM",
    "scope": "all",
    "token_type": "Bearer"
}
```


<br><br>


### STEP 2. 증명서 확인

`GET` `/agent/api/credentials`

Fetch credentials from wallet

- AUTHORIZATION
    - Bearer Token : user token (STEP #1)
- Query Parameters
    - count (Optional) : Maximum number to retrieve
    - start (Optional) : Start index
    - wql (Required) : (JSON) WQL query
        
    wql example

    모바일가입증명 Schema ID 요청. (기타 Schema ID는 별도 전달)
    
    ```
    {"schema_id": "cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0"}
    ```


Request Example

```
curl -X 'GET' \
  'https://dev.mobile-wallet.co.kr/agent/api/credentials?wql=%7B%22schema_id%22%3A%20%22cU8rErjgKj8fgn1kTDren%3A2%3APersonIdentityCredential%3A1.0%22%7D' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer eyJhbGcixxxxpXVCJ9.eyJhdWQiOiIyYWZmZWVhMy04YWNlLTQ5OGEtOxxxxxCI6MTY1MjY4MjE4OCwic3Vxxxxa1FnTGdRbUQxdFxxxxxo5NFR4WU40OUcifQ.y_c9o2Mx_LihYWCV55xxFToyQ5TKnEdvxHcUk3-xxAMgExxwkD0HPkd7iw'
```

Response Example

- referent : VC 고유 번호. 유효성 체크할때 사용
- schema_id : 증명서 종류. 학생증 Schema등
- cred_def_id : 증명서 고유 번호. SKT대학 학생증등 기관별로 다름
- rev_reg_id : 증명서 폐기를 위해 기관이 사용
- cred_rev_id : 증명서 폐기를 위해 기관이 사용
- attrs : 증명서의 항목들. Key, Value로 제공 됨. Schema 마다 다름.


```
{
  "results": [
    {
      "referent": "b452xxx6cc-f2c4-xxx-9fad-xxxxxxx",
      "schema_id": "cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
      "cred_def_id": "TmisnEAGxxxxxxjtAXPdYt:3:CL:0:v01",
      "rev_reg_id": null,
      "cred_rev_id": null,
      "attrs": {
        "person_name": "김증명",
        "exp_date": "20230419",
        "mobile_num": "01012345678",
        "date_of_birth": "20000101",
        "ci": "xxxxx/xxxxx/RXZzIyqUvu//xx+xxxxxxx==",
        "gender": "M",
        "is_foreigner": "N",
        "telecom": "SKT"
      }
    }
  ]
}
```

<br><br>



### STEP 3. 증명서 유효성 체크

`GET` `/agent/api/credential/revoked/{credential_id}`

Query credential revocation status by id

- AUTHORIZATION
    - Bearer Token : user token (STEP #1)
- Query Parameters
    - credential_id (Required) : Credential identifier (STEP #2의 `referent`) Example : 3fa85f64-5717-4562-b3fc-2c963f66afa6


Request Example

```
curl -X 'GET' \
  'https://dev.mobile-wallet.co.kr/agent/api/credential/revoked/b452xxx6cc-f2c4-xxx-9fad-xxxxxxx' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer eyJhbGcixxxxpXVCJ9.eyJhdWQiOiIyYWZmZWVhMy04YWNlLTQ5OGEtOxxxxxCI6MTY1MjY4MjE4OCwic3Vxxxxa1FnTGdRbUQxdFxxxxxo5NFR4WU40OUcifQ.y_c9o2Mx_LihYWCV55xxFToyQ5TKnEdvxHcUk3-xxAMgExxwkD0HPkd7iw'
```

Response Example

- revoked : true(폐기됨) | false(유효함)

```
{
  "revoked": true
}
```

<br><br>

