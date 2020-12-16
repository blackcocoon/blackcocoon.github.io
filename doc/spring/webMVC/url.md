---
sort: 1
---

# URL 생성

```note
org.springframework.web.util.UriComponentsBuilder  
org.springframework.web.util.DefaultUriBuilderFactory
```

```java
@Test
void test1() {
    var uriComponents = UriComponentsBuilder.newInstance()
                                            .path("/sample/board")
                                            .queryParam("bno", 12)
                                            .queryParam("perPage", 20)
                                            .build();

    // Result : /sample/board?bno=12&perPage=20
    log.info("Result : {}", uriComponents.toUriString());
}

@Test
void test2() {
    var uriComponents = UriComponentsBuilder.newInstance()
                                            .path("/{a}/{b}")
                                            .queryParam("bno", 12)
                                            .queryParam("perPage", 10)
                                            .queryParam("item", "한글테스트")
                                            .build()
                                            .expand("sample", "board")
                                            .encode(StandardCharsets.UTF_8);

    // Result : /sample/board?bno=12&perPage=10&item=%ED%95%9C%EA%B8%80%ED%85%8C%EC%8A%A4%ED%8A%B8
    log.info("Result : {}", uriComponents.toUriString());
}

@Test
void test3() {
    var uriComponents = UriComponentsBuilder.newInstance()
                                            .scheme("https")
                                            .host("www.example.com")
                                            .path("/{path-1}/{path-2}/")
                                            .queryParam("test", "a", "b")
                                            .build()
                                            .expand("java", "example")
                                            .encode();

    // Result : https://www.example.com/java/example/?test=a&test=b
    log.info("Result : {}", uriComponents.toUriString());
}

@Test
void test4() {
    var uriComponents = UriComponentsBuilder
            .fromUriString("https://example.com/hotels/{city}")
            .queryParam("word1", "{word1}")
            .queryParam("word2", "{word2}")
            .encode()
            .buildAndExpand("seoul", "hello", "world");

    // test4 : https://example.com/hotels/seoul?word1=hello&word2=world
    log.info("Result : {}", uriComponents.toUriString());
}


@Test
void test5() {
    var uriComponents = UriComponentsBuilder.newInstance()
                                            .queryParam("page", 2)
                                            .queryParam("perPageNum", 10)
                                            .build();

    // Result : ?page=2&perPageNum=10
    log.info("Result : {}", uriComponents.toUriString());
}

@Test
void test6() {
    var uri = UriComponentsBuilder
            .fromUriString("https://example.com/city/{hotel}?word={word}")
            .build("seoul", "helloworld");

    // Result : https://example.com/city/seoul?word=helloworld
    log.info("Result : {}", uri.toString());
}

@Test
void UriComponentsBuilder_encode_versus_UriComponents_encode() {
    /**
      UriComponentsBuilder_encode : path에 ';' 가 존재하면 '%3B'변환함.
      UriComponents_encode : path에 ';' 가 존재하면 '%3B' 변환하지 않음.
      */

    // encode before build, replace ';' -> '%3B' only in path.
    var uriComponents = UriComponentsBuilder.newInstance()
                                            .path("/{a}")
                                            .queryParam("i;tem", "한글;테스트")
                                            .encode(StandardCharsets.UTF_8)
                                            .build()
                                            .expand("boa;rd");

    // Result : /boa%3Brd?i;tem=%ED%95%9C%EA%B8%80;%ED%85%8C%EC%8A%A4%ED%8A%B8
    log.info("Result : {}", uriComponents.toUriString());

    // encode after build, doesn't replace ';' -> '%3B'
    var uriComponents2 = UriComponentsBuilder.newInstance()
                                             .path("/{a}")
                                             .queryParam("i;tem", "한글;테스트")
                                             .build()
                                             .expand("boa;rd")
                                             .encode(StandardCharsets.UTF_8);

    // Result : /boa;rd?i;tem=%ED%95%9C%EA%B8%80;%ED%85%8C%EC%8A%A4%ED%8A%B8
    log.info("Result : {}", uriComponents2.toUriString());
}

@Test
void test7() {
    // import org.springframework.web.util.DefaultUriBuilderFactory.EncodingMode;

    String baseUrl = "https://example.org";
    DefaultUriBuilderFactory factory = new DefaultUriBuilderFactory(baseUrl);
    factory.setEncodingMode(DefaultUriBuilderFactory.EncodingMode.TEMPLATE_AND_VALUES);

    RestTemplate restTemplate = new RestTemplate();
    restTemplate.setUriTemplateHandler(factory);
}

@Test
void test8() {
    // import org.springframework.web.util.DefaultUriBuilderFactory.EncodingMode;

    String baseUrl = "https://example.org";
    DefaultUriBuilderFactory factory = new DefaultUriBuilderFactory(baseUrl);
    factory.setEncodingMode(DefaultUriBuilderFactory.EncodingMode.TEMPLATE_AND_VALUES);

    // WebClient client = WebClient.builder().uriBuilderFactory(factory).build();

}

@Test
void test9() {
    String baseUrl = "https://example.com";
    
    DefaultUriBuilderFactory uriBuilderFactory = new DefaultUriBuilderFactory(baseUrl);
    
    URI uri = uriBuilderFactory.uriString("/hotels/{hotel}")
                               .queryParam("q", "{q}")
                               .build("Westin", "123");

    // Result : https://example.com/hotels/Westin?q=123
    log.info("Result : {}", uri.toString());
}

@Test
void test10() {
    URI uri = UriComponentsBuilder.fromPath("/hotel list/{city}")
                                  .queryParam("q", "{q}")
                                  .encode()
                                  .buildAndExpand("New York", "foo+bar")
                                  .toUri();

    // Result : /hotel%20list/New%20York?q=foo%2Bbar
    log.info("Result : {}", uri.toString());
}

@Test
void test11() {
    URI uri = UriComponentsBuilder.fromPath("/hotel list/{city}")
                                  .queryParam("q", "{q}")
                                  .build("New York", "foo+bar");

    // Result : /hotel%20list/New%20York?q=foo%2Bbar
    log.info("Result : {}", uri.toString());
}

@Test
void test12() {
    // '?'가 '%3F'로 변경됨.
    URI uri = UriComponentsBuilder.fromPath("/hotel list/{city}?q={q}")
                                  .build("New York", "foo+bar");

    // Result : /hotel%20list/New%20York%3Fq=foo%2Bbar
    log.info("Result : {}", uri.toString());
}

@Test
void test13() {
    String baseUrl = "https://example.com";
    DefaultUriBuilderFactory factory = new DefaultUriBuilderFactory(baseUrl);
    factory.setEncodingMode(DefaultUriBuilderFactory.EncodingMode.TEMPLATE_AND_VALUES);

    // Customize the RestTemplate..
    RestTemplate restTemplate = new RestTemplate();
    restTemplate.setUriTemplateHandler(factory);

    // Customize the WebClient..
    // WebClient client = WebClient.builder().uriBuilderFactory(factory).build();
}
```