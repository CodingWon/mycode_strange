# 1. 잘못된 구조의 문제 깨닫기

## 1.1 의미를 알 수 없는 이름

- 기술 중심 명명

![20240730203823](https://raw.githubusercontent.com/CodingWon/mycode_strange/master/imgs/20240730203823.png)

- 일련번호 명명

![20240730204518](https://raw.githubusercontent.com/CodingWon/mycode_strange/master/imgs/20240730204518.png)

## 1.2 이해하기 어렵게 만드는 조건 분기 중첩

![20240731054454](https://raw.githubusercontent.com/CodingWon/mycode_strange/master/imgs/20240731054454.png)

## 1.3 수많은 악마를 만들어 내는 데이터 클래스

- 데이터와 로직이 분리되어 처리되는 경우

  ![20240731054548](https://raw.githubusercontent.com/CodingWon/mycode_strange/master/imgs/20240731054548.png)

- 로직이 곳곳에 있는경우
- 코드 중복
- 코드 중복이 많으면 수정이 누락 될 수 있다.
- 코드 중복이 많으면 가독성이 저하 된다.
- 초기화 되지 않은 쓰레기 객체
- 잘못된 값 할당

# 2. 설계 첫걸음

## 2.1 의도를 분명히 전달할 수 있는 이름 설계하기

- 의도를 알 수 없는 이름
  - 코드가 짧아서 타이핑 하기 좋지만 다른 사람들이 알아보기 힘들다.

![20240731060135](https://raw.githubusercontent.com/CodingWon/mycode_strange/master/imgs/20240731060135.png)

![20240731060231](https://raw.githubusercontent.com/CodingWon/mycode_strange/master/imgs/20240731060231.png)

- 의도를 알 수 있는 이름 붙이기

![20240731060326](https://raw.githubusercontent.com/CodingWon/mycode_strange/master/imgs/20240731060326.png)

## 2.2 목적별로 변수를 따로 만들어 사용하기

- (코드 2.2 에서) 데미지를 나타내는 damageAmount에 값이 여러 번 할당 되고 있다.

  - ① 에서 플레이어의 공격력의 총합을 나타낸다.
  - ② 에서는 적 방어력 총합을 나타낸다.

- 목적별로 변수 만들어 사용하기

  ![20240731060752](https://raw.githubusercontent.com/CodingWon/mycode_strange/master/imgs/20240731060752.png)

## 2.3 단순 나열이 아니라, 의미 있는 것을 모아 메서드로 만들기

1. 메서드를 사용하지 않을 때

   - (코드 2.3 에서) 일련의 흐름을 모두 작성하였다. 

   - 로직이 어디서에서 시작해서 어디에서 끝나는지 무슨일을 하는지 알기 어렵다.

2. 의미를 알기 쉽게 메서드로 만들기

   ```java
   // 플레이어의 공격력 합계 계산
   int sumUpPlayerAttackPower(int playerArmPower, int playerWeaponPower){
       return playerArmPower + playerWeaponPower;
   }
   
   // 적의 방어력 합계 계산
   int sumUpEnemyDefence(int enemyBodyDefence, int enemyArmorDefence){
       return enemyBodyDefence + enemyArmorDefence;
   }
   
   // 데미지 평가
   int estimateDamage(int totalPlayerAttackPower, int totalEnemyDefence){
       int damageAmout = totalPlayerAttackPower - (totalEnemyDefence / 2);
       if (damageAmount < 0){
           return 0;
       }
       return damageAmount;
   }
   ```

3. 메서드를 호출하는 형태로 개선

   - 읽기 쉬운 코드로 개선이 되었다.

   ```java
   // 플레이어 공격력
   int totalPlayerAttackPower = sumUpPlayerAttackPower(playerBodyPower, playerWeaponPower);
   // 적의 방어력
   int totalEnemyDefence = sumUpDefence(enemyBodyDefence, enemyArmorDefence);
   // 데미지 평가
   int damageAmount = estimateDamage(totalplayerAttackPower, totalEnemyDefence)
   ```

## 2.4 관련된 데이터와 로직을 클래스로 모으기

- 캡슐화
  - 클래스를 활용하여 밀접한 관계를 갖고 있는 데이터와 로직을 묶는다.

```java
// 히트포인트(HP)를 나타내는 클래스
class HitPoint {
    private static final int MIN = 0;
    private static final int MAX = 999;
    final int value;
    
    HitPoint(final int value){
        if(MIN > value) throw new IlleagalArgumentException(MIN + " 이상을 지정해 주세요.");
        if(MAX < value ) throw new IlleagalArgumentExcetion(MAX + " 이하를 지정해 주세요. ");
    	this.value = value;
    }
    
    // 대미지를 받음.
    HitPoint damage(final int damageAmout){
        final int damaged = value - dmageAmount;
        final int corrected = damaged < MIN ? MIN : damaged;
        return new HitPoint(corrected);
    }
    
    // 회복
    HitPoint recover(final int recoveryAmount){
        final int recovered = value + recoveryAmount;
        final int corrected = MAX < recovered ? MAX : recovered;
        return new HitPoint(corrected);
    }
    
}
```

# 3. 클래스 설계 : 모든 것과 연결 되는 설계 기반

## 3.1. 클래스 단위로 잘 동작하도록 설계하기

- 클래스는 클래스 하나로도 잘 동작할 수 있게 설계해야 한다.
- 복잡한 초기 설정을 하지 않더라도 곧바로 사용할 수 있게 해야한다.
- 클래스를 마음대로 조작해서 클래스 전체가 고장 나는(버그 발생)이 없게, 최소한의 조작 방법(메서드)만 외부에 제공해야 한다.

### 3.1.1 클래스의 구성요소

- 인스턴스 변수
- 메서드

### 3.1.2 모든 클래스가 갖추어야 하는 자기 방어 임무(캡슐화)

## 3.2 성숙한 클래스로 성장시키는 설계 기법

### 3.2.1 생성자로 확실하게 정상적인 값 설정하기

- 조건으로 가드 설정하기

```java
class Money {
	private int amount;
 	private Currency currency;
    
    Money(int amount, Currency currency){
        if(amount < 0){
            throw new IlleagalArgumentException("금액을 0 이상의 값을 지정해 주세요.");
        }
        
        if(currency == null){
            throw new NullpointException("통화 단위를 지정해 주세요");
        }
        
        this.amount = amouont;
        this.currency = currency;
    }
}
```

### 3.2.2 계산 로직도 데이터를 가진 쪽에 구현하기

### 3.2.3 불변 변수로 만들어서 예상히지 못한 동작 막기

- 변수의 값이 계쏙 바뀌면, 값이 언제 변경되었는지
- 현재 어떤 값인지 신경 써야한다.
- 예상치 못한 부수 효과가 쉽게 발생한다.

```java
class Money{
    
    final int amount;
    final Currency currency;
    
    Money(int amount, Currency currency){
        ...
    }
}
```

### 3.2.4 변경하고 싶다면 새로운 인스턴스 만들기

- 불변을 유지하면서도 값을 변경할 수 있다.

```java
class Money {
    // 생략
    Money add(int other){
        int added = amount + other;
        return new Money(added, currency);
    }
    
}
```

### 3.2.5 메서드 매개변수와 지역 변수도 불변으로 만들기

```java
class Money {
    // 생략
    Money add(final int other){
        final int added = amount + other;
        return new Money(added, currency);
    }
    
}
```

### 3.2.6 엉뚱한 값을 전달하지 않도록 하기

- 기본 자료형으로 매개변수를 정하면 엉뚱한 값을 넘길 수 있다.

```java
final int ticketCount = 3; // 티켓의 수
money.add(ticketCount);	   // 가격이 넘어가야하는데 티켓 수가 넘어갔다.
```

- 객체 자료형을 매개변수로 정하기

```java
class Money {
    // 생략
    Money add(final Money other){
        final int added = amount + other.amount;
        return new Money(added, currency);
    }
    
}
```

- 추가적으로, 통화 단위가 다른 Money가 들어올때 예외를 발생시킬 수 있다.

```java
class Money {
    // 생략
    Money add(final Money other){
        if(!currency.equals(other.currency)){
            throw new IllegalArgumentException("통화 단위가 다릅니다.")
        }
        final int added = amount + other.amount;
        return new Money(added, currency);
    }
}
```

### 3.2.7 의미 없는 메서드 추가하지 않기

- 회계 시스템에서 필요한 기능 : 덧셈, 뺄셈, 나눗셈

- 불필요한 기능 : 곱하기
- 불필요한 기능은 넣지 않는다.!!!!!

## 3.3  악마 퇴치 효과 검토하기

- Money 클래스

  ```java
  class Money {
      final int amount;
      final Currency currency;
      
      Money(final int amount, final Currency currency){
          if(amount < 0)
              throw new IlleagalArgumentException("금액을 0 이상의 값을 지정해 주세요.");
          if(currency == null)
              throw new NullPointException("통화 단위를 지정해 주세요.");
          
          this.amount = amount;
          this.currency = currency;
      }
      
      Money add (final Money other){
          if(!currency.equals(other.currency)){
              throw new IllegalArgumentException("통화 단위가 다릅니다.");
          }
          final int added = amount + other.amount;
          return new Money(added, currency);
      }
  }
  ```

- Money 클래스에서 퇴치한 악마

| 퇴치한 악마             | 이유                                                         |
| :---------------------- | ------------------------------------------------------------ |
| 중복 코드               | 필요한 로직이 Money 클래스 내부에 모여 있어서, 다른 클래스에 중복 코드가 작성될 일이 줄어듬 |
| 수정 누락               | 중복 코드가 발생하지 않으므로, 수정 시 누락이 발생할 일이 줄어듦. |
| 가독성 저하             | 필요한 로직이 Money 클래스 내부에 모여 있어서, 디버깅 or 기능 변경시 로직 찾기 쉬움 |
| 쓰레기 객체             | 생성자에서 인스턴스 변수의 값을 확정하므로, 초기화 되지 않은 상태가 있을 수 없음 |
| 잘못된 값               | 잘못된 값을 막을 가드를 설치하고, 인스턴스 변수에 final 수식자를 붙여 불변으로 만들었으므로 ,<br /> 잘못된 값이 들어오지 않음 |
| 생각하지 못한 부수 효과 | final 수식자를 붙여 불변 변수로 만들었으므로, 부수 효과로부터 안전함 |
| 값 전달 실수            | 매개변수를 Money 자료형으로 바꿧으므로, 다른 자료형의 값을 실수로 넣었을 때 컴파일 오류가 발생 |

## 3.4 프로그램 구조의 문제 해결에 도움을 주는 디자인 패턴

- 디자인 패턴

  - 응집도가 높은 구조로 만들거나, 잘못된 상태로부터 프로그램을 방어하는 등 프로그램의 구조를 개선하는 설계 방법

    | 디자인 패턴                         | 효과                                                      |
    | ----------------------------------- | --------------------------------------------------------- |
    | 완전 생성자                         | 잘못된 상태로부터 보호함.                                 |
    | 값 객체                             | 특정한 값과 관련된 로직의 응집도를 높임                   |
    | 전략 (strategy)                     | 조건 분기를 줄이고, 로직을 단순화함                       |
    | 정책 (policy)                       | 조건 분기를 단순화하고, 더 자유롭게 만듦                  |
    | 일급 컬렉션(First Class Collection) | 값 객체의 일종으로 컬랙션과 관련된 로직의 응집도를 높임.  |
    | 스프라우트 클래스(Sprout Class)     | 기존 로직을 변경하지 않고, 안전하게 새로운 기능을 추가함. |

    *Monecy 클래스는 완전 생성자 , 값 객체 라는 디자인 패턴을 적용*

### 3.4.1 완전 생성자

- 잘못된 상태로부터 클래스를 보호하기 위한 디자인 패턴
  1. 인스턴스 변수를 모두 초기화 하기
  2. 생성자 내부에 가드를 사용해서 잘못된 값이 들어오지 않게 만들기
  3. 인스턴스 변수에 final 수식자를 붙여서 불변으로 만들기

### 3.4.2 값 객체

- 값을 클래스(자료형) 으로 나타내는 디자인 패턴

- 다양한 값 객체

  - 금액, 날짜, 주문 수 , 전화번호 등....

- 값 객체로 만들 수 있는 사례

- | 애플리케이션           | 값 객체로 만드는 값과 개념                                   |
  | ---------------------- | ------------------------------------------------------------ |
  | 온라인 쇼핑몰          | 세금 제외 금액, 세금 포함 금액, 상품명, 주문 수 , 전화번호, 배송지, 할이 포인트, 할인 금액, 배송 날짜 등 |
  | 태스크 관리 도구       | 태스크 이름, 태스크 설명, 코멘트, 시작 일, 종료 일, 우선순위, 진행 상태, 담당자 ID, 담당자 이름 등 |
  | 건강 관리 애플리케이션 | 연령, 성별, 키, 몸무게, BMI, 혈압, 복부 둘레, 체지방량, 기초 대사량 등 |
  | 게임                   | 최대 히트 포인트, 남은 히트 포인트, 히트포인트 회복량, 공격력, 매직포인트 , 매직포인트 소비량, 소지금, <br /> 적이 떨어뜨린 금액, 아이템 가격, 아이템 이름 등 |

- 값 객체와 완전 생성자는 얻을 수 있는 효과가 거의 비슷하므로, 일반적으로 함께 사용한다.
