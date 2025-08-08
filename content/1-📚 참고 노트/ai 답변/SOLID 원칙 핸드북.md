---
publish: true
---
#2025-08-08 00:34

Tags:

# SOLID 원칙 핸드북

### 1. 만들어진 이유: 견고하고 유연한 설계를 위한 5가지 기둥

소프트웨어 개발의 역사는 '변경'과의 싸움이었습니다. 새로운 기능 추가, 비즈니스 로직 변경, 기술의 발전 등 소프트웨어는 끊임없이 변화를 요구받습니다. 이 과정에서 잘못된 설계는 코드를 단단하게 굳어버리게(**경직성**), 작은 수정이 예상치 못한 오류를 낳게(**취약성**), 다른 곳에서 재사용하기 어렵게(**부동성**) 만듭니다.

이러한 '썩어가는 코드'의 문제를 해결하기 위해, 로버트 C. 마틴은 객체 지향 프로그래밍 및 설계의 핵심 원칙 다섯 가지를 정리하여 **SOLID**라는 이름으로 세상에 내놓았습니다. SOLID는 단순히 코드를 예쁘게 만드는 기법이 아닙니다. **변화에 유연하게 대처하고, 유지보수가 용이하며, 이해하기 쉬운 소프트웨어를 만들기 위한 설계 철학이자 튼튼한 기둥**입니다.

이 다섯 가지 원칙들은 서로 유기적으로 작용하여, 시스템의 **결합도(Coupling)는 낮추고 응집도(Cohesion)는 높여** 건강한 소프트웨어 구조를 유지하도록 돕습니다.

---

### 2. SOLID: 다섯 가지 원칙 상세 탐구

SOLID는 다음 다섯 가지 원칙의 앞 글자를 딴 이름입니다.

- **S**: 단일 책임 원칙 (Single Responsibility Principle)1
    
- **O**: 개방-폐쇄 원칙 (Open-Closed Principle)2
    
- **L**: 리스코프 치환 원칙 (Liskov Substitution Principle)3
    
- **I**: 인터페이스 분리 원칙 (Interface Segregation Principle)4
    
- **D**: 의존관계 역전 원칙 (Dependency Inversion Principle)
    

#### S: 단일 책임 원칙 (SRP)

> **"하나의 클래스는 단 하나의 변경 이유만을 가져야 한다."**

이는 클래스가 책임져야 할 **액터(Actor) 또는 비즈니스 관심사가 단 하나**여야 함을 의미합니다. 여러 책임을 가진 클래스는 각 책임이 변경될 때마다 수정되어야 하므로 불안정해집니다.

- **핵심**: 책임을 분리하여 변경의 파급 효과를 최소화.
    
- **예시**: 직원의 급여 계산, 근무 시간 보고, 데이터베이스 저장 기능이 한 클래스에 있다면, 이를 `PayCalculator`, `HourReporter`, `EmployeeRepository`처럼 각각의 책임에 따라 별도의 클래스로 분리.
    

#### O: 개방-폐쇄 원칙 (OCP)

> **"소프트웨어 개체(클래스, 모듈, 함수 등)는 확장에 대해서는 열려 있어야 하지만, 변경에 대해서는 닫혀 있어야 한다."**

새로운 기능을 추가할 때 기존 코드를 수정하는 것이 아니라, 새로운 코드를 추가하여 기능을 확장해야 한다는 원칙입니다. 이는 **추상화(인터페이스, 추상 클래스)와 다형성**을 통해 달성됩니다.

- **핵심**: 기존의 안정적인 코드는 건드리지 않고, 새로운 기능을 쉽게 추가할 수 있는 구조를 만듦.
    
- **비유**: 스마트폰. 새로운 앱(기능 확장)을 설치할 수 있지만, 그로 인해 스마트폰의 운영체제(기존 코드)가 변경되지는 않음.
    

**OCP 위반 사례 (Bad Case)**

Java

