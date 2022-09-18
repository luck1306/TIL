# unity c# - how to move character

- public GameObject obj;
위 변수는 unity game object를 저장할 수 있는 변수

- publuc Animator animator
위 변수는 적용된 game object의 animator data를 가져옴

- void Start(), void Update()
Start 함수는 스크립트 실행 시 한 번만 실행되는 함수
Update 함수는  스크립트 실행 시 매 프레임 마다 실행되는 함수

### transform

- transform.position
script가 적용된 game object의 position, 위치를 바꾸는 메서드
할당 값으로 new Vector(0, 0, 0) 과 같은 값으로 할당하면 x = 0, y = 0, z = 0의 위치로 이동한다.

- transform.Translate
이 메서드는 지정된 방향으로 움직이게 하는 함수\
Time.deltaTime은 1프레임 당 걸리는 시간이지만 모니터 마다 1프레임 당 걸리는 시간이 다르다.\
예시 : 60fps의 모니터와, 240fps의 모니터\
이를 해결하기 위해 값에 Time.deltaTime을 곱하면 모니터 프레임이 다르다 해도 결과는 같아진다.

- transform.localScale
다음 메소드는 현재 오브젝트의 모양을 제어함
ex) sword_man의 scare 기본값이 1, 1, 1일때 -1, 1, 1로 하면 좌우 반전, 1, -1, 1로 하면 상하 반전, 1, 1, -1로 하면 앞 뒤로 반전된 모습( 예시 : 여러 game object로 이루어진 부모 오브젝트의 scare z축을 반전 시켰을 때 원래 보고 있던 모습의 반대 모습이 보이게 된다.)

## Input

- Input.GetAxis("Horizontal")
방향 키 <-, -> 에 따라서 값이 달라지는 메서드로
<-는 -1, 누르지 않으면 0, ->는 1을 반환한다

---

## In Unity

- Animator\
Make Transition은 연결되는 animation을 지정하는 것이다.\
예시 : Idle -> Run (Idle에서 Run으로 animation 바꾸는게 가능하다)\

**자세한 내용 : https://hoil2.tistory.com/6**