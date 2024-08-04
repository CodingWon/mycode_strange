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

# 4. 불변 활용하기 : 안정적으로 동작하게 만들기

## 4.1 재할당

- 재할당은 변수의 의미를 바꿔 추측하기 어렵게 만든다.

  ```java
  int damage() {
  	// 멤버의 힘과 무기 성능을 기본 공격력으로 활용합니다.
  	int tmp = member.power() + member.weaponAttack();
      // 멤버의 속도로 공격력을 보정합니다.
      tmp = (int)(tmp * (1f + member.seepd() / 100f));
      // 공격력에서 적의 방어력을 뺀 값을 대미지로 사용합니다.
      tmp = tmp - (int)(enemy.defenc / 2);
      // 대미지가 음수가 되지 않고 조정합니다.
      tmp = Math.max(0,tmp);
      
      return tmp;
  }
  ```

### 4.1.1 불변 변수로 만들어서 재할당 막기

- 불변 변수 이용하기

  ```java
  int damage(){
  	final int basicAttackPower = member.power() + member.weaponAttack();
  	final int finalAttackPower = (int)(basicAttackPower * (1f + member.speed()/100f));
  	final int reduction = (int)(enemy.defenc/2);
  	final int damage = Math.max(0,finalAttackPower - reduction);
  }
  ```

### 4.1.2 매개변수도 불변으로 만들기

- 매개변수 productionPrice 를 재할당 하는 코드

  ```java
  void addPrice(int productPrice) {
  	productPrice = totalPrice + productPrice;
  	if(MAX_TOTAL_PRICE < productPrice){
  		throw new IlleagalArgumentException("구매 상한 금액을 넘었습니다.");
  	}
  }
  ```

- 매개변수에 final을 붙여 불변으로 만들기

  ```java
  void addPrice(final int productPrice){
  	final int increasedTotalPrice = totalPrice + productPrice;
  	if(MAX_TOTAL_PRICE < increasedTotalPrice){
  		throw new IllegalArgumentException("구매 상한 금액을 넘었습니다.");
  	}
  }
  ```

## 4.2 가변으로 인해 발생하는 의도하지 않은 영향

### 4.2.1 사례 1 : 가변 인스턴스 재사용하기

- 공격력을 나타내는 클래스

  ```JAVA
  class AttackPower{
  	static fianl int MIN = 0;
      int value; //final을 붙이지 않았으므로 가변
      
      AttackPower(int value){
          if(value < MIN){
              throw new IllegalArgumentException();
          }
          
          this.value = value;
      }
  }
  ```

- 무기를 나타내는 클래스

  ```java
  class Weapon {
  	final AttackPower attackPower;
  	
  	Weapon(AttackPower attackPower){
  		thia.attackPower = attackPower;
  	}
  }
  ```

- AttackPower 인스턴스 재사용하기

  ```java
  AttackPower attackPower = new AttackPower(20);
  Weapon weaponA = new Weapon(attackPower);
  Weapon weaponB = new Weapon(attackPower);
  ```

  ```
  '무기 각각의 공격력을 강화할 수 있도록 조건을 변경하자'
  	- 어떤 무기의 공격력을 강화하면, 다른 무기의 공격력도 강회되는 버그가 발생
  	- weaponA의 공격력을 변경 했더니, weaponB의 공격력도 함께 바뀌었다.
  ```

- 재사용하고 있는 공격력을 변경하면?

  ```java
  AttackPower attackPower = new AttackPower(20);
  
  Weapon weaponA = new Weapon(attackPower);
  Weapon weaponB = new Weapon(attackPower);
  
  weaponA.attackPower.value = 25;
  
  System.out.println("Weapon A attack power : " + weaponA.attackPower.value);
  System.out.println("Weapon B attack power : " + weaponB.attackPower.value);
  
  -----------------------------
  Weapon A attack power : 25
  Weapon B attack power : 25
  ```

- 공격력 인스턴스 개별적으로 생성하기

  ```JAVA
  AttackPower attackPowerA = new AttackPower(20);
  AttackPower attackPowerB = new AttackPower(20);
  
  Weapon weaponA = new Weapon(attackPowerA);
  Weapon weaponB = new Weapon(attackPowerB);
  
  weaponA.attackPower.value += 5;
  
  System.out.println("Weapon A attack power : " + weaponA.attackPower.value);
  System.out.println("Weapon B attack power : " + weaponB.attackPower.value);
  
  -------------------------
  Weapon A attack power : 25
  Weapon B attack power : 20
  ```

