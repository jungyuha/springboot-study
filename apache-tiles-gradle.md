# Apache Tiles 적용하기(Gradle)

**기록 ✍️**

#### author : jung yuha

#### **first Registered :** 2022-10-28 Fri

#### last modified : 2022-10-28 Fri

## \[1] Tiles 설정하기

### (1) build.gradle에 의존성 추가

1. 다음과 같이 의존성을 추가
2. 프로젝트 우클릭 - Gradle -  Refresh Gradle Project 를 실행한다.

#### build.gradle

```properties
dependencies {    
    implementation 'org.apache.tiles:tiles-jsp:3.0.8'
}
```

### (2) TilesConfig.java 파일 생성

config 폴더 내에 TilesConfig.java 파일을 다음과 같이 생성한다.

```java
@Configuration
public class TilesConfig implements WebMvcConfigurer {
    @Bean
    public TilesConfigurer tilesConfigurer() {
        TilesConfigurer configurer = new TilesConfigurer();
        configurer.setDefinitions(new String[]{"/WEB-INF/tiles/tiles.xml"});
        configurer.setCheckRefresh(true);
        return configurer;
    }

    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        TilesViewResolver viewResolver = new TilesViewResolver();
        registry.viewResolver(viewResolver);
    }
}
```

### (3) 기본 jsp 생성

#### 1. attribute JSP 생성

1. WEB-INF 아래의 tiles/layout/attributes 디렉토리를 생성한다.
2. attributes 디렉토리 내에 기본적인 jsp를 생성한다.

![](<.gitbook/assets/image (3).png>)

#### 2. layout JSP 생성

layout 디렉토리 내에 layout jsp를 생성한다.

![](<.gitbook/assets/image (5).png>)

#### 3. body에 들어갈 JSP 생성

템플릿 내의 body에 들어갈 JSP를 생성한다.

![](<.gitbook/assets/image (2).png>)

### (4) tiles.xml 생성

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE tiles-definitions PUBLIC 
        "-//Apache Software Foundation//DTD Tiles Configuration 3.0//EN"
        "http://tiles.apache.org/dtds/tiles-config_3_0.dtd">
<tiles-definitions>
    <definition name="/*" template="/WEB-INF/tiles/layout/mainLayout.jsp" >
      <put-attribute name="body" value="/WEB-INF/views/{1}.jsp" />
      <put-attribute name="footer" value="/WEB-INF/tiles/layout/attributes/footer.jsp" />
    </definition>
```

### (5) 컨트롤러 생성

```java
@Controller
public class testController {
	
    @RequestMapping("/test")
    public ModelAndView test(Model model) {
	List<String> list = new ArrayList<>();
	model.addAttribute("list", list);     
        return new ModelAndView("/test1");
    }
}
```

