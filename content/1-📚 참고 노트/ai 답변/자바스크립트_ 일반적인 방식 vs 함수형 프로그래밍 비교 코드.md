---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 자바스크립트: 일반적인 방식 vs 함수형 프로그래밍 비교 코드

## 1. 배열 처리: 데이터 변환

### 일반적인 방식 (명령형)

```javascript
// 숫자 배열을 2배로 만들기
const numbers = [1, 2, 3, 4, 5];
const doubled = [];

for (let i = 0; i < numbers.length; i++) {
    doubled.push(numbers[i] * 2);
}

console.log(doubled); // [2, 4, 6, 8, 10]
```


### 함수형 방식 (선언형)

```javascript
// 숫자 배열을 2배로 만들기
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(x => x * 2);

console.log(doubled); // [2, 4, 6, 8, 10]
```

**차이점**[^1][^2]:

- **명령형**: "어떻게" 할지 단계별로 명시 (반복, 인덱스 관리, push)
- **함수형**: "무엇을" 할지 선언 (`map`으로 변환 의도 명확)


## 2. 배열 필터링: 조건부 선택

### 일반적인 방식

```javascript
// 짝수만 선택하기
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const evens = [];

for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] % 2 === 0) {
        evens.push(numbers[i]);
    }
}

console.log(evens); // [2, 4, 6, 8, 10]
```


### 함수형 방식

```javascript
// 짝수만 선택하기
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const evens = numbers.filter(x => x % 2 === 0);

console.log(evens); // [2, 4, 6, 8, 10]
```

**차이점**[^3][^4]:

- **명령형**: 루프와 조건문으로 단계별 구현
- **함수형**: `filter`로 의도를 직접적으로 표현


## 3. 배열 집계: 합계 계산

### 일반적인 방식

```javascript
// 배열의 모든 수 더하기
const numbers = [1, 2, 3, 4, 5];
let sum = 0;

for (let i = 0; i < numbers.length; i++) {
    sum += numbers[i];
}

console.log(sum); // 15
```


### 함수형 방식

```javascript
// 배열의 모든 수 더하기
const numbers = [1, 2, 3, 4, 5];
const sum = numbers.reduce((acc, curr) => acc + curr, 0);

console.log(sum); // 15
```

**차이점**[^2][^4]:

- **명령형**: 가변 변수(`sum`)를 직접 조작
- **함수형**: `reduce`로 축약 로직을 표현, 초기값 명시


## 4. 복합 데이터 처리: 체이닝

### 일반적인 방식

```javascript
// 짝수를 찾아서 2배로 만든 후 합계 구하기
const numbers = [1, 2, 3, 4, 5, 6];
const evens = [];
let sum = 0;

// 1단계: 짝수 필터링
for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] % 2 === 0) {
        evens.push(numbers[i]);
    }
}

// 2단계: 2배로 만들기
const doubled = [];
for (let i = 0; i < evens.length; i++) {
    doubled.push(evens[i] * 2);
}

// 3단계: 합계 구하기
for (let i = 0; i < doubled.length; i++) {
    sum += doubled[i];
}

console.log(sum); // 24 (2*2 + 4*2 + 6*2)
```


### 함수형 방식

```javascript
// 짝수를 찾아서 2배로 만든 후 합계 구하기
const numbers = [1, 2, 3, 4, 5, 6];
const sum = numbers
    .filter(x => x % 2 === 0)  // 짝수 선택
    .map(x => x * 2)           // 2배로 만들기
    .reduce((acc, curr) => acc + curr, 0); // 합계

console.log(sum); // 24
```

**차이점**[^5][^3]:

- **명령형**: 단계별로 중간 배열 생성, 3개의 반복문
- **함수형**: 메서드 체이닝으로 파이프라인 구성


## 5. 객체 데이터 처리

### 일반적인 방식

```javascript
// 사용자 목록에서 성인만 선택해서 이름 추출
const users = [
    { name: 'Alice', age: 25 },
    { name: 'Bob', age: 17 },
    { name: 'Charlie', age: 30 },
    { name: 'David', age: 16 }
];

const adultNames = [];
for (let i = 0; i < users.length; i++) {
    if (users[i].age >= 18) {
        adultNames.push(users[i].name);
    }
}

console.log(adultNames); // ['Alice', 'Charlie']
```


### 함수형 방식

