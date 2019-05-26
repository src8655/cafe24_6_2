## 1.3 DAO의 확장
### 1.3.1 클래스의 분리

- 상속을 이용하여 DBConnection을 구현하였을 때의 문제점
> Connection클래스와 DAO클래스의 연관관계를 분리하지 못함

#### DAO와 Connection클래스의 분리를 위한 아이디어
> 1. DAO클래스의 멤버로 Connection을 포함

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/3~4.DAO%EC%9D%98%20%ED%99%95%EC%9E%A5%2C%20%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84(IoC)/ConnectionMakerDiv.PNG)

- 이 아이디어의 문제점
> - Connection 클래스를 구현한 곳마다 메소드 이름이 다를 경우 DAO이름을 매번 수정해주어야 한다.
> - 구현된 Connection의 클래스명을 DAO클래스가 알고 있어야 한다.

### 1.3.2 인터페이스의 도입

> 2. Connection의 타입을 인터페이스로 설정

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/3~4.DAO%EC%9D%98%20%ED%99%95%EC%9E%A5%2C%20%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84(IoC)/interface.PNG)

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/3~4.DAO%EC%9D%98%20%ED%99%95%EC%9E%A5%2C%20%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84(IoC)/interfacecode1.PNG)

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/3~4.DAO%EC%9D%98%20%ED%99%95%EC%9E%A5%2C%20%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84(IoC)/interfacecode2.PNG)

- 이 아이디어의 문제점
> - 생성자의 ConnectionMaker 초기화 코드에 ConnectionMaker의 구현 클래스명이 필요하다.

### 1.3.3 관계설정 책임의 분리

- UserDao가 Connection클래스를 알아야 하는 근본적인 이유
> - UserDao의 관심사가 아닌 DB Connection 부분을 UserDao를 구현한 사람이 설정해준다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/3~4.DAO%EC%9D%98%20%ED%99%95%EC%9E%A5%2C%20%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84(IoC)/dependency.PNG)

> 3. 클라이언트 오브젝트가 UserDao와 Connection의 관계를 설정

> UserDao를 실제로 사용하는 클라이언트에서, 어떤 Connection을 사용할지 결정해서 UserDao에 주입해준다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/3~4.DAO%EC%9D%98%20%ED%99%95%EC%9E%A5%2C%20%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84(IoC)/UserDaoTest.PNG)

- UserDaoTest 클래스에서 UserDao와 Connection을 설정했을 때의 장점
> - UserDao가 Connection 구현 클래스 이름을 알 필요가 없다.
> - Connection 구현 클래스가 달라지더라도 손쉽게 확장 가능하다.

### 1.3.4 원칙과 패턴

#### 객체지향 설계 5대 원칙
> - 객체지향 기술은 어느 날 한 번에 만들어진 것이라기보다는 오랜 시간에 걸쳐 점진적으로 발전해온 기슐이라고 볼 수 있다. 그렇기 때문에 객체지향 프로그래밍 언어의 종류도 다양하고 객체지항 기술을 받아들이고 적용하는 관점과 기법도 조금씩 차이가 있다. 그럼에도 객체지향이라는 이름으로 묶ㅇㄹ 수 있는 분명한 특징이 있다. 객체지향 설계 원칙은 객체지향의 특징을 잘 살릴 수 있는 설계의 특징을 말한다.

- SRP(The Single Responsibility Principle): 단일 책임 원칙 - 한 클래스는 하나의 책임만 가져야 한다.
- OCP(The Open Closed Principle): 개방 폐쇄 원칙 - 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.
- LSP(The Liskov Substitution Principle): 리스코프 치환 원칙 - 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.
- ISP(The Interface Segregation Principle): 인터페이스 분리 원칙 - 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.
- DIP(The Dependency Inversion Principle): 의존관계 역전 원칙 - 프로그래머는 추상화에 의존해야지, 구체화에 의존하면 안된다.

#### 높은 응집도와 낮은 결합도

> - 높은 응집도 : 변경이 일어날 때 모듈의 많은 부분이 함께 바뀐다면 응집도가 높다고 말할 수 있다.
> - 낮은 결합도 : 하나의 변경이 발생할 때 마치 파문이 이는 것처럼 여타 모듈과 객체로 변경에 대한 요구가 전파되지 않는 상태를 말한다.

#### 전략 패턴

> - 전략 패턴이란 자신의 기능 맥락에서, 필요에 따라 변경이 필요한 알고리즘을 인터페이스를 통해 통째로 외부로 분리시키고，이를 구현한 구체적인 알고리즘 클래스를 필요에 따라 바꿔서 사용할 수 있게 하는 디자인 패턴이다.
> - 개방 패쇄 원칙의 실현에도 가장 잘 들어맞는 패턴이라고 볼 수 있다.
> - 디자인 패턴의 꽃이라고 불릴 만큼 다양하게 자주 시용되는 패턴이다.

## 1.4 제어의 역전(IoC)
### 1.4.1 오브젝트 팩토리

- 팩토리
> - 객체의 생성 방법을 결정하고 그렇게 만들어진 오브젝트를 돌려주는 클래스
> - 특별한 문제를 해결하기 위해 사용되는 추상 팩토리 패턴이나 팩토리 메소드 패턴과는 다르다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/3~4.DAO%EC%9D%98%20%ED%99%95%EC%9E%A5%2C%20%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84(IoC)/UserDaoFactory.PNG)


#### 설계도로서의 팩토리
- 분리된 오브젝트들의 역할과 관계
> - UserDao와 ConnectionMaker는 각각 애플리케이션의 핵심적인 데이터 로직과 기술 로직을 담당
> - DaoFactory는 이런 애플리케이션의 오브젝트들을 구성하고 그 관계를 정의히는 책임을 맡고 있음

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/3~4.DAO%EC%9D%98%20%ED%99%95%EC%9E%A5%2C%20%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84(IoC)/ObjectFactory.PNG)


### 1.4.2 오브젝트 팩토리의 활용
- 오브젝트 팩토리의 문제점
> - 다른 Dao의 기능을 구현할 때, ConnectionMaker의 구현 클래스를 선택하는 부분이 중복으로 나타난다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/3~4.DAO%EC%9D%98%20%ED%99%95%EC%9E%A5%2C%20%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84(IoC)/FactoryDup.PNG)

> - 이를 해결하려면 Connection 구현 부분을 별도의 메소드로 뽑아낸다.

![enter image description here](https://raw.githubusercontent.com/src8655/cafe24_6_2/master/1.%ED%86%A0%EB%B9%84%EC%9D%98%20%EC%8A%A4%ED%94%84%EB%A7%81%203.1/1%EC%9E%A5%20%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8%EC%99%80%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84/3~4.DAO%EC%9D%98%20%ED%99%95%EC%9E%A5%2C%20%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84(IoC)/ConnectionMethod.PNG)

### 1.4.3 제어권의 이전을 통한 제어관계 역전
- 제어의 역전
> - 프로그램의 제어 흐름 구조가 뒤바뀌는 것
> - 이미 프로그램의 순서는 정해져 있고, 그 안에 자신이 만든 기능들을 적용하는 것
> - Connection의 인터페이스를 UserDao에서 참조하는 것, UserDao와 Connection을 UserDaoTest에서 결정해서 실행하는 것 모두 제어의 역전이 적용된 경우이다.
