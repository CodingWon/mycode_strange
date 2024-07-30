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