```javascript
// 사용자 목록에서 성인만 선택해서 이름 추출
const users = [
    { name: 'Alice', age: 25 },
    { name: 'Bob', age: 17 },
    { name: 'Charlie', age: 30 },
    { name: 'David', age: 16 }
];

const adultNames = users
    .filter(user => user.age >= 18)
    .map(user => user.name);

console.log(adultNames); // ['Alice', 'Charlie']
```


## 6. 상태 변경: 가변성 vs 불변성

### 일반적인 방식 (가변 데이터)

```javascript
// 사용자 정보 업데이트 (원본 수정)
let user = { name: 'John', age: 25, city: 'Seoul' };

function updateUserAge(user, newAge) {
    user.age = newAge; // 원본 객체 직접 수정
    return user;
}

function updateUserCity(user, newCity) {
    user.city = newCity; // 원본 객체 직접 수정
    return user;
}

console.log('원본:', user);
updateUserAge(user, 26);
console.log('나이 변경 후:', user); // 원본이 변경됨
updateUserCity(user, 'Busan');
console.log('도시 변경 후:', user); // 원본이 또 변경됨
```


### 함수형 방식 (불변 데이터)

```javascript
// 사용자 정보 업데이트 (새 객체 생성)
const user = { name: 'John', age: 25, city: 'Seoul' };

const updateUserAge = (user, newAge) => ({
    ...user,
    age: newAge // 새 객체 생성
});

const updateUserCity = (user, newCity) => ({
    ...user,
    city: newCity // 새 객체 생성
});

console.log('원본:', user);
const userWithNewAge = updateUserAge(user, 26);
console.log('나이 변경 후 새 객체:', userWithNewAge);
console.log('원본 유지:', user); // 원본 그대로

const userWithNewCity = updateUserCity(userWithNewAge, 'Busan');
console.log('최종:', userWithNewCity);
```

**차이점**[^6][^7][^8]:

- **명령형**: 원본 데이터 직접 수정 (가변성)
- **함수형**: 스프레드 연산자로 새 객체 생성 (불변성)


## 7. 부작용(Side Effects) 처리

### 일반적인 방식 (부작용 있음)

```javascript
// 전역 변수에 의존하는 비순수 함수
let counter = 0;

function processItems(items) {
    const results = [];
    
    for (let i = 0; i < items.length; i++) {
        counter++; // 외부 상태 변경 (부작용)
        console.log(`처리 중: ${items[i]}`); // 로그 출력 (부작용)
        results.push(items[i].toUpperCase());
    }
    
    return results;
}

const items = ['apple', 'banana', 'cherry'];
console.log(processItems(items));
console.log('처리된 항목 수:', counter); // 전역 상태에 의존
```


### 함수형 방식 (순수 함수)

```javascript
// 순수 함수로 부작용 분리
const processItems = (items) => 
    items.map(item => item.toUpperCase());

const logProgress = (item, index) => 
    console.log(`처리 중 ${index + 1}: ${item}`);

const countItems = (items) => items.length;

// 사용
const items = ['apple', 'banana', 'cherry'];

// 부작용이 있는 작업은 별도로 처리
items.forEach(logProgress);

// 순수 함수로 데이터 변환
const results = processItems(items);
console.log(results);

// 순수 함수로 카운트
const count = countItems(items);
console.log('처리된 항목 수:', count);
```

**차이점**[^9][^10][^11]:

- **명령형**: 함수 내에서 외부 상태 변경과 로그 출력
- **함수형**: 순수 함수와 부작용을 분리


## 8. 조건부 로직 처리

### 일반적인 방식

```javascript
// 사용자 등급별 할인율 적용
const users = [
    { name: 'Alice', type: 'premium', purchaseAmount: 1000 },
    { name: 'Bob', type: 'regular', purchaseAmount: 500 },
    { name: 'Charlie', type: 'premium', purchaseAmount: 1500 }
];

const processedUsers = [];

for (let i = 0; i < users.length; i++) {
    let discount = 0;
    let finalAmount = users[i].purchaseAmount;
    
    if (users[i].type === 'premium') {
        discount = 0.2; // 20% 할인
    } else if (users[i].type === 'regular') {
        discount = 0.1; // 10% 할인
    }
    
    finalAmount = users[i].purchaseAmount * (1 - discount);
    
    processedUsers.push({
        ...users[i],
        discount: discount,
        finalAmount: finalAmount
    });
}

console.log(processedUsers);
```


