### 게시판 기능 구현 (1)

#### ~~1. 프로젝트 생성~~

#### ~~2. DB 연결~~

#### ~~3. 로그인 Front Page 작성~~

#### ~~4. 로그인 기능 구현~~

#### 5. 게시판 Front Page 작성

#### 6. 게시판 기능 구현

### Spring Security(스프링 시큐리티)

---

회원가입, 로그인 / 로그아웃에 대한 간단한 구현이 끝났습니다.

여기서는 회원 탈퇴, 유저 정보 변경을 진행하도록 하겠습니다.

유저 정보 변경은 JPA의 **Dirty checking**을 이용해 구현하겠습니다.

**Dirty checking**

더티 체킹(Dirty checking) 이란 상태 변경 검사입니다.

JPA에서는 트랜잭션이 끝나는 시점에

**최초 조회 상태**와 비교해 변화가 있는 모든 엔티티 객체를 DB에 자동으로 반영해줍니다.

JPA에서는 엔티티를 조회했을 때 조회 상태 그대로를 **스냅샷** 으로 만들어놓습니다.

그리고 트랜잭션이 끝나는 시점에 이 스냅샷과 비교해 다른점이 있으면 Updata Query를 DB에 전달합니다.

먼저 Users Entity를 수정하겠습니다.

**Users.java**

```java
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter
@Entity
@DynamicUpdate
public class Users extends BaseTimeEntity {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	
	@Column(length = 20,unique = true, nullable = false)
	private String userId;
	
	@Column(nullable = false)
	private String password;
	
	@Column(unique = true)
	private String userName;
	
	@Builder
	public Users(String userId, String password, String userName) {
		this.userId = userId;
		this.password = password;
		this.userName = userName;
	}
	
	public void changeUserName(String userName) {
		this.userName = userName;
	}
}
```

엔티티의 값을 수정할 수 있도록 **changeUserName** 이라는 메서드를 만들었습니다.

**UserSecurityService**

```java
@Service
@AllArgsConstructor
public class UserSecurityService implements UserDetailsService  {
	private UsersRepository usersRepository;
	
	@Transactional
	public Long accountUser(UsersSaveRequestDto saveDto) {
		BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
		saveDto.setPassword(passwordEncoder.encode(saveDto.getPassword()));
		
		return usersRepository.save(saveDto.toEntity()).getId();
	}
	
	@Override
	public UserDetails loadUserByUsername(String userId) throws UsernameNotFoundException {
		// TODO Auto-generated method stub
		Optional<Users> userEntitiy = usersRepository.findByUserId(userId);
		Users findUser = userEntitiy.get();
		ArrayList<GrantedAuthority> authorities = new ArrayList<>();
		
		authorities.add(new SimpleGrantedAuthority("ROLE_USER"));
		
		return new User(findUser.getUserId(), findUser.getPassword(), authorities);
	}
	
	@Transactional
	public void changeUserName(String userId, String changeName) {
		Optional<Users> users = usersRepository.findByUserId(userId);
		Users user = users.get();
		user.changeUserName(changeName);
	}
}
```
**UserSecurityService** 클래스에 **changeUserName** 메서드를 추가했습니다.

사용자의 Id를 받아 DB에서 검색한 다음 가져온 엔티티의 값을 변경하는 메서드입니다.

**WebRestController**

```java
@RestController
@AllArgsConstructor
public class WebRestController {
	private UserSecurityService userSecurityService;
	
	@PostMapping("/users/signup")
	public ResponseEntity<Map<String, Object>> saveUsers(@RequestBody UsersSaveRequestDto dto) {
		userSecurityService.accountUser(dto);
		Map<String, Object> map = new HashMap<>();
		
		map.put("msg", "save");
		return new ResponseEntity<>(map, HttpStatus.OK);
	}
	
	@GetMapping("/user/userName")
	public ResponseEntity<Map<String, Object>> getUserName(Principal principal) {
		Map<String, Object> map = new HashMap<>();
		map.put("userName", principal.getName());
		return new ResponseEntity<>(map, HttpStatus.OK);
	}
	
	@PostMapping("/user/changeName")
	public ResponseEntity<Map<String, Object>> chageUserName(@RequestBody Map<String, Object> map,
			Principal principal) {
		String changeName = map.get("changeName").toString();
		String userId = principal.getName();
		
		userSecurityService.changeUserName(userId, changeName);
		
		Map<String, Object> responseMap = new HashMap<>();
		responseMap.put("msg", "success");
		
		return new ResponseEntity<>(responseMap, HttpStatus.OK);
	}
}
```

