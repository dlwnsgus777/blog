### 스택(Stack) 구현하기

이번에는 앞서 설명한 Stack을 구현해보도록 하겠습니다.

구현에 앞서 필요한 기능들을 정리하자면

#### 1. push  
스택의 맨 뒤에 Data를 추가합니다. 


#### 2. pop
스택에서 Data를 추출합니다.

스택은 LIFO(Last In First Out)의 형태이기 때문에 스택에서 제일 마지막 Data를 꺼내오게됩니다.


#### 3. peek

스택의 마지막 데이터를 보여줍니다. 

단순히 스택의 마지막 데이터를 보여주기때문에 스택의 값에는 변함이 없습니다.


### 구현
List를 이용해 Stack을 구현하겠습니다.

```java
import java.util.ArrayList;

public class StackPractice {

	public static void main(String[] args) {
		Stack stack = new Stack();
		
		stack.push(1);
		stack.push(2);
		stack.push(3);
		
		stack.peek();
		System.out.println(stack.pop());
		stack.peek();
		System.out.println(stack.pop());
		stack.peek();
		System.out.println(stack.pop());
		System.out.println(stack.pop());
	}

}

class Stack {
	private ArrayList<Object> stackArray;
	
	public Stack() {
		this.stackArray = new ArrayList<Object>();
	}
	
	public void push(int param) {
		this.stackArray.add(param);
	}
	
	public Object pop() {
		if (stackArray.size() == 0) {
			System.out.println("Stack is Empty");
			return null;
		} 
		
		Object data = this.stackArray.get(stackArray.size() - 1);
		this.stackArray.remove(stackArray.size() - 1);
		return data;
	}
	
	public void peek() {
		System.out.println("Last Data : " + this.stackArray.get(stackArray.size() - 1));
	}
}
```

