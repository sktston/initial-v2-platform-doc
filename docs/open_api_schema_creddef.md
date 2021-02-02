Schema & Credential Definition Example
================

<div class="admonition note">
<p class="admonition-title">note</p>
<p> 기본증명양식(Schema ID)은 initial 관리자가 생성하여 제공함. 아래 양식에 맞춰 관리자에게 신규 생성 요청 가능함  </p>
</div>

예시)

 항목 | 한글 | 영어
 --- | --- | ---
 한글명 | 한글 스키마 제목 (e.g ID카드신청) | 영어 제목 (e.g IDCardApplication) 
 Schema ID (개발)| 생성 후 발급  | (e.g. N6r4nLwAkcYUX8c8Kb8Ufu:2:IDCardApplication:1.0)
 Schema ID (운영) | 생성 후 발급  | (e.g. N6r4nLwAkcYUX8c8Kb8Ufu:2:IDCardApplication:1.0)
 Attribute #1 | 사업장코드 | contract_no
 Attribute #2 | 카드번호  | mid
 Attribute #3 | 사원(학생)번호 | employee_no 
 Attribute #4 | 이름 | name
  
<br><br>

### STEP 1. Create Credential Definition & Revocation Registry.


<div class="admonition note">
<p class="admonition-title">note</p>
<p> Web Console 발행양식생성에서 진행  </p>
</div>

아래와 같이 증명서ID(Credential Definition ID) 정보를 확인 해야함.<br>
해당 정보 기반으로 증명서 발행 가능. 

![webconsole 1](img/creddefid.png)


<br><br>

