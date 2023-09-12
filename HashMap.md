HashMap의 element 저장방식 (bucket)

- capacity(the maximum amount that something can contain) == bucket의 크기
- 한 HashMap객체의 저장된 데이터 수 == capacity * load_factor
라고 할 때

(16 : 14), (17 : 3), (34 : 8), (1 : 9)의 순서대로 HashMap에 삽입된다고 가정한다.
*이때 capacity=16(default value), load_factor=0.75(default value)*

- bucket의 크기 == 16
- 삽입되는 HashMap의 index 구하는 공식
```java
int index = key.hashCode() % capacity;
```
_Integer class의 hashCode는 해당 값(여기서는 key 값)을 그대로 사용함_
_bucket에 저장되는 값은(key, value, next)이다._
- 따라서 첫 번째 HashMap(16 = 14)는 16(key) % 16(capacity) = 0 이므로 0번째 bucket에 저장됩니다.
- 다음 HashMap(17 = 3)도 위와 같이 풀이하면 17(key) % 16(capacity) = 1이므로 1번째 bucket에 저장됩니다.
- 다음 HashMap(34 = 8)은 위와 같이 풀이하면 34(key) % 16(capacity) = 2이므로 2번째 bucket에 저장됩니다.
- 다음 HashMap(1 = 9)을 풀이하면 1(key) % 16(capacity) = 1이므로 1번째 bucket에 저장되는데 기존에 있는(17 = 3)의 앞에 (1 = 9)를 배치 시키고 (1 = 9)의 next값이 (17 = 3)을 참조하도록 함
__위와 같이 저장하는 방식을 Separate Chaining이라고 한다.__

만약 여기서 get() method로 값을 가져올 경우
- get(16)로 데이터를 가져올 때 16(argument) % 16(capacity) = 0이므로 0번째 bucket 조회(하나만 저장되어 있기에 저장된 value을 반환해 준다.)
- get(17)로 데이터를 가져올 때 17(argument) % 16(capacity) = 1이므로 1번째 bucket 조회(해당 bucket에는 두 개 이상의 노드가 있기에 equals method로 비교하고 일치하지 않을 시 next에 참조된 노드를 비교하는 방식으로 17(key)를 찾는다.)

__문제점__
-  만약 위 HashMap에 16만 개의 데이터가 추가 되었을 때 각 bucket마다 1만 개의 노드가 있을 것이다.
	- 그럼 get() 한번에 최악의 경우 1만 번 equals() 해야 한다.
	- 이를 방지하고자 HashMap은 (local_factor == 총 저장된 데이터 수 / capacity)가 될 때 bucket의 개수를 추가한다(약 두배) 
	- load_factor가 작을 수록 capacity가 커지므로 검색속도가 빨라지지만 메모리 크기는 증가한다.
	- load_factor가 클 수록 capacity가 작아지므로 검색속도가 느려지지만 메모리 크기는 감소한다.
	- 그래서 나온 load_factor의 이상적인 수가 0.75(default value)
	- capacity를 증가시키는 방법은 빈 공간이 보다 많이 남아 메모리가 낭비될 것이고, Iterator 상황에서는 성능이 저하됨
- 만약 HashMap에 저장될 데이터 수가 짐작가능하다면 반드시 capacity값을 지정하는것이 좋음
	- 만약 capacity가 16인 HashMap에 저장될 데이터가 16만개일때 capacity증가 작업이 수없이 많이 일어날 것이다.
	- capacity가 증가할 때 마다, 원래 저장되어있던 bucket이 아니라 바뀐 capacity에 맞춰 새 bucket을 할당받고 값을 마이그레이션 해야하는데 이를 __rehashing__ 이라고 한다.