# ISOLATION 레벨

![Date](https://img.shields.io/badge/Date-2024--12--24-blue)
![Tag](https://img.shields.io/badge/Tag-Database-white)
---

## READ UNCOMMITTED

> 트랜잭션에서 커밋되지 않은 데이터에도 접근할 수 있는 격리 수준.

- **특장점**
    - 가장 낮은 격리 수준으로, 성능이 매우 뛰어남.
    - 읽기 작업이 우선시되고 데이터 일관성이 덜 중요한 경우에 적합.

- **고려할 사항**
    - Dirty Read, Non-Repeatable Read, Phantom Read 모두 발생 가능.
    - 데이터 무결성 보장이 거의 없음.
    - 잘못된 데이터 기반으로 작업이 진행될 위험성 존재.
    - 로그 분석 시스템처럼 데이터 정확성이 중요하지 않은 경우에만 사용 권장.

> Dirty Read : 어떤 트랜잭션에서 처리한 작업이 완료되지 않았음에도 다른 트랜잭션에서 조회할 수 있는 현상

> Non-Repeatable Read : 같은 트랜잭션 내에서 같은 행을 반복해서 읽었을 때, 다른 트랜잭션에서 해당 행을 삭제 및 수적 시 결과가 달라질 수 있는 현상

> Phantom Read : 같은 트랜잭션 내에서 동일한 쿼리를 반복 실행 시, 다른 트랜잭션에서 새로운 데이터 행이 삽입 되거나 삭제되는 현상
---

## READ COMMITTED

> 다른 트랜잭션에서 커밋하여 완료된 데이터에만 접근할 수 있는 격리 수준.

- **특장점**
    - Dirty Read 방지.
    - 대부분의 RDBMS(예: Oracle, SQL Server)에서 기본 설정으로 사용.
    - 트랜잭션 격리와 동시성 간 균형을 제공.

- **고려할 사항**
    - Non-Repeatable Read, Phantom Read 발생 가능.
    - 데이터 변경이 빈번하거나 읽기 작업의 일관성이 중요하지 않은 환경에서 적합.
    - 데이터 무결성을 강하게 요구하지 않는 경우에 적합.

---

## REPEATABLE READ

> 모든 트랜잭션이 레코드 조회에는 동시에 접근할 수 있지만, 수정이나 쓰기 작업에 대해서는 순차적으로 처리하여, 조회 작업은 동시 처리 가능하나 수정 및 쓰기는 동시 접근이 불가능한 격리 수준.

- **특장점**
    - 데이터 읽기의 일관성을 보장.
    - Dirty Read, Non-Repeatable Read 방지.
    - 팬텀 리드가 발생할 가능성은 있지만 일반적인 데이터 무결성 요구에는 적합.

- **고려할 사항**
    - Phantom Read 방지하지 않으므로 데이터 삽입이 중요한 시나리오에서는 추가적인 조치 필요.
    - 높은 동시성이 필요한 환경에서는 성능 문제가 발생할 수 있음.
    - MVCC를 지원하는 DB에서 효율적으로 동작(예: MySQL의 InnoDB).

> 일반적인 DBMS는 SELECT FOR UPDATE 가 사용되는 경우 문제가 발생한다. SELECT FOR UPDATE는 새로운 데이터 삽입에 대해서는 막질 못한다.

> 단, MySQL InnoDB는 Repeatable Read 격리 수준에서 Phantom Read를 방지한다. 왜나하면 MySQL 에서는 검색 조건 범위에 해당되는 모든 행에 대해서 락을 걸어 새로운 데이터 삽입을 방지한다.
> 

---

## SERIALIZABLE

> 모든 트랜잭션이 작업을 직렬화된 순서로 처리하도록 강제하여, 동시에 동일한 데이터에 접근하거나 작업을 처리할 수 없게 만드는 격리 수준.

- **특장점**
    - 데이터 무결성과 일관성을 가장 강하게 보장.
    - Dirty Read, Non-Repeatable Read, Phantom Read 모두 방지.
    - 동시성 문제를 완전히 제거.

- **고려할 사항**
    - 트랜잭션 간 동시성 매우 낮음.
    - 성능 저하가 발생할 수 있으므로 고성능이 중요한 시스템에서는 사용에 주의 필요.
    - 높은 락 충돌 가능성으로 인해 데드락 발생 위험 증가.

---

## 비교 표

| **격리 수준**         | **방지되는 문제**                   | **특장점**                        | **고려 사항**                        |
|----------------------|-----------------------------------|---------------------------------|-----------------------------------|
| **READ UNCOMMITTED** | 없음                             | 성능 우수                         | 모든 문제 발생 가능, 무결성 보장 없음 |
| **READ COMMITTED**   | Dirty Read                       | Dirty Read 방지, 트랜잭션 간 균형 제공 | Non-Repeatable Read, 팬텀 리드 가능 |
| **REPEATABLE READ**  | Dirty Read, Non-Repeatable Read   | 데이터 읽기의 일관성 보장           | 팬텀 리드 발생 가능, 성능 저하 가능성 |
| **SERIALIZABLE**     | Dirty Read, Non-Repeatable Read, Phantom Read | 데이터 무결성과 일관성 보장, 모든 문제 방지 | 성능 저하, 데드락 가능성, 낮은 동시성 |

## Reference
[10분 테코톡 - 러쉬의 MySQL 트랜잭션 격리 수준](https://youtu.be/QHWwNTGkwAU?si=W8ZJkbtUTpi3Fq50)
