# Initial Platform Console API Documents
An API to issue, manage, and verify self-sovereign identity credentials

### Initial Cloud Agent REST APIs

#### Wallet
> 1. `POST` ```/wallet​/did​/create```        : Local non-Public DID 생성
> 2. `GET`  ```/wallet/did ```              : DID list 출력
> 3. `POST` ```/wallet/did/public```        : Public DID를 Assign
> 4. `GET` ```/wallet/did/public```         : 현재 할당된 Public DID 출력
> 5. `POST` ```/wallet/set-did-endpoint```  : DID에 Endpoint setting
> 6. `GET` ```/wallet/set-did-endpoint```   : DID Endpoint 정보 출력
> 7. `PATCH` ```/wallet/did/local/rotate-keypair```    : Rotate keypair for a local non-public DID

#### Connection
> 1. `GET` `/connections` 