**WebRestController** 에 **chageUserName** 메서드를 추가했습니다.

화면의 사용자 정보는 `Principal` 을 매개변수로 받아 가져옵니다.

`principal.getName()`을 하면 **UserSecurityService**에서 **loadUserByUsername** 을 구현할때 return 값으로 전달한 User 객체에서 설정한 Id값을 가져옵니다. 

**info.html**

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org"
	xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5"
	xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
	layout:decorate="~{cmmn/layout}">

<section layout:fragment="content">
	<div class="container wrap__content">
		<div class="row text-center">
			<div class="col-sm-8 col-md-8 col-md-offset-2">
				<div class="thumbnail">
					<div class="caption">
						<h3>
							<span th:text="${userName}"></span> 님 환영합니다.
						</h3>
						<div>
							<button type="button" class="btn btn-primary" data-toggle="modal"
								data-target="#chageUserModal" id="btn-change-modal">회원 정보 변경</button>
							<button class="btn btn-default" role="button" id="btn-delete">회원탈퇴</button>
						</div>
					</div>
				</div>
			</div>
		</div>

		<div class="modal fade" id="chageUserModal" tabindex="-1"
			role="dialog" aria-labelledby="chageUserLabel" aria-hidden="true">
			<div class="modal-dialog" role="document">
				<div class="modal-content">
					<div class="modal-header">
						<h5 class="modal-title" id="accountUserLabel">회원 가입</h5>
						<button type="button" class="close" data-dismiss="modal"
							aria-label="Close">
							<span aria-hidden="true">&times;</span>
						</button>
					</div>
					<div class="modal-body">
						<form id="changeName-frm">
							<div class="form-group">
								<label for="user_name">이름</label> <input type="text"
									class="form-control" id="changeName" th:placeholder="${userName}">
							</div>
						</form>
					</div>
					<div class="modal-footer">
						<button type="button" class="btn btn-secondary"
							data-dismiss="modal">취소</button>
						<button type="button" class="btn btn-primary" id="btn-change">변경</button>
					</div>
				</div>
			</div>
		</div>
	</div>
	<script>
	let userInfo = {
		init: function() {
			this.clickChageName();
		},
		csrf: {
			token: $("meta[name='_csrf']").attr("content"),
			header: $("meta[name='_csrf_header']").attr("content")
		},
		clickChageName: function() {
			let $this = this;
			$("#btn-change").click(function() {
				let data = {
					changeName: $("#changeName").val()
				}
				console.log(data)
		        $.ajax({
		            type: 'POST',
		            url: '/user/changeName',
		            dataType: 'json',
		            contentType:'application/json; charset=utf-8',
		            data: JSON.stringify(data),
		            beforeSend: function(xhr) {
	    				xhr.setRequestHeader($this.csrf.header, $this.csrf.token);
	    			}
		        }).done(function(result) {
		        	if (result.msg === "success") {
			        	alert("변경에 성공했습니다.");
			        	location.reload();
		        	}
		        }).fail(function (error) {
		            console.log(error);
		        });
			})
		}
	}
	$(function() {
		userInfo.init();
	})
	
	</script>
</section>
```
ajax 통신을 사용해 서버에 값을 전달했습니다.

변경에 성공하면 Page를 새로고침해 화면에 변경한 유저의 이름이 나오도록 했습니다.

**WebController**

```java
@Controller
@AllArgsConstructor
public class WebController {
	private UsersRepository userRepository;
	
	@GetMapping("/")
	public String init() {
		return "contents/index";
	}
	
	@GetMapping("/board")
	public String hello() {
		return "contents/board";
	}
	
	@GetMapping("/login/error")
	public String error() {
		return "contents/error";
	}
	
	@PostMapping("/login/fail")
	public String initPost() {
		return "contents/index";
	}
	
