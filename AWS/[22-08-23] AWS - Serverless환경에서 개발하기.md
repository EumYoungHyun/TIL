# 크몽 X AWS 클라우드 웨비나

## Serverless 환경에서 개발하기

개발자가 AWS의 서버리스 플랫폼을 이용할때 이용해야하는 툴, 환경설정 등

### 왜 서버리스인가?

- 낮은 인프라 비용 (코드가 실행되는 동안에만 비용 지불)
- 사용량에 따라 비용 조정
- 확장성 및 가용성 포함 (사람이 직접 스케일을 관리할 필요 X)
- 혁신적 개발 속도 (운영 환경에 대한 관리가 필요 없기 떄문에 빠른 개발을 가능케 함)

### AWS 서버리스 서비스 - Lambda를 중점으로 학습

1. function overview
2. 코드 페이지
   코드를 직접 작성할 수 있음,
   CI/CD 연동 가능
3. 테스트 코드 직접 작성
4. 람다를 작성하면 CloudWatch에 자동으로 로그가 남았음,  
   따로 로그 시스템을 만들 필요 없이 기본적으로 로그를 남길 수 있음

### lambda가 실행되는 과정

1. 이벤트 트리거 작동 (lambda의 invoke)
2. 람다의 실행
3. dynamoDB or Kinesis등 다른 솔루션의 호출

lambda를 관리할때 필요한 액세스 제어

1. IAM 리소스 정책을 통해 람다 함수 접근 권한 제어
   - S3를 이용한다면 S3까지 권한을 넣어주어야 함

### S3 권한 부여를 통핸 lambda의 실행

```json
{
  "Records": [
    {
      "eventVersion": "2.0",
      "eventSource": "aws:s3",
      "awsRegion": "us-east-1",
      "eventTime": "1970-01-01T00:00:00.000Z",
      "eventName": "ObjectCreated:Put",
      "userIdentity": {
        "principalId": "EXAMPLE"
      },
      "requestParameters": {
        "sourceIPAddress": "127.0.0.1"
      },
      "responseElements": {
        "x-amz-request-id": "EXAMPLE123456789",
        "x-amz-id-2": "EXAMPLE123/5678abcdefghijklambdaisawesome/mnopqrstuvwxyzABCDEFGH"
      },
      "s3": {
        "s3SchemaVersion": "1.0",
        "configurationId": "testConfigRule",
        "bucket": {
          "name": "eumericano",
          "ownerIdentity": {
            "principalId": "EXAMPLE"
          },
          "arn": "arn:aws:s3:::eumericano"
        },
        "object": {
          "key": "test%2Fkey",
          "size": 1024,
          "eTag": "0123456789abcdef0123456789abcdef",
          "sequencer": "0A1B2C3D4E5F678901"
        }
      }
    }
  ]
}
```

1. S3 이벤트 트리거에 등록하여 S3에 이미지가 올라가면 동작하는 함수 제작

```py
import json
import urllib.parse
import boto3

s3 = boto3.client('s3')


def lambda_handler(event, context):
    #print("Received event: " + json.dumps(event, indent=2))

    # Get the object from the event and show its content type
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'], encoding='utf-8')
    try:
        response = s3.get_object(Bucket=bucket, Key=key)
        print("CONTENT TYPE: " + response['ContentType'])
        return response['ContentType']
    except Exception as e:
        print(e)
        print('Error getting object {} from bucket {}. Make sure they exist and your bucket is in the same region as this function.'.format(key, bucket))
        raise e

```

2. lambda를 실행시켜 dynamoDB를 관리

```py
import json
import urllib.parse
import boto3

table = boto3.resource('dynamodb').Table('awsome20db')

def lambda_handler(event, context):
    #print("Received event: " + json.dumps(event, indent=2))

    # Get the object from the event and show its content type
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'], encoding='utf-8')
    try:
        response = table.put_item(
            Item = {
                'bucketname': bucketname,
                'key': key,
                'createdAt': "2021-01-01 01:01:01"
            }
        )
    except Exception as e:
        print(e)
        print('Error getting labmda function.'.format(key, bucket))
        raise e

```

- 롤 에러, 롤 수정 추가

---

## 프로덕트 관점의 람다

1.  객체 지향 코드 작성
2.  자동화 된 환경

