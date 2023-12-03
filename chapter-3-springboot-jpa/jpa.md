# JPA 소개



웹 서비스를 개발하고 운영하다 보면 피할 수 없는 문제가 데이터 베이스를 다루는 일이다.

객체 모델링 보다는 테이블 모델링에만 집중하게 된다.

문제의 해결책으로 JPA라는 자바 표준 ORM 기술을 만나게 됩니다.



관계형 데이터베이스가 계속해서 웹 서비스의 중심이 되면서 모든 코드는 SQL 중심으로 되었습니다.

관계형 데이터베이스는 어떻게 데이터를 저장 할지에 초점이 맞춰진 기술입니다.

반대로 객체지향 프로그래밍 언어는 메시지를 기반으로 기능과 속성을 한 곳에서 관리하는 기술입니다.

관계형 데이터베이스에서 추상화, 캡슐화, 정보은닉, 다형성 등 표현하기란 쉽지 않습니다.



개발자는 객체지향적으로 프로그래밍을 하고, JPA가 이를 관계형 데이터베이스에 맞게 SQL을 대신 생성해서 실행합니다.

개발자는 항상 객체지향적으로 코드를 표현할 수 있으니 더는 SQL에 종속적인 개발을 하지 않아도 됩니다.