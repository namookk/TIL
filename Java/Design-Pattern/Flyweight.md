## Flyweight(플라이웨이트) 패턴

### Flyweight패턴이란?

플라이웨이트 패턴은 어떤 클래스의 인스턴스 한 개만 가지고 여러 개의 가상 인스턴스를 제공하여 객체를 가볍게 만들어 메모리 사용을 줄이는 패턴입니다.

![](https://images.velog.io/images/papakang22/post/9d500547-7077-49f0-be39-148c17c2a4d3/R1920x0.png)

자주 변하는 속성(또는 외적인 속성, extrinsit)과 변하지 않는 속성(또는 내적인 속성, instrinsit)을 분리하고 재사용하여 메모리 사용을 줄일 수 있다.

Flyweight패턴의 예제는 아래와 같습니다.

### Before
```java
public class Character {
    private char value;
    private String color;
    private String fontFamily;
    private int fontSize;

    public Character(char value, String color, String fontFamily, int fontSize) {
        this.value = value;
        this.color = color;
        this.fontFamily = fontFamily;
        this.fontSize = fontSize;
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        Character c1 = new Character('h', "white", "Nanum", 12);
        Character c2 = new Character('e', "black", "Nanum", 12);
        Character c3 = new Character('l', "white", "Nanum", 12);
        Character c4 = new Character('l', "black", "Nanum", 12);
        Character c5 = new Character('o', "white", "Nanum", 12);
    }
}
```

위와 같이 문자를 객체화 시켜 만드는 작업을 한다고 가정한다면 해당 객체의 필드를 플라이웨이트 패턴을 사용하여 경량화 할 수 있습니다.

### After

```java
public class Character {
    private char value;
    private String color;
    private Font font;

    public Character(char value, String color, Font font) {
        this.value = value;
        this.color = color;
        this.font = font;
    }
}
```
```java

//불변한 객체
public final class Font {
    private final String fontFamily;
    private final int size;

    public Font(String fontFamily, int size) {
        this.fontFamily = fontFamily;
        this.size = size;
    }

    public String getFontFamily() {
        return fontFamily;
    }

    public int getSize() {
        return size;
    }
}
```
```java

public class FontFactory {

    private Map<String, Font> cache = new HashMap<>();

    public Font getFont(String font) {
        if (cache.containsKey(font)){
            return cache.get(font);
        }else{
            String[] split = font.split(":");
            Font newFont = new Font(split[0], Integer.parseInt(split[1]));
            cache.put(font, newFont);
            return newFont;
        }
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        FontFactory fontFactory = new FontFactory();
        Character c1 = new Character('h', "white", fontFactory.getFont("nanum:12"));
        Character c2 = new Character('e', "black", fontFactory.getFont("nanum:12"));
        Character c3 = new Character('l', "white", fontFactory.getFont("nanum:12"));
        Character c3 = new Character('l', "black", fontFactory.getFont("nanum:12"));
        Character c3 = new Character('o', "white", fontFactory.getFont("nanum:12"));
    }
}
```

자주 변하는 속성인 문자값과 색상과 자주 변하지 않는 속성인 폰트체와 글씨크기를 분리합니다.
FontFactory에서 캐시화 된 객체를 반환받아 Character객체를 생성함으로써 메모리 사용을 줄일 수 있습니다.

Flyweight패턴은 자바의 모든 래퍼 클래스의 valueOf()메소드에서 사용하고 있습니다.
래퍼 클래스를 생성할 때 new 키워드를 사용하여 인스턴스를 매번 생성하기 보다는 valueOf()를 통해 생성하는 것이 더 메모리 사용을 줄일 수 있습니다.

