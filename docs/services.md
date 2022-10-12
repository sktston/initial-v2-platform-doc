**initial DID Platform Product and Services**
=========


## **1. initial Web Console**
- Web GUI 기반으로 손쉽게 Agent, Wallet 생성하고, VC(Verifiable Credential)을 발행/검증할 수 있는 기능 제공

    - 디지털 자격 증명을 사용자에게 발행(Issue) 하기 위한 양식 생성 가능
    - 발행된 디지털 자격 증명서를 쉽고 간편하게 검증(Verify) 하기 위한 양식 생성 가능
    - 발행/검증 관련 모든 통계 data 제공
    - 사용자 추가 초대 기능 제공

## **2. 기관(Enterprise) 이용자를 위한 Open API Services**
- REST API 기반 Wallet 생성 및 VC 발행/검증 지원 
  
    - 기존 Legacy system과 REST APIs를 통한 손쉬운 개발 지원
    - REST APIs와 연결을 위한 Guide 지원


## **3. initial SDK & Agent**
- Mobile/Server SDK/Agent 제공 (협의 필요)

    - iOS Native SDK
    - Android Native SDK
    - Java Server SDK(Deprecated)
    - Python Server Agent


## **4. Definition and Terms**

### **기본 용어**

#### - DID(Decentralized Identifier)
- 기존 신원확인 방식과 달리 중앙 시스템에 의해 통제되지 않으며 개개인이 자신의 정보에 완전한 통제권을 갖도록 하는 기술. W3C등에서 표준을 작성하고 있다.
- Public DID : Public 공개하는 DID. 주로 기관등이 Issuer(발급자)로 DID를 Ledger에 공개한다. initial에서 일반 사용자(Holder)는 DID를 Ledger에 공개하지 않는다.
- Pairwise(Private) DID : Peer-to-Peer 연결을 위해 Private 생성하는 DID이고 오직 연결 상대에게만 공개한다. 매번 연결하다 Pairwise DID가 생성된다.

#### - Issuer
- [VC](#vcverifiable-credential)/증명서등을 발행하는 주체. 주로 신뢰성 있는 기관들이 해당되며 Issuer로 참여하기 위해서는 Blockchain ledger에 등록할 수 있는 권한(Endorser)을 관리자로 부터 획득해야 한다.

#### - Verifier
- Holder가 제출하는 [VC](#vcverifiable-credential)를 검증하는 주체.

#### - Holder
- [VC](#vcverifiable-credential)를 보관하고 제출하는 Self Sovereign을 담당하는 주체.

#### - Peer
- Issuer,Verifier,Holder등은 DID 환경에서 모두 동일한 peer에 해당한다.

#### - VC(Verifiable Credential)
- Issuer(발급자)가 발행한 검증이 가능한 증명서.
- e.g)신분증, 출입증, 학생증, 계좌증명서등.

#### - Schema
- Credential의 기본 Template. 권한이 있는 [Issuer](#issuer)만 생성 가능하고 Ledger에 기록됨.
- Ledger에 등록된 Schema는 모든 [Issuer](#issuer)들이 활용하여 Credental 발행 가능함.

모바일가입증명 Schema 예시 

```json
{
    "schema": {
        "ver": "1.0",
        "id": "cU8rErjgKj8fgn1kTDren:2:PersonIdentityCredential:1.0",
        "name": "PersonIdentityCredential",
        "version": "1.0",
        "attrNames": [
            "ci",
            "telecom",
            "date_of_birth",
            "is_foreigner",
            "person_name",
            "mobile_num",
            "gender",
            "exp_date"
        ],
        "seqNo": 1618987943
    }
}
```


#### - Credential Definition
- 이미 생성된 [Schema](#schema)로 부터 VC를 발행하기 위한 최종 설정.
- Revocation 사용 여부, 총 발행량, 버전 정보, 전자서명등이 Ledger에 기록.
- 권한이 있는 Issuer만 생성 가능하고, 생성한 Issuer 자신만 사용 가능.

#### - Wallet
- 사용자의 중요 정보를 담기 위한 Secure 공간.
- initial에서 Wallet은 Secret Key 뿐만 아니라 VC, Connection 정보등 많은 data 저장한다.


### **Keys**
DID는 보안 강화를 위해 다양한 종류의 Key들을 사용한다.

#### - MasterKey
- Wallet Key을 암호화 하기 위한 Symmetric Encryption Key(chacha20-poly1305 기반)

#### - Wallet Key
- Wallet의 실제 data를 암호화 할때 사용하는 Symmetric Encryption Key(chacha20-poly1305).
- MasterKey에 의해서 암호화 되어, Wallet의 Metadata 영역에 저장/관리 된다.
- Wallet key는 총 7개의 Key로 구성되어 있다.

#### - DID Key
- DID를 사용하기 위한 Asymmetric Key로 Wallet에 저장되어 있음(ed25519), Digital Sign, Public Key Encryption에 사용함.
- Pairwise DID : 상대방과 연결(Connection)에서만 사용하는 DID. Private DID라고 불리기도 함.
- Public DID (Primary DID) : Blockchain Ledger에 Transaction(Write)을 발생하기 위한 용도의 DID. Public DID는 Trustee, Steward, Endorser 권한이 있다. 권한이 없는 사용자는 Read만 가능하다.

#### - VerKey
- verification key. 공개키로 public key와 동일한 개념이다.
- DID와 같이 공개된 정보이나, 비밀키 유출 시 rotate-key를 통해 DID는 유지하면서 VerKey를 변경할 수 있다.

### **Agency 용어**
#### - Agency
- Agent to Agent 메시징 기본 개념은 중개자(Mediator) 및 중계(Relay)의 요소가 존재하고, Peer(Issuer,Verifier,Holder) Agent들 간의 연결 및 Message 중계를 위해 Agency가 존재.
- Initial Platform 기반의 DID 메시지 communication은 모두 SKT Agency를 통해서 동작.

#### - Mediator
- Agency의 역할은 Relayer(단순 메시지 전달), Mediator(Peer의 서버 역할), Full Function(Cloud Wallet등 모든 기능 포함)등으로 구분가능 하다.
- Mediator는 Message을 encryption 하여 임시 보관하고, Target으로 전달하는 역할하는 능동적인 일을 하지만, 메시지 내용 확인은 불가능 하다.
- https://github.com/hyperledger/aries-rfcs/blob/master/concepts/0046-mediators-and-relays/README.md

#### - Cloud Agent
- Agency 내부에서 Peer를 대신해서 wallet 및 암호화 message등을 관리하는 기능 제공.

### **Web Console**
- Web GUI 기반 DID Agent 동작을 지원하는 서비스

### **Cryptography (암호기법)**
- Initial에서 사용하는 암호기술

#### - 영지식증명(ZKP or Zero Knowledger Proof)
- 내정보를 공개하지 않고, 참/거짓을 판별할 수 있는 기술
- CL Signature가 사용되고 있으며, 2021년 하반기 BBS+ 지원 예정

#### - Accumulator
- Revocation에서 개인정보를 공개하지 않고 사용되는 암호학적 기술 

 

