
# 3.6 스프링의 jdbcTemplate

### jdbcTemplate
- 스프링이 제공하는 JDBC 코드용 기본 템플릿

### UserDao에서 사용할 준비
- dataSource를 jdbcTemplate의 생성자로 전달

## 3.6.1 update()
- PreparedStatementCreator 타입의 콜백을 받아서 사용하는 jdbcTemplate에서의 템플릿 메소드로 다음과 같이 사용할 수 있지만

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img01.JPG)


- 다음처럼 내장 콜백으로 편리하게 사용할 수 있다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img02.JPG)



- PreparedStatement에서 치환자에 바인딩 해주는 기능도 jdbcTemplate에서 더 간단하게 가능하다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img03.JPG)

- SQL의 "?"의 치환자는 동일하고 update() 메서드의 인수에 차례대로 넣어주면 된다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img03_1.JPG)

## 3.6.2 queryForInt()
- SQL 쿼리를 실행하고 ResultSet을 통해 값을 가져오는 코드
	=> jdbcTemplate에서는 query() 메서드로 가능하다
	=> PreparedStatementCreator과 ResultSetExtractor 콜백을 파라미터로 받는다.
- 동작방식
	PreparedStatementCreator는 jdbcTemplate에서 Connection을 인수로 받고 PreparedStatement를 돌려준다.
	ResultSetExtractor는 jdbcTemplate에서 ResultSet을 인수로 받고 결과를 돌려준다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img04.JPG)

- 이것 역시 jdbcTemplate가 내장하고 있는 queryForInt() 메서드로 편리하게 사용할 수 있다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img05.JPG)

	=> Integer 타입을 리턴해주며 Integer 타입의 결과를 가져올 수 있는 SQL 문장만 전달해주면 된다.


## 3.6.3 queryForObject()

- getCount() 처럼 단순한 하나의 값이 나오는 것이 아니라 User 오브젝트로 만드는 작업을 하는 get() 메소드에 JdbcTemplate을 적용해본다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img06.JPG)

> 첫 번째 인자는 PreparedStatement를 만들기 위한 SQL
> 두 번째 인자는 바인딩으로 getCount()와 다르게 배열을 전달한다
> 세 번째 인자는 ResultSet으로 User 객체를 채워서 만들어 줄 RowMapper 콜백

## 3.6.4 query()

### getAll()에 대한 기능 정의
- getAll() 메소드는 테이블의 모든 로우를 다 가져온다
- 여러개로 리턴하기 때문에 List<User> 타입의 컬렉션으로 만들어 리턴한다.
- 리스트에 담는 순서는 기본키인 id 순으로 정렬해서 가져온다.

### 테스트를 먼저 만들어보자
- 등록할 데이터는 user1, user2, user3 세 개이다.
- getAll()로 가져온 데이터는 동일성 비교가 아니라 동등성 비교를 해야한다.
	=> 반복적으로 사용되는 checkSameUser() 메소드처럼 User객체 안의 값을 비교한다.
- user를 하나씩 추가하면서 매번 getAll()을 실행해서 결과를 확인한다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img07.JPG)



### getAll()을 구현
- JdbcTemplate의 query()을 사용하여 구현
- 첫 번째 인자는 실행 할 SQL 쿼리를 넣는다.
- 두 번째 인자는 바인딩 할 배열이지만 없다면 생략할 수 있다.
- 세 번째 인자는 RowMapper 콜백 오브젝트로 get()과 동일하다

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img08.JPG)

### 테스트 보완
- 위에서 getAll()에 대한 테스트 코드를 통해 테스트가 깔끔하게 통과되었지만 더 개선할 부분이 없는지 꼼꼼하게 생각해 보는 것이 좋다.
- 긍정적인 결과만 테스트하려는 것 보다 부정적인 자세가 필요하다
- 예외상황을 테스트 해 볼 필요가 있다.

> 만약 getAll()에서 결과가 없다면 어떻게 해야 할까?

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img09.JPG)

## 3.6.5 재사용 가능한 콜백의 분리

### DI를 위한 코드 정리
- UserDao의 모든 메소드는 DataSource를 직접 사용하지 않고 JdbcTemplate을 이용하므로 DataSource 인스턴스 변수를 제거한다.
- JdbcTemplate는 생성하면서 DataSource를 전달받아야 하므로 setJdbcTemplate이라는 수정자 메소드를 남겨둔다.

### 중복 제거
- 중복된 코드는 없나 살펴본다.
- 앞서 만들었던 get()과 getAll()에서 RowMapper라는 콜백 객체의 내용이 똑같았다.
- RowMapper 콜백 객체를 userMapper라는 인스턴스 변수를 만들고 콜백 오브젝트를 초기화 하도록 만든다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img10.JPG)


- get()과 getAll() 메소드는 다음과 같이 userMapper 인스턴스 변수를 사용한다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/3%EC%9E%A5%20%ED%85%9C%ED%94%8C%EB%A6%BF/6%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20JDBCTEMPLATE/img/img11.JPG)