```
// 새로운 결제 수단이 추가될 때마다 이 클래스를 수정해야 함 (변경에 열려 있음)
public class PaymentProcessor {
    public void process(String paymentType) {
        if (paymentType.equals("creditCard")) {
            // 신용카드 결제 로직
        } else if (paymentType.equals("paypal")) {
            // 페이팔 결제 로직
        }
        // 새로운 결제 수단 추가 시 여기에 else if가 계속 늘어남
    }
}
```

**OCP 적용 사례 (Good Case)**

Java

```
// 1. 결제 인터페이스 정의 (추상화)
interface PaymentMethod {
    void processPayment();
}

// 2. 구체적인 결제 방법 구현
class CreditCardPayment implements PaymentMethod {
    public void processPayment() { /* 신용카드 결제 로직 */ }
}

class PaypalPayment implements PaymentMethod {
    public void processPayment() { /* 페이팔 결제 로직 */ }
}

// 새로운 결제 수단이 생겨도 기존 코드는 수정할 필요가 없음
class BankTransferPayment implements PaymentMethod {
    public void processPayment() { /* 계좌 이체 로직 */ }
}

// 3. 결제 처리기는 인터페이스에만 의존
public class PaymentProcessor {
    public void process(PaymentMethod paymentMethod) {
        paymentMethod.processPayment(); // 어떤 결제 방식이든 동일하게 호출
    }
}
```

#### L: 리스코프 치환 원칙 (LSP)

> **"하위 타입은 언제나 자신의 기반 타입(상위 타입)으로 교체될 수 있어야 한다."**

이는 **상속 관계의 일관성을 보장**하는 원칙입니다. 자식 클래스는 부모 클래스의 역할을 온전히 수행할 수 있어야 하며, 자식 클래스를 사용한다고 해서 프로그램의 동작이 예기치 않게 변경되어서는 안 됩니다.

- **핵심**: 상속은 'IS-A' 관계를 넘어서, '행동의 일관성'까지 보장해야 함.
    
- **유명한 예시**: '정사각형-직사각형' 문제. 정사각형(`Square`)은 직사각형(`Rectangle`)의 한 종류이므로 상속 관계(`Square extends Rectangle`)가 성립하는 것처럼 보입니다. 하지만 직사각형의 `setWidth()`와 `setHeight()`가 독립적으로 동작해야 한다는 규칙을 정사각형은 위반합니다(너비를 바꾸면 높이도 바뀌어야 하므로). 따라서 `Square` 객체는 `Rectangle` 객체를 완벽히 대체할 수 없으므로 LSP를 위반합니다.
    

**LSP 위반 사례 (Bad Case)**

Java

```
class Bird {
    public void fly() { /* 나는 로직 */ }
}

class Ostrich extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("타조는 날 수 없습니다."); // 부모의 행동을 수행할 수 없음
    }
}

public class BirdWatcher {
    public void watch(Bird bird) {
        bird.fly(); // Ostrich 객체가 들어오면 여기서 예외 발생
    }
}
```

#### I: 인터페이스 분리 원칙 (ISP)

> **"클라이언트는 자신이 사용하지 않는 메서드에 의존하도록 강요되어서는 안 된다."**

하나의 거대한(fat) 인터페이스보다는, 여러 개의 구체적인(client-specific) 인터페이스로 분리하는 것이 좋다는 원칙입니다. 이는 SRP가 클래스에 적용되는 것과 유사하게, 인터페이스에 적용되는 버전이라고 볼 수 있습니다.

- **핵심**: 클라이언트의 목적에 맞게 인터페이스를 작게 유지하여, 불필요한 의존성을 제거.
    
- **비유**: 복합기. 나는 '스캔' 기능만 필요한데, '인쇄', '팩스' 기능까지 모두 포함된 거대한 인터페이스를 다룰 필요는 없음. '스캐너 인터페이스'만 사용하는 것이 더 효율적.
    

**ISP 위반 사례 (Bad Case)**

Java