### 4.2.2  사례 2:  함수로 가변 인스턴스 조작하기

- 공격력을 변화시키는 메서드 추가하기

  ```java
  class AttackPower{
  	static fianl int MIN = 0;
      int value; //final을 붙이지 않았으므로 가변
      
      AttackPower(int value){
          if(value < MIN){
              throw new IllegalArgumentException();
          }
          
          this.value = value;
      }
      /**
      * 공격력 강화하기
      * @param increment 공격력 증가량
      */
      void reinforce(int increment){
      	value += increment;
      }
      
      /** 무력화하기 */
      void disable(){
          value = MIN;
      }
  }
  ```

- 공격력을 강화하는 처리

  ```java
  AttackPower attackPower = new AttackPower(20);
  
  //생략
  attackPower.reinforce(15);
  System.out.println("attack power : " + attackPower.value);
  
  -----------------------
  attack power : 35
  ```

- 공격력이 0이 되는 상황이 발생

  - AttackPower 인스턴스가 다른 스레드에서 사용되었음을 확인
  - 다른 스래드에서 실행하면서, 공격력을 0으로 만드는 AttackPower.disable 메서드를 호출한 것

  ```
  attack power : 0
  ```

### 4.2.3 부수 효과의 단점

- 주요 작용 : 함수(메서드)가 매개변수를 전달받고, 값을 리턴하는 것

- 부수 효과 : 주요 작용 이외의 상태 변경을 일으키는 것 (상태 변경 : 함수 밖에 있는 상태를 변경하는 것)

-  상태 변경

  1. 인스턴스 변수 변경

  2. 전역 변수 변경

  3. 매개변수 변경

  4. 파일 읽고 쓰기 같은 I/O 조작

     - 파일은 파일을 읽는 시점에 반드시 존재한다고 할 수 없고, 파일을 읽는 중에 내용이 변경될 수 도 있다.

     - 그래서 항상 같은 결과가 나온다고 보장할 수 없다.

### 4.2.4 함수의 영향 범위 한정하기

- 부수효과가 있는 함수는 영향 범위를 예측하기 힘들다.

- 따라서, 범위를 한정하는 것이 좋다.

  1. 데이터(상태)는 매개변수로 받는다.
  2. 상태를 변경하지 않는다.
  3. 값은 함수의 리턴 값으로 돌려준다.

  ```
  "상태를 변경하지 않고 값을 리턴하기만 하는 함수가 이상적이다 !!!"
  ```

  ```
  객체지향 프로그래밍 언어는 함수의 부수 효과로 인한 범위를 클래스 내부까지 허용하는 것이 일반적
  ```

### 4.2.5 불변으로 만들어서 예기치 못한 동작 막기

- 기능 변경 떄에 의도하지 않게 부수효과가 있는 함수가 만들어져서, 예상하지 못한 동작을 일으킬 가능성은 항상 존재

- 불변으로 견고해진 AttackPower 클래스

  ```java
  class AttackPower{
  	static final int MIN = 0;
  	final int value; // final로 불변으로 만들었습니다.
  	
  	AttackPower(final int value){
  		if(value < MIN){
  			throw new IlleagalArgumentException();
  		}
  		this.value = value;
  	}
  	/**
  	* 공격력 강화하기
  	* @param increment 공격력 증가량
  	* @return 증가된 공격력
  	*/
      AttackPower reinforce(final AttackPower increment){
          return new AttackPower(this.value + increment.value);
      }
      
      /**
      * 무력화 하기
      * @return 무력화한 공격력
      */
      AttackPower disable(){
          return new AttackPower(MIN);
      }
  }
  ```

- 영향 범위를 줄인 공격력 강화

  ```java
  fianl AttackPower attackPower = new AttackPower(20);
  // 생략
  final AttackPower reinforced = attackPower.reinforce(new AttackPower(15));
  ```

- 인스턴스를 생성했으므로 영향을 주지 않음

  ```java
  // 다른 스레드에서 처리
  final AttackPower disabled = attackPower.disable();
  ```

- 무기를 나타내는 클래스(개선 버전)

  ```java
  class Weapon {
  	fianl AttackPower = attackPower;
  	
  	Weapon(fianl AttackPower attackPower){
  		this.attackPower = attackPower;
  	}
      /**
      * 무기 강화하기
      * @param increment 공격력 강화
      * @return 강화된 무기
      */
      Weapon reinforce(final AttackPower increment){
          final AttackPower reinforced = attackPower.reinforce(increment);
          return new Weapon(reinforced);
      }
      
  }
  ```