	@GetMapping("/info")
	public String info(Principal principal, ModelMap model) {
		Optional<Users> users = userRepository.findByUserId(principal.getName());
		Users user = users.get();
		
		model.addAttribute("userName", user.getUserName());

		return "contents/info";
	}
}
```

**/info**
요청이 오면 해당 유저를 검색해 화면에 닉네임 값을 전달해줍니다.

![addjquery](images/infopage.png)

유저 정보 화면입니다. 

유저의 닉네임이 화면에 출력되는걸 확인할 수 있습니다.

![addjquery](images/clickchan.png)

**input** 의 placeholder 에 유저의 닉네임을 넣어줬습니다.

![addjquery](images/succhan.png)

닉네임을 **test1** 으로 변경해보겠습니다.

![addjquery](images/sucinfo.png)

화면이 새로고침되며 유저의 변경된 닉네임이 화면에 출력됩니다.

### 회원 탈퇴

---

이제 회원 탈퇴 기능을 진행하겠습니다.

보통은 DB에 DEL_YN 필드를 만들어 삭제 여부를 **Y**, **N** 으로 저장해 검색시 DEL_YN 필드값이 **N** 인 회원들을 검색하는게 정석입니다.

이 기능은 추후에 필요하면 추가하겠습니다. 

지금은 단순히 DB에서 삭제시키는 걸로 하겠습니다.

**info.html**

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org"
	xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5"
	xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
	layout:decorate="~{cmmn/layout}">

<section layout:fragment="content">
	<div class="container wrap__content">
		<div class="row text-center">
			<div class="col-sm-8 col-md-8 col-md-offset-2">
				<div class="thumbnail">
					<div class="caption">
						<h3>
							<span th:text="${userName}"></span> 님 환영합니다.
						</h3>
						<div>
							<button type="button" class="btn btn-primary" data-toggle="modal"
								data-target="#chageUserModal" id="btn-change-modal">회원 정보 변경</button>
							<button class="btn btn-default" role="button" id="btn-delete">회원탈퇴</button>
						</div>
					</div>
				</div>
			</div>
		</div>

		<div class="modal fade" id="chageUserModal" tabindex="-1"
			role="dialog" aria-labelledby="chageUserLabel" aria-hidden="true">
			<div class="modal-dialog" role="document">
				<div class="modal-content">
					<div class="modal-header">
						<h5 class="modal-title" id="accountUserLabel">회원 가입</h5>
						<button type="button" class="close" data-dismiss="modal"
							aria-label="Close">
							<span aria-hidden="true">&times;</span>
						</button>
					</div>
					<div class="modal-body">
						<form id="changeName-frm">
							<div class="form-group">
								<label for="user_name">이름</label> <input type="text"
									class="form-control" id="changeName" th:placeholder="${userName}">
							</div>
						</form>
					</div>
					<div class="modal-footer">
						<button type="button" class="btn btn-secondary"
							data-dismiss="modal">취소</button>
						<button type="button" class="btn btn-primary" id="btn-change">변경</button>
					</div>
				</div>
			</div>
		</div>
	</div>
	<script>
	let userInfo = {
		init: function() {
			this.clickChageName();
			this.clickDeleteUser();
		},
		csrf: {
			token: $("meta[name='_csrf']").attr("content"),
			header: $("meta[name='_csrf_header']").attr("content")
		},
		clickChageName: function() {
			let $this = this;
			$("#btn-change").click(function() {
				let data = {
					changeName: $("#changeName").val()
				}
				console.log(data)
		        $.ajax({
		            type: 'POST',
		            url: '/user/changeName',
		            dataType: 'json',
		            contentType:'application/json; charset=utf-8',
		            data: JSON.stringify(data),
		            beforeSend: function(xhr) {
	    				xhr.setRequestHeader($this.csrf.header, $this.csrf.token);
	    			}
		        }).done(function(result) {
		        	if (result.msg === "success") {
			        	alert("변경에 성공했습니다.");
			        	location.reload();
		        	}
		        }).fail(function (error) {
		            console.log(error);
		        });
			})
		},
		clickDeleteUser: function() {
			let $this = this;
			$("#btn-delete").click(function() {
		        $.ajax({
		            type: 'DELETE',
		            url: '/user',
		            dataType: 'json',
		            contentType:'application/json; charset=utf-8',		        
		            beforeSend: function(xhr) {
	    				xhr.setRequestHeader($this.csrf.header, $this.csrf.token);
	    			}
		        }).done(function(result) {
		        	console.log(result);
			        alert("삭제에 성공했습니다.");
			        location.href="/user/logout";
		        }).fail(function (error) {
		            console.log(error);
		        });
			})
		}
	}
	$(function() {
		userInfo.init();
	})
	
	</script>
</section>
```
info.html의 script를 다음과 같이 수정했습니다.

Ajax에서 DELETE 메서드로 값을 넘깁니다.

삭제 후에 session을 제거하기위해 logout을 호출했습니다.

