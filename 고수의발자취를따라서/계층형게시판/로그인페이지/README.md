### 로그인 페이지

#### [기억보단 기록을](https://jojoldu.tistory.com/284?category=689637) 블로그를 참고하여 만들었습니다.

#### ~~1. 프로젝트 생성~~

#### ~~2. DB 연결~~

#### 3. 로그인 Front Page 작성

#### 4. 로그인 기능 구현

#### 5. 게시판 Front Page 작성

#### 6. 게시판 기능 구현

### Thymeleaf 연결

---

DB 연결을 완료하였으니 이제 로그인 페이지를 만들겠습니다.

원래는 JSP를 사용하려고 했지만 스프링부트에서는 여러가지 [제약조건](https://docs.spring.io/spring-boot/docs/2.1.5.RELEASE/reference/htmlsingle/#boot-features-jsp-limitations)이 있고, 권장하지는 않았습니다.

찾아보니 JSP, Freemarker, Velocity는 몇 년간 업데이트가 되지않고 있다고도 합니다.

스프링부트에서는 Thymeleaf(타임리프)라는 템플릿 엔진을 권장합니다.

Handlebar라는 템플릿과 Thymeleaf 둘 중에 고민을 하던 중

- 스프링에서 밀어주고 있기 때문에 view 설정을 따로하지 않아도 되는 점

- html 문법을 사용하기 때문에 좀 더 익숙하다는 점

- 문법이 생각보다 쉬워보였던 점 (제가 느끼기에....ㅎ)

이러한 이유로 Thymeleaf를 사용하기로 결정했습니다.

#### 프로젝트에 Thymeleaf 연결

#### build.gradle

```java
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
```

**build.gradle** 에 다음과 같이 디펜던시를 추가하겠습니다.

#### applications.yml

```java
thymeleaf:
	cache: false
```

**applications.yml** 에 다음과 같이 설정을 추가하겠습니다.

cache값에 **false** 를 두면 수정내용을 프로젝트 재시작없이 확인할 수 있습니다.

추후 배포를 할때는 **true** 값으로 변경해야합니다.

![add index](images/addindex.png)

**resource > templates > index.html** 파일을 생성합니다.

```html
<!DOCTYPE html>
<html>
  <head xmlns:th="http://www.thymeleaf.org">
    <meta charset="UTF-8" />
    <title>Hierarchical - Board</title>
  </head>
  <body>
    <h1>테스트해보자</h1>
  </body>
</html>
```

위처럼 head 태그에 `xmlns:th="http://www.thymeleaf.org"` 를 추가하시면 thymeleaf 문법을 사용할 수 있습니다.

이제 Controller를 작성하겠습니다.

![add webcon](images/addwebcon.png)

위의 사진처럼 web 패키지에 WebController 클래스를 생성합니다.

#### WebController Class

```java
@Controller
@AllArgsConstructor
public class WebController {

	@GetMapping("/")
	public String init() {
		return "index";
	}

}

```

`@Controller` 를 사용하면 View(화면)을 반환하게 됩니다.

여기서는 **resource/templates/index.html** 을 반환합니다.

이제 Test 코드를 작성하여 확인해보겠습니다.

![add webtest](images/addwebtest.png)

위의 사진과 같이 webservice / web 패키지를 만들고

WebControllerTest 클래스를 생성합니다.

#### WebControllerTest class

```java
@ExtendWith(SpringExtension.class)
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
public class WebControllerTest {

	@Autowired
	private TestRestTemplate testRestTemplate;

	@Test
	public void 메인페이지() {
		//when
		String body = this.testRestTemplate.getForObject("/", String.class);

		//then
		assertThat(body).contains("테스트해보자");

	}
}

```

반환하는 페이지에 `테스트해보자` 라는 text가 있는지 확인하는 코드입니다.

![webcon 테스트코드 통과](images/passtestwebcon.png)

테스트 코드가 통과되었으니 실제로 화면이 잘 뜨는지 확인합니다.

![webcon main](images/mainpage.png)

실제 화면이 잘 출력되니 간단하게 로그인화면을 구현하겠습니다.

### Thymeleaf Layout

---

Thymeleaf Layout을 이용해 html에서 공통적인 부분들을 나누겠습니다.

Thymeleaf에서 Layout 기능을 사용하려면 **build.gradle** 파일에 디펜던시를 추가해야합니다.

```java
implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect'
```

Thymeleaf Layout에 대한 자세한 설명은 따로 [여기서](https://blog.jiniworld.me/44) 보시기 바랍니다.

![tamplate](images/temlates.png)

저는 위와같이 html에서 공통적인 부분들을 따로 layout.html에 빼놨습니다.

추후 부트스트랩 사용을 위해 미리 부트스트랩까지 추가했습니다.

부트스트랩 추가는 [기억보단 기록을](https://jojoldu.tistory.com/255?category=635883) 참고 하시기 바랍니다.

```html
<!DOCTYPE html>
<html
  lang="ko"
  xmlns:th="http://www.thymeleaf.org"
  xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5"
  xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
>
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>고수의 발자취를 따라서</title>
    <!-- 부트스트랩 -->
    <link rel="stylesheet" href="/css/lib/bootstrap.min.css" />
  </head>
  <body>
    <header>
      <h2>테스트 레이아웃</h2>
    </header>
    <nav>
      <a href="/">main</a>
      <a href="/hello">hello</a>
    </nav>
    <section layout:fragment="content"></section>
    <footer>
      <p>개발인생</p>
    </footer>
    <!-- Jquery, bootstrap -->
    <script src="/js/lib/jquery.min.js"></script>
    <script src="/js/lib/bootstrap.min.js"></script>
  </body>
</html>
```

이제 페이지에서 Content가 되는 부분을 작성해보겠습니다.

![addhell](images/addhell.png)

위처럼 페이지에서 변화가 있는 부분은 따로 **content** 폴더에 만들겠습니다.

#### index.html

```html
<!DOCTYPE html>
<html
  lang="ko"
  xmlns:th="http://www.thymeleaf.org"
  xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5"
  xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
  layout:decorate="~{cmmn/layout}"
>
  <section layout:fragment="content">
    <h1>테스트해보자</h1>
  </section>
</html>
```

### hello.html

```html
<!DOCTYPE html>
<html
  lang="ko"
  xmlns:th="http://www.thymeleaf.org"
  xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5"
  xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
  layout:decorate="~{cmmn/layout}"
>
  <section layout:fragment="content">
    <h1>Hello</h1>
  </section>
</html>
```

이제 **hello.html** 을 불러올 수 있게 **Webcontroller** 클래스를 수정합니다.

```java
@Controller
@AllArgsConstructor
public class WebController {

	@GetMapping("/")
	public String init() {
		return "contents/index";
	}

	@GetMapping("/hello")
	public String hello() {
		return "contents/hello";
	}

}
```

이제 프로젝트를 실행해서 레이아웃이 잘 동작하는지 확인하겠습니다.

#### main 클릭시

![main](images/clickmain.png)

#### hello 클릭시

![hello](images/clickhello.png)

공통으로 지정한 부분을 제외하고 화면이 변하는 걸 확인할 수 있습니다.

이제 로그인 화면을 만들겠습니다.

정말 간단하게 화면을 만들었습니다. 부족한 점은 앞으로 진행하면서 추가해 나가도록 하겠습니다.

#### layout.html

```html
<!DOCTYPE html>
<html
  lang="ko"
  xmlns:th="http://www.thymeleaf.org"
  xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5"
  xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
>
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>고수의 발자취를 따라서</title>
    <!-- 부트스트랩 -->
    <link rel="stylesheet" href="/css/lib/bootstrap.min.css" />

    <style>
      body {
        margin: 0;
        padding: 0;
      }
      .wrap__content {
        margin-top: 100px;
      }
    </style>
  </head>
  <body>
    <header>
      <h4>계층형 게시판</h4>
    </header>
    <ul class="nav nav-tabs">
      <li role="presentation"><a href="/">main</a></li>
      <li role="presentation"><a href="/hello">hello</a></li>
    </ul>

    <section layout:fragment="content"></section>
    <footer>
      <div class="footer-copyright text-center py-3">by 개발인생</div>
    </footer>

    <!-- Jquery, bootstrap -->
    <script src="/js/lib/jquery.min.js"></script>
    <script src="/js/lib/bootstrap.min.js"></script>
  </body>
</html>
```

#### index.html

```html
<!DOCTYPE html>
<html lang="ko"
	xmlns:th="http://www.thymeleaf.org"
	xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5"
	xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
	layout:decorate="~{cmmn/layout}">

<section layout:fragment="content">
	<div class="container wrap__content">
	<div class="col-md-6 col-md-offset-3">
	<form class="form-horizontal">
  <div class="form-group">
    <label for="inputEmail3" class="col-sm-2 control-label">Email</label>
    <div class="col-sm-10">
      <input type="email" class="form-control" id="inputEmail3" placeholder="Email">
    </div>
  </div>
  <div class="form-group">
    <label for="inputPassword3" class="col-sm-2 control-label">Password</label>
    <div class="col-sm-10">
      <input type="password" class="form-control" id="inputPassword3" placeholder="Password">
    </div>
  </div>
  <div class="form-group">
    <div class="col-sm-offset-2 col-sm-5">
    <button type="button" class="btn btn-primary" data-toggle="modal" data-target="#accountUserModal">회원가입</button>
      <button type="submit" class="btn btn-default">Sign in</button>
    </div>
  </div>
</form>

    <div class="modal fade" id="accountUserModal" tabindex="-1" role="dialog" aria-labelledby="accountUserLabel" aria-hidden="true">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="accountUserLabel">회원 가입</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                    <form>
                        <div class="form-group">
                            <label for="user_name">이름</label>
                            <input type="text" class="form-control" id="userName" placeholder="이름을 입력하세요">
                        </div>
                        <div class="form-group">
                            <label for="user_id">아이디</label>
                            <input type="text" class="form-control" id="userId" placeholder="아이디를 입력하세요">
                        </div>
                        <div class="form-group">
                            <label for="password">패스워드</label>
                            <input class="form-control" id="password" placeholder="비밀번호을 입력하세요"></input>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">취소</button>
                    <button type="button" class="btn btn-primary" id="btn-save">가입</button>
                </div>
            </div>
        </div>
    </div>
    </div>
    </div>
</section>
```

![로그인페이지](images/loginpage.png)

로그인 화면을 작성했습니다.

이제 본격적으로

- 회원가입
- 로그인 / 로그아웃

기능을 구현하겠습니다.
