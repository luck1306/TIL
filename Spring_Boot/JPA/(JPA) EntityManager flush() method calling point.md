
### 일반적인 상황에서의 XXXRepository.save()

|**common method(Bean이나 그외의 test class가 아닌 class의 method)** | **test method (test class의 method)**|
|-|-|
|객체 저장 후 flush() method 자동호출|객체 임의 저장 후 flush() method 미호출|

### @Transactional annotation을 적용한 XXXRespository.save()

|common method( \* )|test method( \* )|
|-|-|
|method 종료 시점에 transaction commit|단위테스트 환경에서 일반적인 상황과 마찬가지로 flush가 호출되지 않는다|
