### 문자열 체크 로직 차이

문자열 체크를 할 때 Spring Framework에서 제공하는 util중 StringUtils에 있는 isEmpty를 사용하곤 했었습니다.  

하지만 hasText를 알게되면서 isEmpty(), hasText() 두개가 어떤 차이점이 알아봅시다.

### isEmpty
```java
public static boolean isEmpty(@Nullable Object str) {
    return str == null || "".equals(str);
}
```

### hasText

```java
public static boolean hasText(@Nullable String str) {
    return str != null && !str.isEmpty() && containsText(str);
}

private static boolean containsText(CharSequence str) {
    int strLen = str.length();
    
    for(int i = 0; i < strLen; ++i) {
      if (!Character.isWhitespace(str.charAt(i))) {
        return true;
      }
    }
    
    return false;
}
```

StringUtils.hasText 내부로직을 살펴보면 위와 같이 되어있습니다.  
return문을 보시면 null체크와 빈값체크까지는 isEmpty와 동일합니다. 단 뒤에 containsText 메소드를 통해 whitespace까지 체크하는 것을 볼 수 있습니다.

그리고 

### 차이점 테스트
```java
@Test
void String체크_차이점_테스트() {
  assertThat(!StringUtils.isEmpty(" ")).isTrue();
  assertThat(StringUtils.hasText(" ")).isFalse();
}
```
위 와 같이 whitespace에 대해서 isEmpty는 체크를 안하고 hasText에서 체크가 되는 것을 확인 할 수 있습니다.


### 마무리 
최근 Spring 5.3.9 버전부터 isEmpty도 deprecated 되어서 더이상 지원하지 않는다고 하니 isEmpty보단 hasText를 사용하는게 좋아보이네요.

또, Java11버전에서 String클래스에 isBlank가 생겼습니다. isBlank또한 hasText와 동일하게 whitespace까지 체크되므로 isBlank 사용도 고려해볼만 합니다.
