---
description: JWT의 개념과 토큰 생성 및 검증하기
---

# JWT의 개념과 토큰 생성 및 검증하기

**기록 ✍️**

#### author : jung yuha

#### **first Registered :** 2022-10-05 Wed

#### last modified : 2022-10-05 Wed

## \[1]JWT의 개념과 기초

### 1) JWT 개념

* JSON Web Token의 줄임말이다.
* JSON 객체를 안전하게 주고받기 위해 사용된다.
* HMAC, RSA 등의 암호화 방식을 사용한다.

### 2) JWT 구조

* JWT는 **aaaaa.bbbbb.cccccc의 형태를 가진다.**
  * 차례로 **Header, Payload, Signature**로 구성되며 위 형식에서 각각 점으로 구분된다.

#### &#x20;1. Header : 토큰 유형(JWT)과 사용중인 서명 알고리즘(HMAC SHA256 등)을 포함한다.

```json
{
"typ" : "JWT",    // 토큰 유형(JWT)
"alg" : "HS256"    // 서명 알고리즘
}
```

#### 2. Payload : 실질적인 사용자의 정보를 담은 `클레임`으로 구성된다.

* **Registered Claims , Private Claims , Public Claims**로 구분된다.
  * **Registered Claims(등록된 클레임)**: iss(발행자), exp(만료시간), sub(주제), aud(청중) 등이 있다.
  * **Public Claims(개인 클레임)** : 사용자의 원하는 정보를 담을 수 있는 지정 클레임이다.

```json
{
"sub" : "yuha",
"exp" : 1659002265,
"userName" : "yuha",
"userType" : "admin"
}
```

#### 3. Signature

* **Header과 Payload**를 각각 **Base64 URL-sale Encoding**을 적용하여 **해싱한뒤 비밀키로 서명한 값**이다.
  * 즉 ,**Header, Payload, secret key 를 합쳐 암호화한 결과값**이다.
* 만약 비밀키로 암호화하지 않는다면 JWT의 Header와 payload는 다시 디코딩하여 누구나 값을 확인할 수 있게된다. 암호화가 반드시 필요한 이유이다.

```
HS256( base64UrlEncode( header ) + "." + base64UrlEncode( payload ) , "yuhaSecretKey" )
```

#### 결과값

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9
.eyJzdWIiOiJlcG1zX2F1dGhJbmZvIiwic3VwcGx5X25hbWUiOiJwYXNzIn0
.1n6rWO9p_BzbZp5EG4kPZ70YGf8OKOI_41E09h_I8WNr9_TCsvNcN1Y__fBROSiHT9bO3_VLiqb0nLQcVS2y6g
```

## \[2] JWT 토큰 생성 및 검증 2가지 방법

### &#x20;1) jsonwebtoken 라이브러리 사용하기

#### JWT 토큰 생성

* Map 형태로 직접 Header와 Payload를 지정한다.
* builder 메서드를 통해 토큰을 생성한다.

#### JWT 토큰 검증

* parser 메서드를 통해 토큰을 검증한다.

#### 의존성 추가

```
implementation group: 'io.jsonwebtoken', name: 'jjwt', version: '0.9.1'
```

#### 예제

```java
import java.io.UnsupportedEncodingException;
import java.util.HashMap;
import java.util.Map;

import io.jsonwebtoken.Claims;
import io.jsonwebtoken.ExpiredJwtException;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;

public class JwtUtils {
        // jwt 암호화 키 값
	private final String key = "yuhaSecretKey";
	   
	// jwt 토큰 생성 메서드
	public String createJwt(String userName, String userType) {
	//payload에 넣을 사용자 정보를 받아온다.

        //Header 부분 설정
        Map<String, Object> headers = new HashMap<>();
        headers.put("typ", "JWT"); // 토큰 유형
        headers.put("alg", "HS256"); // 서명 알고리즘

        //payload 부분 설정 : 실질적인 사용자 정보
        Map<String, Object> payloads = new HashMap<>();
        payloads.put("userName", userName);
        payloads.put("userType", userType);
        
        // 토큰 Builder를 통해 JWT 토큰 생성
        String jwt = Jwts.builder()
                .setHeader(headers) // Header 설정
                .setClaims(payloads) // Claims 설정
                .signWith(SignatureAlgorithm.HS256, key.getBytes()) // HS256 서명 알고리즘과 Key로 암호화
                .compact(); // 토큰 생성

        return jwt;
    }

    //parser 메서드를 통한 검증 jwt 토큰 검증
    public Map<String, Object> verifyJwt1(String jwt) throws UnsupportedEncodingException {
        Map<String, Object> claimMap = null;
        try {
            Claims claims = Jwts.parser() // parser 메서드를 통한 검증
                    .setSigningKey(key.getBytes("UTF-8")) // 암호를 풀 키 값 설정
                    .parseClaimsJws(jwt) // jwt의 정보를 파싱해서 시그니처 값을 검증한다.
                    .getBody();

            claimMap = claims;
            
        } catch (ExpiredJwtException e) { // 토큰이 만료되었을 경우
            System.out.println(e);
            
        } catch (Exception e) { // 그 외 에러를 처리한다.
            System.out.println(e);
        }
        return claimMap;
    }    
}
```

### 2) java-jwt 라이브러리 사용하기

#### JWT 토큰 생성

* create 메서드를 통해 토큰을 생성한다.

#### JWT 토큰 검증

* require 메서드를 통해 토큰을 검증한다.

#### 의존성 추가

```
implementation group: 'com.auth0', name: 'java-jwt', version: '3.19.2'
```

#### 예제

```java
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

public class JwtUtils {
        // jwt 암호화 키 값
	private final String key = "yuhaSecretKey";
	   
	// jwt 토큰 생성 메서드
	public String createJwt(HttpServletRequest request, HttpServletResponse response) throws IOException, ServletException{
            // create 메서드를 통한 JWT 토큰 생성
            String jwtToken = JWT.create()
                            .withSubject("yuha")    // Claims 설정(사용자 정보)
                            .withExpiresAt(new Date(System.currentTimeMillis() + 1000*60*30 ))
                            .withClaim("userName", "userName1")
                            .withClaim("userType", "userType1") 
                            .sign(Algorithm.HMAC256(key));    // 키값으로 암호화하기
            return jwtToken;
    }

    //require 메서드를 통한 검증 jwt 토큰 검증
    public boolean verifyJwt2(ttpServletRequest request, HttpServletResponse response) throws IOException, ServletException {
        tring jwtHeader = request.getHeader("Authorization");
        LOGGER.info("jwtHeader : ", jwtHeader);

        if(jwtHeader == null || !jwtHeader.startsWith("Bearer")) { // 헤더값 누락 : 검증 실패
            return false;
        }

        String jwtToken = jwtHeader.replace("Bearer ", "");
        // require 메서드를 통한 검증 jwt 토큰 검증
        String userName = JWT.require(Algorithm.HMAC256(key))
                .build().verify(jwtToken).getClaim("userName").asString();

        if(username != null) {
            LOGGER.info("검증 완료 ::: "+userNameava);
        }
    }    
}
```
