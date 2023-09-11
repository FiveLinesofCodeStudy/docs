# 2장 리팩터링 깊게 들여다보기

### 2-1 가독성 및 유지보수성 향상

**유지보수성**

- 유지보수성에 대한 정의가 애매했는데 (대충 알아보기 얼마나 힘들고 고칠때 응집도가 높아서 바꾸기 힘드냐,, 정도로 이해했었음)
- 책에서는 **일부 기능을 변경하기위해 얼마나 많은 후보를 조사해야하는지** 라고 나와있어서 그럴수 있겠다 싶었다.

### 취약하다

- 책에서 취약성의 근원은 보통 ‘전역상태’ 라고 말한다.
- 이때 전역상태란, 우리가 고려한 범위( scope)를 벗어난 것을 의미. → 예상치 못한 범위

<img src="https://github.com/FiveLinesofCodeStudy/docs/assets/40741363/90a9c2fc-5c9a-4476-8984-65b2d9b9b626" width="200"/>



ex. 생각해보기…

- 저번 시간에 우리가 이야기한 클로저에 의한 버그 (클로저의 영향범위 파악 후 개선)
- 예상치 못한 className의 중복 (문화/정책적으로 네이밍 규칙 정하기)
- props로도 충분하다고 생각한 변수가 사실 더 넓은 범위로 사용해야 할 때 (전역상태관리로 리팩토링 필요)
- 보안상 우리가 예상치 못한 취약점
    - url이나 api의 노출로 크리티컬한 데이터를 get 할 수 있다던지,
    - 인증/세션관리가 취약해 사내관련자 말고도 전역으로 이용이 가능하다던지,
    - 암호화가 필요한 정보가 안되어있다던지
    - post 메세지등의 형식으로 헤더 등에 담아 통신시, 노출을 지양해야할 프로퍼티가 get으로 노출되며 사용되고 있다던지

💡또 어떤 것들이 있을까?

---

### 불변속성

요런 것들은 특수한 경우니까 주석이나 문서로 남겨도 될 것 같다.



### 리팩터링 중엔 리팩터링 > 코드 성능

둘다 동시에 가져가긴 확실히 애매한 것 같다.

기능을 우선적으로 구현하고, 더 좋은 알고리즘이나 정책으로 성능만을 위한 리팩토링도 분리하면 큰일을 작게 쪼개서 하는 느낌이라 좋을듯?



### 리팩터링시 블랙박스 주의

→ 서로가 리팩터링 중인 코드를 예약하면 좋다는데 보통 어떻게하지?

그냥 저~~ 할게요~~ 하나? 아님 괜찮은 소통 툴이 있나? ⇒ 지라?


---

### 컴포지션

- 컴포지션을 사용할 때 오버라이딩& 오버로딩을 잘 활용하면 더 깔끔하고 응집도 낮은 코드를 작성할 수 있을 것 같다.
- 오버라이딩 vs 오버로딩

  참고글 : [오버라이딩과-오버로딩-그리고-추상-class](https://doitnow-man.tistory.com/entry/typescript-11-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9%EA%B3%BC-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EC%B6%94%EC%83%81-class)

<details>
<summary>오버라이딩 vs 오버로딩</summary>
<div markdown="1">
  **오버로딩 - 기존에 없는 새로운 메소드를 추가하는 것**

  타입스크립트에서는 method의 이름은 같지만 매개변수의 개수는 동일하게 type은 다르게 정의하여 사용하는 방법입니다

    ```
    - 다른 언어의 Overloading 개념은 method명만 같으면 되지만 typescript는 Overloading을 사용하기 위해서는 함수명과 매개변수의 개수가 같아야 합니다.
    
    => 오 신기하다! JAVA는 그냥 함수명과 매개변수의 개수가 달라도 메소드 추가 가능했었는데 TS는 안되나봄
    
    ```

<img src="https://github.com/FiveLinesofCodeStudy/docs/assets/40741363/a79402ca-0b14-46d9-bb71-121d916f2e3b" width="500" />
 
**오버라이딩 - 상속받은 메소드를 재정의 하는 것**

<img src="https://github.com/FiveLinesofCodeStudy/docs/assets/40741363/f28a79e2-d4ef-4e17-883d-8dbbd08ea2a8" width="500" />

- 타고타고 가서 안봐도 되니 편리할 듯
</div>
</details>



💛 대신  **이런 경우는 컴포지션보다 상속이 좋다고 생각**한다.

- 예전에 프론트는 다 완성이 되었는데 서버가 완성이 안된 경우가 있었다. 이때 서버와 프론트 간에 통신이 필요한 부분에서 꼭 필요한 프로퍼티들을 담은 인터페이스를 만들고, 상속받아 사용했었다.

⇒ 이유는 **변경이 불가능한 것을 장점으로 활용하기 위해! (클래스가 특정 interface의 조건을 만족하는지 체크하기 위해서)**

해당 프로퍼티/type 들을 모두 사용했는지 체크용이기도 했고, 후에 서버가 실제로 완성되었을때 연결 부위만 mockData 에서 실제 Data로 살짝 갈아끼워서 사용!

<img src="https://github.com/FiveLinesofCodeStudy/docs/assets/40741363/58229fea-35c3-434d-ab0e-6ea97418e491" width="300" />

---

<details>
<summary> 추상클래스와 인터페이스의 차이</summary>
<div markdown="1">

**abstract vs interface의 차이는 무엇인가**

- abstract class를 extends한 class는 해당 함수는 오버라이딩할 수 있지만, 하지 않을 수도 있다.
```
// abstract class를 extends한 class는 해당 함수는 오버라이딩할 수 있지만, 하지 않을 수도 있다.
abstract class Creature {
live() {
console.log("I am alive");
}
}

// interface를 implements한 class는 반드시 해당 interface를 구현해야 한다.
interface Talkable {
talk: () => void;
}

class Person extends Creature implements Talkable {
// talk 정의하지 않으면 컴파일 에러 발생
talk() {
console.log("bla bla bla");
}
}

class Animal extends Creature {}
```
</div>
</details>
