# Chapter 3 SpringBoot 에서 JPA 사용하기





웹 서비스를 개발하고 운영하다 보면 피할 수 없는 문제가 데이터 베이스를 다루는 일이다.

객체 모델링 보다는 테이블 모델링에만 집중하게 된다.

문제의 해결책으로 JPA라는 자바 표준 ORM 기술을 만나게 됩니다.



## JPA 소개



관계형 데이터베이스가 계속해서 웹 서비스의 중심이 되면서 모든 코드는 SQL 중심으로 되었습니다.

관계형 데이터베이스는 어떻게 데이터를 저장 할지에 초점이 맞춰진 기술입니다.

반대로 객체지향 프로그래밍 언어는 메시지를 기반으로 기능과 속성을 한 곳에서 관리하는 기술입니다.

관계형 데이터베이스에서 추상화, 캡슐화, 정보은닉, 다형성 등 표현하기란 쉽지 않습니다.



개발자는 객체지향적으로 프로그래밍을 하고, JPA가 이를 관계형 데이터베이스에 맞게 SQL을 대신 생성해서 실행합니다.

개발자는 항상 객체지향적으로 코드를 표현할 수 있으니 더는 SQL에 종속적인 개발을 하지 않아도 됩니다.



## Spring Data JPA

JPA 는 인터페이스로서 자바 표준 명세서입니다.

인터페이스인 JPA를 사용하기 위해서는 구현체가 필요하다.

Spring Data JPA 라는 모듈을 이용하여 JPA 기술을 다룹니다.

이들의 관계를 보면 다음과 같다.

<figure><img src=".gitbook/assets/스크린샷 2023-11-21 오후 10.21.27.png" alt=""><figcaption></figcaption></figure>

Hibernate 를 쓰는 것과 Spring Data JPA를 쓰는 것 사이에는 큰 차이가 없습니다.

Spring Data JPA 를 사용하게 되면 Hibernate 외에 다른 구현체로 쉽게 교체가 가능하다.

다음으로 저장소 교체의 용이성이다.

서비스 초기에는 관계형 데이터베이스로 모든 기능이 처리가 가능하지만,

점점 트래픽이 많아져 관계형 디비로는 더저히 감당이 안될때가 있다.

그럴때 MongoDB로 교체가 필요하다면 개발자는 Spring Data JPA에서 Spring Data MontoDB로 의존성만 교체하면 됩니다.

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

<figure><img src=".gitbook/assets/스크린샷 2023-11-21 오후 10.46.08.png" alt="" width="375"><figcaption></figcaption></figure>

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



## Spring Data JPA 테스트 코드 작성하기

<figure><img src=".gitbook/assets/스크린샷 2023-11-21 오후 10.50.42.png" alt="" width="375"><figcaption></figcaption></figure>

`PostsRepositoryTest` 에서는 다음과 같이 save, findAll 기능을 테스트 합니다.



```java
package com.jojoldu.book.springboot.domain.posts;

import org.junit.After;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.time.LocalDateTime;
import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest
public class PostsRepositoryTest {

    @Autowired
    PostsRepository postsRepository;
    
    @After
    public void cleanup() {
        postsRepository.deleteAll();
    }
    
    @Test
    public vode 게시글저장_불러오기() {
        // given
        String title = "테스트 게시글";
        String content = "테스트 본문";
        
        postsRepository.save(Posts.builder()
                                    .title(title)
                                    .content(content)
                                    .author("jojoldu@gmail.com")
                                    .build());
        // when
        List<Posts> postsList = postsRepository.findAll();
        
        // then
        Posts posts = postsList.get(0);
        assertThat(posts.getTitle()).isEqualTo(title);
        assertThat(posts.getContent()).isEqualTo(content);
        
    }
}
```



별다른 설정이 없다면 `@SpringBootTest` 를 사용할 경우 H2 데이터베이스를 자동으로 실행해준다.

이 테스트 역시 실행할 경우 H2 가 자동으로 실행된다.

<figure><img src=".gitbook/assets/스크린샷 2023-11-21 오후 11.18.48.png" alt="" width="375"><figcaption></figcaption></figure>

테스트가 잘 실행이 된다.

