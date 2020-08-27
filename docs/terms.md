# Definition and Terms

## 1. 기본 용어

### DID(Decentralized Identifier)
 - 기존 신원확인 방식과 달리 중앙 시스템에 의해 통제되지 않으며 개개인이 자신의 정보에 완전한 통제권을 갖도록 하는 기술. W3C등에서 표준을 작성하고 있다.
 - Public DID - Public 공개하는 DID. 주로 기관등이 Issuer(발급자)로 DID를 Ledger에 공개한다. Initial에서 일반 사용자(Holder)는 DID를 Ledger에 공개하지 않는다.
 - Pairwise DID - Peer-to-Peer 연결을 위해 Private 생성하는 DID이고 오직 연결 상대에게만 공개한다. 매번 연결하다 Pairwise DID가 생성된다.  

### Issuer
 - [VC](#vcverifiable-credential)/증명서등을 발행하는 주체. 주로 신뢰성 있는 기관들이 해당되며 Issuer로 참여하기 위해서는 Blockchain ledger에 등록할 수 있는 권한(Endorser)을 관리자로 부터 획득해야 한다.
 
### Verifier
 - Holder가 제출하는 [VC](#vcverifiable-credential)를 검증하는 주체. 

### Holder
 - [VC](#vcverifiable-credential)를 보관하고 제출하는 Self Sovereign을 담당하는 주체.
 
### Peer
 - Issuer,Verifier,Holder등은 DID 환경에서 모두 동일한 peer에 해당한다.
 
### VC(Verifiable Credential)
 - Issuer(발급자)가 발행한 검증이 가능한 증명서.
 - e.g)신분증, 출입증, 학생증, 계좌증명서등.

### Schema
 - Credential의 기본 Template. 권한이 있는 [Issuer](#issuer)만 생성 가능하고 Ledger에 기록됨. 
 - Ledger에 등록된 Schema는 모든 [Issuer](#issuer)들이 사용 가능함.
 
### Credential Definition
 - 이미 생성된 [Schema](#schema)로 부터 VC를 발행하기 위한 최종 설정.
 - Revocation 사용 여부, 총 발행량, 버전 정보등이 Ledger에 기록.
 - 권한이 있는 Issuer만 생성 가능하고, 생성한 Isser 자신만 사용 가능.

### Wallet
 - 사용자의 중요 정보를 담기 위한 공간.
 - Initial에서 Wallet은 Secret Key 뿐만 아니라 VC, Connection 정보등 많은 data 저장한다.
 

## 2. Keys
DID는 보안 강화를 위해 다양한 종류의 Key들을 사용한다

### MasterKey
 - Wallet Key을 암호화 하기 위한 Symmetric Encryption Key(chacha20-poly1305)
 - 사용자는 MasterKey를 직접 관리해야 하며, 보안 사고시 Rotate key를 통해 변경를 해야 한다. 
 
### Wallet Key
 - Wallet의 실제 data를 암호화 할때 사용하는 Symmetric Encryption Key(chacha20-poly1305).
 - MasterKey에 의해서 암호화 되어, Wallet의 Metadata 영역에 저장/관리 된다
 - Wallet key는 총 7개의 Key로 구성되어 있다
 
### DID Key
 - DID를 사용하기 위한 Asymmetric Key로 Wallet에 저장되어 있음(ed25519)
 - Digital Sign, Public Key Encryption에 사용함.
 
#### Pairwise DID Key
 - 상대방과 연결에서만 사용하는 DID. Private DID라고 불리기도 함.
 
#### Enterprise DID (Issuer DID)
 - Blockchain Ledger에 접근하기 위한 용도의 DID
 - Ledger 접근하기 위해 Trustee, Steward, Endorser 권한이 있으며, Transaction을 발생할 수 있다
 - 권한이 없는 사용자는 Read만 가능하다.  
 
#### VerKey
 - verification key. 공개키로 public key와 동일한 개념이다.
 - DID와 같이 공개된 정보이나, 비밀키 유출 시 rotate-key를 통해 DID는 유지하면서 VerKey를 변경할 수 있다.
   
## 3. Agency 용어
### Agency
 - Agent to Agent 메시징 기본 개념은 중개자(Mediator) 및 중계(Relay)의 요소가 존재하고, Peer(Issuer,Verifier,Holder) Agent들 간의 연결 및 Message 중계를 위해 Agency가 존재. 
 - Initial Platform 기반의 DID 메시지 communication은 모두 SKT Agency를 통해서 동작.
 
### Mediator
  - Agency의 역할은 Relayer(단순 메시지 전달), Mediator(Peer의 서버 역할), Full Function(Cloud Wallet등 모든 기능 포함)등으로 구분가능 하다.
  - Mediator는 Message을 encryption 하여 임시 보관하고, Target으로 전달하는 역할하는 능동적인 일을 하지만, 메시지 내용 확인은 불가능 하다.
  - https://github.com/hyperledger/aries-rfcs/blob/master/concepts/0046-mediators-and-relays/README.md
  
### Cloud Agent
 - Agency 내부에 Peer를 대신해서 wallet 및 message등을 관리하는 프로그램. Client당 한개의 Agent가 생성된다.

### Connection Agent
 - 참여자들과 연결마다 생성되는 특별한 용도의 프로그램.

### Initial Client
 - 이니셜 eco-system의 서비스를 제공 받는 참여자들을 말함. Holder, Issuer, Verifier등 해당. 여기서 서비스 서비스를 제공하는 Server는 Agency를 말함.
 
## 4. Web Console
 - Web GUI 기반 DID 동작을 지원하는 서비스 (업데이트 중)


 
 

