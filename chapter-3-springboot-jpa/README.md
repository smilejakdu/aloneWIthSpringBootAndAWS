# Chapter 3 SpringBoot 에서 JPA 사용과 테스트 코드

<figure><img src="../.gitbook/assets/스크린샷 2023-11-21 오후 10.46.08.png" alt="" width="375"><figcaption></figcaption></figure>



## Spring Data JPA 테스트 코드

<figure><img src="../.gitbook/assets/스크린샷 2023-11-21 오후 10.50.42.png" alt="" width="375"><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/스크린샷 2023-11-21 오후 11.18.48.png" alt="" width="375"><figcaption></figcaption></figure>

테스트가 잘 실행이 된다.

**인-메모리 데이터베이스 사용 ->** H2와 같은 인-메모리 데이터베이스를 사용하여 테스트를 실행합니다. 인-메모리 데이터베이스는 테스트가 끝나면 데이터가 모두 사라지기 때문에, 실제 데이터베이스를 오염시키지 않습니다.

그러면 Spring Boot 에서 설정하지 않아도 자동으로 H2와 같은 인-메모리 데이터베이스가 실행이 되는걸까 ??

## ✅ 테스트를 위한 H2 인-메모리 데이터베이스 사용

인-메모리 데이터베이스, 예를 들어 H2 데이터베이스를 사용하기 위해서는 몇 가지 설정이 필요합니다. 단순히 테스트 파일을 실행한다고 해서 자동으로 인-메모리 데이터베이스가 사용되지는 않습니다. 주로 다음과 같은 단계를 거쳐야 합니다:

1.  **의존성 추가:** 먼저, `build.gradle` (Gradle을 사용하는 경우) 또는 `pom.xml` (Maven을 사용하는 경우) 파일에 H2 데이터베이스 관련 의존성을 추가해야 합니다. 예를 들어, Gradle을 사용하는 경우 `build.gradle` 파일에 다음과 같은 의존성을 추가합니다:

    ```gradle
    dependencies {
        testImplementation 'com.h2database:h2'
        // 기타 필요한 의존성들...
    }
    ```
2.  **테스트 환경 설정:** 테스트 환경에서 인-메모리 데이터베이스를 사용하도록 설정해야 합니다. 이는 `src/test/resources` 디렉토리 아래에 위치한 `application.properties` 또는 `application.yml` 파일을 통해 설정할 수 있습니다. 예를 들어, 다음과 같이 설정할 수 있습니다:

    ```properties
    # src/test/resources/application.properties
    spring.datasource.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;MODE=MySQL
    spring.datasource.driver-class-name=org.h2.Driver
    spring.datasource.username=sa
    spring.datasource.password=
    spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
    ```

    이 설정은 테스트 실행 시 H2 인-메모리 데이터베이스를 사용하도록 지시합니다.
3. **테스트 실행:** 이제 테스트를 실행하면, 설정된 인-메모리 데이터베이스를 사용하여 테스트가 진행됩니다. 테스트가 종료되면 인-메모리 데이터베이스에 저장된 모든 데이터는 사라집니다.

이러한 설정을 통해, 테스트 실행 시 실제 운영 데이터베이스가 아닌 H2와 같은 인-메모리 데이터베이스를 사용하여 데이터베이스 관련 테스트를 안전하게 수행할 수 있습니다.



만약 로컬 환경에서는 개발데이터베이스가 연결을 하고 싶고 , 테스트 실행시에는 H2 인-메모리 데이터베이스를 실행하고 싶을땐 어떻게 해야할까??

## ✅ 개발 데이터베이스와 H2 인-메모리 데이터베이스 설정 분리

Gradle의 `build.gradle` 파일에 `testImplementation 'com.h2database:h2'`를 추가하는 것은 테스트 환경에서 H2 데이터베이스를 사용할 수 있도록 하는 설정입니다. 그러나 이 설정 자체만으로는 테스트 실행 시 자동으로 H2 데이터베이스가 사용되는 것은 아닙니다. 테스트 환경에서 H2를 사용하도록 명시적으로 설정해야 합니다.

테스트 코드 실행 시 H2 데이터베이스를 사용하고, 일반 애플리케이션 실행(예: 로컬에서 애플리케이션을 실행) 시 MySQL과 같은 다른 데이터베이스를 사용하려면 다음과 같이 설정해야 합니다:

1.  **테스트 환경 설정:** `src/test/resources` 디렉토리에 `application.properties` 또는 `application.yml` 파일을 만들고, 여기에 H2 데이터베이스 관련 설정을 추가합니다.

    ```properties
    # src/test/resources/application.properties
    spring.datasource.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;MODE=MySQL
    spring.datasource.driver-class-name=org.h2.Driver
    spring.datasource.username=sa
    spring.datasource.password=
    spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
    ```
2.  **개발/운영 환경 설정:** `src/main/resources` 디렉토리에 있는 `application.properties` 또는 `application.yml` 파일에 MySQL 또는 다른 데이터베이스에 대한 설정을 추가합니다.

    ```properties
    # src/main/resources/application.properties
    spring.datasource.url=jdbc:mysql://localhost:3306/your_database
    spring.datasource.username=your_username
    spring.datasource.password=your_password
    spring.jpa.database-platform=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

이렇게 설정하면, 테스트를 실행할 때는 `src/test/resources`의 설정이 적용되어 H2 데이터베이스를 사용하고, 애플리케이션을 일반적으로 실행할 때는 `src/main/resources`의 설정이 적용되어 MySQL 또는 지정한 다른 데이터베이스를 사용하게 됩니다.

즉, `build.gradle`에 의존성을 추가하는 것은 해당 라이브러리를 프로젝트에서 사용할 수 있도록 하는 것이며, 실제 어떤 데이터베이스를 사용할지는 애플리케이션의 설정 파일에서 결정됩니다.

