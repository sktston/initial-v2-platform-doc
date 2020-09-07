Wallet and Create Authorize & DID 
================

**Admin** page에 접근하기 위해서는 Wallet의 생성이 필요
<br>

<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p> Cloud Agent 접속을 위해 Auth 필요. Local demo에서는 직접 생성할 수 있지만, 실제 서비스에서는 관리자에게 생성 요청해야 </p>
</div>
    

<p></p>
Cloud Agent 생성을 위해서는 Authorize 필요 <br>
아래는 local demo 기준 설명 <br>
Demo의 최초 Wallet Auth는 docker-compose.yml 파일의 `admin`으로 세팅되어 있음

```json
 "--wallet-type", "indy",
 "--wallet-name", "admin",
 "--wallet-key", "admin.key",
```

위 정보를 http://localhost:8021/api/doc#/ 접속하여 아래 그림과 같이 authorize 버튼 클릭 후 `admin`을 입력해야 동작 가능
![Authorize](img/authorize.png)

<br>
이후 아래와 같이 `header`값에 `wallet`의 parameter에 wallet name이 있어야 동작 가능

`curl -X GET "http://localhost:8021/wallet" -H "accept: application/json" -H "Wallet: admin"`

<p></p>

### STEP 1. Admin Authorize and Create Wallet.

* Method and Resource

    `POST` `/wallet` Cloud Agent에 새로운 wallet 생성하기
<p></p>

* Parameter

     Name | Description 
     --- | --- 
     seed | Seed used for did derivation (example: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa)
     wallet_key | Master key used for key derivation(example: MySecretKey123)
     wallet_name | Wallet identifier (example: MyNewWallet)            
     wallet_type | string (example: indy)


<p></p>

* Example 

    * input <br>
    `seed` : `aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa`<br>
    `wallet_key` : `MySecretKey123`<br>
    `wallet_name` : `MyNewWallet`<br>
    `wallet_type` : `indy`<br>

<br>
    * Response  (생성된 초대장)
```json
Code 201
Created
```
    
<p></p>
* Next Step
<br>
다시 한번 authorize를 클릭하여 기존 admin은 logout하고 새로 생성한 `MyNewWallet`으로 login 하여 demo 진행     
![Authorize](img/authorize.png)

<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p> Demo에서는 Cloud Agent 재실행 시 Wallet의 data는 모두 초기화  </p>
</div>
<br>
<br>

### STEP 2. Blockchain Node AUTH_MAP Rules.

Node에 접근 하기 위해서는 DID의 Auth Setting이 필요함 <br>

기본적인 Node의 Auth Rule은 아래와 같음

Auth | Description
--- | ---
None (common USER) | 일반 사용자 (Public DID)
“0” (TRUSTEE) | 모든 권한을 부여할 수 있는 Super User. Steward, Endorser 및 network_monitor 권한을 부여할 수 있다.
“2” (STEWARD) | Endorser 권한을 생성할 수 있다.
“101” (ENDORSER) | Node transaction을 할 수 있는 최소 권한. VC를 issue 하기 위해서는 최소 endorser 권한이 필요하다.
“201” (NETWORK_MONITOR) | -

* Method and Resource

   - `POST` `/wallet/did/create` 신규 DID를 Local wallet에 생성할 수 있다.
   - `POST` `/ledger/register-nym` 생성된 DID에 권한을 부여하고 Ledger에 등록한다. 최소 Steward 이상 권한만 가능하다.
   
   
<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> Initial 개발 Cloud Agent에서 계정을 생성하면 기본적으로 Endorser 권한이 부여되어 VC 발급등 가능하다.</p>
</div>