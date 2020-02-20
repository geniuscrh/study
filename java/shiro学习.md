# 学习网址

**h**ttp://412887952-qq-com.iteye.com/blog/2299777



# build.gradle添加

```groovy
compile group: 'org.apache.shiro', name: 'shiro-spring', version: '1.4.0'
```



# 实例

## Reaml

```java
package com.geniuscrh.wangluo.shiro;

import com.geniuscrh.wangluo.user.entity.PermissionEntity;
import com.geniuscrh.wangluo.user.entity.RoleEntity;
import com.geniuscrh.wangluo.user.entity.UserEntity;
import com.geniuscrh.wangluo.user.entity.UserRepository;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

import javax.annotation.Resource;

//继承AuthorizingRealm
public class MyShiroRealm extends AuthorizingRealm {

    @Resource
    UserRepository user_rep;

    /*
    *权限认证
    */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        SimpleAuthorizationInfo simpleAuthorizationInfo=new SimpleAuthorizationInfo();

		//获取用户信息
        String userName=principals.getPrimaryPrincipal().toString();
        UserEntity userEntity=user_rep.findByUserName(userName);

		//添加权限
        for(RoleEntity role:userEntity.getRoleList()){
            simpleAuthorizationInfo.addRole(role.getRoleName());
            for(PermissionEntity p:role.getPermissionList()){
                simpleAuthorizationInfo.addStringPermission(p.getPermissionName());
            }
        }

         return simpleAuthorizationInfo;
    }

	/*
	*身份验证
	*/
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {

        if(token.getPrincipal()==null){
            return null;
        }

        String userName=token.getPrincipal().toString();

        UserEntity userEntity=user_rep.findByUserName(userName);

        if(userEntity==null){
            return null;
        }

        SimpleAuthenticationInfo simpleAuthenticationInfo=new SimpleAuthenticationInfo(
                userEntity.getUserName(),
                userEntity.getUserPwd(),
                getName()
        );
        return simpleAuthenticationInfo;
    }
}


```



## ShiroConfiguration

```java
package com.geniuscrh.wangluo.shiro;

import org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.apache.shiro.mgt.SecurityManager;
import org.springframework.web.servlet.HandlerExceptionResolver;

import java.util.LinkedHashMap;
import java.util.Map;

@Configuration
public class ShiroConfiguration {

    @Bean
    public ShiroFilterFactoryBean shirFilter(SecurityManager securityManager){
        System.out.println("ShiroConfiguration init");

        ShiroFilterFactoryBean shiroFilterFactoryBean=new ShiroFilterFactoryBean();
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        //拦截器
        Map<String,String> filterChainDefinitionMap=new LinkedHashMap<String,String>();
		
        //设置注销
        filterChainDefinitionMap.put("/logout", "logout");
        //无需鉴权
        filterChainDefinitionMap.put("/gis_wap*", "anon");
        //需要登录
        filterChainDefinitionMap.put("/*", "authc");
       	//登录界面
        shiroFilterFactoryBean.setLoginUrl("/login");
        
        //设置成功页面
		shiroFilterFactoryBean.setSuccessUrl("/");
		//设置无权限
		shiroFilterFactoryBean.setUnauthorizedUrl("/");


        shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);

        return shiroFilterFactoryBean;

    }

    @Bean
    public MyShiroRealm myShiroRealm(){
        MyShiroRealm myShiroRealm=new MyShiroRealm();
        return myShiroRealm;
    }
    
    @Bean
    public SecurityManager securityManager(){
        DefaultWebSecurityManager securityManager =  new DefaultWebSecurityManager();
        securityManager.setRealm(myShiroRealm());
        return securityManager;
    }

    //支持Shiro注释
    @Bean
    public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor(SecurityManager securityManager) {
        AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor = new AuthorizationAttributeSourceAdvisor();
        authorizationAttributeSourceAdvisor.setSecurityManager(securityManager);
        return authorizationAttributeSourceAdvisor;
    }

    //自定义异常处理
    @Bean
    public HandlerExceptionResolver solver(){
        HandlerExceptionResolver handlerExceptionResolver=new ShiroExceptionResolver();
        return handlerExceptionResolver;
    }
}


```



## ShiroExceptionResolver

```java
public class ShiroExceptionResolver implements HandlerExceptionResolver {
public ModelAndView resolveException(HttpServletRequest request,
                                         HttpServletResponse response, Object handler, Exception ex) {
        // TODO Auto-generated method stub
        //System.out.println("==============异常开始=============");
        //如果是shiro无权操作，因为shiro 在操作auno等一部分不进行转发至无权限url
        if (ex instanceof UnauthorizedException) {
            ModelAndView mv = new ModelAndView("sys/403.html");
            return mv;
        }
        ex.printStackTrace();
        return   null;
    }

}

```

## Controller登录

```java
	//生成Token
	UsernamePasswordToken token=new UsernamePasswordToken(userName,userPwd);
    Subject subject=SecurityUtils.getSubject();

    try{
        //登录操作
        subject.login(token);
       
        json.put("success",true);
        return json;
    }

    catch (UnknownAccountException unknownAccountException){
        json.put("msg","无用户");
    }
    catch (IncorrectCredentialsException incorrectCredentialsException){
        json.put("msg","密码错误");
    }
    catch (Exception e){
        json.put("msg","登录失败");
        e.printStackTrace();
    }
    json.put("success",false);
    return json;

```



# @RequiresPermissions多权限任选一参数用法

@RequiresPermissions(value={"xxx:xxx","xxx:xxx"},logical=Logical.OR)