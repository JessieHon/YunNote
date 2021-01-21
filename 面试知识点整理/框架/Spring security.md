# Spring Security

#### Authentication

##### 比较熟悉的标准认证过程：

1.用户使用username和password登录

2.系统验证这个password对于该username是正确的（**验证过程**）

3.假设第二步验证成功，获取该用户的上下文信息（如他的角色列表）

4.围绕该用户建立安全上下文（security context）

5.用户可能继续进行的一些操作被一个验证控制机制潜在的管理，这个验证机制会根据当前用户的安全上下文来验证权限。



##### 认证过程就是又前三项构成的。在Spring Security中是这样处理这三部分的：

1.username和password被获得后封装到一个UsernamePasswordAuthenticationToken（Authentication接口的实例）的实例中

2.这个token被传递给AuthenticationManager进行验证

3.成功认证后AuthenticationManager将返回一个得到完整填充的Authentication实例

4.通过调用SecurityContextHolder.getContext().setAuthentication(...)，参数传递authentication对象，来建立安全上下文（security context）



```java
public class AuthenticationExample {
private static AuthenticationManager am = new SampleAuthenticationManager();
public static void main(String[] args) throws Exception {
    String name = "";
    String password = "";
    try {
        // request就是第一步，使用name和password封装成为的token
        Authentication request = new UsernamePasswordAuthenticationToken(name, password);
        // 将token传递给Authentication进行验证
        Authentication result = am.authenticate(request);
        SecurityContextHolder.getContext().setAuthentication(result);
        break;
    } catch (AuthenticationException e) {
        System.out.println("认证失败：" + e.getMessage());
    }
    System.out.println("认证成功，Security context 包含：" + SecurityContextHolder.getContext().getAuthentication());
}
}
```
```java
// 自定义验证方法
class SimpleAuthenticationManager implements AuthenticationManager {
    static final List<GrantedAuthority> AUTHORITIES = new ArrayList<GrantedAuthority>();
// 构建一个角色列表
static {
    AUTHORITIES.add(new SimpleGrantedAuthority("ROLE_USER"));
}

// 验证方法
public Authentication authenticate(Authentication auth) throws AuthenticationException {
    // 这里我们自定义了验证通过条件：username与password相同就可以通过认证
    if (auth.getName().equals(auth.getCredentials())) {
        return new UsernamePasswordAuthenticationToken(auth.getName(), auth.getCredentials(), AUTHORITIES);
    }
    // 没有通过认证则抛出密码错误异常
    throw new BadCredentialsException("Bad Credentials");
}
}
```
通常不需要像上边这样写代码，这些过程都是内部自动进行的。当SecurityContextHolder包含一个完整填充的Authentication对象，用户就是验证通过了。



##### Web Application

考虑一个典型的Web应用认证过程：

1.访问首页，随便点击一个链接

2.发送一个请求到服务器，服务器判断你是否在访问一个收到保护的资源

3.此时你还没有进行认证，服务器会返回一个响应告诉你必须先通过认证。这个响应可以是一个HTTP响应码或者是重定向到指定的web页面

4.根据认证机制，你的浏览器可能会重定向到一个登录页面，或者通过某种方式恢复你的身份（通过一个基础的认证对话框，cookie，X.509证明等）

5.浏览器回应服务器。这可以是一个HTTP POST请求，包含你所填写的表单信息，也可以是一个HTTP请求头，包含你的认证详情

6.接下来服务器会判定提交的凭证是否通过认证。如果认证通过，那么继续下一步。如果没有通过认证，那么重新进行上边的步骤

7.你在认证之前，原始的请求（即触发认证的请求）将会重新发起。



Spring Security已经实现了上述的大多数过程。主要有ExceptionTranslationFilter，AuthenticationEntryPoint和一个认证机制，负责调用上面讨论过的AuthenticationManager。



ExceptioTranslationFilter

ExceptionTranslationFilter是用来检测Spring Security抛出的任何异常的过滤器。



AuthenticationManager

用来处理一个认证请求。只有一个authentication(Authentication authentication)函数。

尝试去认证传入的Authentication对象，如果认证成功，返回一个完整填充的Authentication对象（包括授予的权限）。



一个AuthenticationManager必须处理以下异常：

- DisabledException：当一个账户被禁用且AuthenticationManager可以检测出来这个状态，要抛出该异常
- LockedException：当一个账户被锁且AuthenticationManager可以检测这个状态，要抛出该异常
- BadCredentialsException：当账户认证失败，必须抛出该异常。（一个AuthenticationManager**必须**检测这个状态）



这些异常应该按照顺序抛出，（比如如果一个账户被锁定，那么不进行账户认证）



AuthenticationProvider

用来处理一个指定的认证。有一个authenticate(Authentication authentication)函数和一个supports(Class<?> authentication)函数。

其中authenticate函数的用法与AuthenticationManager的authenticate一样。

supports函数用来指明该Provider是否适用于该类型的认证，如果不合适，则寻找另一个Provider进行验证处理。



ProviderManager

通过AuthenticationProviders迭代认证请求。

AuthenticationProviders通常按照顺序尝试，知道返回一个不为null的响应。非空响应代表provider可以提供认证并且不会继续请求下一个provider。如果后边的provider成功进行了验证，那么前边provider抛出的异常将被忽略。



















