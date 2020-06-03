### 객체지향 연습하기 (Java) - 볼링 게임 점수판

---

[지난 포스트](https://pro-dev.tistory.com/51) 에 이어서 Frame 클래스를 인터페이스를 이용하여 추상화 시키는 작업을 진행하겠습니다.

---

**Frame Interface**

```java
public interface Frame {

    boolean hasTurn();

    void deductTurn();
}
```

프레임은 공통적으로 투구의 횟수가 남았는지와 투구 후에 턴을 계산하는 메세지를 수신할 수 있습니다.

**NomalFrame**

```java
public class NomalFrame implements Frame {
    private static final int NOMAL_FRAME = 2;
    private static final int TOTAL_PIN = 10;

    private int turn;
    private int firstShot;
    private int secondShot;

    public NomalFrame(int frameNumber) {
        firstShot = 0;
        secondShot = 0;
        turn = NOMAL_FRAME;
    }

    @Override
    public boolean hasTurn() {
        return turn > 0;
    }

    @Override
    public void playBawling(int pinCount) {
        setScore(pinCount);
        setTurn();
    }

    private void setScore(int pinCount) {
        if (!hasTurn()) {
            secondShot = pinCount;
            return;
        }
        firstShot = pinCount;
    }

    private void setTurn() {
        if (firstShot == TOTAL_PIN) {
            turn -= 2;
            return;
        }
        turn--;
    }
}
```

**FinalFrame**

```java
public class FinalFrame implements Frame {
    private static final int FINAL_FRAME = 3;
    private static final int FIRST_SHOT = 3;
    private static final int SECOND_SHOT = 2;
    private static final int FINAL_SHOT = 1;

    private int turn;
    private int firstShot;
    private int secondShot;
    private int finalShot;

    public FinalFrame() {
        turn = FINAL_FRAME;
        firstShot = 0;
        secondShot = 0;
        firstShot = 0;
    }

    @Override
    public boolean hasTurn() {
        return turn > 0;
    }

    @Override
    public void playBawling(int pinCount) {
        setScore(pinCount);
        setTurn();
    }

    private void setScore(int pinCount) {
        switch (turn) {
            case FIRST_SHOT:
                firstShot = pinCount;
                break;
            case SECOND_SHOT:
                secondShot = pinCount;
                break;
            case FINAL_SHOT:
                finalShot = pinCount;
                break;
        }
    }

    private void setTurn() {
        turn--;
    }
}
```

위처럼 Frame 객체를 구현했습니다. 

투구시마다 해당 차례에 대한 점수를 저장하는 방식으로 구현했습니다.

첫번째 투구시에 firstShot에 점수를 저장하고,

두번째 투구시에 secondShot에 점수를 저장하는 방식입니다.

이렇게 한 이유는 스트라이크나 스페어시에 해당 프레임의 점수는 다음 프레임의 점수와 합산하기 때문입니다.

점수 계산을 하려고보니 Frame 객체에서 맡은 책임이 많아지는 것 같습니다.

게다가 

```java
    // FinalFrame의 인스턴스 변수
    private int turn;
    private int firstShot;
    private int secondShot;
    private int finalShot;
```

이렇게 객체 내부의 상태가 많아지게 되어 클래스의 응집도가 낮아지게 됩니다.

여기서 응집도란 해당 기능을 수행하기 위해 연관된 책임과 아이디어가 얼만큼 뭉쳐있는지에 대한 정도입니다.

현재 Frame에는 투구 횟수와 관련된 책임만 가지고,

점수 계산은 다른 객체에게 책임을 위임하겠습니다.

대신, **해당 프레임에서 획득한 점수** 이기 때문에 Frame과 연관 관계를 맺도록 하겠습니다.

**Score**



