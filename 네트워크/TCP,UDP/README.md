### TCP UDP란

---

**TCP, UDP** 는 **TCP/IP** 모델의 **전송 계층** 에서 사용하는 프로토콜 입니다.

TCP, UDP 모두 패킷을 한 컴퓨터에서 다른 컴퓨터로 전달해주는 IP 프로토콜을 기반으로 구현되어있습니다.

### TCP

TCP는 Transmission Control Protocol의 약자입니다.

네트워크에 연결된 컴퓨터에서 실행되는 프로그램 간의 데이터를 

안정적으로, 순서대로, 에러없이 교환하게 해줍니다.

#### 연결형 서비스

TCP는 연결형 서비스로 가상 회선 방식을 제공합니다.

**3-way handshaking**을 통해 연결을 설정하고

**4-way handshaking**을 통해 연결을 해제합니다.

![3way](images/3way.png)

_3-way handshaking_

##### SYN: synchronize sequence numbers

##### ACK: acknowledgment


1. 클라이언트는 서버에 접속을 요청하는 **SYN** 패킷을 보낸다.
    
    이때 클라이언트는 **SYN/ACK** 응답을 기다리는 **SYN_SENT** 상태가 된다.

2. 서버는 SYN 요청을 받고 클라이언트에게 요청을 수락한다는 ACK와 SYN flay가 설정된 패킷을 전송한다.

    이때 서버는 클라이언트가 다시 ACK로 응답하기를 기다리는 **SYN_RECEIVED**상태가 된다.

3. 클라이언트는 다시 서버에게 ACK를 보내고 이후로는 연결이 이루어지고 데이터가 오고가게 된다. 

    이때 서버의 상태는 **ESTABLISHED**가 된다.


![4way](images/4way.png)

_4-way handshaking_

1. 클라이언트가 연결을 종료하겠다는 **FIN** 플래그를 전송한다.

2. 서버에서는 확인 메시지를 보내고 자신의 통신이 끝날때까지 기다리게 된다.

    이때 서버의 상태는 **TIME_WAIT** 가 된다.

3. 서버의 통신이 끝났으면 연결이 종료되었다고 클라이언트에게 **FIN** 플래그를 전송한다.

4. 클라이언트는 확인했다는 메시지를 보낸다.

#### 흐름제어 (Flow Control)

데이터 처리 속도를 조절하여 수진자의 버퍼 오버플로우를 방지합니다.

데이터를 송신하는 쪽에서 감당이 안될정도로 많은 데이터를 빠르게 보내는 것을 방지합니다.

수신자가 **Window Size** 값을 통해 수신량을 정할 수 있습니다.

#### 혼잡제어 (Congestion Control)

 네트워크 내의 패킷 수가 넘치게 증가하지 않도록 방지합니다.

 정보의 전송량이 많다면 패킷을 조금만 전송하여 혼잡 붕괴 현상이 일어나는 것을 방지합니다.

 #### 신뢰성이 높은 전송 (Reliable Transmission)

 * Dupack-based retransmission

    정상적인 상황에서는 ACK값이 연속적으로 전송되어야 합니다.

    ACK값이 중복으로 올 경우 패킷 이상을 감지하고 재전송을 요청합니다.

* Timeout-based retransmission 

    일정시간동안 ACK 값을 수신하지 못할 경우 재전송을 요청합니다.


#### 전이중, 점대점 방식








