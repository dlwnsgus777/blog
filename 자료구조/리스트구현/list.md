### 링크드 리스트(Linked List) Java로 구현하기
---
링크드 리스트(Linked List)를 자바로 구현해보도록 하겠습니다.

이번에도 역시 필요한 기능들이 어떤게 있는지 먼저 생각해보겠습니다.

#### 1. add
링크드 리스트에 값을 추가합니다.

#### 2. remove 
링크드 리스트에 값을 삭제합니다.

#### 3. 노드(Node)
노드란 리스트의 저장단위라고 생각하시면 됩니다.
노드에는 저장할 데이터 값, 다음 노드의 주소값이 저장됩니다. 

```java
public class LinkedListPractice {

	public static void main(String[] args) {
		LinkedList list = new LinkedList("one");
		list.add(2);
		list.findData(2);
		list.add("one", 3);
		list.printAll();
		list.add("Test");
		list.printAll();
	}
}

class Node {
	
	private Object data;
	private Object next;
	
	public Node(Object data) {
		this.data = data;
		this.next = null;
	}
	
	public Object getData() {
		return this.data;
	}
	
	public Object getNext() {
		return this.next;
	}
	
	public void setNext(Object nextNode) {
		this.next = nextNode;
	}
	
	public void setData(Object data) {
		this.data = data;
	}
}

class LinkedList {
	
	private Node data;
	private Node head;
	
	public LinkedList(Object data) {
		this.data = new Node(data);
		this.head = this.data;
	}
	
	public void add(Object data) {
		if (this.data.getNext() == null) { 
			Node node = new Node(data);
			this.data.setNext(node);
		} else {
			Node endNode = findEndData();
			Node node = new Node(data);
			endNode.setNext(node);
		}
		
		System.out.println("Success Add");
	}
	
	// 첫번째 인자로 받은 값을 가지는 리스트 다음에 data추가.
	public void add(Object between, Object data) {
		Node beforeNode = findData(between);
		Node afterNode = (Node) beforeNode.getNext();
		
		if (beforeNode != null) {
			Node node = new Node(data);
			beforeNode.setNext(node);
			node.setNext(afterNode);
			System.out.println("Success Add Between Data -> " + beforeNode.getData());
		} 
	}
	
	public Node findEndData() {
		Node node = this.data;
		while (node.getNext() != null) {
			node = (Node) node.getNext();
		}	
		return node;
	}
	
	public Node findData(Object value) {
		if (this.head.getNext() == null) {
			return null;
		}
		Node node = this.head;
		while (node != null) {
			if (node.getData() == value) {
				System.out.println("find Success : " + node.getData());
				return node;
			}
			node = (Node) node.getNext();
		}
		return null;
	}
	
	public void printAll() {
		Node head = this.head;
		
		while (head != null) {
			System.out.println(head.getData());
			head = (Node) head.getNext();
		}
	}
}
```

가장 기본적인 기능만 간단하게 구현했습니다.  
아직 객체지향적인 프로그래밍에 익숙하지 않은게 느껴집니다... 추후 수정할 수 있도록하겠습니다.



