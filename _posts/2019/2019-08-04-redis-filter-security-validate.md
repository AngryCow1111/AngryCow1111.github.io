---
layout: post
title:  简单实现请求安全检验
no-post-nav: true
category: nosql
tags: [nosql filter]
excerpt: nosql nosql 安全检验
---
# 使用filter+redis简单实现请求安全检验
## 模拟应用场景
在项目中，简单实现对用户请求的合法性就行校验，对不合法的请求进行在调用接口之前，进行拦截。比如在header中添加token。
## 具体实现
1. 创建token类，示例代码如下：
```java
@Data
public class Token {
    private Long userId;
    private String mobile;
    private Long expiredTime;
 ```
 2. 编写filter类
 ```java
 @Slf4j
@Component
@Order( 1 )
@WebFilter( filterName = "preRequestFilter", urlPatterns = "/*" )
public class PreRequestFilter implements Filter {

    @Autowired
    private RedisUtils redisUtils;

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        HttpServletRequest httpServletRequest = (HttpServletRequest) request;
        HttpServletResponse httpServletResponse = (HttpServletResponse) response;
        String encrptToken = httpServletRequest.getHeader("token");
        String requestURI = httpServletRequest.getRequestURI();

        if (!StringUtils.contains(requestURI, "auth")) {

            chain.doFilter(request, response);
            return;
        }
        if (StringUtils.isBlank(encrptToken)) {

            processFailResponse(httpServletRequest, httpServletResponse, ResponseResult.buildFail(1001, "用户验证失败！"));
            return;
        }
        Token token = TokenUtils.decryptGenerateToken(encrptToken);

        boolean isRight = TokenUtils.validateToken(token);
        if (!isRight) {
            processFailResponse(httpServletRequest, httpServletResponse, ResponseResult.buildFail(1001, "用户验证失败！"));
            return;
        }

        String tokenKey = String.format(RedisConstants.TOKRN.getValue(), token.getUserId(), token.getMobile());
        String encrptTokenRedis = redisUtils.getValue(tokenKey);
        if (StringUtils.equals(encrptToken, encrptTokenRedis)) {
            chain.doFilter(request, response);
            return;
        }
        processFailResponse(httpServletRequest, httpServletResponse, ResponseResult.buildFail(1001, "用户验证失败！"));
    }

    @Override
    public void destroy() {

    }

    private void processFailResponse(HttpServletRequest requestWrapper, HttpServletResponse response, ResponseResult responseResult) {
        response.setStatus(HttpServletResponse.SC_OK);
        response.setContentType("application/json;charset=UTF-8");
        PrintWriter out = null;

        try {
            out = response.getWriter();
            out.append(JSON.toJSONString(responseResult));
        } catch (IOException e) {
            log.error(e.getMessage());
        } finally {
            if (out != null) {
                out.close();
            }
        }
    }
}
```
这里我使用的HttpServletRequest和HttpServletResponse类，如果有需要可以使用装饰者模式对其进行增强。     
基本实现逻辑：

    1. 先判断是否是需要检验的请求，可以在请求路径中加入关键词进行标记，比如auth。如果不是的话，不是就放行。否则，进行后续校验。     
    
    2. 从request对象中获取需要检验的token（存入时进过加密），并对token进行校验是否合法，比如我的token包含mobile，就可以对mobile的格式进行校验等。不符合就拦截；否则，进行后续校验。
    
    3. 从redis中获取之前登录时，存入的token值。
    4. 对2个token进行比较，相同则放行；否则，拦截并提示用户验证失败。
## 鸡汤时间
嗯，哼哼哼。忘词了.........  
