
## 환경설정

https://www.mongodb.com/ko-kr/docs/manual/tutorial/install-mongodb-on-os-x/

__설치__

```bash
brew tap mongodb/brew
brew update
brew install mongodb-community@8.0
```

__실행__

```bash
brew services start mongodb-community@8.0
```

__중지__

```bash
brew services stop mongodb-community@8.0
```

__연결__

```bash
mongosh
```

---

## Database

- 생성 
```
use DATABASE
```

- 삭제
```
use DATABASE
db.dropDatabase();
```

- 조회
```
show databases;
```


---

## Collection

- 생성 : https://www.mongodb.com/ko-kr/docs/manual/reference/method/db.createCollection/
```
db.createCollection(name, {options});
```

_options_

| Field               | Type    | Description                                                                                 |
| ------------------- | ------- | ------------------------------------------------------------------------------------------- |
| capped(optional)    | boolean | 고정된 크기의 Collection 생성 여부. "size" options이 필수이며, Collection 용량이 size를 초과하면 가장 오래된 데이터부터 덮어씀 |
| autoIndex(optional) | boolean | \_id 필드의 index 생성여부                                                                         |
| size(optional)      | number  | capped 속성의 최대용량 bytes로 지정                                                                   |
| max(optional)       | number  | capped 속성의 최대 Document 수 지정                                                                 |

- 삭제
```
db.COLLECTION.drop();
```

- 조회
```
show collections;
```

---

## Document

### 생성
```
db.COLLECTION.insert(document);
```

### 제거
```
db.COLLECTION.remove(criteria, justOne);
```
_criteria : document 조건문_
justOne(optional) : 한 개(Document) 삭제 여부

---
### 조회
```
db.COLLECTION.find(query, projection);
```

_Comparison Operation_
```
db.COLLECTION.find(
{
	age: {
		$gte: 10, 
		$lte: 19
	}
}
);
```

| operation | description           |
| --------- | --------------------- |
| $eq       | equal                 |
| $gt       | greater than          |
| $gte      | greater than or equal |
| $lt       | less than             |
| $lte      | less than of equal    |
| $ne       | not equal             |
| $in       | values in array       |
| $nin      | values not in array   |

_Logical Operation_
```
db.COLLECTION.find({
	$and: [
		{name: {$eq: 'Chris'}},
		{age: {$in: [18, 19, 20]}}
	]
})
```

| operation |
| --------- |
| $or       |
| $and      |
| $not      |
| $nor      |

_Etc. Operation_

| operaion   | description                 | example                                   | 비고                                                                    |
| ---------- | --------------------------- | ----------------------------------------- | --------------------------------------------------------------------- |
| $elemMatch | Embedded Document Array에 조건 | comments: {$elemMatch: {age: {\$gt: 20}}} | Embedded Document Array인 comments 필드에서 요소의 age가 20을 초과하는 document만 검색 |

_조회 관련 Method_

| method         | description                                           | example                                  | 비고                                           |
| -------------- | ----------------------------------------------------- | ---------------------------------------- | -------------------------------------------- |
| sort(document) | document의 Key(Field)를 Value(Number)(오름차순, 내림차순)으/로 정렬 | ...find().sort({"name": 1, "_id_": -1}); | name을 기준으로 오름차순, \_id를 기준으로 내림차순             |
| limit(number)  | 출력 Document 개수 지정                                     | ...find().limit(5);                      | Document 5개까지 출력                             |
| skip(number)   | 출력에서 제외할 Document 개수 지정                               | ...find().skip(2);                       | 출력할 Document 중 앞에 위치한 2개의 Document를 제외한 후 출력 |

---

### 수정
```
db.COLLECTION.update(criteria, update, options);
```

_options_

| Field            | Type    | Description                                       |
| ---------------- | ------- | ------------------------------------------------- |
| upsert(optional) | boolean | criteria에 부합하는 Document가 존재하지 않을 경우 새 Document 생성 |
| multi(optional)  | boolean | 여러 개의 Document 수정 여부                              |
_Update Operations_

| operaion | description   | example                                              | 비고                                                      |
| -------- | ------------- | ---------------------------------------------------- | ------------------------------------------------------- |
| $set     | 변경하거나 생성 후 저장 | ...update({age: 20}, {$set: {age: 23, score: 10}});  | age가 20인 Document의 age를 23으로 변경 후 score를 10으로 (변경/생성)한다 |
| $unset   | Field 삭제      | ...update({age: 1}, {$unset: {score: true}});        | age가 1인 Document의 score를 삭제                             |
| $push    | 배열 값 추가       | ...update({type: "server"}, {$push: {errors: 404}}); | errors 배열에 404 추가                                       |
| $pull    | 배열 값 제거       | ...update({type: "server"}, {$pull: {errors: 200}}); | errors 배열에서 모든 200 제거                                   |
