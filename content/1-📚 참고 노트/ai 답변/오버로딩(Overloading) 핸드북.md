---
publish: true
---
#2025-08-09 12:53

Tags:

# 오버로딩(Overloading) 핸드북

## 1. 만들어진 이유: 왜 오버로딩이 필요한가?

프로그래밍을 하다 보면, **같은 이름의 작업을 하지만 처리하는 데이터의 종류나 개수가 다른 경우**가 자주 발생.

예를 들어, '더하기'라는 기능을 만든다고 가정. 정수 두 개를 더하는 기능, 실수 두 개를 더하는 기능, 그리고 정수 세 개를 더하는 기능이 필요할 수 있음.

오버로딩이 없다면, 이 기능들을 모두 다른 이름으로 만들어야 함.

```
int add_integers(int a, int b);
double add_doubles(double a, double b);
int add_three_integers(int a, int b, int c);
```

이 방식은 몇 가지 문제를 야기함:

- **직관성 저하:** 같은 '더하기' 기능임에도 불구하고 개발자는 `add_integers`, `add_doubles` 등 여러 이름을 기억하고 구분해서 사용해야 함.
    
- **유지보수의 어려움:** 기능의 이름이 여러 개로 흩어져 있어 코드를 관리하고 수정하기 번거로움.
    

**오버로딩**은 이러한 문제를 해결하기 위해 탄생. **하나의 이름으로 여러 버전의 함수(또는 메서드)를 정의**할 수 있게 하여, 프로그래머가 기능의 본질에만 집중하도록 돕는 것이 핵심 목표. 즉, **코드의 가독성과 재사용성을 극대화**하기 위한 문법적 설탕(Syntactic Sugar)과 같음.

> **비유:** 한 사람(함수 이름)이 여러 언어(매개변수 타입)를 구사하는 것과 같음. 우리는 그 사람에게 '안녕하세요'라고 한국어로 말할 수도 있고, 'Hello'라고 영어로 말할 수도 있음. 그 사람은 어떤 언어로 말을 걸었는지에 따라 적절하게 알아듣고 반응함.

## 2. 구조 및 원리: 컴퓨터는 어떻게 구분하는가?

컴퓨터(정확히는 컴파일러)는 함수 이름만으로 함수를 구분하지 않음. **함수 시그니처(Function Signature)**라는 고유한 식별자를 사용.

**함수 시그니처**는 다음 요소들로 구성됨:

1. **함수 이름**
    
2. **매개변수(Parameter)의 개수**
    
3. **매개변수의 데이터 타입**
    
4. **매개변수의 순서**
    

**중요:** **반환 타입(Return Type)은 함수 시그니처에 포함되지 않음.** 따라서 반환 타입만 다른 함수는 오버로딩할 수 없음.

컴파일러는 코드를 기계어로 번역할 때, 같은 이름의 함수들이라도 시그니처를 기반으로 내부적으로는 서로 다른 이름(예: `add_int_int`, `add_double_double`)으로 변환하여 관리함. 이 과정을 **네임 맹글링(Name Mangling)**이라고 부름.

따라서 개발자가 `add(10, 20);` 코드를 작성하면, 컴파일러는 '정수 두 개를 받는 `add` 함수'를 찾아 연결해주고, `add(3.14, 2.71);` 코드를 작성하면 '실수 두 개를 받는 `add` 함수'를 찾아 연결해주는 것. 이 모든 과정은 컴파일 시점에 결정됨.

## 3. 사용법: 실제 코드 예시

오버로딩은 함수뿐만 아니라, 클래스의 생성자(Constructor)에서도 매우 유용하게 사용됨.

### 함수 오버로딩 (Function Overloading)

하나의 `print` 함수로 다양한 데이터 타입을 출력하는 예시.

```
class Printer {
    // 정수를 출력하는 print 메서드
    public void print(int a) {
        System.out.println("정수: " + a);
    }

    // 실수를 출력하는 print 메서드
    public void print(double a) {
        System.out.println("실수: " + a);
    }

    // 문자열을 출력하는 print 메서드
    public void print(String a) {
        System.out.println("문자열: " + a);
    }
}

public class Main {
    public static void main(String[] args) {
        Printer p = new Printer();
        p.print(100);         // 정수 버전 호출
        p.print(3.14);        // 실수 버전 호출
        p.print("Hello");     // 문자열 버전 호출
    }
}
```

### 생성자 오버로딩 (Constructor Overloading)

객체를 다양한 방법으로 초기화할 수 있도록 여러 버전의 생성자를 만드는 예시.

```
#include <iostream>
#include <string>

class User {
public:
    std::string name;
    int age;

    // 기본 생성자
    User() {
        name = "Guest";
        age = 0;
    }

    // 이름만으로 초기화하는 생성자
    User(std::string n) {
        name = n;
        age = 0;
    }

    // 이름과 나이로 초기화하는 생성자
    User(std::string n, int a) {
        name = n;
        age = a;
    }

    void introduce() {
        std::cout << "이름: " << name << ", 나이: " << age << std::endl;
    }
};

int main() {
    User user1;               // 기본 생성자 호출
    User user2("Alice");      // 이름만 받는 생성자 호출
    User user3("Bob", 30);    // 이름과 나이를 받는 생성자 호출

    user1.introduce();
    user2.introduce();
    user3.introduce();

    return 0;
}
```

## 4. 심화 내용: 오버라이딩과의 차이점

오버로딩은 종종 **오버라이딩(Overriding)**과 혼동되곤 함. 두 개념은 이름은 비슷하지만 완전히 다름.

| 구분        | **오버로딩 (Overloading)**        | **오버라이딩 (Overriding)**          |
| --------- | ----------------------------- | ------------------------------- |
| **정의**    | 같은 이름, 다른 시그니처의 함수를 여러 개 정의   | 부모 클래스의 메서드를 자식 클래스에서 재정의       |
| **관계**    | 같은 클래스 내에서 발생                 | 상속 관계의 부모-자식 클래스 간에 발생          |
| **목적**    | 이름 하나로 다양한 기능 제공 (사용 편의성)     | 부모의 기능을 자식에 맞게 변경 (다형성 구현)      |
| **시그니처**  | **반드시 달라야 함** (매개변수 개수, 타입 등) | **반드시 같아야 함** (이름, 매개변수, 반환 타입) |
| **결정 시점** | **컴파일 타임(Compile-time)**에 결정  | **런타임(Run-time)**에 결정           |
| **별칭**    | 정적 다형성 (Static Polymorphism)  | 동적 다형성 (Dynamic Polymorphism)   |

간단히 말해, **오버로딩은 '새로운 버전 추가'**이고, **오버라이딩은 '기존 버전 덮어쓰기'**라고 생각하면 이해하기 쉬움.

오버로딩에 대해 더 궁금한 점이나 다른 프로그래밍 개념에 대해 알고 싶은 것이 있으신가요?

## References
[[5-💎 영구 노트/오버로딩]]