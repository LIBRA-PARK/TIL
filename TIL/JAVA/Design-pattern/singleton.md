# 싱글톤 패턴(Singleton)
![Date](https://img.shields.io/badge/Date-2024--12--09-blue)
![Tag](https://img.shields.io/badge/Tag-Java-orange)
---
## 요약
인스턴스화 된 객체가 단 하나만 있다는 것을 보장해 전체 애플리케이션 내에서 동일한 객체 인스턴스에 접근을 보장하고, 생성된 인스턴스를 재활용해 생성에 필요한 리소스를 절약할 수 있다.  

---
## 싱글톤은 왜 사용할까?
**싱글톤 패턴은 클래스에 대해서 최초로 생성되는 객체에 대해서만 메모리를 할당해 전체 애플리케이션에서 단 하나의 인스턴스 사용을 보장**한다. 이 패턴은 사용이 고안 되었을까? 
가장 큰 이유는 **단일 인스턴스를 사용하게 될 경우 전체 애플리케이션에서 메모리에 대한 효율성을 증대시킬 수 있으며, 부가적으로 단일 인스턴스를 사용하기에 전역적으로 관리해야할 상태 또는 값에 대해서 손쉽게 관리가 가능**한 이점이 있다.

초기화 과정에 대해서 비용이 많이 소모되거나 전역적으로 관리가 필요한 사용처에서 많이 이용되는데 대표적으로 `Database Connection Pool` 과 `logging`에 많이 사용된다.
두 사용처 모두 공통적으로 **전역적으로 사용되다보니 무분별한 사용으로 리소스가 낭비될 위험이 크고, 연결 정보나 로거에 대한 설정이 관리되어야 하기 때문에 싱글톤 패턴을 사용한다.** 

## 어떻게 보장하는 걸까
`Spring Bean`이 등장하기 이전 `Java`에서는 어떻게 구현해 단일 인스턴스 객체를 보장하고 전역적으로 사용할 수 있었을까?
싱글톤 패턴 자체를 구현하는 것 그리 어렵지 않는데 사용하고자 하는 **클래스에 대해서 무분별한 생성으로 발생되는 인스턴스화를 막고, 의도적으로 이미 생성된 객체를 재사용할 수 있도록 반환**해주는 것이 주요 포인트 이다.

```java
public class SingletonClass {
    // 클래스가 로딩 될 때 객체 인스턴스화를 통해서 초기화
    private static final SingletonClass instance = new SingletonClass();
    // 프로그램 내에서 무분별한 인스턴스화를 막기위해서 생성자 private 화
    private SingletonClass() { }
    // 생성된 객체를 재사용하기 위해서 의도적으로 이미 생성된 인스턴스를 반환해서 사용하도록 함
    public static SingletonClass getInstance() {
        return instance;
    }
}
```
본 클래스는 `static final`을 이용해서 전역적으로 사용할 수 있도록 인스턴스를 `JVM`에 클래스를 로드할 때 자동적으로 초기화할 수 있도록 구성하고, 생성자를 `private`로 만들어 애플리케이션 내부에서 의도치 않은 인스턴스화를 막을 수 있도록 구현한다.
이렇게 구성될 경우엔 해당 클래스를 생성자로 신규 인스턴스를 만들 수 없어 `JVM`에 클래스가 로드될 때 생성되는 인스턴스가 애플리케이션내에서 유일한 인스턴스를 보장할 수 있게 된다.
그리고, `getInstance()`메소드를 구현하여 애플리케이션내에서 필요할 경우 미리 생성된 인스턴스를 반환해서 사용하도록 구현할 수 있게 된다.
```java
import junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertSame;
import static org.junit.jupiter.api.Assertions.assertNotNull;

class SingletonClassTest {
    @Test
    void 단일_인스턴스_테스트() {
        SingletonClass singleton1 = SingletonClass.getInstance();
        SingletonClass singleton2 = SingletonClass.getInstance();
        
        assertSame(singleton1, singleton2, "단일 인스턴스가 아닙니다.");
    }
    
    @Test  
    void 인스턴스_Not_null() {
        SingletonClass singleton = SingletonClass.getInstance();
        
        assertNotNull(singleton, "인스턴스는 null이 아니어야 합니다.");
    }
}
```

이렇게 간단하게 싱글톤 패턴의 의도를 고려해서 구현해 만들어 낼 수 있다.
>`Spring Bean` 과 `Java Singleton`의 차이
>

## 구현
하지만 위의 방식에는 좋지 불필요한 리소스가 소모되는 문제가 있는데, 

## 무조건 좋은가?

### 동시성 문제
