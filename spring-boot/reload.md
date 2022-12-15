# 소스 변경시 자동 reload하기

**기록 ✍️**

#### author : jung yuha

#### **first Registered :** 2022-10-28 Fri

#### last modified : 2022-10-28 Fri

## \[0] 적용 스택

* **spring-boot-devtools 라이브러리**

## \[1] build.gradle에 의존성 추가

1. 다음과 같이 의존성을 추가
2. 프로젝트 우클릭 - Gradle -  Refresh Gradle Project 를 실행한다.

#### build.gradle

```properties
dependencies {    
    implementation 'org.springframework.boot:spring-boot-devtools'
}
```
