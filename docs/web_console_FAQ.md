# 개발 FAQ

Q : 연결(Connection ID) 및 DID는 재사용 가능한가?
<br>A : 기술적으로 가능합니다. connection이 생성되면, 기관 사용자(issuer/verifier)는 같은 connection_id 기반으로 새로운 연결을 맺지 않고 재사용 가능합니다. 다만 initial의 정책상 개인의 privacy 보호 및 사용 이력 추적을 방지하기 위해 항상 새로운 connection 및 DID가 생성하도록 설정되어 있습니다.

Q : 증명서(VC)를 사용자당 한개만 발급해야 하는 경우 개발은?
<br>A : 학생증, 자격증등 사용자당 1개만 발급해야 하는경우 이니셜은 별도로 관리하지 않고, 발급기관에서 관리를 해야 합니다. 발급기관 서버에서 사용자 VC 발급 요청이 들어왔때, 기존 발급 유무를 체크하여 재발급 시 기존 VC의 폐기(revoke) 후 발급해야 합니다. 폐기를 하지 않으면 사용자는 기본적으로 복수개의 VC를 가지개 됩니다.

Q : 사용자가 이니셜앱에서 VC 삭제 시 폐기 되는가?
<br>A : 폐기되지 않습니다. VC삭제 시 Wallet에서 사라질 뿐입니다. 다만 사용자가 Wallet을 백업하지 않았다면 복구는 불가능 합니다. 사용자가 VC를 삭제해도 발급기관은 폐기가 가능합니다. 폐기는 검증 기관이 확인할 수 있도록 블록체인 원장에 기록을 하는 행위 입니다.