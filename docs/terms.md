# Definition and Terms

## Agency
 - Peer(Issuer,Verifier,Holder)들의 연결을 지원하는 SKT의 서비스. Initial 기반의 DID 동작은 모두 Agency를 통해서 진행.
 
## Agent
 - Agency 내부에 Peer를 대신해서 wallet 및 message등을 관리하는 프로그램. Client당 한개의 Agent가 생성된다.

## Connection Agent
 - 참여자들과 연결마다 생성되는 특별한 용도의 프로그램.

## Initial Client
 - 이니셜 eco-system의 서비스를 제공 받는 참여자들을 말함. Holder, Issuer, Verifier등 해당. 여기서 서비스 서비스를 제공하는 Server는 Agency를 말함.
 
## Web Console
 - Web 기반 DID 동작을 지원하는 서비스

## Issuer
 - VC/증명서등을 발행하는 주체
 
## Verifier
 - VC를 검증하는 주체

## Holder
 - VC를 보관하고 Self Sovereign을 담당하는 주체.
 
## Peer
 - Issuer,Verifier,Holder등은 DID 세계에서 각각 peer
 
## Credential
 - 검증이 가능한 증명서
 - e.g)신분증, 출입증, 학생증, 계좌증명서등

## Schema
 - Credential의 기본 Template. 권한이 있는 Issuer만 생성 가능하고 Ledger에 기록됨.
 
## Credential Definition
 - 이미 생성된 Schema로 부터 Credential을 발행하기 위한 최종 설정.
 - Revocation 사용 여부, 총 발행량, 버전 정보등이 Ledger에 기록
 - 권한이 있는 Issuer만 생성 가능

## Wallet
 - 사용자의 중요 정보를 담기 위한 공간으로 Client가 직접 보관한다.
 - Key, Credential, 연결정보등을 저장한다.
 
# Keys
DID는 수많은 보안 강화를 위해 많은 Key들을 사용한다

## MasterKey
 - Wallet Key을 암호화 하기 위한 Symmetric Encryption Key. MasterKey는 사용자가 관리해야하는 의무가 있다.
 - 사용자는 MasterKey를 직접 관리해야 하며, 보안 사고시 Rotate key를 통해 key 변경를 해야 한다. 
 
## Wallet Key
 - Wallet의 실제 data를 암호화 할때 사용하는 Symmetric Encryption Key(chacha20-poly1305).
 - MasterKey에 의해서 암호화 되어, Wallet의 Metadata 영역에 저장/관리 된다
 - Wallet key는 총 7개의 Key로 구성되어 있다
 
## DID Key
 - DID를 사용하기 위한 Asymmetric Key로 Wallet에 저장되어 있음(ed25519)
 - Digital Sign, Public Key Encryption에 사용함.
 
### Pairwise DID Key
 - 상대방과 연결에서만 사용하는 DID. Private DID라고 불리기도 함
 
### Enterprise DID (Issuer DID)
 - Blockchain Ledger에 접근하기 위한 용도의 DID
 - Ledger 접근하기 위해 Trustee, Steward, Endorser 권한이 있으며, Transaction을 발생할 수 있다
 - 권한이 없는 사용자는 Read만 가능하다.   