### 함수형 방식

```javascript
// 사용자 등급별 할인율 적용
const users = [
    { name: 'Alice', type: 'premium', purchaseAmount: 1000 },
    { name: 'Bob', type: 'regular', purchaseAmount: 500 },
    { name: 'Charlie', type: 'premium', purchaseAmount: 1500 }
];

const getDiscount = (userType) => {
    const discounts = {
        premium: 0.2,
        regular: 0.1,
        default: 0
    };
    return discounts[userType] || discounts.default;
};

const applyDiscount = (user) => {
    const discount = getDiscount(user.type);
    const finalAmount = user.purchaseAmount * (1 - discount);
    
    return {
        ...user,
        discount,
        finalAmount
    };
};

const processedUsers = users.map(applyDiscount);
console.log(processedUsers);
```


## 9. 에러 처리

### 일반적인 방식

```javascript
// 나이 검증 및 처리
function processUsers(users) {
    const adults = [];
    const errors = [];
    
    for (let i = 0; i < users.length; i++) {
        try {
            if (typeof users[i].age !== 'number' || users[i].age < 0) {
                throw new Error(`Invalid age for ${users[i].name}`);
            }
            
            if (users[i].age >= 18) {
                adults.push({
                    ...users[i],
                    status: 'adult'
                });
            }
        } catch (error) {
            errors.push(error.message);
        }
    }
    
    return { adults, errors };
}
```


### 함수형 방식

```javascript
// 나이 검증 및 처리
const isValidAge = (age) => 
    typeof age === 'number' && age >= 0;

const isAdult = (age) => age >= 18;

const validateUser = (user) => 
    isValidAge(user.age) 
        ? { success: true, data: user }
        : { success: false, error: `Invalid age for ${user.name}` };

const processAdult = (user) => 
    isAdult(user.age) 
        ? { ...user, status: 'adult' }
        : null;

const processUsers = (users) => {
    const validations = users.map(validateUser);
    
    const errors = validations
        .filter(v => !v.success)
        .map(v => v.error);
    
    const adults = validations
        .filter(v => v.success)
        .map(v => processAdult(v.data))
        .filter(Boolean);
    
    return { adults, errors };
};
```


## 10. 성능 비교

### 성능 테스트 예제

```javascript
// 성능 테스트용 큰 배열
const largeArray = Array.from({ length: 1000000 }, (_, i) => i);

// 명령형 방식 성능 측정
console.time('명령형');
const imperativeResult = [];
for (let i = 0; i < largeArray.length; i++) {
    if (largeArray[i] % 2 === 0) {
        imperativeResult.push(largeArray[i] * 2);
    }
}
console.timeEnd('명령형');

// 함수형 방식 성능 측정
console.time('함수형');
const functionalResult = largeArray
    .filter(x => x % 2 === 0)
    .map(x => x * 2);
console.timeEnd('함수형');

console.log('결과 길이 동일:', imperativeResult.length === functionalResult.length);
```

**성능 특성**[^1][^12]:

- **명령형**: 일반적으로 더 빠름 (단일 루프, 메서드 호출 오버헤드 없음)
- **함수형**: 가독성이 좋지만 중간 배열 생성으로 메모리 사용량 증가


## 결론

| 측면 | 일반적인 방식 (명령형) | 함수형 방식 |
| :-- | :-- | :-- |
| **가독성** | 단계별 구현으로 복잡함 | 의도가 명확하고 선언적 |
| **재사용성** | 특정 상황에 특화됨 | 작은 함수 조합으로 재사용 용이 |
| **디버깅** | 중간 상태 확인 가능 | 순수 함수로 예측 가능 |
| **성능** | 일반적으로 더 빠름 | 중간 배열 생성으로 오버헤드 |
| **메모리** | 효율적 | 불변성으로 메모리 사용량 증가 |
| **테스트** | 상태 의존성으로 복잡 | 격리된 순수 함수로 간단 |

**실무 권장사항**:

- **성능이 중요한 대용량 데이터**: 명령형 방식
- **비즈니스 로직, 데이터 변환**: 함수형 방식
- **복잡한 상태 관리**: 적절한 혼합 사용

두 방식 모두 상황에 따른 장단점이 있으므로, **문제의 특성과 요구사항에 맞는 선택**이 중요합니다.

