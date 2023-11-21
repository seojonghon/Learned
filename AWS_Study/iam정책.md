# IAM정책

# **IAM(Identity and Access Management)**

- AWS 서비스 및 리소스에 대한 액세스를 안전하게 제어할 수 있는 웹 서비스
- 사용자(user), 그룹(group), 역할(role), 정책(policy)으로 구성
- 리전에 속하는 서비스가 아니고, 글로벌 서비스
- IAM 보안 모범 사례
    - https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/best-practices.html
    - 루트 계정(AWS 회원 가입 시 만들어지는 계정)은 최초 사용자 생성 이후 가능하면 사용하지 말 것
    - 사용자 계정으로 서비스를 사용하고, 사용자는 필요한 최소한의 권한만 부여 (최소 권한의 원칙)
    - 루트 계정과 개별 사용자 계정에 강력한 암호 정책과 멀티팩터 인증(MFA) 적용

## **사용자(user)**

- AWS의 기능과 자원을 이용하는 객체
- 사람 또는 애플리케이션

### **사용자를 식별하는 방법**

1. 사용자 생성 시 지정한 이름
2. 사용자의 고유 식별자
3. ARN(Amazon Resource Name) ⇒ arn:aws:*service*:*region*:*account-id*:*resource* 형식

arn: 모든 ARN의 시작

aws: AWS를 나타내는 고정값

service: 리소스를 제공하는 AWS 서비스

region: 리소스가 위치한 AWS 지역 (선택)

account-id: AWS 계정 ID

resource: 서비스별로 리소스를 고유하게 식별하는 정보

### **사용자의 자격증명 방법**

