
## 1.5 스프링의 IOC
### 1.5.1 오브젝트 팩토리를 이용한 스프링 loC

> - 빈(Bean) : 스프링에서는 스프링이 제어권을 가지고 직접 만들고 관계를 부여하는 오브젝트
> - 빈 팩토리 : 스프링에서 빈의 생성과 관계설정 같은 제어를 담당하는 IoC 오브젝트
> - 애플리케이션 컨텍스트 : 빈 팩토리와 비슷하지만 스프링이 제공하는 지원 기능을 모두 포함한 것


- 설정정보

> - 애플리케이션 컨텍스트는 빈을 생성하고 관계를 설정하는데 별도의 설정정보를 사용한다.
> - 설정정보를 만드는 방법은 다양하지만 아래는 자바 코드로 만든 예제이다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/5~6.%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98IoC,%20%EC%8B%B1%EA%B8%80%ED%86%A4%20%EB%A0%88%EC%A7%80%EC%8A%A4%ED%8A%B8%EB%A6%AC%EC%99%80%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%20%EC%8A%A4%EC%BD%94%ED%94%84/img/img01.JPG)


> - @Configuration 이라는 애노테이션을 추가해서 스프링이 빈 팩토리를 위한 오브젝트 설정을 담당하는 클래스라고 인식할 수 있게 한다.
> - @Bean 이라는 애노테이션을 추가하여 오브젝트를 만들어주는 메소드라고 인식할 수 있게 한다.
> - UserDaoTest에는 AnnotationConfigApplicationContext에 설정정보를 전달하고 “이름”으로 UserDao를 요청해 받아온다.


- 메소드는 하나뿐인데 왜 굳이 이름을 사용할까?
> UserDao를 생성하는 방식이나 구성을 다르게 가져올 수 있게 하기 위함


### 1.5.2 애플리케이션 컨텍스트의 동작방식

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/5~6.%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98IoC,%20%EC%8B%B1%EA%B8%80%ED%86%A4%20%EB%A0%88%EC%A7%80%EC%8A%A4%ED%8A%B8%EB%A6%AC%EC%99%80%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%20%EC%8A%A4%EC%BD%94%ED%94%84/img/img02.JPG)

> 	- @Configuration이 붙은 설정정보를 AppllicationContext에 등록한다.
> 	- 클라이언트는 "userDao“라는 이름으로 AppllicationContext에 요청한다.
> 	- AppllicationContext는 빈 목록을 조회하여 UserDao를 생성하는 함수에 요청한다.
> 	- 생성된 UserDao 객체를 사용자가 사용한다.


- 애플리케이션 컨텍스트의 장점
> 	- 클라이언트는 구체적인 팩토리 클래스롤 알 필요가 없다.
> 	- 애플리케이션 컨텍스트는 종합 IoC 서비스톨 제공해준다.
> 	- 애풀리케이션 컨텍스트는 빈올 검색하는 다양한 방법올 제공한다.
> 
## 1.6 싱글톤 레지스트리와 오브젝트 스코프

- 기존의 DaoFactory와 스프링 컨텍스트를 사용한 방식의 차이점

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/5~6.%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98IoC,%20%EC%8B%B1%EA%B8%80%ED%86%A4%20%EB%A0%88%EC%A7%80%EC%8A%A4%ED%8A%B8%EB%A6%AC%EC%99%80%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%20%EC%8A%A4%EC%BD%94%ED%94%84/img/img03.JPG)


> 기존의 방식대로 UserDao를 생성한 객체 두 개를 콘솔로 출력해보면 내용을 같을 지 몰라도 객체 고유한 값은 다르다는 것을 알 수 있다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/5~6.%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98IoC,%20%EC%8B%B1%EA%B8%80%ED%86%A4%20%EB%A0%88%EC%A7%80%EC%8A%A4%ED%8A%B8%EB%A6%AC%EC%99%80%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%20%EC%8A%A4%EC%BD%94%ED%94%84/img/img04.JPG)


> 스프링 컨텍스트로부터 가져온 두 개의 UserDao 객체는 객체 고유값이 같다는 것을 알 수 있다.

- 왜 그럴까?
> 스프링은 주로 서버환경에서 동작한다.
> 수많은 사용자의 요청이 들어올 때마다 객체를 새로 만들고 없애는 부하를 준다면 아무리 가비지 컬렉션의 성능이 좋다고 해도 서버가 감당하기 힘들기 때문이다.

- 싱글톤 패턴의 한계
> 자바에서 싱글톤을 구현한 방법의 예제이다.
> 아래 예제처럼 만들면 최초 getInstance()를 호출한 시점에는 new UserDao를 생성하지만 그 다음부터 호출할 때는 처음 생성했던 UserDao를 가져와 사용하게 된다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/5~6.%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98IoC,%20%EC%8B%B1%EA%B8%80%ED%86%A4%20%EB%A0%88%EC%A7%80%EC%8A%A4%ED%8A%B8%EB%A6%AC%EC%99%80%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%20%EC%8A%A4%EC%BD%94%ED%94%84/img/img05.JPG)


- 일반적으로 싱글톤 패턴 구현 방식의 문제
>	- private의 생성자 때문에 상속할 수 없다.
>	- 오브젝트를 마음대로 주입하기 힘들기 때문에 테스트 할 때 테스트용 오브젝트로 대체하기가 힘들다.
>	- 서버환경에 따라서 싱글톤이 하나만 만들어 지는 것을 보장하지 못한다.
>	- 전역 상태를 만들 수 있다.

- 싱글톤 레지스트리
> 스프링에서 직접 싱글톤 형태의 객체를 만들고 관리하는 기능
> 위에 나왔던 싱글톤 패턴 구현 방식과 다르게 일반적인 자바 클래스를 싱글톤으로 활용하게 해준다.



### 1.6.2 싱글톤과 오브젝트의 상태
> 싱글톤은 여러 사용자가 동시에 사용되므로 메소드 안에서 사용하는 로컬 변수, 리턴값, 파라미터는 사용할 수 있지만 객체 내부에 공통으로 사용되는 상태 정보를 가지고 있지 않은 “무상태” 방식으로 만들어져야 한다.
- “상태유지” 방식으로 만드는 경우
> 여러 사용자가 동시에 접속하여 누군가 저장해 놓은 변수 값을 또 다른 누군가가 덮어 쓰고 자신이 저장하지 않은 값을 읽어올 수 있어 매우 위험하다.
	
