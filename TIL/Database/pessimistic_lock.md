# 비관적 락(Pessimistic)

![Date](https://img.shields.io/badge/Date-2024--12--25-blue)
![Tag](https://img.shields.io/badge/Tag-Database-white)
---
> 실제 락을 사용하는 동시성 제어 하는 방식

- 충돌이 자주 발생할 것이라고 가정
- Table 이나 Row Lock을 걸고 트랜잭션 작업
- 다른 트랜잭션은 Lock 획득까지 대기
- 해당 작업 완료 시 Lock 해제

Lock을 사용
Lock을 통한 동시성 제어
충돌이 자주 일어날 때 주로 사용
상대적으로 느린 속도 ( 락으로 인한 처리가 늦어지기 때문에, 낙관적 락 보단 느릴 수 있음)

래퍼런스

[우테코](https://youtu.be/era8W7q3CeQ?si=fuzQWfd3vt8Nw8Yf)