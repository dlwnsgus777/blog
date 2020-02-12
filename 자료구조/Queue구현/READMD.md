### 큐(Queue) 구현하기

이번에는 앞서 설명한 Queue를 구현해보도록 하겠습니다.

구현에 앞서 필요한 기능들을 정리하자면

#### 1. enqueue


큐에 데이터를 삽입합니다.
#### 2. dequeue

큐의 맨 앞에 있는 데이터를 꺼내옵니다.  
꺼내온 뒤에는 큐에서 제거합니다.
#### 3. peek

큐의 맨 앞에 있는 데이터가 뭔지 확인합니다.
#### 4. isEmpty

큐가 비어있는지 확인합니다.

### 구현하기

여기서는 Java의 List를 사용해 구현하도록 하겠습니다. 

```java
import java.util.ArrayList;

public class QuerePractice {

	public static void main(String[] args) {
		Queue queue = new Queue();
		
		// 데이터 넣기
		queue.enqueue(123);
		queue.enqueue("queue");
		
		// 데이터 빼면서 삭제하기
		queue.peek();	// 123
		System.out.println(queue.dequeue());	// "123"
		System.out.println(queue.dequeue());	// "queue"
		
		// 큐가 비어있는지 확인하기
		System.out.println(queue.isEmpty());	// true -> 비어있다
		
		// 비어 있는 큐에서 데이터를 가져오려고 할때
		System.out.println(queue.dequeue());
	}
}

// 큐 구현
class Queue {
	private ArrayList<Object> queue = new ArrayList<>();

	public void enqueue(Object param) {
		this.queue.add(param);
	}
	
	public Object dequeue() {
		if (this.queue.size() == 0) {
			System.out.println("Queue가 비어있습니다. ");
			return null;
		}
		Object data = this.queue.get(0);
		this.queue.remove(0);
		return data;
	}
	
	public void peek() {
		System.out.println(this.queue.get(0));
	}
	
	public Boolean isEmpty() {
	    if (this.queue.size() == 0) {
	      return true;
	    }
	    return false;
	}
}
```

차근차근 설명해보겠습니다. 

### 1. queue
```java
	private ArrayList<Object> queue = new ArrayList<>();
```
리스트로 큐를 구현하기 위해 Queue라는 클래스를 만들고 
멤버변수로 ArrayList타입의 queue를 선언했습니다. 

### 2. enqueue
```java
	public void enqueue(Object param) {
		this.queue.add(param);
	}
```
파라미터로 전달받은 Data를 리스트에 추가해줍니다. 
이렇게 list의 __add()__ 메서드를 이용하면 데이터를 넣을때마다 차례대로 저장됩니다. 


### 3. dequeue
```java
	public Object dequeue() {
		if (this.queue.size() == 0) {
			System.out.println("Queue가 비어있습니다. ");
			return null;
		}
		Object data = this.queue.get(0);
		this.queue.remove(0);
		return data;
	}
```
리스트 맨 앞에 저장되어있는 데이터를 가져옵니다. 

리스트가 비어있을 때 데이터를 가져오려고 하면 오류가 발생하니 오류가 발생하지 않게 데이터를 꺼내기전에 리스트가 비어있는지 확인해봅니다.

리스트가 비어있지 않으면 맨 앞의 데이터를 반환하고, 
__remove()__ 메서드를 사용해 리스트에서 삭제시켜줍니다.

### 4. isEmpty
```java
	public Boolean isEmpty() {
	    if (this.queue.size() == 0) {
	      return true;
	    }
	    return false;
	}
```
리스트의 사이즈가 0이라면 리스트가 비어있다는 뜻이므로
__true__ 를, 사이즈가 0이 아니라면 리스트에 데이터가 있다는 뜻이므로 __false__ 를 반환해줍니다.