### 람다의 핸들러

- Java의 main method같은 동일한 엔트리 포인트  
  함수를 분할해서 작성,  
  프로젝트 단위로 분할해서 구현해야 함

1. 핸들러

- 함수 구성, lambda관련 코드 및 dependency, 비즈니스 로직 제외

2. 컨트롤러

- 구체적인 비즈니스 로직 분할

3. 서비스

- 외부 통합 및 서비스 추상화, dynamoDB, Kinesis와 통신

## 라이브러리 및 프레임워크 사용

부트스트랩 시간 제한

- 종속성 최소화
- 시작 비용 고려
- 컴파일된 라이브러리를 정적으로 링크

lambda의 고유한 프로세스 과정

1. 코드 다운로드 및 실행 컨텍스트 시작
2. 실행 시간 부트스트랩

- 1 + 2의 시간 = 콜드 스타트
- 라이브러리, 디펜던시가 무거워 질수록 느려짐
- 람다에서는 이를 최소화 하는게 가장 중요한 사항
  - 스프링은 굉장히 무거운 프레임워크다. 콜드 스타트에는 쥐약
  - 시간 = 비용, 가급적이면 가벼운 프레임워크 사용
  - 동적 데이터 로드보다는 정적 데이터 로드 추천
  - AWS Serverless Java Container (Java), AWS Serverless Express (Node js), Zappa(Python) 서비스 지원

3. 코드 시작

## 전통적인 애플리케이션과 서버리스 애플리케이션 관리점의 차이

1. 익숙한 도구를 사용하세요.

- Source Control
- IDE (VSCode 등)
- 빌드 (Maven, gradle 등)
- 배포

* 서버리스 애플리케이션 프레임워크 사용

전통적인 배포 단계
코드 작성 -> 빌드 -> 패키지 -> 배포

서버리스 빌드
코드 빌드 -> 코드 압축 -> S3에 업로드 -> 역할 생성/업데이트 -> lambda 함수 생성 및 할당 -> Trigger 설정 (REST Gateway를 통한 API생성) -> 리소스 생성 -> http매서드 생성 -> Integration 업데이트 -> 테이블 생성

이 모든 과정을 AWS CloudFormation, SAM 템플릿을 통해 쉽게, 자동으로 배포해줄 수 있다.
SAM이 좀 더 사용자 관점적인 템플릿

AWS SAM은 CLI 서비스를 제공함  
serverless, ZAPPA, APEX, SPARTA등
다른 서버리스 프레임워크 옵션을 사용해도 괜찮다

## 코드 repository 구성

람다의 코드는 어느단위로 구성해야 할까?

전통적인 코드의 하나의 repository는 전체 구조를 담는 형식이었다.  
람다는 함수단위일까? 아니면 모놀리스 같은 전체 구조를 담는게 좋을까?

이는 정답이 없다, 서비스의 성격에 따라 조정하면 된다.  
#이미지

#이미지
후자가 SAM등을 통해 개정별로 관리하기 편함

### 서버리스 CI/CD

서버리스 환경에서도 테스트를 별도로 작업하고 배포하는 과정은 기존의 방식과 동일하다.

## 서버리스 애플리케이션 테스트

1. 계층 테스트  
   로컬테스트 -> 원격 통합 테스트 (샌드박스 환경 테스트) -> CI/CD 파이프라인 (자동화된 통합 테스트 진행) -> (필요하다면) 다양한 환경과 정상적인 통신이 되는지 테스트

2. 단위 테스트

   - 로컬 테스트에서 진행하는 기본 테스트
     핸들러 -> 컨트롤러 -> 서비스 구조에서
     비즈니스로직이 들어간 컨트롤러와 외부와 통신하는 서비스로 분리할때 컨트롤러단에서 단위 테스트를 실행하면 된다.
     람다는 기존의 방식과 다르게 분할된 유닛테스트를 쉽게 만들 수 있다.

   - Mocking 옵션
     DynamoDB 모킹등 디비를 모킹할 수 있음

## Lambda의 원격 디버깅

람다는 포트에 연결할 수 없는데, 어떻게 디버깅해야할까?

SAM CLI는 도커 환경 처럼 도커 이미지를 실행할 수 있다.  
SAM CLI를 통해 라인단위 테스팅이 가능하다.