**UserRepository**

```java
public interface UsersRepository extends JpaRepository<Users, Long> {
	Optional<Users> findByUserId(String userId);
	Long deleteByUserId(String userId);
}
```

**UserRepository**에 **deleteByUserId** 메서드를 추가했습니다.


**WebRestController**

```java
@RestController
@AllArgsConstructor
public class WebRestController {
	private UserSecurityService userSecurityService;
	
	@PostMapping("/users/signup")
	public ResponseEntity<Map<String, Object>> saveUsers(@RequestBody UsersSaveRequestDto dto) {
		userSecurityService.accountUser(dto);
		Map<String, Object> map = new HashMap<>();
		
		map.put("msg", "save");
		return new ResponseEntity<>(map, HttpStatus.OK);
	}
	
	@GetMapping("/user/userName")
	public ResponseEntity<Map<String, Object>> getUserName(Principal principal) {
		Map<String, Object> map = new HashMap<>();
		map.put("userName", principal.getName());
		return new ResponseEntity<>(map, HttpStatus.OK);
	}
	
	@PostMapping("/user/changeName")
	public ResponseEntity<Map<String, Object>> chageUserName(@RequestBody Map<String, Object> map,
			Principal principal) {
		String changeName = map.get("changeName").toString();
		String userId = principal.getName();
		
		userSecurityService.changeUserName(userId, changeName);
		
		Map<String, Object> responseMap = new HashMap<>();
		responseMap.put("msg", "success");
		
		return new ResponseEntity<>(responseMap, HttpStatus.OK);
	}
	
	@DeleteMapping("/user")
	public ResponseEntity<Map<String, Object>> deleteUser(Principal principal) {
		String userId = principal.getName();
		userSecurityService.deleteUser(userId);
		Map<String, Object> responseMap = new HashMap<>();
		return new ResponseEntity<>(responseMap, HttpStatus.OK);
	}
}
```

**WebRestController**에 deleteUser 메서드를 만들고 

userSecurityService에서 deleteUser 메서드를 실행할 수 있게 작성합니다.


**UserSecurityService**

```java
@Service
@AllArgsConstructor
public class UserSecurityService implements UserDetailsService  {
	private UsersRepository usersRepository;
	
	@Transactional
	public Long accountUser(UsersSaveRequestDto saveDto) {
		BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
		saveDto.setPassword(passwordEncoder.encode(saveDto.getPassword()));
		
		return usersRepository.save(saveDto.toEntity()).getId();
	}
	
	@Override
	public UserDetails loadUserByUsername(String userId) throws UsernameNotFoundException {
		// TODO Auto-generated method stub
		Optional<Users> userEntitiy = usersRepository.findByUserId(userId);
		Users findUser = userEntitiy.get();
		ArrayList<GrantedAuthority> authorities = new ArrayList<>();
		
		authorities.add(new SimpleGrantedAuthority("ROLE_USER"));
		
		return new User(findUser.getUserId(), findUser.getPassword(), authorities);
	}
	
	@Transactional
	public void changeUserName(String userId, String changeName) {
		Optional<Users> users = usersRepository.findByUserId(userId);
		Users user = users.get();
		user.changeUserName(changeName);
	}
	
	@Transactional
	public void deleteUser(String userId) {
		usersRepository.deleteByUserId(userId);
	}
}
```

deletUser는 유저의 Id를 매개변수로 받아서 DB에서 해당 Id의 값을 삭제하는 기능을 합니다.

이제 화면에서 기능을 확인해보겠습니다.

![addjquery](images/clickdel.png)

회원탈퇴 클릭시

![addjquery](images/sucdel.png)


![addjquery](images/suclogout.png)


회원탈퇴 성공시

대략적인 회원가입, 로그인 / 로그아웃 기능이 완성되었습니다.

앞으로 만들면서 수정할 부분이 있으면 수정하겠습니다.


---

시큐리티에서 유저 정보를 변경하고 session을 변경하고 싶을때가 있습니다.

SecurityContext의 유저 정보를 새로 세팅하는 법입니다.

시큐리티 유저 정보 변경
```java
		List<GrantedAuthority> authorities = new ArrayList<>();
		authorities.add(new SimpleGrantedAuthority("ROLE_USER"));
		
		Authentication auth = new UsernamePasswordAuthenticationToken(user.getEmail(), user.getPassword(), authorities);
		SecurityContextHolder.getContext().setAuthentication(auth);
```
