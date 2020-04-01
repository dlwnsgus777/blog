### 게시판 기능 구현 (4)

#### ~~1. 프로젝트 생성~~

#### ~~2. DB 연결~~

#### ~~3. 로그인 Front Page 작성~~

#### ~~4. 로그인 기능 구현~~

#### ~~5. 게시판 Front Page 작성~~

#### 6. 게시판 기능 구현

### 게시판 기능 구현 - 게시글 조회

---

이제 본격적으로 게시판의 기능을 구현해보도록 하겠습니다.

기본적인 CRUD기능을 구현하기 전에 Entitiy의 연관 관계를 설정하겠습니다.

CRUD란

- Create
- Read
- Update
- Delete

의 앞 글자들을 따서 CRUD라고 부릅니다.

---

![mkdir](images/mkdir.png)

**domain** 패키지 아래 위의 사진처럼

**boards**, **images** 패키지를 생성합니다.

**board** 패키지 안에 **Boards** 클래스를 생성합니다.

**Boards** (import 부분은 생략했습니다)

전체 코드는 **[Git Hub](https://github.com/dlwnsgus777)** 에 있습니다.

```java
package com.board.webserivce.domain.boards;

@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter
@Entity
@DynamicUpdate
public class Boards extends BaseTimeEntity {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;

	@ColumnDefault("0")
	private int depth;

	@Column(length = 200, nullable = false)
	private String title;

	@Column(columnDefinition = "TEXT", nullable = false)
	private String content;

	@ColumnDefault("null")
	private Long parentId;

	@ManyToOne
	@JoinColumn(name = "author_id")
	private Users author;

	@OneToMany(orphanRemoval = true, cascade = CascadeType.ALL)
	@JoinColumn(name = "boardId")
	private List<Images> images = new ArrayList<>();

	@Builder
	public Boards(String content, String title, Long parentId, int depth, Users author) {
		this.title = title;
		this.content = content;
		this.parentId = parentId;
		this.depth = depth;
		this.author = author;
	}
}
```

게시글을 저장하는 Entity 입니다.

마찬가지로 **images** 패키지 안에 **Images** 클래스를 생성합니다.

```java
package com.board.webserivce.domain.images;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

import org.hibernate.annotations.DynamicUpdate;

import lombok.AccessLevel;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter
@Entity
@DynamicUpdate
public class Images {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;

	@Column(nullable = false)
	private String fileName;

	private Long boardId;

	@Builder
	public Images(String fileName, Long boardId) {
		this.fileName = fileName;
		this.boardId = boardId;
	}
}
```

이미지를 저장하는 Entity 입니다.

- Boards
- Users
- Images

이 3개의 Entity에 대한 관계를 설명하겠습니다.

JPA에서는 JDBC를 사용할 때 처럼 참조하는 테이블의 PK를 변수로 가지고 있지 않고

참조하는 테이블의 **객체**를 가지고 있습니다.

```java
  // jpa
	private Users author;
```

Boards 엔티티의 author라는 맴버 변수는
Users라는 엔티티의 객체를 자료형으로 가지고 있습니다.

단순히 저렇게 변수만 선언한다고 연관관계가 성립되지는 않습니다.

```java
	@ManyToOne
	@JoinColumn(name = "author_id")
	private Users author;
```

위의 코드 처럼 연관 관계를 맺어주는 어노테이션을 사용해야합니다.

코드에 대한 설명을 하기에 앞서 개념에 대한 설명을 간단히 하겠습니다.

#### 1) 다중성

- Many to One - 다대일

  N : 1 관계 입니다.

* Many To Many - 다대다

  N : N 관계 입니다.

- One To Many - 일대다

  1 : N 관계 입니다.

* One To One - 일대일

  1 : 1 관계 입니다.

#### 2) 양방향, 단방향

- 양방향

  두개의 엔티티가 관계를 맺을 때, 양쪽이 서로를 참조하는 경우입니다.

- 단방향

  두개의 엔티티가 관계를 맺을 때, 한 쪽의 엔티티만 반대쪽 엔티티를 참조하는 경우입니다.

#### 3) 연관 관계의 주인

- 연관 관계를 맺을 때, 외래키(PK)를 가지고 있는 테이블이
  연관 관계의 주인이 됩니다.

  이때 연관 관계의 주인만 외래키를 등록, 수정, 삭제 할 수 있습니다.

  주인이 아닌 엔티티는 읽기만 할 수 있습니다.

이제 프로젝트의 게시글과 이미지의 관계에 대해서 설명하겠습니다.

![post_image](images/post_images.png)

위의 사진처럼 **하나**의 게시글에는 **여러개**의 이미지를 가질 수 있습니다.

하나의 게시글이 여러개의 이미지

- One to Many

```java
	@OneToMany(orphanRemoval = true, cascade = CascadeType.ALL)
	@JoinColumn(name = "boardId")
	private List<Images> images = new ArrayList<>()
```

코드로 표현하면 이렇게 할 수 있습니다.

JPA에서는 연관 관계를 매핑하면 새로운 테이블을 만들어서 관리하게 됩니다.

이렇게 따로 테이블을 만들어 연관 관계를 매핑하지 않고 연관 관계를 관리하려면

```
@JoinColumn(name = "boardId")
```

이렇게 어노테이션을 설정하면 됩니다.

Images 엔티티에 있는 boardId 컬럼이랑 매핑이 된다고 생각하시면 됩니다.

이제 게시글과 작성자(User)의 관계입니다.

```java
	@ManyToOne
	@JoinColumn(name = "author_id")
	private Users author;

```

게시글은 여러명의 유저가 작성할 수 있습니다.

위의 코드처럼

`@ManyToOne` 을 이용해 매핑해줍니다.

![create_repo](images/createrepo.png)

엔티티를 만들었으니 Repository를 생성하겠습니다.

**BoardsRepository** 클래스를 추가하겠습니다.

**BoardsRepository.java**

```java
public interface BoardsRepository extends JpaRepository<Boards, Long> {

}

```

**ImagesRepository** 클래스를 추가하겠습니다.

**ImagesRepository.java**

```java
public interface ImagesRepository extends JpaRepository<Images, Long> {

}
```

간단히 연관 관계를 매핑해봤습니다.

다음 포스트부터 본격적으로 CRUD를 진행해보겠습니다.
