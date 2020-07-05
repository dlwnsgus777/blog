### 객체지향 연습하기 (Java) - 볼링 게임 점수판

---

[지난 포스트](https://pro-dev.tistory.com/53) 에 이어서 점수 계산과 화면 출력에 대해 구현해보겠습니다.

자세한 코드는 [깃 허브](https://github.com/dlwnsgus777/java-oop) 에 저장되어 있습니다.

---

이제 본격적으로 점수 계산 기능을 구현하겠습니다.

10개의 Frame이 자신의 점수를 가지고 있고, 점수에 따른 Frame의 상태

즉, **STRIKE, SPAIRE** 인지 아니면 단순히 **평범하게 점수를 획득한 Frame**인지에 대한 값을 가지게 됩니다.

먼저 **Score** 객체를 수정하겠습니다.

```java
    private int firstShot;
    private int secondShot;
    private int finalShot;
```

기존의 Score 객체가 가지는 상태 값입니다.

int 형 변수를 3개 사용하고 있습니다.

이 3개의 변수는 **점수**라는 하나의 공통된 의미가 있습니다.

이 3개의 변수를 배열로 만들어 하나의 변수로 만들겠습니다.

그리고 추가로 **STATE** 라는 변수를 만들어 현재 FRAME이 획득한 점수가 어떤 것인지

저장하겠습니다.

**Score**

```java
package bowling.domain;

import java.util.Arrays;

public class Score {
    private static final int FIRST_SHOT = 0;
    private static final int SECOND_SHOT = 1;
    private static final int FINAL_SHOT = 2;

    private static final int FINAL_FRAME = 3;
    private static final int NOMAL_FRAME = 2;

    private static final int NOMAL_FRAME_FIRST_TURN = 2;

    private static final int FINAL_FRAME_FIRST_TURN = 3;
    private static final int FINAL_FRAME_SECOND_TURN = 2;

    private int[] shotScores;
    private String state;

    public Score(int frameState) {
        if (frameState == FINAL_FRAME) {
            shotScores = new int[FINAL_FRAME];
        }
        shotScores = new int[NOMAL_FRAME];
        state = "NOMAL";
    }

    public String getState() {
        return state;
    }

    public int[] getShotScore() {
        return shotScores;
    }

    public void setScore(int pinCount, int turn) {
       if (shotScores.length == FINAL_FRAME) {
           finalFrameSetScroe(pinCount, turn);
           return;
       }
       nomalFrameSetScore(pinCount, turn);
    }

    public int getTotalScore() {
        return Arrays.stream(shotScores).sum();
    }

    public boolean hasFinalTurn() {
        return shotScores[FIRST_SHOT] + shotScores[SECOND_SHOT] >= 10;
    }

    private void finalFrameSetScroe(int pinCount, int turn) {
        switch (turn) {
            case FINAL_FRAME_FIRST_TURN:
                shotScores[FIRST_SHOT] = pinCount;
                break;
            case FINAL_FRAME_SECOND_TURN:
                shotScores[SECOND_SHOT] = pinCount;
                break;
            default:
                shotScores[FINAL_SHOT] = pinCount;
                break;
        }
    }

    private void nomalFrameSetScore(int pinCount, int turn) {
        switch (turn) {
            case NOMAL_FRAME_FIRST_TURN:
                shotScores[FIRST_SHOT] = pinCount;
                break;
            default:
                shotScores[SECOND_SHOT] = pinCount;
                break;
        }
    }

    public void calculateState() {
        if (shotScores[FIRST_SHOT] == 10) {
           state = "STRIKE";
            return;
        }

        if (shotScores[FIRST_SHOT] + shotScores[SECOND_SHOT] == 10) {
            state = "SPAIRE";
            return;
        }
    }
}
```

---

이제 Frame 객체를 수정하겠습니다.

Frame 객체는 현재 자신이 몇번째 Frame인지에 대한 변수와

Score 객체에게 메시지 요청을 하기위한 인터페이스를 추가하겠습니다.

**Frame Interface**

```java
package bowling.domain;

public interface Frame {

    boolean hasTurn();

    void playBawling(int pinCount);

    int[] getScore();

    int getTotalScore();

    int getFrameNumber();

    String getState();
}
```
