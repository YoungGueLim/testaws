# Cloud Architecture

NW 관점 구성도 추가 필요(TGW)  
DEV/TEST/PROD 별 VPC 추가  
TGW 넣기  
FW 추가  
S3 Archieve 추가해야되나?  
  
  •	관리가 쉽고, 보안적으로 안전하며, 확장성이 좋으며, 고가용성을 보장하며, 장애를 잘 감내하며, 자동 복구를 하는 클라우드 아키텍처를 설계합니다  
  
  * 서비스 개발팀이 필요한 부분에 대해서 추후 찾아 볼 수 있도록 관련된 Reference를 명기
  
----------------------

## 목차

>1. 요구사항
>2. 구성도
>3. 리소스별 권장 Role



## 1. 요구사항

>* 가정
>  1.	AWS 조직을 여러 개의 계정으로 그 역할을 나누어 관리한다. 각 서비스 계정들 (DEV, TEST, PROD), 관리 계정들(Log, Master, Sec)로 구분되어 있다.
>  2.	EC2들은 이미 생성되어 있다고 가정, EC2 생성 관련 설계는 고려하지 않는다
>  3.	EC2들은 Master 계정에 생성되어있다.



1. 각 서비스 계정들에 존재하는 S3에 접근하는 Role은 S3가 속한 각 계정의 특정한 Role만 허용한다.
2. 각 서비스 계정들의 S3의 접근 기록은 Log 계정의 Log용 S3 한 곳에 저장되어야 한다.
3. 데이터 생산팀의 권한은 S3에 모델을 읽고, 쓰고, 변경하고 삭제할 수 있다.
4. 데이터 분석팀의 권한은 S3에서 모델을 읽을 수 있다
5. 데이터분석팀이 조회 이외의 행위를 시도했을 때 메일로 알림을 받을 수 있어야 한다.


## 2. 구성도

* Account 관점 Architecture



  ![image](https://user-images.githubusercontent.com/11408378/159255440-b8e81423-4a97-4bba-818c-fb37c0e57c9d.png)
  
  
  
  

* 알람 동작 Flow


  ![image](https://user-images.githubusercontent.com/11408378/159253263-a4faba7a-70c7-4b9b-8c3b-eb11774ce284.png)



## 3. 리소스별 권장 Role

* 데이터생산팀 EC2
>{  
    　"Version": "2012-10-17",  
    　"Statement": [  
       　　 {'  
           　　　　 "Sid": "DataMakerAllow",  
            　　　　"Effect": "Allow",  
            　　　　"Action": [  
		　　　　　　"s3:GetObject",  
		　　　　　　"s3:PutObject",  
		　　　　　　"s3:DeleteObject"  
	  　　　　],  
    　　　　"Resource": [  
		    　　　　　　    "arn:aws:s3:::dev-s3-bucket/*",  
	       　　 　　	　　"arn:aws:s3:::test-s3-bucket/*",  
	        　　	　　　　"arn:aws:s3:::prod-s3-bucket/*",  
	　　 　　 ]  
       　　 }  
    　]  
}  

* 데이터분석팀 EC2
>{  
    　"Version": "2012-10-17",  
    　"Statement": [  
       　　 {'  
           　　　　 "Sid": "DataAnalyticsAllow",  
            　　　　"Effect": "Allow",  
            　　　　"Action": "s3:GetObject"  
	  　　　　],  
    　　　　"Resource": [  
		    　　　　　　    "arn:aws:s3:::dev-s3-bucket/*",  
	       　　 　　	　　"arn:aws:s3:::test-s3-bucket/*",  
	        　　	　　　　"arn:aws:s3:::prod-s3-bucket/*",  
	　　 　　 ]  
       　　 }  
    　]  
}  

* 서비스(Dev/Test/Prod)별 S3 버킷 정책
>{  
    　"Version": "2012-10-17",  
    　"Id": "Logging",  
    　"Statement": [  
       　　 {'  
           　　　　 "Sid": "LoggingToLogAccountS3",  
            　　　　"Effect": "Allow",  
            　　　　"Action": ["s3:GetObject", "s3:GetObjectAcl"  
	  　　　　],  
    　　　　"Resource": [  
		    　　　　　　    "arn:aws:s3:::log-s3/*",  
	　　 　　 ]  
       　　 }  
    　]  
}  


