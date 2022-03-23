Application Architecture

----------------  
  

![image](https://user-images.githubusercontent.com/11408378/159664106-8681998a-b23e-43c4-bffa-252ee23a0d24.png)  

     
 1. 적절한 S3 버킷 사용 권한을 가지지 못한 사용자가 S3 버킷 사용을 시도한다.  
   
 2. 비정상적인 접근이 감지되면 Lambda는 SES를 통해 관련 내용을 전파한다.  
   
          * 데이터분석팀이 조회 이외의 행위를 했을 때 cloud-noti@kakaobank.com 로 알람을 발송한다.  
          * 데이터분석팀이 조회 이외의 행위를 했을 때 위반 사실을 콘솔 로그에 남긴다.  
   
 3. IAM 사용자에게 이미 연결된 관리 정책 수가 허용된 수보다 많은 경우 인라인 정책을 추가하여 액세스를 거부한다.
