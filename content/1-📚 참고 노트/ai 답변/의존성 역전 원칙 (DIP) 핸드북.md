---
publish: true
---
#2025-08-09 00:20

Tags:

# 의존성 역전 원칙 (DIP) 핸드북

## 1. 만들어진 이유: 소프트웨어 부패와의 전쟁

소프트웨어는 시간이 지남에 따라 점점 썩어가는 경향이 있음. 처음에는 깔끔했던 구조가 기능 추가와 수정이 반복되면서 스파게티처럼 얽히고설켜, 결국에는 작은 수정 하나가 시스템 전체에 예상치 못한 오류를 일으키는 '부패한' 코드가 됨.

로버트 C. 마틴(Robert C. Martin, a.k.a Uncle Bob)은 이러한 소프트웨어 부패의 핵심 원인 중 하나가 **'잘못된 의존성'** 때문임을 간파함. 구체적으로, **변하기 쉬운 것(구체적인 구현)에 변하기 어려운 것(중요한 정책, 비즈니스 로직)이 직접적으로 의존**하는 구조가 문제의 근원.

예를 들어, '고수준 정책'인 "알림을 보낸다"는 기능이 '저수준 구현'인 "이메일로 보낸다"에 직접 의존하는 상황을 상상해볼 수 있음.

```
// 나쁜 예: 고수준 정책이 저수준 구현에 직접 의존
class Notifier {
    private EmailSender sender = new EmailSender(); // EmailSender라는 구체적인 클래스에 의존

    public void sendNotification() {
        sender.sendEmail();
    }
}
```

이 구조에서는 나중에 "SMS로도 보내고 싶다"는 요구사항이 생기면 `Notifier` 클래스 자체를 수정해야 함. `Notifier`는 시스템의 중요한 정책을 담고 있는데, 단지 알림 방식이라는 세부 사항 때문에 계속 변경되어야 하는 것. 이런 변경이 쌓이면 시스템은 안정성을 잃고 부패하기 시작함.

**의존성 역전 원칙(Dependency Inversion Principle, DIP)**은 바로 이 문제를 해결하기 위해 탄생. "부패하기 쉬운 저수준의 구체적인 구현으로부터, 안정적인 고수준의 정책을 보호하자"는 것이 핵심 아이디어. 즉, **의존성의 방향을 '역전'시켜** 고수준 모듈이 저수준 모듈의 변경에 영향을 받지 않도록 만드는 것이 목표.

## 2. 핵심 개념: "추상화에 의존하라"

DIP는 두 가지 핵심 원칙으로 정의됨.

> 1. **고수준 모듈은 저수준 모듈에 의존해서는 안 된다. 둘 다 추상화에 의존해야 한다.**
>     
> 2. **추상화는 세부 사항에 의존해서는 안 된다. 세부 사항이 추상화에 의존해야 한다.**
>     

이 말을 쉽게 풀면 다음과 같음.

- **"고수준 모듈" (High-level modules)**: 시스템의 핵심 비즈니스 로직이나 정책. "무엇을 할 것인가"를 정의. (예: 주문 처리, 알림 발송)
    
- **"저수준 모듈" (Low-level modules)**: 고수준 모듈이 정의한 작업을 실제로 "어떻게 할 것인가"를 구현한 세부 사항. (예: MySQL 데이터베이스에 주문 저장, 이메일 API를 사용해 알림 발송)
    

DIP 이전의 전통적인 의존성 관계는 `고수준 모듈 → 저수준 모듈` 이었음.

`주문 처리 로직` → `MySQL 저장 로직`

DIP는 이 흐름을 끊고, 중간에 **"추상화" (Abstraction)**, 즉 인터페이스나 추상 클래스를 도입함.

`고수준 모듈` → **`추상화`** ← `저수준 모듈`

`주문 처리 로직` → **`주문 저장 인터페이스`** ← `MySQL 저장 로직`

이제 고수준 모듈인 `주문 처리 로직`은 `MySQL 저장 로직`이라는 구체적인 세부 사항을 전혀 모름. 오직 `주문 저장 인터페이스`라는 약속(추상화)에만 의존함. `MySQL 저장 로직` 역시 자신이 `주문 저장 인터페이스`라는 약속을 지키고 있다는 것만 알면 됨.

이로써 의존성의 방향이 `고수준 → 저수준`에서 `저수준 → 추상화`로 **역전**된 것. 이것이 바로 '의존성 역전'의 진정한 의미.

## 3. 구조: 제어의 역전(IoC)과 의존성 주입(DI)

DIP를 실제로 구현하는 기술적인 방법이 바로 **제어의 역전(Inversion of Control, IoC)**과 **의존성 주입(Dependency Injection, DI)**.

- **제어의 역전 (IoC)**: 객체가 자신의 의존성을 직접 생성하고 제어하는 것이 아니라, 외부(프레임워크나 컨테이너)에서 제어의 흐름을 넘겨받는 개념. DIP는 IoC의 철학적 기반.
    
