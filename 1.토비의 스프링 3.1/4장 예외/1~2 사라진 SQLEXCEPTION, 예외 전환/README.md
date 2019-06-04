
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
