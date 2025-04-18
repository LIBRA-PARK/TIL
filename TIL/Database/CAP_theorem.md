# CAP 이론

![Date](https://img.shields.io/badge/Date-2025--01--06-blue)
![Tag](https://img.shields.io/badge/Tag-Theorem-black)
---

## 일관성(Consistency)

휴대폰을 개통해서 전화번호가 발급되었다면 각 통신사에 해당 번호가 통신사를 통해서 새로 개통되었다고 알려 최신 데이터를 알려주어야만 한다.
그렇지않다면 전국 어느 지점에서 동일한 번호로 또 다른 휴대폰을 개통하게되어 본인 이외의 다른 사람과 동일한 전화번호를 사용하게 되는 불상사가 발생한다.
즉, 내가 `A` 지점 통신사에서 휴대폰을 개통해 전화번호를 발급 받았다면, 다른 `B` 지점에서 해당 사실을 공유받아 동일한 전화번호를 개통할 수 없게 해야만 이런 문제를 막을 수 있다.
이렇게 **하나의 클라이언트의 요청에 의해서 변경된 데이터가 다른 모든 클라이언트에서도 동일한 데이터를 조회**할 수 있어야한다. 
만약에 업데이트된 데이터가 다른 클라이언트에서 업데이트된 데이터가 조회되지 않는다면 일관성이 보장되지 못하는 것을 뜻 한다.

> 시스템을 이루는 분산된 노드들이 반드시 데이터를 동기화 되어 데이터의 일관성을 유지하는 속성

## 가용성(Availability)

편의점은 전국에 지점을 배치해 어디서든 언제든지 제품을 판매해 구매할 수 있는 서비스를 제공하는데, 만약 `A`편의점이 잠시 휴무로 인해서 편의 제품을 구매할 수 없더라도 다른 곳에 있는 `B` 지점을 방문해 편의 제품을 구매할 수 있다.
이 처럼 **모종의 이유로 서비스를 받을 수 없는 곳이 있더라도 다른 곳에서 서비스를 이용할 수 있게 구성해, 언제든지 사용이 가능하고 동작**할 수 있어야한다.
만약에 문제가 발생해 요청에 대해서 올바른 응답하지 못할 경우에는 가용성이 보장되지 못하는 것을 뜻 한다. 

> 시스템이 제공하는 서비스 중 화나가 실패하더라도 정상적인 서비스가 이용할 수 있도록 보장하는 속성

## 분할 내성(Partition tolerance)

전 세계로 이어진 항공 서비스는 한국과 미국간 통신이 일시적으로 연결이 끊겨 잠시 비행기가 예약이 되지 않더라도, 한국과 일본간 연결은 독립적으로 연결되어 운영되기 때문에 정상적으로 이용할 수 있다.
이렇게 한국과 미국에 연결이 끊겨서 이용이 불가한 상황에서도 한국과 일본 예약 서비스는 정상적으로 제공될 수 있도록 구축해 서비스가 중단되지 않고 작동을 보장하는 것이다.
즉, **네트워크에 에러가 발생해서 연결이 끊겨 통신 오류가 발생하더라도 전체적인 시스템은 계속해서 동작**할 수 있어야한다.
만약에 일시적인 네트워크 오류로 서비스간 연결이 끊기더라도 전체적인 시스템에서 동작 오류가 발생할 경우엔 분할 내성이 보장되지 못하는 것을 뜻 한다.

>  네트워크 분할(파티션)이 발생하더라도 시스템이 계속 동작할 수 있는 속성

## CAP 이론 이란?

> 일관성(Consistency), 가용성(Availability), 분할 내성(Partition tolerance) 의 약어로 이루어진 이론으로 세 가지 속성을 동시에 모두 만족시킬 수 없다는 이론

해당 이론에 따르면 해당 세 가지 속성 중 두 가지만 만족시킬 수 있고, 어떤 두 가지 속성을 선택함에 따라서 설계된 시스템의 특징이 결정난다고 말하는 이론이다.
예를들어, 돈 관련 서비스를 제공하는 **금융 시스템은 데이터의 일관성이 유지되어야만 금전적인 문제 발생을 막을 수 있고, 장애나 오류로 인해서 이체에 실패하거나 입금에 실패할 순 있지만 데이터가 변경되진 말아야 합니다.**
반대로 **소셜 플랫폼이나 메시지 관련된 서비스는 모든 사용자에게 반드시 동일한 데이터를 보여줄 필요는 없지만, 게시글이나 메시지는 항상 보내고 받을 수 있어야하는 서비스** 입니다. 따라서, 일관성 보다는 가용성에 좀더 가치를 두게 되는 것 입니다.

### CP 시스템 ( 일관성 + 분할 내성 )
네트워크 오류에도 시스템의 안정성을 위해선 분산 시스템에서 주로 활용되는 특성으로, 네트워크 분할이 발생하더라도 데이터의 일관성을 유지하기 위해서 분산된 노드에서 가용성을 포기하게 됩니다.
즉, 네트워크가 발생했을 때 데이터의 동기화를 위해서 일부 요청에 대해서 응답을 거부하거나 대기 상태로 돌입해 가용성을 일정 부분 포기하게 됩니다.
- 금융 서비스 처럼 일관성과 정확성에 대한 가치가 높은 서비스이기 때문에, 네트워크 오류나 분할 작업 할 경우 서비스가 일시적으로 중단될 수 있습니다.
  - 예시 ) TOSS, 은행 서비스

### AP 시스템 ( 가용성 + 분할 내성 )

- 소셜 플랫폼, 실시간 메시지 : 가용성(Availability) + 분할 내성(Partition tolerance)
  - 예시 ) Instagram, Slack

### CA 시스템 ( 일관성 + 가용성 )
- 

## PACELC 이론 


---
## Reference
- [[분산 시스템] CAP 이론 제대로 이해하기 (CAP Theorem)(CAP에 대한 오해를 풀어보자)](https://etloveguitar.tistory.com/159)
- [CAP 이론 소개 - 데이터베이스 초보자용](https://onduway.tistory.com/106)