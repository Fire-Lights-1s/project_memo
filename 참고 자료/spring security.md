# 문제
- 처음에 권한이 없이 로그인 인증을 구현하고 권한 설정 없이 페이지를 이동하려고 했음
- 로그인을 SecurityFilterChain 을 통해서 완료한 이후 페이지 이동 시 접속이 거부됨

## 원인 파악
- permitAll 인 url 리스트에 인증되었는지 확인 할 수 있도록 로그로 출력함
```java
log.info("isAuthenticated" + SecurityContextHolder.getContext()
		 .getAuthentication().isAuthenticated());
```
- 로그인 후 해당 코드가 있는 페이지로 이동한 결과 false로 인증 되지 않았다고 나왔음
## 원인 
- 커스텀 `AuthenticationProvider` 를 구현할 때 일단은 권한 없이 구현하고 싶어서 `UsernamePasswordAuthenticationToken` 에 권한을 안 넣어서 SecurityFilterChain 를 거칠 때 인증 되지 않은 것

```java
@Slf4j  
@RequiredArgsConstructor  
public class CustomAuthenticationProvider implements AuthenticationProvider {  
  
    private final BCryptPasswordEncoder bCryptPasswordEncoder;  
  
    private final ManagerService adminService;  
  
    @Override  
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {  
        log.info("CustomAuthenticationProvider authenticate");  
        String username = authentication.getName();  
        String password = (String) authentication.getCredentials();  
        log.info("username:{}", username);  
        log.info("password:{}", password);  
        // 사용자 정보 조회  
        Manager managerInfo = adminService.getAdminByAdminId(username);  
  
        // password 일치 여부 체크  
        if(!bCryptPasswordEncoder.matches(password, managerInfo.getPass()))  
            throw new LoginException(LoginExceptionResult.NOT_CORRECT);  
  
        // return UsernamePasswordAuthenticationToken  
        // Collections.singleton(new SimpleGrantedAuthority("TEST")) 이부분 없이 구현함
        return new UsernamePasswordAuthenticationToken(  
                managerInfo.getManagerId(),  
                managerInfo.getPass(),  
                Collections.singleton(new SimpleGrantedAuthority("TEST"))  
        );  
    }  
  
    @Override  
    public boolean supports(Class<?> authentication) {  
        return UsernamePasswordAuthenticationToken.class.isAssignableFrom(authentication);  
    }  
  
}
```

- `isuthenticated()` 가 false로 나온 이유는 아래 토큰 생성하는 과정에서 권한에 대한 내용이 없었어서 해당 유저는 인증이 안되었다고 저장이 된 것 같다
```java
public UsernamePasswordAuthenticationToken(Object principal, Object credentials) {  
    super((Collection)null);  
    this.principal = principal;  
    this.credentials = credentials;  
    this.setAuthenticated(false);  
}  
  
public UsernamePasswordAuthenticationToken(Object principal, Object credentials, Collection<? extends GrantedAuthority> authorities) {  
    super(authorities);  
    this.principal = principal;  
    this.credentials = credentials;  
    super.setAuthenticated(true);  
}
```

>참고 사이트
>https://kkyu0718.tistory.com/113#Q1.%20%EB%AA%A8%EB%93%A0%20%EC%97%90%EB%9F%AC%EA%B0%80%20403%20AccessForbidden%20%EC%9C%BC%EB%A1%9C%20%EB%96%A0%EC%9A%94-1