```
// 개발자와 식당 주방장은 모두 '노동자'지만, 필요한 기능이 다름
interface Worker {
    void work();
    void eat();
}

class Developer implements Worker {
    public void work() { /* 코딩하기 */ }
    public void eat() { /* 점심 먹기 */ }
}

class Chef implements Worker {
    public void work() { /* 요리하기 */ }
    public void eat() { /* 저녁 먹기 */ }
}

// 일만 하는 로봇은 eat() 기능이 필요 없지만, 강제로 구현해야 함
class Robot implements Worker {
    public void work() { /* 조립하기 */ }
    public void eat() { /* 아무것도 안 함 - 불필요한 구현 */ }
}
```

**ISP 적용 사례 (Good Case)**

Java

```
// 책임을 기준으로 인터페이스를 분리
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

// 필요한 인터페이스만 구현
class Developer implements Workable, Eatable { /* ... */ }
class Chef implements Workable, Eatable { /* ... */ }
class Robot implements Workable { /* ... */ } // 이제 eat()을 강제로 구현할 필요 없음
```

#### D: 의존관계 역전 원칙 (DIP)

> 1. 상위 수준 모듈은 하위 수준 모듈에 의존해서는 안 된다. 둘 모두 추상화에 의존해야 한다.
> 
> 2. 추상화는 세부 사항에 의존해서는 안 된다. 세부 사항이 추상화에 의존해야 한다.

이는 **'구체적인 구현'이 아닌 '추상적인 인터페이스'에 의존**하라는 원칙입니다. 상위 수준의 비즈니스 로직(상위 모듈)이 하위 수준의 구체적인 구현(하위 모듈, 예: 특정 데이터베이스 라이브러리)에 직접 의존하면, 하위 모듈이 변경될 때 상위 모듈도 함께 변경되어야 합니다. 이를 막기 위해 둘 사이에 '추상화(인터페이스)' 계층을 두는 것입니다.

- **핵심**: 의존성의 방향을 '역전'시켜, 구체적인 것 대신 추상적인 것에 의존하게 만들어 유연성을 확보.
    
- **비유**: 컴퓨터와 키보드. 컴퓨터(상위 모듈)는 'USB 포트'라는 표준 인터페이스(추상화)에 의존합니다. 따라서 'A사 키보드', 'B사 키보드'(하위 모듈, 세부 사항) 어떤 것을 가져와도 USB 포트 규격만 맞으면 컴퓨터는 문제없이 작동합니다. 컴퓨터가 특정 'A사 키보드'의 내부 회로에 직접 의존하지 않는 것과 같습니다.
    

**DIP 위반 사례 (Bad Case)**

Java

```
// 상위 모듈인 Car가 하위 모듈인 NormalTire에 직접 의존
class NormalTire {
    public String getTireType() { return "일반 타이어"; }
}

class Car {
    private NormalTire tire; // 구체적인 클래스에 의존

    public Car() {
        this.tire = new NormalTire(); // 직접 생성
    }

    public void printTireType() {
        System.out.println(tire.getTireType());
    }
}
```

**DIP 적용 사례 (Good Case)**

Java

```
// 1. Tire 인터페이스 정의 (추상화)
interface Tire {
    String getTireType();
}

// 2. 세부 사항이 추상화에 의존
class NormalTire implements Tire {
    public String getTireType() { return "일반 타이어"; }
}

class SnowTire implements Tire {
    public String getTireType() { return "스노우 타이어"; }
}

// 3. 상위 모듈이 추상화에 의존
class Car {
    private Tire tire; // 추상 인터페이스에 의존

    // 외부에서 의존성을 주입받음 (Dependency Injection)
    public Car(Tire tire) {
        this.tire = tire;
    }

    public void printTireType() {
        System.out.println(tire.getTireType());
    }
}
```

---

### 3. 심화: SOLID는 함께할 때 더 강하다

SOLID의 다섯 가지 원칙은 독립적으로도 의미가 있지만, 함께 적용될 때 진정한 시너지를 발휘합니다.

- **OCP**를 지키기 위해서는 **DIP**와 **LSP**가 기반이 되어야 합니다. 변화에 닫혀 있으려면 구체적인 구현이 아닌 추상화에 의존해야 하고(DIP), 확장을 위해 만들어진 하위 타입이 상위 타입을 문제없이 대체할 수 있어야 하기 때문입니다(LSP).
    
