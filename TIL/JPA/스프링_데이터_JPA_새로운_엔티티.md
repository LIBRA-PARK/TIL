# Spring Data JPA 의 새로운 Entity 판단 기준

![Date](https://img.shields.io/badge/Date-2025--04--13-blue)
![Tag](https://img.shields.io/badge/Tag-Spring-green)
![Tag](https://img.shields.io/badge/Tag-Spring--Data--JPA-white)
---
`Spring` 과 `Spring Data JPA`를 사용하다보면 단순 엔티티를 사용해서 `@Repository`이용해서 `Entity`를 저장할 경우,
엔티티의 존재 유무에 따라 알아서 저장되고 수정되는 편리한 동작을 제공한다. 그렇다면 개발자가 따로 체크할 필요 없이 어떻게 새로운 `Entity`임을 알아내는 걸까?

---

## 메소드의 구현체
```java
@Transactional
public <S extends T> S save(S entity) {
    Assert.notNull(entity, "Entity must not be null");
    if (this.entityInformation.isNew(entity)) { //Entity 구분 조건 분기
        this.entityManager.persist(entity);
        return entity;
    } else {
        return this.entityManager.merge(entity);
    }
}
```
`org.springframework.data.jpa.repository.JpaRepository` 인터페이스에 `save`의 메소드가 정의되어있으며, 그 구현체는 `SimpleJpaRepository`로 위의 코드로 구현되어있다.
`save` 메소드는 파라미터로 받은 `entity`에 대해서 `isNew(entity)` 반환값을 기준으로 `persist`와 `merge`로직을 분리한다.
* **persist**
  * 기존에 `entity` 존재해 배로 `INSERT` 쿼리를 실행
* **merge** 
  * `entity`의 `id`를 통해서 1차 캐시를 조회
    * 존재 하지 않는다면 테이블에 `SELECT` 쿼리를 통해서 조회 후 1차 캐시에 저장
  * 조회된 `entity`를 병합 후 반환

---

## isNew 메소드

```java
public boolean isNew(T entity) {
    ID id = this.getId(entity);
    Class<ID> idType = this.getIdType();
    if (!idType.isPrimitive()) {
        return id == null;
    } else if (id instanceof Number) {
        return ((Number)id).longValue() == 0L;
    } else {
        throw new IllegalArgumentException(String.format("Unsupported primitive id type %s", idType));
    }
}
```
`isNew` 메소드에 동작을 확인하기 위해서 `org.springframework.data.repository.core.support`패키지에 `AbstractEntityInformation` 참고했다.
해당 메소드는`entity`의 식별자와 타입을 가져와 `wrapper`와 `primitive` 타입에 판단 조건을 달리한다.
* **wrapper**
  * null 체크를 통해서 식별자를 구분
* **primitive**
  * 숫자 타입이면서, 0 인지 체크