- AttackPower 와 Weapon 사용(개선 버전)

  ```java
  final AttackPower attackPowerA = new AttackPower(20);
  final AttackPower attackPowerB = new AttackPower(20);
  
  final Weapon weaponA = new Weapon(attackPowerA);
  final Weapon weaponB = new Weapon(attackPowerB);
  
  final AttackPower increment = new AttackPower(5);
  final Weapon reinforcedWeaponA = weaponA.reinforce(increment);
  
  System.out.println("Weapon A attack power : " + weaponA.attackPower.value);
  System.out.println("Reinforced weapon A attack power : " + reinforcedWeaponA.attackPower.value);
  System.out.println("Weapon B attack power : " + weaponB.attackPower.value);
  
  ------------------------
  Weapon A attack power : 20
  Reinforced weapon A attack power : 25
  Weapon B attack power : 20
  ```

## 4.3 불변과 가변은 어떻게 다루어야 할까

### 4.3.1 기본적으로 불변으로

- 변수를 불변으로 만들면 다음과 같은 장점이 있다.
  1. 변수의 의미가 변하지 않으므로, 혼란을 줄일 수 있다.
  2. 동작이 안정적이게 되므로, 결과를 예측하기 쉬움
  3. 코드의 영향 범위가 한정적이므로, 유지 보수가 편리해짐

### 4.3.2 가변으로 설계해야 하는 경우

- 성능 이슈
  1. 대량의 데이터를 빠르게 처리해야 하는 경우
  2. 이미지를 처리해야 하는 경우
  3. 리소스에 제약이 큰 임베디드 소프트웨어를 다루는 경우
- 스코프
  - 스코프가 국소적인 경우에는 가변으로 사용해도 좋다.
  - 예) 반복문 카운터 등 반복 처리 스코프에서만 사용되는 지역 변수

### 4.3.3 상태를 변경하는 메서드 설계하기

- 인스턴스 변수를 가변으로 만들었을 때 주의할점

```
- 히트 포인트는 0 이상
- 히트 포인트가 0이 되면, 사망 상태로 변경
```

- 정상적으로 동작하지 않는 로직

  ```java
  class HitPoint {
      int amount;
  }
  
  class Member {
      final HitPoint hitPoint;
      final States states;
      // 생략
      
      /**
      * 대미지 받기
      * @param damageAmount 대비지 크기
      */
      void damage(int damageAmount){
          hitpoint.amount -= damageAmount;
      }
  }
  ```

- 뮤테이터 : 조건에 맞는 올바른 상태로 변경하는 메소드

- 가변으로 할 떄는 반드시 올바른 상태로만 변경하도록 설계하기

  - HitPoint

  ```java
  class HitPoint{
  	private static final int MIN = 0;
  	int amount;
      
      HitPoint(final int amount){
          if(amount < MIN){
              throw new IllegalArgumentException();
          }
          this.amount = amount;
      }
      
      /**
      * 대미지 받는 처리
      * @param damageAmount 대미지 크기
      */
      void damage(final int damageAmount){
          final int nextAmount = amount - damageAmount;
          amount = Math.max(MIN, nextAmount); 	// 가변처리
      }
      
      /** @return 히트포인트가 0이라면 true */
      boolean isZero(){
          return amount == MIN;
      }
  }
  ```

  - Member

  ```java
  class Member {
  	final HitPoint hitPoint;
  	final States states;
  	// 생략
      
      /**
      * 대미지 받는 처리
      * @param damageAmount 대미지 크기
      */
      void damage(final int damageAmount){
          hitPoint.damage(damageAmount);
          if(hitPoint.isZero()){
              states.add(StateType.dead);
          }
      }
  }
  ```

### 4.3.4 코드 외부와 데이터 교환은 국소화하기

- 외부 와의 데이터 교환
  - 파일을 읽고 쓰는 I/O 조작
  - 데이터 베이스

```
- 파일 OR 데이터베이스가 다른 시스템에 의해서 덮어 쓰여질 수 있다.
- 특별한 이유없이 외부 상태에 의존하는 코드를 작성하면, 동작 예측이 힘들어 지므로 문제가 발생할 가능성이 높아진다.
```

- Repository pattern
  - 코드와 외부와 데이터 교환을 국소화하는 테크닉
  - 데이터베이스의 영속화를 캡슐화하는 디자인 패턴
    - 영속화 : 데이터베이스에 데이터를 저장하는 것
