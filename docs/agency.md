#### Agency의 역할 및 사용법
 
상세한 정보는 링크 참조 [Mediator Agency in Aries](https://github.com/hyperledger/aries-rfcs/blob/master/concepts/0046-mediators-and-relays/README.md
)

## Agency란?
Agency는 Peer간 연결을 위한 cloud agent를 provisioning하고 영구적으로 사용가능한 endpoint를 생성한다. <br>
그리고 암호화된 Message를 전달하는 Mediator의 역할을 한다.<br>
Peer들의 Cloud Agent가 Agency 내부에 생성되고 Peer들의 각각 서버로서 기능을 하게 된다.<br>
Peer가 Offline 상황에서도 항상 Encrypted Message를 받아 임시보관하고, 전달할 수 있는 기능을 제공한다. 


<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> Initial은 Static endpoint를 가지기 힘든 Mobile(Holder) 전용으로 Agency를 제공하고, Issuer와 Verifier는 전용 Cloud Agent를 별도로 제공 </p>
</div>


![workflow](img/agency_architecture.png)

## Mobile Agency 사용

Hyperledger VCX Protocol(Aries Protocol support) 기반 Agency 사용을 원하는 Mobile App 개발사는 
Auth 인증을 위한 API Key 발급 필요.

contact : jaehyeon.ju@sk.com

## Mobie Agency Workflow
* `Faber` - Issuer/Verifier등 기관. 
* `Alice` - Holder등 사용자.
* `Agency` - Initial Platform의 message 전달을 담당하는 Hub.
* `Agent Entity` - Issuer/Verifier/Holder의 Cloud Agent. Notification을 담당.
* `Connection Agent Entity` - 각각 Pairwise 연결 담당.
![workflow](img/agency_workflow_simple.png)


### Entities
Agnecy는 Entity의 개념을 중심으로 동작하고, 여러 유형의 Entity를 포함.<br> 
각 Entity의 특성은 아래와 같다.<br>

#### Entity definition

- Entity has DID (EntityDID, E-DID)
- Entity has Verkey (EntityVkey, E-Vkey)
- Entity is addressable by DID and by Verkey
- Entity has wallet
- Entity can be read/written using Entity Access Object (AO)
- AO can be be restored from Entity Record
- Entity Record is generated upon initial creation of Entity data.

#### Entity types

- Forward Agent (FWA)
- Agent
- Agent-Connection

##### Entity Forward Agent (FWA)

- 오직 1개의 FWA만 존재함. FWA는 Agency 그 자체로 Initial이 해당됨.
- Agency의 Agent를 대표.
- Agency와 Peer간 초기화 통신의 보안성 확보.
- Agency에 Agent를 생성하는 포인트 제공. 

##### Entity Agent

- Agent is the "Account" a client creates in Agency. Basically mailbox accepting messages on client's behalf.
- Agent can create entities of Agent-Connection type. It represent pairwise connection with 3rd party.
- Agent knows EntityDIDs of its Agent Connections
- Agent knows UserPairwiseDid of its Agent Connections


##### Entity Agent-Connection

- Agent-Connection represent mailbox for pairwise connection with a 3rd party.
- Agent-Connection knows EntityDID of Agent it belongs to.
- Agent-Connection has associated UserPairwiseVKey. Agent-Connection owner must use UserPairwiseVKey to communicate with this entity. 
- Agent-Connection has associated UserPairwiseDID. It is how Agent-Connection owner identifies the pairwise connection with 3rd party.


### Capabilities

#### Entity Agent
- Agent can retrieve messages across all its connections. Message store must require AgentDID for any message retrieval.
- Agent can retrieve messages by Agent-Connection's UserPairwiseDIDs.