<div style="text-align: center">⁂</div>

[^1]: https://www.manuelsanchezdev.com/blog/functional-vs-imperative-javascript-performance

[^2]: https://flaviocopes.com/javascript-loops-map-filter-reduce-find/

[^3]: https://tgdwyer.github.io/functionaljavascript/

[^4]: https://dev.to/joelnet/map-filter-reduce-vs-for-loops-syntax-2k5l

[^5]: https://codeburst.io/examples-in-javascript-functional-programming-part-1-c9e2df8a411a

[^6]: https://www.linkedin.com/pulse/javascript-mutable-immutable-bharath-kumar-murugan

[^7]: https://corner.buka.sh/understanding-mutable-and-immutable-data-in-javascript-a-beginners-guide/

[^8]: https://www.freecodecamp.org/news/mutability-vs-immutability-in-javascript/

[^9]: https://jrsinclair.com/articles/2018/how-to-deal-with-dirty-side-effects-in-your-pure-functional-javascript/

[^10]: https://www.freecodecamp.org/news/pure-function-vs-impure-function/

[^11]: https://dev.to/vonheikemen/dealing-with-side-effects-and-pure-functions-in-javascript-16mg

[^12]: https://www.reddit.com/r/javascript/comments/4nvcd5/performancewise_is_the_for_loop_better_than_map/

[^13]: https://www.nerdfish.be/blog/2023/04/javascript-fundamentals-functional-vs-object-oriented-programming

[^14]: https://learn.microsoft.com/en-us/dotnet/standard/linq/functional-vs-imperative-programming

[^15]: https://engx.space/global/en/blog/javascript-functional-programming-vs-oop

[^16]: https://dev.to/miku86/example-imperative-vs-functional-1n15

[^17]: https://www.toptal.com/javascript/functional-programming-javascript

[^18]: https://careerfoundry.com/en/blog/web-development/functional-programming-vs-oop/

[^19]: https://bytebytego.com/guides/imperative-vs-functional-vs-object-oriented-programming/

[^20]: https://www.reddit.com/r/functionalprogramming/comments/u43nd0/real_world_examples_of_functional_javascript/

[^21]: https://stackoverflow.com/questions/37231841/beginner-javascript-oop-vs-functional

[^22]: https://stackoverflow.com/questions/68789697/functional-programming-declarative-vs-imperative

[^23]: https://hackernoon.com/functional-programming-with-javascript-a-deep-dive

[^24]: https://www.reddit.com/r/javascript/comments/sssf2r/askjs_object_oriented_or_functional_which_one_you/

[^25]: https://www.reddit.com/r/functionalprogramming/comments/o5wp51/imperative_vs_declarative_programming_in/

[^26]: https://www.freecodecamp.org/news/functional-programming-in-javascript/

[^27]: https://javascript.plainenglish.io/functional-vs-object-oriented-programming-in-javascript-fb5fbf15a35d

[^28]: https://www.geeksforgeeks.org/theory-of-computation/difference-between-functional-and-imperative-programming/

[^29]: https://dev.to/mpodlasin/functional-programming-in-js-part-i-composition-currying-lodash-and-ramda-1ohb

[^30]: https://www.skoumal.com/en/performance-of-built-in-higher-order-functions-map-filter-reduce-and-flatmap-vs-for-in-loop-in-swift/

[^31]: https://velog.io/@onezerokang/ES6-map-filter-reduce-메서드-사용법

[^32]: https://www.greatfrontend.com/questions/quiz/explain-the-difference-between-mutable-and-immutable-objects

[^33]: https://rondeveloper.tistory.com/31

[^34]: https://dev.to/muthuraja_r/understanding-mutable-vs-immutable-data-in-react-and-their-impact-on-rendering-ldc

[^35]: https://javascript.plainenglish.io/pure-functions-and-side-effects-in-javascript-common-interview-questions-7e9db2487b91

[^36]: https://javascript.plainenglish.io/javascript-mutable-vs-immutable-1efb662d78c8

[^37]: https://www.syncfusion.com/blogs/post/pure-impure-functions-javascript

[^38]: https://bangj.tistory.com/90

[^39]: https://blog.devgenius.io/mutable-and-immutable-in-javascript-78a3cbc6187c

[^40]: https://blog.greenroots.info/what-are-pure-functions-and-side-effects-in-javascript

