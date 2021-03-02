# 14. 컬렉션과 부가 기능

### @Converter

컨버터를 사용하면 엔티티의 데이터를 변환해서 데이터베이스에 저장할 수 있다.

```java
@Converter
public class BooleanToYNConverter implements AttributeConverter<Boolean, String> {
    
    @Override
    public String convertToDatabaseColumn(Boolean attribute) {
        return (attribute != null && attribute) ? "Y" : "N";
    }
    
    @Override
    public Boolean convertToEntityAttribute(String dbData) {
        return "Y".equals(dbData);
    }
}
```

컨버터 클래스는 @Converter 어노테이션을 사용하고 AttributeConverter 인터페이스를 구현해야 한다.  
그리고 제네릭에 현재 타입과 반환할 타입을 지정해야 한다.  

모든 Boolean 타입에 컨버터를 적용하려면 @Converter(autoApply = true) 옵션을 적용하면 된다.  
글로벌 설정을 하면 따로 엔티티에 @Convert를 지정하지 않아도 모든 Boolean 타입에 대해 자동으로 컨버터가 적용된다.

### 리스너

JPA 리스너 기능을 사용하면 엔티티의 생명주기에 따른 이벤트를 처리할 수 있다.

PostLoad
- 엔티티가 영속성 컨텍스트에 조회된 직후 또는 refresh를 호출한 후(2차 캐시에 저장되어 있어도) 호출된다.

PrePersist  
- persist() 메서드를 호출해서 엔티티를 영속성 컨텍스트에 관리하기 직전에 호출된다.  
식별자 생성 전략을 사용한 경우 엔티티에 식별자는 아직 존재하지 않는다.

PreUpdate
- flush나 commit을 호출해서 엔티티를 데이터베이스에 수정하기 직전에 호출된다.  

PreRemove
- remove() 메서드를 호출해서 엔티티를 영속성 컨텍스트에서 삭제하기 직전에 호출된다.  

PostPersist
- flush나 commit을 호출해서 엔티티를 데이터베이스에 저장한 직후에 호출된다.

PostUpdate
- flush나 commit을 호출해서 엔티티를 데이터베이스에 수정한 직후에 호출된다.  

PostRemove
- flush나 commit을 호출해서 엔티티를 데이터베이스에 삭제한 직후에 호출된다.

이벤트는 엔티티에서 직접 받거나 별도의 리스너를 등록해서 받을 수 있다.  

엔티티에 직접 적용하는 방법은 엔티티에 이벤트가 발생할 때마다 어노테이션으로 지정한 메서드가 실행된다.

@EntityListeners(DuckListener.class) 를 붙여 별도의 리스너를 등록해서 사용할 수도 있다.

이외 모든 엔티티의 이벤트를 처리하기 위해 기본 리스너로 등록하여 사용하는 방법도 있다.  

여러 리스너를 등록했을 때 아래와 같은 순서로 이벤트가 호출된다.  
1. 기본 리스너
2. 부모 클래스 리스너
3. 리스너
4. 엔티티