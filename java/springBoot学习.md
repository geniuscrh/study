# @Controller

```java
//增加注释
@Controller

//自动生成
@Autowired
private GISRepository gis_rep;

//函数注释
@PostMapping("/block_list.xhtml")
@ResponseBody
@RequestParam Map<String, String> params

```



# 拦截器





Config

```java
@Configuration
public class GisServiceConfig implements WebMvcConfigurer {

    @Bean
    SSOInterceptor createSSOInterceptor(){
        return new SSOInterceptor();
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        		 registry.addInterceptor(createSSOInterceptor())
                     .addPathPatterns("/**")
                     .excludePathPatterns("/static");
    }
}
```



```java
public class SSOInterceptor implements HandlerInterceptor {

    @Value("${wangluo.url.login}")
    private String login_url;


    @Autowired
    RedisApiService redis_api_service;


    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        HttpSession session=request.getSession();
        Object user_session=session.getAttribute("user");

        //已登录
        if(user_session!=null&&StringUtils.isNotBlank(user_session.toString())){

        }
        //未登录
        else{
            System.out.println("interceptor：session无数据");
            String token_cookie=CookieUtils.getCookieValue(request,"token");
            //有在Cookie
            if(StringUtils.isNotBlank(token_cookie)){
                System.out.println("interceptor：cookie有数据");
                //根据cookie的token获取redis数据
                String token_redis=redis_api_service.get(token_cookie);
                //获取Redis数据
                if(StringUtils.isNotBlank(token_redis)){
                    UserEntity userEntity=MapperUtil.json2pojo(token_redis,UserEntity.class);
                    session.setAttribute("user",userEntity);
                    System.out.println("已登录");
                }else {
                    //redis无数据
                    System.out.println("interceptor：redis无数据，跳转登录页");
                    response=gotoLogin(response);
                }

            }else{
                System.out.println("interceptor：cookie无数据，跳转登录页");
                //不存在Cookie，肯定未登录，跳转到登录页面
                response=gotoLogin(response);
            }
            //setRedisValue("token","test",10);
            //String id=getRedisValue("id");
            //System.out.println(id);
        }
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) {

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {

    }

    protected HttpServletResponse gotoLogin(HttpServletResponse response) throws IOException{
        response.sendRedirect(login_url);
        return response;
    }
}
```













# 技巧

## 读取application.properties属性

```java
@Value("${crh.wangluo.tempFilePath}")
private String tempFilePath;
```



## 自定义路径（temp文件夹）

### application.properties属性

```properties
crh.wangluo.tempFilePath=/temp/
crh.wangluo.tempFilePath_static=D:/temp_files/
```

### 增加WebMvcConfigurer

```java
@Configuration
public class WangluoConfig implements  WebMvcConfigurer {
    @Value("${crh.wangluo.tempFilePath}")
    private String tempFilePath;
    @Value("${crh.wangluo.tempFilePath_static}")
    private String tempFilePathStatic;
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler(tempFilePath+"**").addResourceLocations("file:"+tempFilePathStatic);
    }
}

```





### 全局异常处理

```java
package com.geniuscrh.wangluo.system.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

@ControllerAdvice
public class GlobalDefaultExceptionHandler {

    //读取application.properties设置的log文件目录位置
    @Value("${crh.wangluo.log_static}")
    private String logFilePath;

    @ExceptionHandler(Exception.class)
    @ResponseBody
    public Map<String,Object> exception(Exception e){
        Map<String,Object> map=new HashMap<String,Object>();

        System.out.println("---------------------------------------------");
        e.printStackTrace();
        System.out.println("---------------------------------------------");

        /*
        *开始输出log
        */
        File logFile=new File(logFilePath);
        try{
            if(!logFile.exists()){
                logFile.createNewFile();
            }
        }catch(Exception ee){
           ee.printStackTrace();
        }

        try {
            //构造函数中的第二个参数true表示以追加形式写文件
            FileWriter fw = new FileWriter(logFilePath,true);
            StackTraceElement[] errorStrack=e.getStackTrace();
            fw.write("############################################################\r\n");
            fw.write(e.toString()+"\r\n");
            fw.write("---------------------------------------------------------------------------\r\n");
            for(StackTraceElement s:errorStrack){
                fw.write(s.toString()+"\r\n");
            }
            fw.write("############################################################\r\n\r\n\r\n\r\n");

            fw.close();
        } catch (IOException ee) {
        }
		/*
		*结束输出log
		*/
        
        map.put("Class","GlobalDefaultExceptionHandler");
        map.put("msg", e.toString());
        map.put("success",false);
        return map;
    }
}

```



## 配置日志文件

```properties
logging:
  file: ../logs/spring-boot-hello.log
  level.org.springframework.web: DEBUG
```

