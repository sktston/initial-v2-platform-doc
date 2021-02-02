Wallet and DID 
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


### STEP 1. Create Wallet.

<div class="admonition note">
<p class="admonition-title">note</p>
<p> Wallet은 회원가입 후 기관생성하면 자동으로 생성 </p>
</div>

<br><br>

### STEP 2. Wallet DID 확인

* DID는 크게 두가지 종류가 존재한다.

    - Public DID : Blockchain Ledger에 등록된 DID. 등록되는 정보는 아래와 같다.
    >> Alias: alice.77.8.11@test.com  | `별칭` <br>
    >> Nym: 8icAACfA19raHA2rfdVXo3  | `DID` <br>
    >> Role: ENDORSER  | `Role`  <br>
    >> Verkey: 5CxiP6UCH8CCvdn3ciJHwoRfUkpmobMxQuMUKMCPCjvg | `verkey` <br>
    >> endpoint: http://localhost/agent/endpoint | `endpoint` <br>

    - Local DID : Local에만 존재하고 Pairwise DID(Connection DID) 전용으로 주로 사용된다.
 
 <br>
 
* Method and Resource

    `GET` `/wallet/did`  Wallet내 DID 확인하기 
<p></p>

* Parameter

     Name | Description 
     --- | --- 
     did | 특정 did를 입력하여 찾을 수 있음 
     public | (true/false) Public DID를 선택할 수 있음 
     verkey | 특정 Verkey로 DID를 찾을 수 있음            


<p></p>

* Example 

    * input <br>
    `did` : ` ` <br>
    `verkey` : ` ` <br>
    `public` : ` `
<p>
</p>

* Response

```json
{
    "results": [
        {
            "did": "24cZcWLJH9BPKHXsWEGEfE",
            "verkey": "aaiMDNMekeReMVZirB6Kas9xnDtS6CMGSYmR87Q6uMK",
            "public": false
        },
        {
            "did": "LU1KBb2AGFU5pwV2jBJMcz",
            "verkey": "BcM62BjQdqr9V6BFA1UxaWNzWfsbz6fkykBsPghBVyqQ",
            "public": true
        },
        {
            "did": "N5Q8RMDjchEoLYf5LCdoNr",
            "verkey": "CVFSCoBPVTQFJf5pSG5xGq6FYYJwunKbAWwQz9m4NMDy",
            "public": false
        }
    ]
}
```
    
<p></p>
<div class="admonition note">
<p class="admonition-title">note</p>
<p> Initial 개인 사용자는 Public DID가 존재 하지 않음. 어떠한 개인정보 공개되지 않음 </p>
</div>


<br><br>

### STEP 3. Public DID Ledger 등록하기 


<div class="admonition note">
<p class="admonition-title">note</p>
<p> 각 기관은 Public DID 등록이 필요함. 회원가입 후 기관생성 시 자동 등록 진행 </p>
</div>

VC발행 권한을 가지기 위해서는 블록체인 Node에 대한 접근권한이 필요 <br>

기본적인 Node의 Auth Rule은 아래와 같음

Auth | Description
--- | ---
None (common USER) | 일반 사용자 (Public DID)
“0” (TRUSTEE) | 모든 권한을 부여할 수 있는 Super User. Steward, Endorser 및 network_monitor 권한을 부여할 수 있다.
“2” (STEWARD) | Endorser 권한을 생성할 수 있다.
“101” (ENDORSER) | Node transaction을 할 수 있는 최소 권한. VC를 issue 하기 위해서는 최소 endorser 권한이 필요하다. Initial에 가입한 기관에게 기본으로 주어지는 권한.
“201” (NETWORK_MONITOR) | -
none or User | Public DID가 Ledger에 등록되어 있는 일반 사용자

<br><br>


### STEP 4. 대표 Public DID 변경등록하기 

 <br>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p> 대표 Public DID 변경을 원하면 initial 관리자에게 문의  </p>
</div>


<p></p>
* Method and Resource
    - `POST` `/wallet/did/create` 신규 DID를 Local wallet에 생성 후 해당 DID와 Verkey를 initial 관리자에게 전달
   
   
