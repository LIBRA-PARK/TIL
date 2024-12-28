# 낙관적 락(Optimistic)

![Date](https://img.shields.io/badge/Date-2024--12--25-blue)
![Tag](https://img.shields.io/badge/Tag-Database-white)
---

> 락을 사용하지 않는 락 방식의 하나

- 충돌이 발생하지 않는다고 가정
- Version Column 을 추가 (Number, Hash, Timestamp 등)
- DB 에서 처음 읽어온 Version 을 기억
- Update 시 현재 DB의 Version과 다르다면 롤백

Lock을 사용하지 않음
Versioning 을 이용한 동시성 제어
충돌이 자주 일어나지 않을 때 주로 사용
상대적으로 빠른 속도 ( 비관적 락 보다 빠름)