Document version : v2.0.3 (last updated 2021-08-30)

Welcome to initial DID Platform Developer Site
===============


이곳은 Hyperledger Aries Protocol 기반 initial DID Platform 사용자/개발자를 위한 사이트 입니다. 
새로운 디지털 ID 표준인 DID(Decentralized Identifier)와 VC (Verifiable Credentials)를 기반으로 SSI (Self-Sovereign Identity)를 쉽게 구현할 수 있도록 지원합니다.
DID를 통해 신분증/자격증명을 효과적으로 전달할 수 있다면 모든 산업 분야에서 많은 기회가 발생합니다.
우리는 이 기회를 활용하는데 도움이되는 API 및 SDK등 기술을 제공합니다.


## 1. Index

* `Introduction` - Platform 소개 / Release note 
* `Web Console Service` - Web기반 DID Agent를 지원하는 서비스.
* `initial DID Agent API Guide` - initial DID Agent Open API 사용 가이드.
* `Reference - Demo Samples` - DID Demo 동작을 통한 기술 이해.


![platform arch](img/initial_platform_architecture.png)

<br>

## 2. W3C DID/VC Specification Support

- initial은 W3C DID 표준을 준수하고, did:ssw: method는 W3C DID에 공식 등록 되어 있습니다.
    - W3C Decentralized Identifiers (DIDs) v1.0 : https://www.w3.org/TR/did-core/
    - W3C DID methods : <https://www.w3.org/TR/did-spec-registries/#did-methods> (did:ssw:)
    - SSW DID method specification: <https://sktston.github.io/ssw-did/did-method-spec.html>

<p></p>

- initial은 W3C VC Data 모델의 표준을 준수하고 있습니다.
    - W3C Verifiable Credentials Data Model 1.0 : https://www.w3.org/TR/vc-data-model/


<br>

## 3. Open Source Contribute

- initial은 Hyperledger open source 진영에서 활동하고 기여하고 있으며, 호환성을 제공합니다. 
    - Hyperledger Indy(SDK)
    <https://github.com/hyperledger/indy-sdk>
    - Hyperledger Aries Cloud Agent - Python
    <https://github.com/hyperledger/aries-cloudagent-python>
    - SKT Ston Project(initial DID Platform)
    <https://github.com/sktston>
    - Absa Node Agency(Mediator)
    <https://github.com/AbsaOSS/vcxagencynode>


<br>

## 4. Global Standardization

- ToIP(Trust over IP)
<https://trustoverip.org/> <br>
    - The Trust over IP Foundation is defining a complete architecture for Internet-scale digital trust that combines both cryptographic trust at the machine layer and human trust at the business, legal, and social layers.


- GSMA Identity group
<https://www.gsma.com/identity/>

  
## 5. Release note

Name | version | Release date| Description
 --- | --- | --- | --- 
Mobile SDK | v1.12.1 | 2021.04.03 | Android/iOS SDK
Cloud Agent API | 0.6 | 2021.2.28 | Cloud Agent API