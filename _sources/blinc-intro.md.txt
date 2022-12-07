BLINC - Blockchain Wallet 서비스
======================================

### 시스템 아키텍처(AWS 구축 예시)

Wallet의 Private Key(비공개 키)는 보안 환경에 구축된 KMS(키 관리 시스템)에서만 관리 되도록 하고 KMS 에서 내/외부로 전송하지 않는다. Wallet 의 Private Key를 관리하는데 필요한 자원들은 별도의 보안 계정에 배치해서 격리시킨다. 보안 요구사항에 따라 접근제어를 강화할 수 있는 구조이다.

![reference_architecture](./_static/aws_system_architecture.png)