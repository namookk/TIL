## MapStruct

JPA를 사용하다보면 Entity객체를 Dto로 변환해주는 작업이 필요합니다.

라이브러리 없이 맵핑하기 위해서 메소드를 생성해봅시다.
```java
class User{
	private Long id;
    private String name;
    private String email;
    private Address address;
}
class Address{
	private String zipcode;
    private String address1;
    private String address2;
}

class UserDto{
	private Long id;
    private String name;
    private String email;
    private String zipcode;
    private String addresss;
    
    
    public static UserDto of(User user) {
    	Address address = user.getAddress();
        return UserDto.builder()
        	.id(user.getId())
            .name(user.getName())
            .email(user.getEmail())
            .zipcode(address.getZipcode())
            .address(address.getAddress1() + address.getAddress2())
            .build();
    }
}
```

위와 같이 직접 메소드를 만들어 사용하면 단점이 존재합니다.
+ DTO마다 반복적인 작업이 증가해 쉽게 피로해진다.
+ 필드의 개수가 늘어나거나 엔티티간의 연관관계가 늘어날수록 가독성이 떨어진다.
+ 개발자의 실수로 다른 데이터가 넣어질 수 있다.

즉, 생산성과 유지보수성이 떨어지게 되어 라이브러리를 사용하는데
Entity <-> Dto간의 객체 Mapping을 도와주는 라이브러리로 `MapStruct`, `ModelMapper`가 있습니다.

### MapStruct 사용 이유

이 중 ModelMapper가 아닌 MapStruct를 사용하게 된 이유는 ModelMapper는 Reflection API를 사용하여 객체 필드 정보를 추출하고 매핑하기 때문에 MapStruct보다 성능이 좋지 않습니다.

또, ModelMapper는 일반적으로 Setter를 사용하여 맵핑하려는 객체에 Setter가 필수로 들어가야 합니다.

마지막으로 MapStruct를 사용하면 컴파일 시점에 코드를 생성하면서 타입이나 매핑이 불가능한 상태 등의 문제가 발생한 경우 컴파일 에러를 발생시켜 런타임에서 안정성을 보장합니다.

단, MapStruct를 Lombok과 같이 사용하게 되면 anotation Processor의 동작 순서에 따라 충돌이 발생할 수 있습니다.

### MapStruct 사용하기

MapStruct를 사용하기 위해 dependency를 추가해야 합니다.

#### pom.xml
```xml
<dependency>
  <groupId>org.mapstruct</groupId>
  <artifactId>mapstruct</artifactId>
  <version>1.4.2.Final</version>
</dependency>
<annotationProcessorPaths>
  <path>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct-processor</artifactId>
    <version>1.4.2.Final</version>
  </path>
</annotationProcessorPaths>
```


#### build.gradle
```xml
implementation "org.mapstruct:mapstruct:1.4.2.Final" 
annotationProcessor "org.mapstruct:mapstruct-processor:1.4.2.Final"
```

#### UserMapper
```java
@Mapper
public interface UserMapper{
  UserMapper INSTANCE = Mappers.getMapper(UserMapper.class);

  @Mapping(target = "zipcode", source = "address.zipcode")
  @Mapping(target = "address", source = "address", qualifiedByName = "address")
  UserDto toDto(User user);

  @Named("address")
  default String address(Address address) {
  	return address.getAddress1() + address.getAddress2();
  }
```

#### 생성된 Impl
```java

public class UserMapperImpl implements UserMapper {

    @Override
    public UserDto toDto(User user) {
        if ( user == null ) {
            return null;
        }

        UserDtoBuilder userDto = UserDto.builder();

        userDto.id( user.getId() );
        userDto.name( user.getName() );
        userDto.email( user.getEmail() );
        userDto.zipcode( user.getAddress().getZipcode() );
        userDto.address( address(user.getAddress() ) );

        return userDto.build();
    }
}

```

간단한 예제로 MapStruct 사용해보았습니다. @Mapping 어노테이션에 존재하는 여러 옵션을 사용하여 맵핑방법을 정의해 줄 수 있고 또, @Mapper 어노테이션의 옵션을 통해 다른 Mapper 인터페이스를 참조할 수 있습니다. 

