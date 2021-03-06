### 신입 개발자가 느낀 공부와 실무의 차이점

---

아직 햇병아리 신입이지만... 신입으로 느낀 실무와 공부의 차이점을 정리하려고 한다.

물론 회사마다 차이점이 있고, 내가 지금 개발자로써 접하는 것은 정말 일부분이지만

새로운 것을 접해도 언젠가는 당연하게 느껴지기 때문에 글로써 정리를 하려고한다.

### 방대한 스케일(?)

---

처음 회사의 프로젝트 코드를 받았을 때 생각보다 스케일이 커서 놀랐었다.

당연히 혼자서 개인 프로젝트를 했을 때랑은 차원이 다른 양이었다.

프로젝트 안에 있는 폴더의 숫자부터 많았기 때문에 역시 **실무는 다르긴 다르구나** 라는 생각을 했었다.

![클라스 차이](images/class.png)

사실 혼자서 공부를 했을 때는 폴더의 구조나, 코드의 구조 같은 설계에 대한 부분은 크게 신경쓰지 않고 개발을 했었다.

작성한 코드의 양이 늘어나고, 프로젝트의 구조가 복잡해질수록 로직의 구조나, 설계를 잘 해야하는 필요성을 느꼈다.

### 공통 로직의 구현

---

위의 얘기와도 이어지는 내용이다.

프로젝트의 규모가 있으니 공통적으로 하는 작업이 생기기 마련이다.

예를들어 Rest api 구현시에도 응답에 대한 로직을 매번 할 수 없기때문에 범용적인 처리를 해주는 로직이 필요하다.

[rest api 설계](https://imasoftwareengineer.tistory.com/35) 이 블로그를 참고해 주시길....

##### 설명이 원할하지 못한 점.....죄송합니다.

오류에 대한 처리도 필요하고, 이렇게 범용적인 처리를 해주는 로직이 있어야 나중에 모니터링을 위해
Agent를 설정할 때도 비교적 쉽게 할 수 있다.

사실 혼자 이것저것 만들때는 그런걸 크게 신경쓰지 않았던 부분이다.

실무를 접하고 이런 공통적인 처리나, 응답 방식에 대한 부분에 부족함을 느꼈다.

### 매 순간 순간 새로운 일

---

생각지도 못한 요구사항, 프로젝트에 대한 새로운 아이디어 들이 넘쳐나기 때문에 매번 같은 일을 반복하지는 않는다.

처음 들어보는 기술들을 그때 그때 학습하고 적용을 시켜야하는 상황이 빈번하게 일어난다는 걸 느꼈다.

예전 개발 멘토링을 받았던 적이 있는데 그때 멘토분께서

> 알고있는 걸 할때보다 처음보는 걸 할때가 훨신 많다

라는 말씀을 하셨다.

그 당시에는 잘 와닿지 않았던 말씀이었다.

근데 실무를 접해보니 내가 아는 걸 할때보다 모르는 걸 할때가 훨씬 많았다.

자료가 많으면 그나마 수월하지만 없는 자료를 가지고 스스로 부딪혀가며 해결해야할 때가 많다.

새삼스레 공식 문서의 중요성을 느끼게 됬다.

또, 짧은 예제나 친절하지않은 공식 문서를 보며 해당 기능을 유추할 수 있는 능력도 중요한 것 같다.

이래서 개발자는 자신만의 공부 방법을 가지고 있어야한다고 하나보다.

매일 매일 공부를 하고, 부딪혀보는게 중요하다는 걸 느꼈다.

---

이 밖에도 많이 있지만 큰 틀을 생각해보면 위의 3가지 정도가

내가 느낀 공부와 실무의 차이다.

개발자로써 앞으로 더 많은 경험을 하고싶고, 부딪혀 보고싶다.
