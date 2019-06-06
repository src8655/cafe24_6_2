
# 4.1 사라진 SQLException

- 다음 두 메소드를 보고 jdbcTemplate의 적용 전 후의 차이를 살펴본다.
- jdbcTemplate 적용 후에는 SQLException이 사라져있다
> SQLException은 어디로 간 것일까?

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img01.JPG)


## 4.1.1 초난감 예외처리

- 예외 처리에 있어서 두 가지의 나쁜 습관에 대한 내용이 나온다.

### 예외 블랙홀
- 1번 코드처럼 예외를 잡고 아무것도 하지 않는 코드는 절대 만들어서는 안된다.
- 2, 3번 코드는 문제없다고 생각 할 수도 있지만 콘솔에 찍히는 메시지는 다른 로그나 메시지에 금방 묻혀버려 놓치기 쉽다.
- 예외를 처리 할 때 원칙은 모든 예외는 적절하게 복구되거나 작업을 중단시키고 운영자 또는 개발자에게 분명하게 통보돼야 한다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img02.JPG)


### 무의미하고 무책임한 throws
- 예외를 일일이 catch하기 귀찮아 무조건 기계적으로 throws Exception을 해버리는 것
- 바로 위에 언급했던 예외를 흔적도 없이 먹어치우는 예외 블랙홀 보다는 낫지만 심각한 문제점이 있다.
- 이런 메소드를 사용하는 메소드 역시 throws Exception을 붙이게 되다 보면 예외가 발생되더라도 어디서 발생되었는지 몰라 예외상황을 다루기 힘들다.

## 4.1.2 예외의 종류와 특징

- 자바에서 throw를 통해 발생시킬 수 있는 예외는 크게 세 가지가 있다.

### Error
- 시스템에서 비정상적인 상황이 발생했을 경우에 사용된다.
- 시스템 레벨에서 특별한 작업을 하는 게 아니라면 애플리케이션에서는 신경 쓰지 않아도 된다.

### Exception과 체크 예외
- 개발자들이 만든 애플리케이션 코드에서 예외가 발생했을 경우에 사용된다.
- Exception 클래스만 상속한 체크예외와 RuntimeException도 상속한 언체크예외가 있다.
- 체크예외는 말 그대로 catch문으로 잡든 throws로 던지든 해야 한다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img03.JPG)


### RuntimeException과 언체크/런타임 예외
- 언체크예외는 예외처리를 강제하지 않는다.
- 런타임 예외라고도 한다.
- 예외처리를 강제하지는 않지만 명시적으로 catch하거나 throws 해줄 수 있다.
- 주로 프로그램의 오류가 있을 때 발생하도록 의도된 것들로 NullPointerException이나 IllegalArgumentException이 대표적이다.

## 4.1.3 예외처리 방법

### 예외 복구
> 상황 : 사용자가 요청한 파일을 읽으려고 시도했으나 해당파일이 없거나 읽히지 않아서 IOException이 발생했다.
- 복구 할 수 없을 경우 사용자에게 알려 다른 파일을 선택하도록 한다.
- 복구 할 수 있을 경우 적절한 처리를 반복적으로 시도해 예외상황을 복구한다.

### 예외처리 회피
- 예외처리를 자신이 담당하지 않고 호출한 쪽으로 던져버리는 것
- throws 문으로 선언해 알아서 던져지게 한다.
- 또는 catch문으로 일단 예외를 잡고 로그를 남긴 후 예외를 다시 던진다.

### 예외 전환
- 예외를 복구해서 정상적인 상태로는 만들 수 없기 때문에 메소드 밖으로 던지는 것
- 예외 회피와 비슷하지만 그대로 넘기는 게 아니라 적절한 예외로 전환해서 던진다는 것이 특징이다.
> - 예)
> - 사용자를 등록하려고 시도했을 때 같은 사용자가 있어 DB 에러가 발생하면 SQLException을 발생시킨다.
> - DAO가 그대로 예외를 던져버리면 서비스계층에서는 왜 예외가 발생되었는지 알 방법이 없다.
> - 로그인 아이디 중복은 충분히 예상 가능하고 복구가 가능한 상황이므로 DAO에서 SQLException의 정보를 해석해서 다른 분명한 예외로 바꿔서 던져주는 것이 좋다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img04.JPG)


