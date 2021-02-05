# Web Console

* initial Platform 사용을 위해 회원가입 및 증명서/검증 양식을 생성할 수 있는 GUI 서비스를 제공
* Web상에서 증명서(VC) 발급/검증 하는 서비스 지원 예정(2022년) 

개발망 : https://console_dev.initial.id (2021.2월 open 예정)

상용/운영망 : https://console.initial.id (2021.2월 open 예정)

#### 회원(Account) 권한의 종류

Name | 설명
--- | ---
Master | VC를 발행 권한을 가진 사용자. 기관 생성이 가능.
User | 회원가입하고 Master 신청이 가능한 사용자. 

* 기관(Organization) : Master에 의해서 생성된 Cloud Agent. Cloud Agent 마다 public DID와 API Access Token이 새롭게 할당된다.

#### 기관(Organization)의 권한 종류

Name | 설명
--- | ---
Owner | 생성된 기관의 관리자. Master가 기관 생성시 default 기관의 owner가 되고, 다른 Member에게 위임 가능하다. 양식의 생성이 가능하다.
Member | Owner에 의해서 초대된 사용자. Read only 권한만 가진다.

Web 화면 
![webconsole 1](img/web_console_1.png)
