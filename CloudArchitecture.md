# Cloud Architecture  
    
  
## 목차

1. 요구사항
2. 구성도
3. 리소스별 IAM Role
  
  
  
## 1. 요구사항  
  
  
1. 각 서비스 계정들 (DEV, TEST, PROD), 관리 계정들(Log, Master, Sec)로 구분되어 있다.
2. 각 서비스 계정들에 존재하는 S3에 접근하는 Role은 S3가 속한 각 계정의 특정한 Role만 허용한다.
3. 각 서비스 계정들의 S3의 접근 기록은 Log 계정의 Log용 S3 한 곳에 저장되어야 한다.
4. 데이터 생산팀의 권한은 S3에 모델을 읽고, 쓰고, 변경하고 삭제할 수 있다.
5. 데이터 분석팀의 권한은 S3에서 모델을 읽을 수 있다
6. 데이터분석팀이 조회 이외의 행위를 시도했을 때 메일로 알림을 받을 수 있어야 한다.

----------------------

## 2. 구성도

* Account 관점 구성도



  ![image](https://user-images.githubusercontent.com/11408378/159255440-b8e81423-4a97-4bba-818c-fb37c0e57c9d.png)
  
  
   ----------------------  
  
  
 * NW 관점 구성도




   ![image](https://user-images.githubusercontent.com/11408378/159674865-59a54064-13f3-453a-be55-b10d78161abe.png)  
   
[인터넷 연동]  
  
① 인터넷 Ingress 트래픽  
  - 일원화된 DMZ VPC에 Internet Gateway 구성
  - DMZ VPC 내 ALB(또는 NLB) 생성, 외부용 
       FW을 통한 전체 Cloud 내부 통제  
         
② 인터넷 Outbound 트래픽  
  - 전체 Outbound 트래픽은 DMZ VPC NAT Gateway를 통해서 트래픽 처리     
  - DMZ VPC 내 외부용 FW을 통해 White-List 기반 트래픽 통제
       
[East-West 통신]  

① VPC-VPC간 통신    
  - VPC별 Transit Gateway Attach  
  - VPC→VPC 통신은 Shared VPC내 내부용 FW을 통해 White-List 기반 통신 제어(TGW 라우팅 테이블 적용)  


② VPC-On-Prem. 통신  
  - On-Prem. <-> AWS 연동은 전용선+Direct Link 구성, TGW Attach 연동  
  - On-Prem. → VPC 통신은 Shared VPC내 내부용 FW을 통해 White-List 기반 통신 제어


[방화벽 구성]  

① 3rd-Party Firewall  
  - ALB/NLB 생성, 방화벽 EC2/License 설정  
  - 기본 Firewall 외 IDS/IPS 고객 Needs 검토  
  
② AWS Network Firewall  
   - 라우팅 Flow는 3rd-Party Firewall과 동일  
   - 실시간 세션 테이블 조회 불가
  
  ----------------------  

* 알람 동작 Flow


  ![image](https://user-images.githubusercontent.com/11408378/159668870-c35c6ac5-0018-49d7-8057-71d52c58bc62.png)  
  
----------------------  

## 3. 리소스별 IAM Role

* 데이터생산팀 EC2  
![image](https://user-images.githubusercontent.com/11408378/159681500-f01a9fd1-166b-4ebb-9160-5b1018f05dcc.png)

#

* 데이터분석팀 EC2  
![image](https://user-images.githubusercontent.com/11408378/159681429-098e86dd-622e-4b71-b0f0-ddcd6b3c634c.png)

#

* 서비스(Dev/Test/Prod)별 S3 버킷 정책  
![image](https://user-images.githubusercontent.com/11408378/159681642-0602301c-dc80-4697-be94-7ea003c6de60.png)

#

* Log S3 버킷 정책  
 ![image](https://user-images.githubusercontent.com/11408378/159681762-9001425a-d174-4528-8343-9a64ca59d486.png)