- **ISP**와 **SRP**는 클래스와 인터페이스의 크기를 적절하게 유지하여 시스템의 복잡도를 낮추고, 결과적으로 다른 원칙들을 적용하기 쉽게 만드는 토대를 제공합니다.
    

SOLID는 규칙이 아닌 원칙입니다. 모든 상황에 맹목적으로 적용하기보다는, 프로젝트의 특성과 변화의 가능성을 고려하여 유연하게 사용하는 지혜가 필요합니다. 이 원칙들을 나침반 삼아 코드를 작성한다면, 시간이 지나도 흔들리지 않는 견고한 소프트웨어를 구축할 수 있을 것입니다.

이 원칙들 중에서 특히 더 자세한 설명이나 다른 예시가 궁금한 부분이 있으신가요?

### **SOLID 원칙 핸드북**

#### **1. 탄생 배경: 왜 SOLID인가?**

소프트웨어 개발의 역사는 '변경'과의 싸움. 로버트 C. 마틴(Robert C. Martin, "Uncle Bob")은 시간이 지나도 유지보수하기 쉽고, 유연하며, 이해하기 쉬운 소프트웨어를 만들기 위한 5가지 객체 지향 설계 원칙을 정리. 이것이 바로 **SOLID**.

이 원칙들은 특정 기능의 구현 방법이 아닌, **'변경'에 어떻게 대응할 것인가**에 대한 지침. 잘 지어진 건물처럼, 일부를 수리하거나 확장할 때 전체 구조가 흔들리지 않게 하는 설계 철학.

#### **2. S: 단일 책임 원칙 (Single Responsibility Principle, SRP)**

> "클래스는 단 하나의 변경 이유만을 가져야 한다."

- **핵심 개념:** 하나의 클래스는 하나의 기능 또는 책임만 담당. 여러 책임을 한 클래스에 몰아넣으면, 관련 없는 기능의 변경이 다른 기능에 영향을 미치는 '나비 효과'를 유발.
    
- **만들어진 이유:** 특정 클래스가 너무 많은 역할을 수행하는 '만능 클래스(God Class)'가 되는 것을 방지. 코드가 비대해지고 복잡해지면 수정과 테스트가 극도로 어려워지기 때문.
    
- **비유:** 스위스 군용 칼은 다재다능하지만, 전문 요리사는 최고의 요리를 위해 잘 벼려진 '셰프의 나이프' 하나에 집중. 칼의 책임은 '자르는 것'에 한정. 코르크를 따야 한다면 코르크스크루를 사용하지, 칼로 억지로 따려 하지 않음.
    
- **적용 예시:**
    
    - **위반 사례:** `Employee` 클래스가 직원의 정보 관리, 급여 계산, 데이터베이스 저장 기능을 모두 가짐.
        
        ```
        class Employee {
            public String name;
            public long basePay;
        
            public double calculatePay() { /*...*/ }
            public String getEmployeeReport() { /*...*/ }
            public void saveToDatabase() { /*...*/ }
        }
        ```
        
    - **준수 사례:** 책임을 분리하여 각기 다른 변경 이유를 가짐.
        
        - `EmployeeData` 클래스: 직원의 핵심 데이터만 관리.
            
        - `PayCalculator` 클래스: 급여 계산 책임.
            
        - `EmployeeRepository` 클래스: 데이터베이스 저장 책임.
            

#### **3. O: 개방-폐쇄 원칙 (Open/Closed Principle, OCP)**

> "소프트웨어 개체(클래스, 모듈, 함수 등)는 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다."

- **핵심 개념:** 기존 코드를 직접 수정하지 않고도 새로운 기능을 추가할 수 있어야 함.
    
- **만들어진 이유:** 이미 안정적으로 작동하는 코드를 수정하는 것은 버그를 유발할 위험이 큼. 새로운 요구사항이 생길 때마다 기존 코드를 건드리는 것은 비효율적이고 위험.
    
