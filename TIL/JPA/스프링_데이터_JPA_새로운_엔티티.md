# Spring Data JPA 의 새로운 Entity 판단 기준

![Date](https://img.shields.io/badge/Date-2025--04--13-blue)
![Tag](https://img.shields.io/badge/Tag-Spring-green)
![Tag](https://img.shields.io/badge/Tag-Spring--Data--JPA-white)
---
`Spring` 과 `Spring Data JPA`를 사용하다보면 단순 엔티티를 사용해서 `@Repository`이용해서 `Entity`를 저장할 경우,
엔티티의 존재 유무에 따라 알아서 저장되고 수정되는 편리한 동작을 제공한다. 그렇다면 개발자가 따로 체크할 필요 없이 어떻게 새로운 `Entity`임을 알아내는 걸까?

## 메소드의 구현체
`org.springframework.data.jpa.repository.JpaRepository` 인터페이스에 `save`의 메소드는 아래처럼 구현되어있다.
중요한 부분은 `isNew(entity)` 조건 분기를 통해서 `persist`와 `merge`로직을 분리한다는 것이다. 

```java
    @Transactional
    public <S extends T> S save(S entity) {
        Assert.notNull(entity, "Entity must not be null");
        if (this.entityInformation.isNew(entity)) {
            this.entityManager.persist(entity);
            return entity;
        } else {
            return this.entityManager.merge(entity);
        }
    }
```

그렇다면 핻

## isNew 메소드

