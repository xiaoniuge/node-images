# Feign

### 一、header 传递

```yml
# 1、yml 配置
feign:
  httpclient:
    enabled: false
  okhttp:
    enabled: true
  hystrix:
    enabled: true

hystrix:
  command:
    default:
      execution:
        isolation:
          strategy: SEMAPHORE
```

```java
package com.cloud.demo.b;

import feign.RequestInterceptor;
import feign.RequestTemplate;
import org.springframework.stereotype.Component;
import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;

import javax.servlet.http.HttpServletRequest;
import java.util.Enumeration;
import java.util.LinkedHashMap;
import java.util.Map;

/**
 * @author nf
 * @since 2020/5/22 17:22
 */
@Component
public class FeignInterceptor implements RequestInterceptor {
    @Override
    public void apply(RequestTemplate requestTemplate) {
        try {
            Map<String,String> headers = getHeaders();
            for(String headerName : headers.keySet()){
                requestTemplate.header(headerName, headers.get(headerName));
            }
        }catch (Exception e){
            e.printStackTrace();
        }
    }
    private Map<String, String> getHeaders(){
        HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getRequest();
        Map<String, String> map = new LinkedHashMap<>();
        Enumeration<String> enumeration = request.getHeaderNames();
        while (enumeration.hasMoreElements()) {
            String key = enumeration.nextElement();
            String value = request.getHeader(key);
            map.put(key, value);
        }
        return map;
    }

}

```

