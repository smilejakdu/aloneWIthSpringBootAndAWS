# Chapter 3 SpringBoot 에서 JPA 사용하기

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

