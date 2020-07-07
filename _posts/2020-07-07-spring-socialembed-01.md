---
title: [SpringBoot] 소셜 게시글을 가져오자!
date: 2020-07-07 11:30:50
photos:
 - https://images.velog.io/images/jbb9229/post/2e922545-6d6e-4e66-b2d6-1200ed9de0ae/localhost_8080_%20(4).png
categories:
 - Social API
tags: 
 - SpringBoot
 - SNS
 - Facebook
 - Instagram
 - Twitter
 - Tiktok
 - SocialAPI
---
# SNS Embed?
>
최근 기사나 블로그들을 보면 SNS의 게시글을 SNS에서 보는 것처럼 
그대로 가져온 것을 보신적 있으신가요?
프로젝트에 해당 기능을 추가하며 내용을 공유하고자 작성합니다.

해당 기능은 모든 소셜에서 퍼가기 기능을 통해 HTML 코드를 반환하고 있습니다.
이번 포스트에서는 해당 기능을 소셜의 URL만을 이용하여 
HTML을 반환받는 API Server를 만들어볼 예정입니다.

각 소셜마다 공식 Document를 확인해보면 Embed API를 확인할 수 있습니다.

## Social API List
1. [Facebook](https://developers.facebook.com/docs/plugins/embedded-posts/?locale=ko_KR)
2. [Instagram](https://developers.facebook.com/docs/instagram/embedding/)
3. [Twitter](https://developer.twitter.com/en/docs/twitter-for-websites/embedded-tweets/overview)
4. [TicToc](https://developers.tiktok.com/doc/Embed)
<br/>

## Code Example
> 실제 프로젝트에서는 에디터의 발췌과정에서 URL을 분석하여
발췌 결과물을 소셜 게시물로 표현했지만 편의상 API의 형태로 작성하였습니다.

### Spring Boot Setting

예제 프로젝트에서 사용할 기술은 다음과 같습니다.

1. [Spring Boot Starter Web](https://www.javatpoint.com/spring-boot-starter-web)
	- Spring Boot에서 사용하는 Web Starter Project  입니다
    - Auto Configuration을 지원합니다
    - Spring MVC, REST 및 내장 Tomcat을 지원합니다.

<br/>

해당 모듈의 의존성을 추가해줍니다.
 - build.gradle
```gradle
    implementation 'org.springframework.boot:spring-boot-starter-web'
```
 - pom.xml
```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
```

### Controller

```java
@RestController
public class EmbedController {

    @Autowired
    EmbedService embedService;

    @GetMapping("/api/socialembed")
    public HttpEntity<Map<String, Object>> socialEmbed(
                                        @RequestParam(value = "url") String url) {
        return embedService.embedProcess(url);
    }

}
```	

### Service

```java
@Service
public class EmbedService {

    private final static String FACEBOOKURL = "https://www.facebook.com/plugins/post/oembed.json/?url=";
    private final static String INSTAGRAMURL = "https://api.instagram.com/oembed?url=";
    private final static String TWITTERURL = "https://publish.twitter.com/oembed?url=";
    private final static String TIKTOKURL = "https://www.tiktok.com/oembed?url=";

    /**
     * 파라미터로 받아온 URL이 어떤 소셜의 URL인지 분기
     * @param paramUrl
     * @return
     * @throws URISyntaxException
     */
    private static String getDomainName(String paramUrl) throws URISyntaxException {
        URI uri = new URI(paramUrl);
        String domain = uri.getHost();
        if (domain.startsWith("www.")) {
            domain = domain.substring(4);
        }
        if (domain.endsWith(".com")) {
            domain = domain.substring(0, domain.length()-4);
        }
        return domain;
    }

    /**
     * 분기한 소셜에 맞게 실제로 Embed 처리할 Method를 호출
     * 지원하지 않는 소셜이거나 잘못된 URL은 실패처리
     * @param paramUrl
     * @return
     */
    public HttpEntity<Map<String, Object>> callEmbedProcess(String paramUrl) {
        Map<String, Object> result = new HashMap<>();
        String domain = "";
        try {
            domain = getDomainName(paramUrl);
        } catch (URISyntaxException e) {
            result.put("result", "Fail");
            result.put("response", "잘못된 URL 입니다.");
            return new ResponseEntity<>(result, HttpStatus.BAD_REQUEST);
        }

        if (domain.equals("instagram")) {
            return getInstagramHTML(paramUrl);
        } else if (domain.equals("facebook")) {
            return getFacebookHTML(paramUrl);
        } else if (domain.equals("twitter")) {
            return getTwitterHTML(paramUrl);
        } else if (domain.equals("tiktok")) {
            return getTiktokHTML(paramUrl);
        } else {
            result.put("result", "Fail");
            result.put("response", "현재 지원하지 않는 Social 이거나, 잘못된 URL 입니다. 다시 확인해주시기 바랍니다.");
            return new ResponseEntity<>(result, HttpStatus.BAD_REQUEST);
        }
    }


    /**
     * Facebook Embed
     * @param paramUrl
     * @return
     */
    private HttpEntity<Map<String, Object>> getFacebookHTML(String paramUrl) {
        Map<String, Object> embedResult = new HashMap<>();
        Map<String, Object> result = new HashMap<>();

        // 정규표현식을 사용해 Facebook이 Embed를 지원하는 URL인지 확인
        boolean isFacebookPost = Pattern.compile("https://www.facebook.com/.*?/(posts|photos|videos)/.*?").matcher(paramUrl).find();
        
        // 지원하지 않는 URL은 실패 처리
        if (isFacebookPost == false) {
            result.put("result", "Fail");
            result.put("response", "지원하지 않는 형식의 Facebook URL 입니다.");
            return new ResponseEntity<>(result, HttpStatus.BAD_REQUEST);
        }
 
        // 지원하는 URL이 판단되면 API를 통해 HTML을 받아온다.
        RestTemplate template = new RestTemplate();
        String embedResponse = template.getForObject(FACEBOOKURL + paramUrl, String.class);
        
        // TODO: Facebook return Type Check
        // 페이스북의 경우 text 형태로 값을 리턴하기에 jackson을 사용하여 다시 Map으로 만들어준다.
        ObjectMapper mapper = new ObjectMapper();
        try {
            embedResult = mapper.readValue(embedResponse, new TypeReference<Map<String, String>>(){});
        } catch (IOException e) {
            e.printStackTrace();
            result.put("result", "Fail");
            result.put("response", "지원하지 않는 형식의 Facebook URL 입니다.");
            return new ResponseEntity<>(result, HttpStatus.BAD_REQUEST);
        }

        result.put("result", "success");
        result.put("response", embedResult.get("html"));

        return new ResponseEntity<>(result, HttpStatus.OK);
    }

    /**
     * Instagram Embed
     * @param paramUrl
     * @return
     */
    private HttpEntity<Map<String, Object>> getInstagramHTML(String paramUrl) {
        Map<String, Object> embedResult = new HashMap<>();
        Map<String, Object> result = new HashMap<>();

        // 인스타그램 포스트 확인
        boolean isInstagramPost = Pattern.compile("(https://www.instagram.com/p/.*?)").matcher(paramUrl).find();
        if (isInstagramPost == false) {
            result.put("result", "Fail");
            result.put("response", "지원하지 않는 형식의 Instagram URL 입니다.");
            return new ResponseEntity<>(result, HttpStatus.BAD_REQUEST);
        }

        // API 요청
        RestTemplate template = new RestTemplate();
        embedResult = template.getForObject(INSTAGRAMURL + paramUrl, Map.class);

        result.put("result", "success");
        result.put("response", embedResult.get("html"));

        return new ResponseEntity<>(result, HttpStatus.OK);
    }

    /**
     * Twitter Embed
     * @param paramUrl
     * @return
     */
    private HttpEntity<Map<String, Object>> getTwitterHTML(String paramUrl) {
        Map<String, Object> embedResult = new HashMap<>();
        Map<String, Object> result = new HashMap<>();
    
        // 트위터 포스트 확인
        boolean isTwitterPost = Pattern.compile("(https://twitter.com/.*/status/.*?)").matcher(paramUrl).find();
        if (isTwitterPost == false) {
            result.put("result", "Fail");
            result.put("response", "지원하지 않는 형식의 Twitter URL 입니다.");
            return new ResponseEntity<>(result, HttpStatus.BAD_REQUEST);
        }

        // API 요청
        RestTemplate template = new RestTemplate();
        embedResult = template.getForObject(TWITTERURL + paramUrl, Map.class);

        result.put("result", "success");
        result.put("response", embedResult.get("html"));

        return new ResponseEntity<>(result, HttpStatus.OK);
    }

    /**
     * Tiktok Embed
     * @param paramUrl
     * @return
     */
    private HttpEntity<Map<String, Object>> getTiktokHTML(String paramUrl) {
        Map<String, Object> embedResult = new HashMap<>();
        Map<String, Object> result = new HashMap<>();

        // 틱톡 포스트 확인
        boolean isTiktokPost = Pattern.compile("(https://www.tiktok.com/.*/video/.*?)").matcher(paramUrl).find();
        if (isTiktokPost == false) {
            result.put("result", "Fail");
            result.put("response", "지원하지 않는 형식의 Instagram URL 입니다.");
            return new ResponseEntity<>(result, HttpStatus.BAD_REQUEST);
        }

        // API 요청
        RestTemplate template = new RestTemplate();
        embedResult = template.getForObject(TIKTOKURL + paramUrl, Map.class);

        result.put("result", "success");
        result.put("response", embedResult.get("html"));

        return new ResponseEntity<>(result, HttpStatus.OK);
    }

}
```

### Test
```java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
public class EmbedControllerTest {

    @Autowired
    private TestRestTemplate template;

    private final String testUrl = "https://www.facebook.com/20531316728/posts/10154009990506729/";

    @Test
    public void embedProcessTest() {
        ResponseEntity<Map> response = template.getForEntity("/api/socialembed?url=" + testUrl, Map.class);
        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(response.getBody().get("result")).isEqualTo("success");
    }

}
```

## Request & Response

### Sample Request

```http request
    http://localhost:{port-number}/api/socialembed?url={social-url}
```

### Sample Response
 
 - Success
 
```json
{
    "result": "success",
    "response": "<div id=\"fb-root\"></div>\n<script async=\"1\" defer=\"1\" crossorigin=\"anonymous\" src=\"https://connect.facebook.net/ko_KR/sdk.js#xfbml=1&amp;version=v7.0\" nonce=\"H1Jocr3v\"></script><div class=\"fb-post\" data-href=\"https://www.facebook.com/20531316728/posts/10154009990506729/\" data-width=\"552\"><blockquote cite=\"https://www.facebook.com/20531316728/posts/10154009990506729/\" class=\"fb-xfbml-parse-ignore\">게시: <a href=\"https://www.facebook.com/facebookapp/\">Facebook App</a>&nbsp;<a href=\"https://www.facebook.com/20531316728/posts/10154009990506729/\">2015년 8월 27일 목요일</a></blockquote></div>"
}
```

 - Fail
 
```json
{
    "result": "Fail",
    "response": "지원하지 않는 형식의 Facebook URL 입니다."
}
```

### Embed 처리가 가능한 소셜 별 URL

1. Facebook
 - https://www.facebook.com/{page-name}/posts/{post-id}
 - https://www.facebook.com/{username}/posts/{post-id}
 - https://www.facebook.com/{page-name}/photos/{photo-id}/
 - https://www.facebook.com/{username}/photos/{photo-id}/
 - https://www.facebook.com/{page-name}/videos/{video-id}/
 - https://www.facebook.com/{username}/videos/{video-id}/
 
2. Instagram
 - https://www.instagram.com/p/{post-id}/
 
3. Twitter
 - https://twitter.com/{username}/status/{post-id}
 
4. TikTok
 - https://www.tiktok.com/{username}/video/{post-id}