- **의존성 주입 (DI)**: IoC를 구현하는 구체적인 기술. 클래스 외부에서 의존 객체를 생성하여 주입(전달)해주는 방식.
    

앞서 본 `Notifier` 예제를 DIP를 적용하여 개선하면 다음과 같음.

**1단계: 추상화(인터페이스) 정의**

먼저 고수준 모듈과 저수준 모듈 사이의 '약속'인 인터페이스를 만듦. 이 인터페이스는 고수준 모듈의 관점에서 정의됨.

```
// 추상화: MessageSender 인터페이스
public interface MessageSender {
    void send();
}
```

**2단계: 저수준 모듈이 추상화를 구현**

각각의 구체적인 알림 방식(저수준 모듈)이 이 인터페이스를 구현하도록 함.

```
// 세부 사항 1: EmailSender
public class EmailSender implements MessageSender {
    @Override
    public void send() {
        // 이메일 발송 로직
        System.out.println("이메일을 보냅니다.");
    }
}

// 세부 사항 2: SmsSender
public class SmsSender implements MessageSender {
    @Override
    public void send() {
        // SMS 발송 로직
        System.out.println("SMS를 보냅니다.");
    }
}
```

**3단계: 고수준 모듈이 추상화에 의존 (의존성 주입 사용)**

고수준 모듈인 `Notifier`는 이제 구체적인 `EmailSender`나 `SmsSender`가 아닌, `MessageSender` 인터페이스에만 의존함. 필요한 의존 객체는 외부에서 생성자를 통해 '주입'받음.

```
// 고수준 모듈: Notifier
public class Notifier {
    private final MessageSender sender;

    // 생성자를 통해 의존성 주입(Constructor Injection)
    public Notifier(MessageSender sender) {
        this.sender = sender;
    }

    public void sendNotification() {
        // 인터페이스의 메서드만 호출
        sender.send();
    }
}
```

**4단계: 외부에서 객체 생성 및 주입 (조립)**

실제 애플리케이션을 실행하는 부분(Main, 또는 Spring 같은 DI 컨테이너)에서 어떤 구현체를 사용할지 결정하고 주입해 줌.

```
public class Main {
    public static void main(String[] args) {
        // 1. 이메일로 보내고 싶을 때
        MessageSender emailSender = new EmailSender();
        Notifier emailNotifier = new Notifier(emailSender);
        emailNotifier.sendNotification(); // "이메일을 보냅니다." 출력

        // 2. SMS로 보내고 싶을 때
        MessageSender smsSender = new SmsSender();
        Notifier smsNotifier = new Notifier(smsSender);
        smsNotifier.sendNotification(); // "SMS를 보냅니다." 출력
    }
}
```

이제 `Notifier` 클래스는 알림 방식이 이메일에서 SMS로, 혹은 나중에 카카오톡으로 바뀌더라도 단 한 줄도 수정할 필요가 없음. 시스템의 중요한 정책이 세부 구현으로부터 완벽하게 분리되어 보호받게 된 것.

## 4. 심화 내용: DIP는 레이어링 그 이상이다

많은 사람들이 DIP를 단순히 '계층(Layer) 사이에 인터페이스를 두는 것'으로 오해하지만, DIP의 본질은 **소유권(Ownership)의 역전**에 있음.

DIP를 적용하기 전에는, 고수준 모듈이 저수준 모듈을 '소유'하고 직접 제어했음. 하지만 DIP를 적용하면, **고수준 모듈이 추상 인터페이스를 '소유'**하게 됨. 저수준 모듈은 단지 그 인터페이스를 구현할 뿐.

즉, 중요한 비즈니스 로직을 담고 있는 고수준 모듈이 자신에게 필요한 서비스(인터페이스)를 직접 정의하고 소유하는 것. 데이터베이스 접근 로직이나 외부 API 통신 로직 같은 세부 사항들은 이 인터페이스에 맞춰 자신을 구현해야 함.

이러한 관점의 전환은 플러그인 아키텍처(Plugin Architecture)의 기반이 됨. 시스템의 핵심 로직(고수준 모듈)을 견고하게 만들고, 다양한 세부 기능(저수준 모듈)을 필요에 따라 '플러그인'처럼 갈아 끼울 수 있는 유연하고 확장 가능한 구조를 만들 수 있게 됨.

의존성 역전 원칙은 단순히 코드를 깔끔하게 만드는 기술을 넘어, 변화에 강하고 지속 가능한 소프트웨어를 만드는 핵심적인 설계 철학입니다.

혹시 DIP를 적용하면서 겪었던 어려움이나 특정 시나리오에서의 적용법에 대해 더 궁금한 점이 있으신가요?

## References
[[5-💎 영구 노트/의존성 역전 원칙]]