1. AWS Management Console (https://console.aws.amazon.com/) 암호 ⇒ 사람이 AWS 서비스를 사용하기 위해서 로그인할 때 사용
2. Access Key = Access Key ID + Secret Access Key ⇒ 프로그램 방식으로 서비스를 사용할 때 사용
3. SSH 키 ⇒ AWS에 생성한 EC2 인스턴스로 SSH 접속할 때 사용
4. 서버 인증서

## **사용자 추가 실습**

### **#1 가입한 회원 계정으로 AWS Management Console에 로그인**


### **#2 IAM 서비스로 이동**

![Alt text](img/image-3.png)

### **#3 사용자 생성**

![Alt text](img/image-2.png)

### **#4 사용자 세부 정보 입력**

![Alt text](img/image-4.png)

### **#5 권한 설정 - 권한 정책을 선택하지 않고 다음으로 진행**

![Alt text](img/image-5.png)

### **#6 검토 및 생성**

![Alt text](img/image-6.png)

### **#7 암호를 확인**

![Alt text](img/image-7.png)

### 

![Alt text](img/image-8.png)

### **#9 IAM 서비스로 이동 ⇒ 권한이 없으므로 대시보스에 내용이 출력되지 않는 것을 확인**

![Alt text](img/image-9.png)
### **#10 (원래 브라우저에서) 생성한 사용자의 식별 정보를 확인**

![Alt text](img/image-10.png)

## **사용자 그룹(user group)**

- 여러 사용자에게 **공통으로 권한을 부여**하기 위해서 만든 개념
- 여러 사용자에게 **일관된 권한 정책**을 쉽게 적용하는 것이 가능

## **권한(permission)**

- AWS의 서비스나 자원에 어떤 작업을 수행할 수 있는지 여부를 명시한 규칙

## **정책(policy)**

- 자격 증명이나 리소스와 연결될 때 해당 권한을 정의하는 AWS의 객체
- 권한의 모음으로 **사용자, 사용자 그룹, 역할에 적용**이 가능
- **사용자, 사용자 그룹, 역할에 권한을 직접 적용할 수 없고, 정책을 생성한 후 적용해야 함**
- https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/access_policies.html
    - AWS는 IAM 보안 주체(사용자 또는 역할)가 요청을 보낼 때 정책을 평가
    - 정책에서 권한은 요청이 허용되거나 거부되는지를 결정
    - 대부분의 정책은 AWS에 JSON 문서로 저장
    - 자격 증명 기반 정책, 리소스 기반 정책, 권한 경계, 서비스 제어 정책(SCP), ACL(접근 제어 목록), 세션 정책이라는 6가지 정책 유형을 지원

### **자격 증명 기반 정책**

- 관리형 정책
    - AWS 계정에 속한 **다수**의 사용자, 그룹 및 역할에 독립적으로 연결할 수 있는 자격 증명 기반 정책
    - AWS 관리형 정책과 고객 관리형 정책이 있음 ⇒ https://docs.aws.amazon.com/ko_kr/aws-managed-policy/latest/reference/policy-list.html
    - 독립적으로 존재할 수 있는 정책
    - 여러 자격 증명에서 공유가 가능
- 인라인 정책
    - 단일 사용자, 그룹 또는 역할에 **직접** 추가하는 정책
    - 정책과 자격 증명을 정확히 1:1 관계로 유지하며 자격 증명을 삭제하면 정책이 삭제됨

### **리소스 기반 정책**

- 요청이 적용되는 AWS 리소스 측에 연결하는 정책
- 지정된 보안 주체에 해당 리소스에 대한 특정 작업을 수행할 수 있는 권한을 부여하고 이러한 권한이 적용되는 조건을 정의
- 리소스 기반 정책은 인라인 정책이며, 관리형 리소스 기반 정책은 없음
- 모든 리소스가 지원하는 것은 아님

### **권한 경계**

- 관리형 정책을 사용해 자격 증명 기반 정책이 IAM 엔티티(사용자 또는 역할)에 부여할 수 있는 최대 권한을 설정하기 위한 고급 기능
- 엔티티의 권한 경계 설정을 통해 자격 증명 기반 정책 및 관련 권한 경계 모드에서 허용되는 작업만 수행할 수 있음
- 리소스 기반 정책은 권한 경계에 제한을 받지 않음

![Alt text](img/image-11.png)

![Alt text](img/image-12.png)

![Alt text](img/image-13.png)

![Alt text](img/image-14.png)
![Alt text](img/image-15.png)

![Alt text](img/image-16.png)

![Alt text](img/image-17.png)
![Alt text](img/image-18.png)
![Alt text](img/image-19.png)
**test-user는 권한 경계로 EC2FullAccess 권한을 설쟁했으므로, IAMFullAccess 권한을 가져도 IAM 작업을 수행할 수 없으며, EC2 관련 권한이 설정되어 있지 않으므로 EC2 작업도 할 수 없음 ⇒ 아무것도 할 수 없는 상태**

### **서비스 제어 정책(SCP, Service Control Policies)**

- 조직의 권한을 관리하는데 사용할 수 있는 조직 정책 유형
- 조직의 모든 계정에 사용 가능한 최대 권한에 대한 중앙 제어를 제공

### **ACL(Access Control List, 접근 제어 목록)**

- 리소스에 액세스할 수 있는 다른 계정의 보안 주체를 제어할 수 있는 서비스 정책
- JSON 정책 문서 형식을 사용하지 않은 유일한 정책 유형
- S3, WAF, VPC 등의 서비스에 적용

### **세션 정책**

- 역할 또는 페더레이션 사용자에 대해 임시 세션을 프로그래밍 방식으로 생성할 때 파라미터로 전달하는 고급 정책
- AssumeRole, AssumeRoleWithSAML, AssumeRoleWithWebIdentity API 작업을 사용하여 프로그래밍 방식으로 역할 세션을 생성하고 세션 정책을 전달

## **JSON 정책 문서**

https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/access_policies.html#access_policies-json

## **안전한 권한 부여 방법 ⇒ 최소 권한 부여**

IAM 정책을 생성할 때는 최소 권한 부여의 표준 보안 조언을 따르거나, **작업 수행에 필요한 최소한의 권한만 부여**합니다. 사용자(역할)가 수행해야 하는 작업을 파악한 후 사용자들이 해당 작업만 수행하도록 사용자에 대한 정책을 작성합니다.

https://lh7-us.googleusercontent.com/q5KqSloVENcFPmNqYN3zf1lCZLY16Xaovuo4u01JDVK0tBSgGLIRZFuC8L_XpcrqAYzMVPEJe6EHjaIJiXMPXqaY8KYKHROPNHk7mDDh-kjbH6KnBJxUDg8QhMVuGKDIiIhAaRoxMHOnDyVJR5-FAWQ

### **모든 리소스에 모든 동작을 허용 → 필요 이상의 권한을 부여 → 안전하지 않음**

{

"Resource": "*",

"Action": "*",

"Effect": "Allow"

}

### **DynamoDB 액션을 구체화 ⇒ DynamoDB의 모든 테이블에 대해 read/wirte 가능 → 접근하면 안되는 다른 테이블에 대해서도 접근이 가능하므로 안전하지 않음**

{

"Resource": "*",

"Action": [ "dynamodb:GetItem", "dynamdb:PutItem" ]

"Effect": "Allow"

}

### **특정 DynamoDB 리소스로 한정 ⇒ myDyanmoDBTable에 대해서만 read/write하도록 제한**

{

"Resource": "arn:aws:dynamedb:us-east-1:4234234234234:table/myDyanmoDBTable",

"Action": [ "dynamodb:GetItem", "dynamdb:PutItem" ]

"Effect": "Allow"

}

### **특정 조건을 명시 ⇒ IP 주소가 1.2.3.4에서만 접근할 수 있도록 제한**

{

"Resource": "arn:aws:dynamedb:us-east-1:4234234234234:table/myDyanmoDBTable",

"Action": [ "dynamodb:GetItem", "dynamdb:PutItem" ]

"Effect": "Allow",

"Condition": [

"IpAddress": {

"aws:SourceIp": "1.2.3.4"

}

]

}

## **역할(role)**

- 어떤 행위를 하는 객체에게 여러 정책을 적용한다는 점에서는 사용자와 비슷하지만, 객체가 사용자가 아닌 서비스나 다른 AWS 계정의 사용자라는 차이가 있음
- 사용자가 아닌 **특정 서비스에서 생성한 객체에 권한을 부여**하는데 사용

# **IAM 실습**

User		Group			Permissions				비교

=============	====================	==================================	====================

user-1		S3-Support		S3 읽기 전용 권한			⇐ 관리형 정책을 적용

user-2		EC2-Support		EC2 인스턴스 읽기 전용 권한		⇐ 관리형 정책을 적용

user-3		EC2-Admin		EC2 인스턴스 읽고, 시작, 중단 권한	⇐ 인라인 정책을 적용

## **#1 user-1, user-2, user-3 사용자 생성**

![Alt text](img/image-20.png)

![Alt text](img/image-21.png)

![Alt text](img/image-22.png)

![Alt text](img/image-23.png)

![Alt text](img/image-24.png)
동일한 방식으로 user-2와 user-3를 생성

![Alt text](img/image-25.png)

## **#2 S3-Support, EC2-Support, EC2-Admin 사용자 그룹을 생성**

![Alt text](img/image-26.png)

### **#2-1 S3-Support 그룹에 user-1 사용자를 추가하고 권한 정책 연결 없이 그룹을 생성**

![Alt text](img/image-27.png)

### **#2-2 EC2-Support 그룹에 user-2 사용자를 추가하고 권한 정책 연결 없이 그룹을 생성**

### **#2-3 EC2-Admin 그룹에 user-3 사용자를 추가하고 권한 정책 연결 없이 그룹을 생성**

![Alt text](img/image-28.png)
## **#3 S3-Support 그룹에 S3 읽기 전용 관리형 정책을 추가**

![Alt text](img/image-29.png)

![Alt text](img/image-30.png)

![Alt text](img/image-31.png)


## **#4 EC2-Support 그룹에 EC2 인스턴스 읽기 전용 권한을 설정**

![Alt text](img/image-32.png)

## **#5 EC2-Admin 그룹에 EC2 인스턴스를 읽고, 실행, 중지 권한을 부여 (인라인 정책을 적용)**

![Alt text](img/image-33.png)

![Alt text](img/image-34.png)

![Alt text](img/image-35.png)

![Alt text](img/image-36.png)

## **#6 EC2 인스턴스를 생성**

### **#6-1 리전을 서울로 변경**

![Alt text](img/image-37.png)

### **#6-2 인스턴스 시작 버튼을 클릭**

![Alt text](img/image-38.png)

![Alt text](img/image-39.png)

![Alt text](img/image-40.png)

![Alt text](img/image-41.png)


나머지 설정을 그대로 유지한 상태에서 [인스턴스 시작] 버튼을 클릭

인스턴스 실행을 확인

![Alt text](img/image-42.png)

## **#7 S3 버킷 생성**

버킷 = 파일 저장소

나머지 설정을 그대로 유지한 상태로 [버킷 만들기] 버튼을 클릭

## **#8 생성한 버킷에 파일을 추가 (용량이 작은 파일을 업로드)**

![Alt text](img/image-43.png)

![Alt text](img/image-44.png)

![Alt text](img/image-45.png)

![Alt text](img/image-46.png)

## **#9 (root 계정이 사용하고 있는 브라우저와 다른 브라우저를 이용해서) user-1 사용자로 로그인해서 S3와 EC2 서비스로 접근**

![Alt text](img/image-47.png)

**user-1 사용자는 S3-Support 그룹에 속해 있고, 해당 그룹은 S3 읽기 권한 부여되어 있으므로, EC2 서비스를 사용할 수 없음**


**버킷 목록 조회, 객체 목록 조회, 객체 상세 조회, 객체 다운로드가 가능**

**user-1 사용자는 S3-Support 그룹에 속해있고, 해당 그룹은 S3 읽기 권한만 부여되어 있으므로, 객체를 추가하는 것을 권한이 없어 수행할 수 없음**

## **#10 user-1 사용자 로그아웃 후 user-2 사용자로 로그인**

**user-2 사용자는 EC2-Support 그룹에 속해 있고, 해당 그룹은 EC2 읽기 전용 권한을 가지고 있기 때문에, S3 서비스에 접근하면 오류가 발생하고, EC2 서비스에 접근하면 조회가 가능**

### **S3 서비스로 접근**

![Alt text](img/image-48.png)

### **EC2 서비스로 접근**

![Alt text](img/image-49.png)
![Alt text](img/image-50.png)
**EC2 읽기 전용 권한만 부여되었기 때문에 인스턴스를 중지, 종료하는 것을 불가능**

