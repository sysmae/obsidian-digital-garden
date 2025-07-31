---
publish:  true
---

'변수'에 관해 만들어진 이유에서 구조, 사용법 등을 통합한 하나의 핸드북 형식의 요약 만들어줘. 프로그래밍

# 프로그래밍 변수(Variable) 완전 핸드북

## 목차

1. [변수의 정의와 개념](#1-%EB%B3%80%EC%88%98%EC%9D%98-%EC%A0%95%EC%9D%98%EC%99%80-%EA%B0%9C%EB%85%90)
2. [변수가 만들어진 역사적 배경](#2-%EB%B3%80%EC%88%98%EA%B0%80-%EB%A7%8C%EB%93%A4%EC%96%B4%EC%A7%84-%EC%97%AD%EC%82%AC%EC%A0%81-%EB%B0%B0%EA%B2%BD)
3. [변수의 구조와 분류](#3-%EB%B3%80%EC%88%98%EC%9D%98-%EA%B5%AC%EC%A1%B0%EC%99%80-%EB%B6%84%EB%A5%98)
4. [변수의 사용법](#4-%EB%B3%80%EC%88%98%EC%9D%98-%EC%82%AC%EC%9A%A9%EB%B2%95)
5. [메모리 관리와 생명주기](#5-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC%EC%99%80-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0)
6. [변수 사용 시 베스트 프랙티스](#6-%EB%B3%80%EC%88%98-%EC%82%AC%EC%9A%A9-%EC%8B%9C-%EB%B2%A0%EC%8A%A4%ED%8A%B8-%ED%94%84%EB%9E%99%ED%8B%B0%EC%8A%A4)

## 1. 변수의 정의와 개념

### 변수란 무엇인가?

**변수(Variable)**는 컴퓨터 프로그래밍에서 **데이터를 저장하는 명명된 저장 공간**으로, 프로그램 실행 중에 값이 변할 수 있는 추상적인 메모리 위치를 의미합니다[^1][^2]. 변수는 프로그래밍의 가장 기본적인 구성 요소로, 정보를 저장하고 참조하며 조작할 수 있게 해주는 컨테이너 역할을 합니다[^3][^2].

변수는 수학에서의 변수 개로그래밍에서의 변수는 물리적인 저장소와 연결되어 있으며, 수학의 변수처럼 방정식의 일부가 될 필요가 없습니다[^1]. 또한 프로그래밍 변수는 명확성을 위해 긴 이름을 가질 수 있는 반면, 수학 변수는 간결함을 위해 보통 한두 글자로 표현됩니다[^1].

### 변수의 기본 구성 요소

모든 변수는 세 가지 핵심 요소로 구성됩니다[^4][^5]:

1. **이름(Name/Identifier)**: 변수를 식별하는 고유한 이름
2. **타입(Type)**: 저장될 데이터의 종류
3. **값(Value)**: 실제로 저장되는 데이터

변수는 **컴파일러가 심볼릭 이름을 실제 데이터 위치로 변환**하는 방식으로 작동합니다. 변수의 이름과 타입, 위치는 고정되어 있지만, 프로그램 실행 중에 저장된 데이터는 변경될 수 있습니다[^1].

### 변수의 중요성

변수는 프로그래밍에서 다음과 같은 이유로 필수적입니다[^3][^6]:

- **코드 가독성 향상**: 의미있는 변수명을 통해 코드의 목적을 명확히 전달
- **데이터 재사용**: 계산 결과나 입력값을 저장하여 반복 사용
- **프로그램 유연성**: 하드코딩된 값 대신 변수를 사용하여 유지보수성 향상
- **메모리 효율성**: 중복 계산을 피하고 처리 성능 최적화


## 2. 변수가 만들어진 역사적 배경

### 초기 컴퓨팅 시대의 한계

컴퓨터 프로그래밍의 초기 단계에서는 변수라는 개념이 존재하지 않았습니다. **1940년대 ENIAC과 같은 초기 컴퓨터**에서는 프로그래밍이 **물리적 스위치 조작과 전선 재연결**을 통해 이루어졌습니다[^7][^8].

Charles Babbage의 차분기관(1822년)에서는 특정 작업을 수행시키기 위해 기어를 물리적으로 변경해야 했으며, 이는 곧 **물리적 움직임이 최초의 컴퓨터 언어 형태**였다고 볼 수 있습니다[^8]. 이후 전기 신호가 물리적 움직임을 대체했지만, 여전히 각 프로그램마다 스위치를 재설정하고 전체 시스템을 다시 연결해야 하는 번거로운 과정이 필요했습니다[^8].

### FORTRAN의 혁신적 등장

**1954년 IBM의 John Backus가 이끄는 팀**이 개발한 **FORTRAN(FORmula TRANslation)**은 최초의 실용적인 고급 프로그래밍 언어로서 **변수 개념을 본격적으로 도입**했습니다[^9][^7]. FORTRAN은 다음과 같은 혁신을 가져왔습니다[^9]:

- **루프, 변수, 조건문 사용 허용**: 하드웨어 조작에서 추상화된 프로그래밍 제공
- **과학적이고 수학적 계산에 특화**: 엔지니어링 애플리케이션에 최적화
- **숫자 계산과 대규모 공학 애플리케이션** 지원

초기에는 버그와 개발 지연, 그리고 "수동으로 작성된" 어셈블리 프로그램과의 효율성 비교 때문에 회의적인 시각이 있었지만, 하드웨어가 빠르게 발전하면서 FORTRAN은 결국 효율성으로 인정받게 되었습니다[^7].

### 변수 명명 관습의 기원

흥미롭게도, 프로그래밍에서 자주 사용되는 **`i`, `j`, `k` 같은 반복문 변수**의 사용은 FORTRAN에서 시작되었습니다[^10]. FORTRAN에서는 **`i`부터 `n`까지의 문자로 시작하는 변수는 정수형**으로, 다른 모든 변수는 부동소수점으로 기본 설정되었기 때문입니다[^10]. 이는 수학에서 합계 인덱스로 `i`, `j`, `k`를 사용하는 관습에서 유래된 것입니다[^10].

## 3. 변수의 구조와 분류

### 데이터 타입별 분류

변수는 저장하는 데이터의 종류에 따라 다음과 같이 분류됩니다[^5][^11][^12]:

#### 기본 데이터 타입

| 타입 | 설명 | 예시 |
| :-- | :-- | :-- |
| **정수형(Integer)** | 양수 또는 음수의 정수 값 저장 | 17, -5, 0 |
| **실수형(Real/Float)** | 소수점을 포함한 숫자 저장 | 17.65, 3.14, -2.5 |
| **문자형(Character)** | 단일 문자 저장 | 'A', '\$', '1' |
| **문자열(String)** | 문자들의 연속된 배열 | "Hello World", "프로그래밍" |
| **불린(Boolean)** | 참(True) 또는 거짓(False) 값만 저장 | true, false |

#### 복합 데이터 구조

**데이터 구조**는 여러 데이터 타입의 집합으로, 특정 연산을 수행할 수 있는 조직화된 데이터 모음입니다[^13]. 대표적인 예로는 스택, 큐, 연결 리스트, 이진 트리 등이 있습니다[^13].

### 스코프(Scope)별 분류

변수의 **스코프**는 프로그램에서 해당 변수에 접근할 수 있는 범위를 정의합니다[^14][^15].

#### 전역 변수(Global Variables)

**함수 외부에서 선언**되어 프로그램 전체에서 접근 가능한 변수입니다[^14][^16]:

```c
int globalVar = 5; // 전역 변수

void function() {
    printf("%d", globalVar); // 접근 가능
}
```


#### 지역 변수(Local Variables)

**함수 내부에서 선언**되어 해당 함수에서만 접근 가능한 변수입니다[^14][^16]:

```c
void function() {
    int localVar = 10; // 지역 변수
    // localVar는 이 함수에서만 사용 가능
}
```


#### 블록 변수(Block Variables)

**특정 블록(중괄호 {} 내부)에서만 유효**한 변수입니다[^14][^15]:

```c
{
    int blockVar = 15; // 블록 변수
    // blockVar는 이 블록에서만 사용 가능
}
```


### 메모리 할당 방식별 분류

#### 정적 변수(Static Variables)

**컴파일 시간에 메모리가 할당**되며, 프로그램 종료까지 메모리에 유지되는 변수입니다[^17][^18]:

```c
static int count = 0; // 정적 변수
```


#### 자동 변수(Automatic Variables)

**함수 호출 시 스택에 생성**되고, 함수 종료 시 자동으로 소멸되는 변수입니다[^18][^19]:

```c
void function() {
    int autoVar = 5; // 자동 변수
} // 함수 종료 시 autoVar 소멸
```


## 4. 변수의 사용법

### 선언(Declaration)과 초기화(Initialization)

#### 선언과 초기화의 차이점

**선언(Declaration)**은 변수에 이름과 타입을 부여하여 메모리 공간을 예약하는 과정이고, **초기화(Initialization)**는 선언된 변수에 최초 값을 할당하는 과정입니다[^20][^21].

```c
int x;        // 선언만
int y = 10;   // 선언과 동시에 초기화
x = 5;        // 나중에 초기화
```


#### 초기화의 중요성

변수를 초기화하지 않으면 **예측할 수 없는 값(가비지 값)**이 들어있을 수 있어 프로그램 오류의 원인이 됩니다[^22][^23]. 따라서 **모든 변수는 사용하기 전에 적절한 값으로 초기화**해야 합니다[^22][^23].

### 명명 규칙(Naming Conventions)

#### 기본 명명 규칙

변수명은 다음 규칙을 따라야 합니다[^24][^25]:

- **문자 또는 밑줄(_)로 시작**해야 함
- **숫자로 시작할 수 없음**
- **영숫자와 밑줄만 포함** 가능
- **대소문자를 구분**함
- **예약어 사용 불가능**


#### 명명 스타일

| 스타일 | 예시 | 설명 |
| :-- | :-- | :-- |
| **camelCase** | `userName`, `accountBalance` | 첫 단어는 소문자, 이후 단어는 대문자로 시작 |
| **PascalCase** | `UserName`, `AccountBalance` | 모든 단어가 대문자로 시작 |
| **snake_case** | `user_name`, `account_balance` | 단어를 밑줄로 구분 |
| **kebab-case** | `user-name`, `account-balance` | 단어를 하이픈으로 구분 (CSS 등에서 사용) |

#### 좋은 변수명 작성 원칙

**의미있고 명확한 이름 사용**[^26][^27]:

```javascript
// 나쁜 예
let d = new Date();
let u = users.filter(x => x.active);

// 좋은 예  
let currentDate = new Date();
let activeUsers = users.filter(user => user.active);
```

**발음 가능한 이름 사용**[^26][^27]:

```javascript
// 나쁜 예
let yyyymmdstr = moment().format('YYYY/MM/DD');

// 좋은 예
let currentDate = moment().format('YYYY/MM/DD');
```

**검색 가능한 이름 사용**[^27]:

```javascript
// 나쁜 예
setTimeout(blastOff, 86400000);

// 좋은 예
const MILLISECONDS_PER_DAY = 60 * 60 * 24 * 1000;
setTimeout(blastOff, MILLISECONDS_PER_DAY);
```


### 변수 선언 위치와 타이밍

#### 선언 위치 권장사항

**함수나 스크립트 상단에 모든 선언을 배치**하는 것이 좋습니다[^22]:

```javascript
function calculate() {
    // 모든 변수를 상단에 선언
    let firstName, lastName, price, discount, fullPrice;
    
    // 나중에 사용
    firstName = "John";
    lastName = "Doe";
    price = 19.90;
    discount = 0.10;
    fullPrice = price - discount;
}
```

이러한 방식은 다음과 같은 장점을 제공합니다[^22]:

- **더 깔끔한 코드** 작성
- **지역 변수를 찾을 수 있는 단일 위치** 제공
- **원치 않는 전역 변수 생성 방지**
- **의도하지 않은 재선언 가능성 감소**


## 5. 메모리 관리와 생명주기

### 정적 메모리 할당

**정적 메모리 할당**은 **컴파일 시간에 메모리가 할당**되는 방식으로, 컴파일 타임 메모리 할당이라고도 불립니다[^17][^18]. C언어는 프로그램이 컴파일될 때 모든 변수에 대해 자동으로 메모리를 할당합니다[^17].

#### 정적 할당의 특징

```c
int students[^20]; // 20개 정수 배열 - 보통 80바이트 할당
```

이 경우 20명의 학생을 위한 공간이 예약되지만, 실제로 12명만 등록했다면 8명분의 공간이 낭비됩니다[^17]. **배열 크기를 변경할 수 없어** 메모리 낭비가 발생할 수 있지만, 프로그램은 정상적으로 작동합니다[^17].

### 동적 메모리 할당

**동적 메모리 할당**은 **프로그램 시작 후 런타임에 메모리가 할당**되는 방식입니다[^17][^28]. 이 방식은 메모리 사용량을 완전히 제어할 수 있는 장점이 있습니다[^17].

#### 동적 할당의 장점과 활용

```c
int *ptr = malloc(5 * sizeof(int));  // 5개 정수 할당
ptr = realloc(ptr, 8 * sizeof(int)); // 8개로 확장
ptr = realloc(ptr, 5 * sizeof(int)); // 5개로 축소
free(ptr); // 메모리 해제
```

동적 메모리 할당은 다음과 같은 상황에서 유용합니다[^29][^28]:

- **배열의 상한을 모르는 경우**
- **스택에 큰 배열을 할당하고 싶지 않은 경우**
- **실행 중 메모리 크기 조정이 필요한 경우**


### 변수의 생명주기(Lifecycle)

변수의 생명주기는 **생성, 사용, 소멸**의 세 단계로 구성됩니다[^30][^31].

#### 생명주기 단계별 특징

| 단계 | 설명 | 시점 |
| :-- | :-- | :-- |
| **선언/생성** | 변수가 메모리에 생성됨 | 렉시컬 환경 생성 시 |
| **사용** | 변수에 값 할당 및 참조 | 프로그램 실행 중 |
| **소멸** | 변수가 메모리에서 제거됨 | 스코프를 벗어날 때 |

#### 스코프와 생명주기의 관계

```c
void function() {
    int localVar = 10; // 함수 시작 시 생성
    // localVar 사용 가능
} // 함수 종료 시 localVar 소멸

static int staticVar = 0; // 프로그램 시작 시 생성, 종료 시까지 유지
```

**지역 변수**는 함수 실행 기간 동안만 존재하고, **정적 변수**는 프로그램 전체 실행 기간 동안 메모리에 유지됩니다[^32][^31].

### 스코프 관리 원칙

#### 스코프의 중요성

스코프는 다음과 같은 이유로 중요합니다[^14][^15]:

- **변수명 충돌 방지**: 같은 이름의 변수가 다른 영역에서 사용 가능
- **메모리 관리**: 불필요한 변수를 정리하여 메모리 효율성 증대
- **보안성**: 변수 접근을 제한하여 의도하지 않은 수정 방지


#### 스코프 관리 베스트 프랙티스

**필요한 범위에서만 변수 선언**:

```c
{
    int temporaryVar = calculation(); // 블록에서만 필요한 변수
    process(temporaryVar);
} // 블록 종료 시 자동 소멸
```

**전역 변수 사용 최소화**: 전역 변수는 프로그램 전체에서 접근 가능하여 예상치 못한 수정이 발생할 수 있으므로 가능한 한 지역 변수를 사용해야 합니다[^22][^14].

## 6. 변수 사용 시 베스트 프랙티스

### 명명 규칙 베스트 프랙티스

#### 의미있는 이름 사용

변수명은 **그 목적을 명확히 전달**해야 합니다[^26][^33]:

```javascript
// 나쁜 예
let d = new Date();
let arr = [...];
let flag = false;

// 좋은 예
let postData = {...};
let roleNamesList = [...];
let isPostPublished = false;
```


#### 명사나 명사구 사용

변수는 **데이터를 저장하는 컨테이너**이므로 명사나 명사구로 명명해야 합니다[^26]:

```javascript
// 나쁜 예
let publish = false; // 동사 사용

// 좋은 예
let isPublished = false; // 명사구 사용
```


#### 발음 가능한 이름 사용

**코드 리뷰나 토론에서 쉽게 소통**할 수 있도록 발음 가능한 이름을 사용해야 합니다[^26][^33]:

```javascript
// 나쁜 예
let modDateYYMMDD;

// 좋은 예
let modificationTimestamp;
let updatedAt;
```


### 스코프 관리 원칙

#### 전역 변수 사용 최소화

**전역 변수는 다른 스크립트에 의해 덮어쓰일 수 있으므로** 사용을 최소화해야 합니다[^22]:

```javascript
// 나쁜 예 - 전역 변수 과다 사용
var globalCounter = 0;
var globalData = {};

// 좋은 예 - 지역 변수 사용
function processData() {
    let localCounter = 0;
    let localData = {};
    // 처리 로직
}
```


#### 적절한 스코프에서 변수 선언

변수는 **필요한 범위에서만 선언하고 사용**해야 합니다[^30][^15]:

```javascript
// 좋은 예 - 적절한 스코프 사용
function calculateTotal(items) {
    let total = 0; // 함수 스코프
    
    for (let i = 0; i < items.length; i++) { // 루프 스코프
        let item = items[i]; // 블록 스코프
        total += item.price;
    }
    
    return total;
}
```


### 초기화 및 선언 베스트 프랙티스

#### 변수 선언 시 초기화

**변수 선언과 동시에 초기화**하여 예상치 못한 오류를 방지해야 합니다[^22][^23]:

```javascript
// 좋은 예 - 선언과 동시에 초기화
let firstName = "";
let lastName = "";
let price = 0;
let isActive = false;
const itemList = [];
const userConfig = {};
```


#### 상수는 const로 선언

**변경되지 않는 값은 const로 선언**하여 실수로 인한 변경을 방지해야 합니다[^22]:

```javascript
// 좋은 예
const MAX_RETRY_COUNT = 3;
const API_BASE_URL = "https://api.example.com";
const userProfile = {name: "John", age: 30}; // 객체 내용은 변경 가능
```


### 코드 가독성 향상 방법

#### 매직 넘버 방지

**하드코딩된 숫자 대신 명명된 상수를 사용**해야 합니다[^26]:

```javascript
// 나쁜 예
const orbPeriod = Math.round(3.1415 * 6367);

// 좋은 예
const PI = Math.PI;
const EARTH_RADIUS = 6367.447;
const orbPeriod = Math.round(PI * EARTH_RADIUS);
```


#### 일관된 명명 규칙 적용

**프로젝트 전체에서 동일한 명명 규칙을 적용**해야 합니다[^34][^35]:

```javascript
// 일관된 camelCase 사용
let userName = "john";
let userEmail = "john@example.com";
let userAge = 25;

// 일관된 함수 명명
function getUserData() { }
function updateUserProfile() { }
function deleteUserAccount() { }
```


### 성능 최적화 고려사항

#### 적절한 데이터 타입 선택

**저장할 데이터에 맞는 적절한 타입을 선택**하여 메모리 효율성을 높여야 합니다[^36]:

```c
// 적절한 타입 선택
unsigned char age = 25;        // 0-255 범위면 충분
int studentCount = 1000;       // 일반적인 정수
double scientificValue = 3.14159265359; // 높은 정밀도 필요
```


#### 변수 재사용 고려

**동일한 목적의 임시 변수는 재사용**을 고려할 수 있지만, 가독성을 해치지 않는 선에서 적용해야 합니다:

```javascript
function processData(data) {
    let temp = data.filter(item => item.active);
    temp = temp.map(item => item.value);
    temp = temp.reduce((sum, value) => sum + value, 0);
    return temp;
}
```

이러한 베스트 프랙티스를 따르면 **유지보수가 쉽고, 가독성이 높으며, 오류가 적은 코드**를 작성할 수 있습니다. 변수는 프로그래밍의 기초이지만, 올바른 사용법을 숙지하는 것이 전체 코드 품질에 큰 영향을 미칩니다[^3][^37].

## 결론

변수는 프로그래밍의 가장 기본적이면서도 핵심적인 개념입니다. 1940년대 물리적 스위치 조작에서 시작하여 1950년대 FORTRAN을 통해 현대적 형태로 발전한 변수는, 오늘날 모든 프로그래밍 언어의 토대가 되고 있습니다.

변수를 효과적으로 사용하기 위해서는 **적절한 타입 선택, 명확한 명명 규칙 적용, 올바른 스코프 관리, 그리고 메모리 효율성 고려**가 필요합니다. 이러한 원칙들을 따르면 가독성이 높고 유지보수가 용이한 고품질 코드를 작성할 수 있습니다.

변수는 단순히 데이터를 저장하는 공간이 아닙니다. 프로그래머의 의도를 코드로 표현하고, 복잡한 논리를 체계적으로 구성하며, 효율적인 프로그램을 만들어가는 핵심 도구입니다. 따라서 변수에 대한 깊이 있는 이해와 올바른 활용법 습득은 모든 개발자에게 필수적인 역량이라 할 수 있습니다.

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/Variable_(computer_science)

[^2]: https://launchschool.com/books/ruby/read/variables

[^3]: https://bluebirdinternational.com/variables-in-programming/

[^4]: https://www.bbc.co.uk/bitesize/guides/zwmbgk7/revision/2

[^5]: https://www.geeksforgeeks.org/dsa/variables-programming/

[^6]: https://www.linkedin.com/pulse/what-variable-programming-why-so-important-md-al-amin-khan-ybayc

[^7]: https://en.wikipedia.org/wiki/History_of_programming_languages

[^8]: https://cs.brown.edu/~adf/programming_languages.html

[^9]: https://codefinity.com/blog/History-of-Programming-Languages

[^10]: https://retrocomputing.stackexchange.com/questions/1796/where-did-the-popularity-of-the-i-variable-come-from

[^11]: https://www.w3schools.com/r/r_data_types.asp

[^12]: https://www.bbc.co.uk/bitesize/guides/zghbgk7/revision/1

[^13]: https://www.geeksforgeeks.org/difference-between-data-type-and-data-structure/

[^14]: https://www.w3schools.com/programming/prog_scope.php

[^15]: https://www.geeksforgeeks.org/dsa/scope-of-a-variable/

[^16]: https://www.w3schools.com/c/c_scope.php

[^17]: https://www.w3schools.com/c/c_memory_allocate.php

[^18]: https://www.gnu.org/software/libc/manual/html_node/Memory-Allocation-and-C.html

[^19]: https://press.rebus.community/programmingfundamentals/chapter/scope/

[^20]: https://stackoverflow.com/questions/31714434/declaring-vs-initializing-a-variable

[^21]: https://www.baeldung.com/cs/definition-vs-declaration-vs-initialization

[^22]: https://www.w3schools.com/js/js_best_practices.asp

[^23]: https://www.mathworks.com/help/coder/ug/best-practices-for-defining-variables-for-c-c-code-generation.html

[^24]: https://www.w3schools.com/go/go_variable_naming_rules.php

[^25]: https://www.oracle.com/java/technologies/javase/codeconventions-namingconventions.html

[^26]: https://www.linkedin.com/pulse/clean-code-8-best-practices-naming-variable-shahryar-tayeb-znhpf

[^27]: https://dev.to/mohitsinghchauhan/clean-code-101-variable-naming-conventions-part-1-1c1a

[^28]: https://www.geeksforgeeks.org/c/dynamic-memory-allocation-in-c-using-malloc-calloc-free-and-realloc/

[^29]: https://kr.mathworks.com/help/fixedpoint/ug/controlling-memory-allocation-of-bounded-data.html

[^30]: https://www.luisllamas.es/en/programming-scope-of-variables/

[^31]: https://stackoverflow.com/questions/71126446/life-cycle-of-a-variable

[^32]: https://www.oceanlabz.in/scope-and-lifetime-of-variables/

[^33]: https://www.ibm.com/docs/en/rpa/23.0.x?topic=guidelines-names

[^34]: https://curc.readthedocs.io/en/latest/programming/coding-best-practices.html

[^35]: https://en.wikipedia.org/wiki/Naming_convention_(programming)

[^36]: https://ppml.dev/types-structures.html

[^37]: https://www.sololearn.com/en/Discuss/2577595/why-variables-are-important-in-programming

[^38]: https://www.lenovo.com/us/en/glossary/variable/

[^39]: https://dev.to/itsahsanmangal/variables-and-data-types-in-programming-a-beginners-guide-499g

[^40]: https://www.bbc.co.uk/bitesize/guides/zh66pbk/revision/3

[^41]: https://www.savemyexams.com/igcse/computer-science/cie/23/revision-notes/8-programming/programming-concepts/local-and-global-variables/

[^42]: https://www.geeksforgeeks.org/cpp/history-of-c/

[^43]: https://www.reddit.com/r/programming/comments/1n5dz1/why_explaining_a_variable_to_new_and_some_old/

[^44]: https://scratchgarden.com/blog/breaking-down-the-basics-of-computer-variables-for-kids/

[^45]: https://rosettacode.org/wiki/History_variables

[^46]: https://blog.nikfp.com/how-to-think-like-a-programmer-what-are-variables-really

[^47]: https://www.advanced-ict.info/programming/variables.html

[^48]: https://www.reddit.com/r/cpp_questions/comments/14s4vmk/how_dynamic_memory_allocation_on_variable_work/

[^49]: https://www.geeksforgeeks.org/c/scope-rules-in-c/

[^50]: https://learn.microsoft.com/en-us/power-automate/desktop-flows/variable-data-types

[^51]: https://forum.allaboutcircuits.com/threads/variable-memory-allocation-in-c.188839/

[^52]: https://langdev.stackexchange.com/questions/2915/data-structures-for-scopes-and-variable-shadowing

[^53]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Data_structures

[^54]: https://www.theserverside.com/feature/A-guide-to-common-variable-naming-conventions

[^55]: https://www.pluralsight.com/resources/blog/guides/declaring-and-initializing-variables-in-c

[^56]: https://docs.oracle.com/cd/E17984_01/doc.898/e14699/variables_data_structs.htm

[^57]: https://www.freecodecamp.org/news/how-to-write-better-variable-names/

[^58]: https://www.ibm.com/docs/SS6V3G_5.3.1/com.ibm.help.gswapplintug.doc/GSW_Declarations_and_Initialization.html

[^59]: https://www.datacamp.com/tutorial/coding-best-practices-and-guidelines

[^60]: https://www.reddit.com/r/C_Programming/comments/eu8a7p/variable_declaration_definition_and/

[^61]: https://stackoverflow.com/questions/5779561/is-it-always-best-practice-to-declare-a-variable

[^62]: https://dev.to/realrichi3/variable-declaration-and-initialization-50ek

[^63]: https://cs.appstate.edu/hillsma/publications/hills-2015-ase.pdf

[^64]: https://kr.mathworks.com/help/simulink/ug/create-edit-and-manage-workspace-variables.html

[^65]: https://poplogarchive.getpoplog.org/computers-and-thought/chap3/node18.html

[^66]: https://excellencetechnologies.in/blog/javascript-variable-scope-and-lifecycle/

[^67]: https://www.acsmotioncontrol.com/KC/Content/SW/SW_Matlab/Document/Variables_Management_Methods.htm

[^68]: https://www.khanacademy.org/computing/intro-to-python-fundamentals/x5279a44ae0ab15d6:computational-thinking-with-variables/x5279a44ae0ab15d6:variables/a/variable-design-patterns

[^69]: https://dmeg.cessda.eu/Data-Management-Expert-Guide/2.-Organise-Document/Designing-a-data-file-structure/Organisation-of-variables

[^70]: https://www.sciencedirect.com/topics/computer-science/variable-pattern

[^71]: https://en.wikipedia.org/wiki/Object_lifetime

[^72]: https://www.highspring.com/blog/3-essential-steps-for-variable-resource-management/

[^73]: https://stackoverflow.com/questions/60840616/best-design-pattern-to-pass-needed-variable-to-all-code-without

[^74]: https://processing.org/examples/variablescope

[^75]: https://www.vauto.com/learning-center/blog/7-variable-management-strategies-to-help-you-run-better-vauto

[^76]: https://refactoring.guru/design-patterns

[^77]: https://forum.arduino.cc/t/lifecycle-of-a-variable-declaration-initialization-deallocation-within-scopes/221493

[^78]: https://www.linkedin.com/pulse/best-practices-environment-variable-management-bodanapati-xfsac

[^79]: https://softwareengineering.stackexchange.com/questions/456525/what-is-the-design-pattern-of-a-program-that-uses-global-arrays-to-store-variabl

[^80]: http://www.cs.emory.edu/~cheung/Courses/255/Syllabus/C/C.-ver1/C-Advanced/scoping.html