- **비유:** 스마트폰에 새로운 기능을 추가하고 싶을 때, 우리는 앱 스토어에서 앱을 '설치(확장)'하지, 스마트폰의 메인보드를 뜯어서 회로를 '납땜(수정)'하지 않음.
    
- **적용 예시:**
    
    - **위반 사례:** 도형의 면적을 계산하는 함수가 `if-else`로 분기. 새로운 도형(예: 삼각형)을 추가하려면 함수 자체를 수정해야 함.
        
        ```
        class AreaCalculator {
            public double calculate(Object shape) {
                if (shape instanceof Rectangle) { /* 사각형 면적 계산 */ }
                if (shape instanceof Circle) { /* 원 면적 계산 */ }
                // 새로운 도형 추가 시 이 부분을 계속 수정해야 함
                return 0.0;
            }
        }
        ```
        
    - **준수 사례:** `Shape` 인터페이스(추상화)를 만들고, 각 도형 클래스가 이를 구현. `AreaCalculator`는 인터페이스에만 의존.
        
        ```
        interface Shape {
            double getArea();
        }
        class Rectangle implements Shape { /*...*/ }
        class Circle implements Shape { /*...*/ }
        
        // 새로운 Triangle 클래스를 추가해도 AreaCalculator는 수정할 필요가 없음
        class AreaCalculator {
            public double calculate(Shape shape) {
                return shape.getArea();
            }
        }
        ```
        

#### **4. L: 리스코프 치환 원칙 (Liskov Substitution Principle, LSP)**

> "서브타입은 언제나 그것의 기반 타입으로 교체될 수 있어야 한다."

- **핵심 개념:** 자식 클래스는 부모 클래스가 사용되는 곳 어디에서든 동일하게 작동해야 함. 즉, 자식 클래스가 부모 클래스의 행동 규약을 어겨서는 안 됨.
    
- **만들어진 이유:** 상속을 잘못 사용하면 예측 불가능한 버그가 발생. 상속은 'IS-A' 관계를 표현하지만, 모든 'IS-A' 관계가 코드상에서 올바른 상속으로 이어지지는 않기 때문.
    
- **비유:** '스마트 TV'는 'TV'의 한 종류(서브타입). TV 리모컨으로 스마트 TV를 조작할 때, 전원 버튼을 누르면 당연히 전원이 켜지거나 꺼져야 함. 만약 전원 버튼이 미사일을 발사한다면, 이는 리모컨 사용자의 기대를 완전히 저버리는 행동이며 LSP 위반.
    
- **적용 예시:**
    
    - **위반 사례:** `Square`(정사각형) 클래스가 `Rectangle`(직사각형) 클래스를 상속. 직사각형은 너비와 높이를 독립적으로 설정할 수 있지만, 정사각형은 너비를 바꾸면 높이도 함께 바뀌어야 함. 이는 부모 클래스의 행동 규약을 깨뜨리는 것.
        
        ```
        void someFunction(Rectangle r) {
            r.setWidth(10);
            r.setHeight(5);
            // r이 Square 객체였다면, 너비와 높이가 같아야 한다는 규칙 때문에
            // 이 함수의 결과는 예측과 달라짐 (넓이가 50이 아닐 수 있음)
            assert r.getArea() == 50;
        }
        ```
        
    - **준수 사례:** 상속 관계를 재고하거나, 공통의 `Shape` 인터페이스를 사용하도록 구조를 변경.
        

#### **5. I: 인터페이스 분리 원칙 (Interface Segregation Principle, ISP)**

> "클라이언트는 자신이 사용하지 않는 메서드에 의존하도록 강요받아서는 안 된다."

- **핵심 개념:** 하나의 거대한 '만능 인터페이스'보다, 역할에 따라 잘게 쪼개진 여러 개의 '전용 인터페이스'가 더 좋음.
    
- **만들어진 이유:** 불필요하게 거대한 인터페이스는 해당 인터페이스를 구현하는 클래스에게 사용하지도 않을 메서드를 강제로 구현하게 만듦. 이는 불필요한 결합을 만들고 시스템을 경직시킴.
    
