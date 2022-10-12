# **Web Console**

* initial DID Platform 사용을 위해 회원가입 및 증명서/검증 양식을 생성할 수 있는 GUI 서비스를 제공합니다

    - 개발망 : <https://dev-console.myinitial.io>

    - 상용/운영망 : <https://console.myinitial.io>

#### **회원(Account) 권한**

Name | 설명
--- | ---
User | 회원가입 후 최초 권한. Master 신청이 가능함. Owner의 초대를 받아 기관의 Member가 되어 정보를 열람 가능. 
Master | VC 발행 권한을 가진 사용자. 기관 생성을 생성하여 Owner 가능.

<br>



#### 기관(Organization) 사용자 권한

* 기관(Organization) : Master에 의해서 생성된 Cloud Agent. Cloud Agent 마다 public DID와 API Access Token이 새롭게 할당된다.
  
Name | 설명
--- | ---
Owner | 기관의 관리자. 기관 생성한 Master가 default owner가 되고, 다른 Member에게 위임 가능하다. 발행/검증 양식의 생성이 가능하다.
Member | Owner에 의해서 초대된 사용자. Read only 권한만 가진다.

<br><br>

#### initial web console 화면

![webconsole 1](img/web_console_1.png)