## 4.1.4 예외처리 전략

### 런타임 예외의 보편화
- 체크예외는 조금이라도 복구가 가능한 예외라고 해서 무조건 적으로 catch나 throws로 처리해야 한다.
- 개발자의 실수를 방지하기 위한 배려라고 볼 수도 있지만, 짜증나게 만드는 원인이 되기도 한다.
- 엔터프라이즈 환경에서는 수많은 사용자가 동시에 요청을 보내기 때문에 작업을 일시중지하고 예외상황을 복구하기 힘들다
- 차라리 애플리케이션 차원에서 미리 파악해서 예외가 발생하지 않도록 차단하는 것이 좋다.
- 자바 초기부터 있었던 JDK와 달리 최근 프레임워크는 언체크 예외로 정의하는 것이 일반화 되어있다.

### add() 메소드의 예외처리
- 바로 위에 나왔던 메소드에서 던지는 DuplicationUserIdException은 충분히 복구 가능한 상황이고 SQLException은 대부분 복구 불가능한 상황으로 본다.
- 때문에 DuplicationUserIdException외의 SQLException은 RuntimeException으로 예외를 포장해 던져 그 밖의 메소드들이 신경 쓰지 않게 해주는 편이 낫다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img05.JPG)



### 애플리케이션 예외
- 시스템 또는 외부의 예외상황이 원인이 아니라 애플리케이션 자체의 로직에 의해 의도적으로 발생시키고, 반드시 catch 해서 조치를 취하도록 요구하는 예외

## 4.1.5 SQLException은 어떻게 됐나?
> DAO에 존재하는 SQLException은 복구가 가능한 예외인가?

- 99%는 코드 레벨에서는 복구할 방법이 없다.
- 개발자의 부주의로 발생하거나 외부상황 때문에 발생하고 시스템 예외라면 당연히 애플리케이션 레벨에서 복구할 방법이 없다.
- 빨리 예외를 개발자에게 전달해 입력 단계에서 검증을 강화하도록 하는 것 외에 할 수 있는게 없다.

### SQLException 처리
- JdbcTemplate은 SQLException이 발생되면 런타임 예외인 DataAccessException으로 포장해서 던져준다.
- JdbcTemplate을 사용하는 곳에선 필요시에만 catch해서 처리해주고 이외에는 무시해도 된다.




# 4.2 예외 전환

## 4.2.1 JDBC의 한계

- JDBC는 DB접근 방법을 추상화된 API 형태로 정의해놓고 DB업체들이 JDBC 표준에 따라 만들어진 드라이버를 제공하게 해준다.
- DB가 바뀌더라도 드라이버만 변경해주면 그대로 사용할 수 있도록 한 것이 JDBC이다.
- 이런 방식은 현실적으로 볼 때 두 가지 걸림돌이가 있다.

### 비표준 SQL
- SQL은 어느 정도 표준화된 언어이다.
- 하지만 DB마다 표준을 따르지 않은 문법과 기능을 제공한다.
- DB를 변경할 때 드라이버 뿐 아니라 SQL도 적지 않게 수정해야 하기 때문에 유연한 코드를 보장해주지 못한다.

### 호환성 없는 SQLException의 DB 에러정보
- DB마다 발생되는 예외도 제각각
- DB마다 정의한 고유 에러 코드도 제각각
- DB를 변경할 때 이미 정의한 놓았던 예외처리 부분을 적지 않게 수정해야 한다.

