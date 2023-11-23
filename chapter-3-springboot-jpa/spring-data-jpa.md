# Spring Data Jpa 적용

JPA 는 인터페이스로서 자바 표준 명세서입니다.

인터페이스인 JPA를 사용하기 위해서는 구현체가 필요하다.

Spring Data JPA 라는 모듈을 이용하여 JPA 기술을 다룹니다.

이들의 관계를 보면 다음과 같다.

<figure><img src="../.gitbook/assets/스크린샷 2023-11-21 오후 10.21.27.png" alt=""><figcaption></figcaption></figure>

Hibernate 를 쓰는 것과 Spring Data JPA를 쓰는 것 사이에는 큰 차이가 없습니다.

Spring Data JPA 를 사용하게 되면 Hibernate 외에 다른 구현체로 쉽게 교체가 가능하다.

다음으로 저장소 교체의 용이성이다.

서비스 초기에는 관계형 데이터베이스로 모든 기능이 처리가 가능하지만,

점점 트래픽이 많아져 관계형 디비로는 더저히 감당이 안될때가 있다.

그럴때 MongoDB로 교체가 필요하다면 개발자는 Spring Data JPA에서 Spring Data MongoDB로 의존성만 교체하면 됩니다.



## 프로젝트에 Spring Data JPA 적용하기

`build.gradle` 에 의존성들을 등록한다.



```java
dependencies {
    compile('org.springframework.boot:spring-boot-starter-web')
    compile('org.projectlombok:lombok')
    compile('org.springframework.boot:spring-boot-starter-data-jpa')
    compile('com.h2database:h2')
    testCompile('org.springframework.boot:spring-boot-starter-test')
}
```



* spring-boot-starter-data-jpa
  * spring boot 용 Spring Data Jpa 추상화 라이브러리 이다.&#x20;
  * JPA 관련 라이브러리들의 버전을 관리해준다.
* h2
  * 인메모리 RDB
  * 테스트용도로 많이 사용

```java
package com.jojoldu.book.springboot.domain.posts;

import com.jojoldu.book.springboot.domain.BaseTimeEntity;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Getter
@NoArgsConstructor
@Entity
public class Posts extends BaseTimeEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 500, nullable = false)
    private String title;

    @Column(columnDefinition = "TEXT", nullable = false)
    private String content;

    private String author;

    @Builder
    public Posts(String title, String content, String author) {
        this.title = title;
        this.content = content;
        this.author = author;
    }
}

```



```java
package com.jojoldu.book.springboot.domain.posts;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;

import java.util.List;

public interface PostsRepository extends JpaRepository<Posts, Long> {

    @Query("SELECT p FROM Posts p ORDER BY p.id DESC")
    List<Posts> findAllDesc();
}


```
