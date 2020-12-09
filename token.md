### pom文件

~~~~xml
<!-- https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt token-->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.6.0</version>
        </dependency>
~~~~





### 工具类

~~~java
package com.victor.javabackend.utils;

import io.jsonwebtoken.Claims;
import io.jsonwebtoken.JwtBuilder;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

public class JwtUtils {

    public static String create(String id, String username) {
        long now = System.currentTimeMillis();//当前时间
        long exp = now + 1000 * 60 * 60;//过期时间为1小时
        JwtBuilder builder = Jwts.builder()
                .setId(id)
                .setSubject(username)
                .setIssuedAt(new Date())
                .signWith(SignatureAlgorithm.HS256, "victor")
                .setExpiration(new Date(exp));//设置过期时间
        return builder.compact();
    }


    public static Map Parse(String token) {
        String compactJws = token;
        Claims claims = Jwts.parser()
                .setSigningKey("victor")
                .parseClaimsJws(compactJws).getBody();
        Map map = new HashMap();
        map.put("id", claims.getId());
        map.put("username", claims.getSubject());
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy‐MM‐dd hh:mm:ss");
        map.put("startTime", claims.getIssuedAt());
        map.put("lastTime", claims.getExpiration());
        return map;
    }
}

~~~