## 4.2.2 DB 에러 코드 매핑을 통한 전환
> SQLException의 비표준 에러 코드와 SQL 상태정보에 대한 해결책에 대한 내용

- DB별 동일한 예외가 발생되는 예외 에러코드를 묶는다.
- 묶은 에러코드에 대해 의미가 분명한 예외로 전환한다.
- 이렇게 하기 위해 DB별 에러 코드를 분류해서 스프링이 정의한 예외 클래스와 매핑해놓은 테이블을 만들어 놓고 사용한다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img06.JPG)

- add() 메소드에서 에러 코드에 따라 발생시킨 에러코드인데

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img07.JPG)

- 스프링의 JdbcTemplate을 사용하도록 바꾸면 다음과 같이 간단해 진다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img08.JPG)

- JdbcTemplate을 이용한다면 JDBC에서 발생하는 DB관련 예외는 신경 쓰지 않아도 된다.
- 직접 정의한 예외를 발생시키고 싶을 때 DAO에서 애플리케이션 레벨의 체크 예외를 던지게 해도 된다.

## 4.2.3 DAO 인터페이스와 DataAccessException 계층구조
- 데이터 액세스 기술의 종류는 다양하다.
- 데이터 액세스 기술에 독림적인 추상화된 예외를 제공한다.
- DataAccessException은 의미가 같은 예외라면 데이터 액세스 종류와 상관없이 일관된 예외가 잘생하도록 만들어준다.

### DAO 인터페이스와 구현의 분리
> DAO를 굳이 따로 만들어서 사용하는 이유는 무엇일까?
- 성격이 다른 코드에서 분리해놓기 위해서이다.
- DAO를 사용하는 쪽에서는 DAO 내부에서 어떤 기술을 사용하는지 신경 쓰지 않아도 된다. => 기술에 독립적이다.
- 인터페이스를 사용해 구현방법을 감추고 DI를 통해 제공되도록 만드는 것이 바람직하다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img09.JPG)

> 이렇게 위처럼 만들어진 인터페이스는 문제가 없을까?
- JdbcTemplate은 가능하지만 JDBC를 사용한다면 SQLException을 던질 것이다.
- 때문에 JDBC를 사용할 것을 대비해 다음과 같이 throws를 같이 선언해야 한다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img09_1.JPG)

> 그래도 문제가 없을까?
- 자신만의 독자적인 예외를 던지는 데이터 액세스 기술의 API들은 또 사용 못한다.

> 결국 구현 기술마다 던지는 예외가 달라진다는 것이 문제
- 무책임한 해결법
	모든 예외를 다 받아주는 throws Exception으로 선언해주는 것
- 부족한 해결법
	모든 SQLException을 런타임 예외로 포장해주는 것

### 데이터 액세스 예외 추상화와 DataAccessException 계층구조
> 낙관적인 락 : 여러 트랜잭션이 충돌하지 않는다고 가정하는 방법

- 데이터 액세스 기술마다 같은 예외 상황이더라도 각기 다른 예외를 발생시킨다.
- 스프링의 예외 전환 방법을 적용하면 ObjectOptimisticLockingFailureException으로 통일시킬 수 있다.
- 기술에 상관없이 낙관적인 락킹이 발생했을 때 OptimisticLockingFailureException을 잡도록 만들면 된다.
- 다음과 같이 OptimisticLockingFailureException을 상속해서 JdbcOptimisticLockingFailureException을 정의해 사용할 수도 있다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img10.JPG)

- DataAccessException 계층구조에는 DAO에서 직접 활용할 수 있는 예외도 정의되어 있다.
- 스프링의 데이터 액세스 지원 기술을 이용하면 기술에 독립적이고 일관성 있는 예외를 던질 수 있다.
- 다음은 DataAccessException 계층구조의 일부이다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img11.JPG)

## 4.2.4 기술에 독립적인 UserDao 만들기

### 인터페이스 적용
> UserDao를 인터페이스와 구현으로 분리하는 내용

