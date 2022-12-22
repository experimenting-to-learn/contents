BLINC - Blockchain Wallet 서비스
======================================

### 시스템 아키텍처(AWS 구축 예시)

Wallet의 Private Key(비공개 키)는 보안 환경에 구축된 KMS(키 관리 시스템)에서만 관리 되도록 하고 KMS 에서 내/외부로 전송하지 않는다. Wallet 의 Private Key를 관리하는데 필요한 자원들은 별도의 보안 계정에 배치해서 격리시킨다. 보안 요구사항에 따라 접근제어를 강화할 수 있는 구조이다.

![reference_architecture](./_static/aws_system_architecture.png)
### 지갑 생성
사용자가 블링크 서비스 계정을 생성하게 되면 블링크 서비스 내에서는 다음과 같은 절차에 걸쳐 사용자의 Wallet(Private Key)를 생성 및 관리한다.

1. Service Account 에서는 사용자의 비밀번호를 전달받아 Wallet Management Account로 Wallet 생성을 요청한다.
    - 사용자의 비밀번호는 Wallet Management Account로 Wallet 생성을 요청한 뒤 Service Account 내의 KMS에서 관리한다.
1. Wallet Management Account에서 생성된 Wallet의 Private Key를 파일화하여 File System에 저장한다.
1. 해당 Private Key File을 사용자의 비밀번호로 암호화한다.
1. 암호화된 Private Key File을 Wallet Management Account 내의 KMS에서 새로운 키를 발급받아 한 번 더 암호화를 수행한다. 
    - 총 2회의 암호화를 수행하고, 각 암호화에 쓰인 키들은 분리되어있는 Service Account 내의 KMS와, Wallet Management Account 내의 KMS에서 각각 관리하기 떄문에 설령 한 쪽의 키가 탈취되더라도 나머지 한 쪽의 키가 안전하게 보관되어있다면 사용자의 Private Key를 지켜낼 수 있다.