BLINC - Blockchain Wallet 서비스
======================================

## 요약

BLINC는 Wallet(지갑)이라는 개념으로 사용되고 있는 Private Key 를 안전하게 관리하는 시스템과 블록체인을 활용하여 비즈니스 케이스를 구현하는 어플리케이션에 필요한 기능을 제공하는 시스템으로 구성된다. Wallet 은 비즈니스 시나리오와 어플리케이션 디자인에 따라 식별된 논리적인 주체를 블록체인에서 데이터 소유의 주체로 식별할 수 있는 메커니즘이다. 예를 들면, 어떤 서비스의 사용자에 Wallet을 연결하고 해당 유저에게 소유 관계가 있는 데이터들을 저장하거나 처리할 수 있다.   

블록체인의 생태계에서 가상 자산을 다루고 있는 경우에 Wallet은 소유자의 직접 관리와 무한 책임의 구조로 구성되는 경우가 많다. 가상 자산의 소유와 사용 권한에 대한 거의 유일한 수단이라는 이유로 다른 주체에 의해서 관리되면 안된다는 원론적인 원칙이 적용 된 것이다. 기존 서비스나 어플리케이션에서 공인 기관이나 서비스를 통해 본인 인증을 하면 분실한 사용자의 보안 정보를 다시 찾아주거나 재발급 할 수 있는 경험들이 일반적으로 제공되고 있는 것을 생각하면, 사용자가 Wallet 을 직접 관리하도록 하는 방식으로는 사용자 경험의 일관성을 유지하기 어렵다.   

BLINC는 블록체인 기술을 활용해서 신규 서비스나 시스템을 디자인 하거나 확장 할 때 사용할 수 있다. Wallet 이라고 하는 시스템 레벨의 요소는 사용자에게 직접적으로 드러내지 않고 안전한 방법으로 Private Key를 관리하면서도 블록체인 기술의 특성인 데이터에 대한 보안과 투명성을 서비스에 도입할 수 있는 것이다.   

BLINC 시스템에서 Private Key 데이터는 별도의 격리된 리소스를 이용해서 관리한다. AWS와 같은 클라우드 서비스를 예로 들면 별도의 계정(Account)에 Private Key 관리에 필요한 자원들을 배치하고 이 계정에 접근할 수 있는 권한은 최소한으로 관리한다. Private Key 는 별도의 개별 Key 로 암호화 하는데, 이 때 사용하는 KEY 는 Private Key 와 격리된 별도의 자원들을 통해서 관리하는 방식으로 보안을 강화한다.   

사용자가 본인의 Wallet을 사용하는 시나리오 에서는 email 이나 SSO 등을 이용한 인증으로 임시 토큰을 발행하고, 토큰의 유효성 검사를 통해 Private Key 에 접근 권한을 관리한다. Private Key 는 보안 환경 밖으로 절대로 전송되지 않는다. 블록체인에서 트랜잭션 수행을 위해 Private Key를 이용한 digital sign 이 필요한 경우 동일한 인증 흐름으로 생성된 sign data 어플리케이션에서 활용할 수 있다.   


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