- 인터페이스의 이름을 정하는 방법
	- Interface의 I를 접두어로 붙이는 방법
	- 각각의 특징을 따르는 이름을 붙이는 방법

- UserDao 인터페이스에는 UserDao를 사용하는 클라이언트들이 필요한 기능만 선언한다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img12.JPG)

- 기능 구현은 다음과 같이 implements를 하고 구현한다
- 본 책에서는 각각의 특징을 따르는 이름을 붙이는 방법을 썼다

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img13.JPG)

### 테스트 보완
> 주입받을 UserDaoJdbc 객체의 클래스명은 어떻게 해야 할까?

- 구현한 클래스는 UserDaoJdbc 이지만 주입받을 변수의 클래스명은 인터페이스명인 UserDao로 해도 주입 가능한 타입으로 빈을 찾아 주입해준다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img14.JPG)

- 다음은 UserDaoTest가 사용하는 UserDao의 인터페이스와 구현의 분리에 대해 설명한 그림이다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img15.JPG)

- UserDaoTest는 중복된 키를 가진 정보를 등록했을 때 예외가 발생되는지를 테스트하는 것
- 다음과 같이 강제로 같은 사용자를 두 번 등록하는 코드를 작성한다.
- 이 때 스프링의 DataAccessException 예외 중의 하나가 던져져야 한다.
- expected=~~~ 가 그 예외를 검증해준다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img16.JPG)

- 실행결과 스프링에서 자동으로 DataAccessException 예외 중 하나를 던져 테스트가 통과되는 것을 볼 수 있다.
- 구체적으로 어떤 예외가 던져졌는지 확인하기 위해서는 expected를 빼고 다시 실행해보면 확인해 볼 수 있다.

### DataAccessException 활용 시 주의사항
> 키 값이 중복되는 상황일 때 발생되는 예외

- 스프링을 사용하면 데이터 액세스 기술에 상관없이 DuplicateKeyException(중복키)라는 동일한 예외가 발생될 것을 기대한다.
- 현실적으로는 그렇지 않다.
- 하이버네이트 같은 경우 중복키가 발생하는 경우 ConstraintViolationException(제약위반) 예외를 발생시킨다.
- 그래서 스프링은 DatalntegrityViolationException(데이터무결성위반) 예외로 변환할 수밖에 없다.
	=> 예외가 포괄적인 때문에 다른 무결성 위반이 발생되더라도 키 값 중복인지 아니면 다른 예외인지 알기 힘들다.

> 만약 DAO에서 사용하는 기술의 종류와 상관없이 동일한 예외를 얻고 싶다면?

- 스프링은 SQLException을 DataAccessException으로 전환하는 다양한 방법을 제공한다.
- 직접 DuplicatedUserIdException 같은 예외를 정의해둔다.
- SQLException을 직접 해석해서 정의해두었던 DuplicatedUserIdException로 전환해준다.
- SQLExceptionTranslator인터페이스를 구현한 SQLErrorCodeSQLExceptionTranslator를 사용하면 된다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/4%EC%9E%A5%20%EC%98%88%EC%99%B8/1~2%20%EC%82%AC%EB%9D%BC%EC%A7%84%20SQLEXCEPTION,%20%EC%98%88%EC%99%B8%20%EC%A0%84%ED%99%98/img/img17.JPG)


- 위 테스트 코드에서는 중복키 예외를 일부러 발생시킨다.
- 발생된 예외에서 DuplicateKeyException은 JDBC에서 처음 발생한 SQLException을 내부에 가지고 있다.
- getRootCause() 메소드로 SQLException을 가지고 올 수 있다.
- SQLErrorCodeSQLExeptionTranslator객체를 생성하고 translate메서드로 SQLException을 전달해 DataAccessException의 예외로 변환해준다.
- 그 DataAccessException이 원하는 DuplicateKeyException인지 확인한다.
