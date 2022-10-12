# **개발 FAQ**

<font color=blue>Q : 연결(Connection ID) 및 DID는 재사용 가능한가?</font>
<br>A : 기술적으로 가능합니다. connection이 생성되면, 기관 사용자(issuer/verifier)는 같은 connection_id 기반으로 새로운 연결을 만들지 않고 재사용 가능합니다. 다만 이니셜앱의 정책상 이니셜앱 삭제등으로 기존 connection 정보가 삭제되면 새롭게 생성 됩니다.

<font color=blue>Q : 증명서(VC)를 사용자당 한개만 발급해야 하는 경우 개발은?</font>
<br>A : 학생증, 자격증등 사용자당 1개만 발급해야 하는경우 이니셜은 발급 여부를 별도로 관리하지 않고, 발급기관에서 관리를 해야 합니다. 발급기관 서버에서 사용자 VC 발급 요청이 들어왔때, 해당 사용자의 발급 유무를 체크하여 재발급 시 기존 VC의 폐기(revoke) 후 발급해야 합니다. 폐기를 하지 않으면 사용자는 기본적으로 복수개의 VC를 가지개 됩니다. 폐기(Revoke)
는 발급 기관만 가능합니다.

<font color=blue>Q : 사용자가 이니셜앱에서 VC 삭제 시 폐기 되는가?</font>
<br>A : 폐기되지 않습니다. VC삭제 시 사용자 Wallet에서 사라질 뿐입니다. 사용자가 Wallet을 백업하지 않았다면 복구는 불가능 합니다. 사용자가 VC를 삭제해도 발급기관은 폐기가 가능합니다. 폐기는 검증 기관이 확인할 수 있도록 블록체인 원장에 기록을 하는 행위 입니다.

<font color=blue>Q : 발급/검증 시 기관에서 특정 Nonce를 사용하여 mapping 관리 가능한지?</font>
<br>A : 가능합니다. Invitation 생성 시 public=false 옵션을 사용하면 connection_id 당 alias를 지정할 수 있습니다. 자세한 내용은 connection 가이드를 참고하시면 됩니다.