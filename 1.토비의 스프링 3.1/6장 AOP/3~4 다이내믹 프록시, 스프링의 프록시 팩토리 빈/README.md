## 6.3 다이내믹 프록시와 팩토리 빈

### 6.3.1 프록시와 프록시 패턴, 데코레이터 패턴

#### 트랜잭션 코드의 분리 다시 살펴보기
- 5장에서 UserService에서 핵심기능 코드와 트랜잭션같은 부가기능이 함께 있었다.
- 6.1 에서 핵심기능 코드와 부가기능 코드를 분리하였다
- 부가기능 코드가 있는 UserServiceTx를 만들고 UserServiceTx에서 핵심기능인 UserServiceImpl을 호출하여 사용하도록 만들었다.
- 클라이언트가 핵심기능을 직접 사용한다면 부가기능을 무시하고 지나칠 수 있다
- 그래서 UserServiceTx와 UserServiceImpl이 같은 인터페이스를 사용하도록 한다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/6%EC%9E%A5%20AOP/3~4%20%EB%8B%A4%EC%9D%B4%EB%82%B4%EB%AF%B9%20%ED%94%84%EB%A1%9D%EC%8B%9C,%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20%ED%94%84%EB%A1%9D%EC%8B%9C%20%ED%8C%A9%ED%86%A0%EB%A6%AC%20%EB%B9%88/img/img01.JPG)

#### 프록시
- 핵심기능(UserServiceImpl)을 실행하기 위해서는 반드시 부가기능(UserServiceTx)을 거쳐야한다
- 부가기능을 거친다는 것이 마치 모든 요청을 부가기능이 대신 받아서 전달해주는 대리인 역할과 비슷하다고 해서 프록시라고 부른다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/6%EC%9E%A5%20AOP/3~4%20%EB%8B%A4%EC%9D%B4%EB%82%B4%EB%AF%B9%20%ED%94%84%EB%A1%9D%EC%8B%9C,%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20%ED%94%84%EB%A1%9D%EC%8B%9C%20%ED%8C%A9%ED%86%A0%EB%A6%AC%20%EB%B9%88/img/img02.JPG)

#### 데코레이터 패턴
> 부가적인 기능을 런타임 시 다이나믹하게 부여해주기 위해 프록시를 사용하는 패턴

- 제품이나 케익을 여러 겹으로 포장하고 장식을 붙이는 것처럼 부가적인 효과를 부여해 줄 수 있다고 해서 데코레이터 패턴이라고 한다.
- 프록시가 꼭 하나일 필요 없다.

#### 사용 예)
	InputStream is = new BufferedInputStream(new FileInputStream("a.txt"));

#### 프록시 패턴
> 일반적으로 프록시는 클라이언트와 타겟 사이의 대리 역할을 맡은 오브젝트를 총칭하고 프록시 패턴의 프록시는 타깃의 기능을 확장하거나 추가하지 않고 클라이언트가 타깃에 접근하는 방식을 변경해준다.
- 클라이언트와 타깃 사이에 존재한다.
- 타깃과 같은 인터페이스를 구현해두고 위임하는 방식으로 만든다.
- 기능의 부가 또는 접근 제어를 담당한다.


### 6.3.2 다이내믹 프록시

#### 프록시의 구성
- 타깃과 같은 메소드를 구현
- 메소드 호출 시 부가기능을 수행
- 부가기능 수행 후 타겟 오브젝트로 위임

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/6%EC%9E%A5%20AOP/3~4%20%EB%8B%A4%EC%9D%B4%EB%82%B4%EB%AF%B9%20%ED%94%84%EB%A1%9D%EC%8B%9C,%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20%ED%94%84%EB%A1%9D%EC%8B%9C%20%ED%8C%A9%ED%86%A0%EB%A6%AC%20%EB%B9%88/img/img03.JPG)

#### 프록시 작성의 문제점
- 타깃의 인터페이스를 구현하고 위임하는 코드를 작성하는 것이 번거롭다. => 부가기능이 필요 없는 타깃 인터페이스의 메소드도 구현해서 타깃으로 위임하는 코드를 일일이 만들어줘야 하기 때문
- 부가기능 코드가 중복될 가능성이 높다. => 트랜잭션의 경우 대부분의 로직에 사용하는 만큼 중복이 많아질 수밖에 없다.

#### 다이내믹 프록시 적용
- 앞에 있었던 프록시 작성의 문제점에서 부가기능을 위해 부가기능이 필요없는 인터페이스의 메소드들도 구현해야하는 번거로움이 있었다.
- 다이내믹 프록시를 이용해 만든다면 번거로움이 해소해줄 수 있다.
- 프록시 팩토리에게 인터페이스 정보만 제공해주면 해당 인터페이스를 구현한 클래스의 오브젝트를 자동으로 만들어준다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/6%EC%9E%A5%20AOP/3~4%20%EB%8B%A4%EC%9D%B4%EB%82%B4%EB%AF%B9%20%ED%94%84%EB%A1%9D%EC%8B%9C,%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20%ED%94%84%EB%A1%9D%EC%8B%9C%20%ED%8C%A9%ED%86%A0%EB%A6%AC%20%EB%B9%88/img/img04.JPG)

- Hello 인터페이스를 제공하면, 프록시 팩토리에게 다이내믹 프록시를 요청하면 Hello 인터페이스의 모든 메소드를 구현한 오브젝트를 생성해준다.
- InvocationHaldler 인터페이스를 구현한 오브젝트를 제공하면, 다이내믹 프록시는 받은 모든 요청을 InvocationHaldler의 invoke() 메소드로 보내준다.
- Hello 인터페이스의 메소드가 아무리 많아도 invoke() 메소드 하나로 처리할 수 있다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/6%EC%9E%A5%20AOP/3~4%20%EB%8B%A4%EC%9D%B4%EB%82%B4%EB%AF%B9%20%ED%94%84%EB%A1%9D%EC%8B%9C,%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20%ED%94%84%EB%A1%9D%EC%8B%9C%20%ED%8C%A9%ED%86%A0%EB%A6%AC%20%EB%B9%88/img/img05.JPG)

#### 구현순서
- InvocationHanldler 구현 클래스 작성 : 모든 요청을 전달받을 invoke()
- 다이내믹 프록시 생성 : Proxy 클래스의 newProxyInstance() 팩토리를 이용
=> 클래스로더, 구현할 인터페이스, 부가기능과 위임코드를 담은 클래스를 인수로 전달한다.
