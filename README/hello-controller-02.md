# Hello Controller 테스트 코드 02



<figure><img src="../.gitbook/assets/스크린샷 2023-11-20 오후 11.48.37.png" alt="" width="375"><figcaption></figcaption></figure>

new -> Java Class 를 클릭해서 이름을 `Application` 으로 생성한다.

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```



위의 코드는 메인 클래스이다.

이 클래스는 항상 프로젝트의 최상단에 위치해야한다.

main 메소드에서 실행하는 `SpringApplication.run` 으로 인해 내장 WAS 를 실행한다.

내장 WAS 란 별도로 외부에 WAS 를 두지 않고 애플리케이션을 실행할 때 내부에서 WAS를 실행하는 것을 얘기한다. 이렇게 되면 항상 서버에 `톰캣을 설치할 필요가 없게 되고`, 스프링 부트로 만들어진 Jar 파일로 실행하면 된다.



꼭 스프링 부트에서만 내장 WAS를 사용할 수 있는 것은 아니지만, 스프링 부트에서는 `내장 WAS를 사용하는 것을 권장` 하고 있다.

언제 어디서나 같은 환경에서 스프링 부트를 배포할 수 있기 때문이다.

이제 테스트를 위한 Controller 를 만든다.



<figure><img src="../.gitbook/assets/스크린샷 2023-11-20 오후 11.57.30.png" alt=""><figcaption></figcaption></figure>

Package 를 생성한다 이름은 web 으로 한다.

컨트롤러와 관련된 클래스들은 web 패키지에 넣도록 한다.

new -> Java Class -> 클래스 이름은 `HelloController` 로 한다.

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello() {
        return "hello";
    }
}
```

`RestController` : 컨트롤러를 JSON 을 반환하는 컨트롤러로 만들어 준다.

이제 테스트를 실행한다.

<figure><img src="../.gitbook/assets/스크린샷 2023-11-21 오전 12.27.50.png" alt=""><figcaption></figcaption></figure>

테스트 코드를 작성할 클래스를 생성합니다.

`HelloControllerTest` 생성

`src/test/java/com/projectName/book/springboot/web/HelloControllerTest`

```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.ResultActions;
import static org.springframework.test.web.servlet.request.MockMvcResultMatchers.get;
import static org.springframework.test.web.servlet.request.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.request.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@WebMvcTest(controllers = HelloController.class)
public class HelloControllerTest {
    
    @Authwired
    private MockMvc mvc;
    
    @Test
    public void hello_return() throws Exception {
        String hello = "hello";
        
        mvc.perform(get("/hello"))
                .andExpect(status().isOk())
                .andExpect(content().string(hello));
    }
}
```



위의 코드는 각각 무슨 의미를 하는지 반드시 알아야한다.

<figure><img src="../.gitbook/assets/스크린샷 2023-11-21 오전 12.43.54.png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/스크린샷 2023-11-21 오전 12.44.29.png" alt="" width="517"><figcaption></figcaption></figure>

이후 테스트 코드 hello\_return 를 클릭해서 테스트를 실행시킨다.

테스트 코드가 실행이 되고 ,&#x20;

`.andExpect(status().isOk())` 와 `andExpect(content().string(hello))` 가 모두 테스트를 통과했음을 의미한다.

이후 실행하고 나면 localhost:8080 에 테스트 코드의 결과가 똑같이 나온다는것을 알 수 있다.

테스트 코드로 먼저 검증 후, 정말 못 믿겠다는 생각이 들 땐 프로젝트를 실행해 확인해야한다.



## 롬복 설치하기

<figure><img src="../.gitbook/assets/스크린샷 2023-11-21 오전 12.48.43.png" alt=""><figcaption></figcaption></figure>

이후 Hello Controller 에 롬복으로 전환하자.

테스트 코드를 작성하게 되면 리팩토링을 수월하게 할 수 있다.

`web` 패키지에 dto 패키지를 추가한다.

모든 응답 dto 는 dto 패키지에 추가한다.

<figure><img src="../.gitbook/assets/스크린샷 2023-11-21 오전 12.51.09.png" alt=""><figcaption></figcaption></figure>

`src/main/java/com/projectName/book/springboot/web/dto/HelloResponseDto`

```java
import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
public class HelloResponseDto {
    private final String name;
    private final int amount;
}
```

`@RequiredArgsConsturctor` : 선언된 모든 final 필드가 포함된 생성자를 생성해 줍니다.

final이 없는 필드는 생성자에 포함되지 않습니다.

Dto에 적용된 롬복이 잘 작동하는지 테스트 코드를 실행해봅니다.

`src/test/java/com/projectName/book/springboot/web/dto/HelloResponseDtoTest`