- **비유:** 레스토랑에서 커피만 마시러 갔는데, 아침, 점심, 저녁, 음료, 디저트가 모두 담긴 거대한 책 한 권 분량의 메뉴판을 받는 상황. 단지 '음료 메뉴'만 받는 것이 훨씬 효율적.
    
- **적용 예시:**
    
    - **위반 사례:** `IWorker` 인터페이스에 `work()`와 `eat()` 메서드가 모두 포함. `HumanWorker`는 둘 다 구현 가능하지만, `RobotWorker`는 `eat()`을 할 수 없음에도 억지로 구현해야 함.
        
        ```
        interface IWorker {
            void work();
            void eat();
        }
        class RobotWorker implements IWorker {
            public void work() { /* 일하기 */ }
            public void eat() { /* 아무것도 안 함. 불필요한 구현 */ }
        }
        ```
        
    - **준수 사례:** 인터페이스를 역할에 따라 분리.
        
        ```
        interface IWorkable { void work(); }
        interface IEatable { void eat(); }
        
        class HumanWorker implements IWorkable, IEatable { /*...*/ }
        class RobotWorker implements IWorkable { /*...*/ }
        ```
        

#### **6. D: 의존관계 역전 원칙 (Dependency Inversion Principle, DIP)**

> "상위 수준 모듈은 하위 수준 모듈에 의존해서는 안 된다. 둘 모두 추상화에 의존해야 한다." "추상화는 세부 사항에 의존해서는 안 된다. 세부 사항이 추상화에 의존해야 한다."

- **핵심 개념:** 구체적인 구현 클래스에 직접 의존하지 말고, 인터페이스나 추상 클래스 같은 '추상화'에 의존.
    
- **만들어진 이유:** 상위 정책(비즈니스 로직)이 하위 구현(세부 기술)의 변경에 직접적으로 영향을 받지 않도록 하기 위함. 시스템의 각 부분을 독립적인 부품처럼 교체할 수 있게 만들어 유연성을 극대화.
    
- **비유:** 램프를 집의 전기 배선에 직접 납땜하지 않음. 대신 '플러그'와 '콘센트'라는 표준 인터페이스(추상화)를 사용. 램프(상위 모듈)와 전기 배선(하위 모듈)은 모두 이 표준 인터페이스에 의존. 덕분에 어떤 제조사의 램프든 콘센트에 꽂아 사용할 수 있음.
    
- **적용 예시:**
    
    - **위반 사례:** `Notification` 클래스가 `EmailSender`라는 구체적인 클래스를 직접 생성하고 사용. 나중에 SMS로 알림 방식을 바꾸려면 `Notification` 클래스 코드를 직접 수정해야 함.
        
        ```
        class Notification {
            private EmailSender emailSender = new EmailSender();
        
            public void send() {
                emailSender.sendEmail();
            }
        }
        ```
        
    - **준수 사례:** `MessageSender`라는 인터페이스를 만들고, `Notification` 클래스는 이 인터페이스에만 의존. 실제 사용할 `EmailSender`나 `SmsSender` 객체는 외부에서 주입(Dependency Injection).
        
        ```
        interface MessageSender {
            void send();
        }
        class Notification {
            private MessageSender sender;
        
            // 생성자를 통해 의존성 주입
            public Notification(MessageSender sender) {
                this.sender = sender;
            }
        
            public void send() {
                sender.send();
            }
        }
        ```
        

SOLID 원칙들은 결국 좋은 설계를 위한 도구 상자입니다. 각 원칙이 어떤 문제를 해결하기 위해 만들어졌는지 이해한다면, 더 견고하고 유연한 소프트웨어를 만드는 데 큰 도움이 될 것입니다.

이 원칙들 중에서 특별히 더 궁금하거나 실제 프로젝트에 적용하는 데 어려움을 느끼는 부분이 있으신가요?
## References
[[5-💎 영구 노트/SOLID]]