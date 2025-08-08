---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Jest 핵심 문법 및 시니어 개발자 테스트 설계 전략

## 핵심 문법 완전 정리

### 1. 기본 테스트 구조

```typescript
// 테스트 그룹화
describe('Calculator', () => {
  // 개별 테스트 케이스
  test('should add two numbers', () => {
    expect(add(2, 3)).toBe(5);
  });
  
  // it은 test의 별칭
  it('should subtract correctly', () => {
    expect(subtract(5, 3)).toBe(2);
  });
});
```


### 2. 핵심 Matchers

```typescript
// 동등성 검사
expect(value).toBe(expected);              // Object.is() 사용 (참조 비교)
expect(value).toEqual(expected);           // 깊은 동등성 비교
expect(value).toStrictEqual(expected);     // undefined 필드까지 엄격 비교

// Truthiness
expect(value).toBeTruthy();               // if문에서 true로 평가
expect(value).toBeFalsy();                // if문에서 false로 평가
expect(value).toBeNull();                 // null만 매치
expect(value).toBeUndefined();            // undefined만 매치
expect(value).toBeDefined();              // undefined가 아님

// 숫자
expect(value).toBeGreaterThan(3);         // > 3
expect(value).toBeGreaterThanOrEqual(3);  // >= 3
expect(value).toBeCloseTo(0.3);           // 부동소수점 근사치

// 문자열
expect(string).toMatch(/pattern/);        // 정규식 매치
expect(string).toContain('substring');    // 부분 문자열 포함

// 배열/반복가능객체
expect(array).toContain(item);            // 요소 포함
expect(array).toHaveLength(number);       // 길이 확인

// 객체
expect(object).toHaveProperty('key');     // 프로퍼티 존재
expect(object).toHaveProperty('key', value); // 프로퍼티 값까지 확인

// 예외
expect(() => {
  throw new Error('Something bad');
}).toThrow();                             // 예외 발생 확인
expect(() => {
  throw new Error('Something bad');
}).toThrow('Something bad');              // 특정 메시지 확인

// 비동기
await expect(promise).resolves.toBe(value);   // Promise 성공
await expect(promise).rejects.toThrow();      // Promise 실패
```


### 3. 모킹 시스템

```typescript
// Mock 함수 생성
const mockFn = jest.fn();
const mockFnWithReturn = jest.fn(() => 'mocked value');
const mockFnOnce = jest.fn()
  .mockReturnValueOnce('first call')
  .mockReturnValueOnce('second call');

// Mock 함수 검증
expect(mockFn).toHaveBeenCalled();                    // 호출됨
expect(mockFn).toHaveBeenCalledTimes(2);             // 호출 횟수
expect(mockFn).toHaveBeenCalledWith(arg1, arg2);     // 특정 인자로 호출
expect(mockFn).toHaveBeenLastCalledWith(arg);        // 마지막 호출 인자

// 모듈 모킹
jest.mock('./module', () => ({
  default: jest.fn(),
  namedExport: jest.fn()
}));

// 부분 모킹
jest.mock('./module', () => ({
  ...jest.requireActual('./module'),
  specificFunction: jest.fn()
}));

// 자동 모킹
jest.mock('./module'); // 모든 export를 jest.fn()으로 대체
```


### 4. 생명주기 훅

```typescript
describe('Test Suite', () => {
  // 모든 테스트 전에 한 번
  beforeAll(async () => {
    await setupDatabase();
  });
  
  // 각 테스트 전에
  beforeEach(() => {
    jest.clearAllMocks();
    setupTestData();
  });
  
  // 각 테스트 후에
  afterEach(() => {
    cleanupTestData();
  });
  
  // 모든 테스트 후에 한 번
  afterAll(async () => {
    await teardownDatabase();
  });
});
```


### 5. 고급 테스트 패턴

```typescript
// 매개변수화된 테스트
describe.each([
  [1, 2, 3],
  [2, 3, 5],
  [3, 4, 7]
])('add(%i, %i)', (a, b, expected) => {
  test(`returns ${expected}`, () => {
    expect(add(a, b)).toBe(expected);
  });
});

// 조건부 테스트
test.skipIf(process.env.NODE_ENV === 'production')('dev only test', () => {
  // 개발 환경에서만 실행
});

// 실패 예상 테스트
test.failing('known broken feature', () => {
  expect(brokenFunction()).toBe(expectedValue);
});

// 비동기 테스트
test('async operation', async () => {
  const result = await asyncFunction();
  expect(result).toBe(expected);
});

// 타임아웃과 재시도
jest.setTimeout(10000);                    // 글로벌 타임아웃
jest.retryTimes(3);                       // 실패 시 재시도

test('with custom timeout', async () => {
  // 개별 테스트 타임아웃
}, 15000);
```


## 시니어 개발자 테스트 설계 전략

### 1. 테스트 아키텍처 설계

#### 1.1 테스트 피라미드 적용

```typescript
// 🏗️ 레이어별 테스트 분배 전략
/**
 * E2E Tests (5-10%)     ← 느리지만 중요한 사용자 시나리오
 * Integration (15-25%)  ← 컴포넌트 간 상호작용
 * Unit Tests (70-80%)   ← 빠르고 안정적인 기초
 */

// 🎯 단위 테스트: 순수 함수, 유틸리티, 비즈니스 로직
describe('BusinessLogic', () => {
  test('calculateDiscount applies correct percentage', () => {
    const order = { total: 100, customerType: 'premium' };
    expect(calculateDiscount(order)).toBe(15);
  });
});

// 🔗 통합 테스트: 서비스 레이어, API 호출, 데이터베이스
describe('UserService Integration', () => {
  test('creates user with hashed password', async () => {
    const user = await userService.create('john', 'password123');
    expect(user.password).not.toBe('password123');
    expect(await bcrypt.compare('password123', user.password)).toBe(true);
  });
});

// 🌐 E2E 테스트: 핵심 사용자 플로우만
describe('Critical User Journey', () => {
  test('user can complete purchase flow', async () => {
    await loginAsUser();
    await addItemToCart('premium-product');
    await proceedToCheckout();
    await completePayment();
    await expect(page).toHaveURL(/order-confirmation/);
  });
});
```


#### 1.2 테스트 조직화 패턴

```typescript
// 📁 디렉토리 구조
/*
src/
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx          ← 단위 테스트
│   │   └── Button.integration.test.tsx ← 통합 테스트
├── services/
│   └── __tests__/                   ← 서비스 테스트 집중
├── utils/
│   └── __tests__/                   ← 유틸리티 테스트
└── __tests__/
    ├── e2e/                         ← E2E 테스트
    ├── fixtures/                    ← 테스트 데이터
    └── helpers/                     ← 테스트 유틸리티
*/

// 🏗️ 테스트 빌더 패턴
class UserBuilder {
  private userData: Partial<User> = {};
  
  withEmail(email: string) {
    this.userData.email = email;
    return this;
  }
  
  withRole(role: UserRole) {
    this.userData.role = role;
    return this;
  }
  
  build(): User {
    return {
      id: faker.datatype.uuid(),
      email: faker.internet.email(),
      role: 'user',
      ...this.userData
    };
  }
}

// 사용법
const adminUser = new UserBuilder()
  .withEmail('admin@test.com')
  .withRole('admin')
  .build();
```


### 2. 모킹 전략

#### 2.1 의존성 주입과 모킹

```typescript
// ❌ 피해야 할 패턴: 하드코딩된 의존성
class UserService {
  async createUser(data: UserData) {
    const hashedPassword = await bcrypt.hash(data.password, 10); // 직접 의존
    return this.userRepository.save({ ...data, password: hashedPassword });
  }
}

// ✅ 권장 패턴: 의존성 주입
class UserService {
  constructor(
    private userRepository: UserRepository,
    private passwordHasher: PasswordHasher  // 추상화된 의존성
  ) {}
  
  async createUser(data: UserData) {
    const hashedPassword = await this.passwordHasher.hash(data.password);
    return this.userRepository.save({ ...data, password: hashedPassword });
  }
}

// 테스트에서 쉬운 모킹
describe('UserService', () => {
  let userService: UserService;
  let mockRepository: jest.Mocked<UserRepository>;
  let mockHasher: jest.Mocked<PasswordHasher>;
  
  beforeEach(() => {
    mockRepository = {
      save: jest.fn(),
      findById: jest.fn()
    } as jest.Mocked<UserRepository>;
    
    mockHasher = {
      hash: jest.fn().mockResolvedValue('hashed_password')
    } as jest.Mocked<PasswordHasher>;
    
    userService = new UserService(mockRepository, mockHasher);
  });
});
```


#### 2.2 스마트 모킹 전략

```typescript
// 🎯 레이어별 모킹 가이드라인
describe('Service Layer Testing', () => {
  // ✅ 외부 API는 항상 모킹
  beforeEach(() => {
    nock('https://api.external.com')
      .get('/users/123')
      .reply(200, { id: 123, name: 'John' });
  });
  
  // ✅ 데이터베이스는 통합 테스트에서만 실제 사용
  const mockDb = {
    user: {
      create: jest.fn().mockImplementation(async (data) => ({
        id: faker.datatype.number(),
        ...data
      }))
    }
  };
  
  // ✅ 시간 기반 로직은 가짜 타이머 사용
  test('schedules job correctly', () => {
    jest.useFakeTimers();
    const mockCallback = jest.fn();
    
    scheduleJob(mockCallback, 1000);
    jest.advanceTimersByTime(1000);
    
    expect(mockCallback).toHaveBeenCalledTimes(1);
    jest.useRealTimers();
  });
});
```


### 3. 테스트 품질 관리

#### 3.1 AAA 패턴 엄격 적용

```typescript
// ✅ 명확한 AAA 구조
describe('OrderCalculator', () => {
  test('applies senior discount correctly', () => {
    // Arrange - 테스트 데이터 준비
    const customer = createCustomer({ age: 70, membershipYears: 5 });
    const items = [
      createItem({ price: 100, category: 'electronics' }),
      createItem({ price: 50, category: 'books' })
    ];
    const calculator = new OrderCalculator();
    
    // Act - 테스트할 동작 실행
    const result = calculator.calculateTotal(items, customer);
    
    // Assert - 결과 검증 (여러 어설션도 OK, 같은 동작에 대한 것이라면)
    expect(result.subtotal).toBe(150);
    expect(result.discount).toBe(15); // 10% 시니어 할인
    expect(result.total).toBe(135);
    expect(result.appliedDiscounts).toContain('SENIOR_DISCOUNT');
  });
});
```


#### 3.2 의미 있는 테스트 작성

```typescript
// ❌ 구현 세부사항 테스트
test('calls setState with correct parameters', () => {
  const component = render(<Counter />);
  fireEvent.click(screen.getByText('Increment'));
  // setState 호출을 테스트하는 것은 의미없음
});

// ✅ 사용자 관점에서 테스트
test('increments counter when increment button is clicked', () => {
  render(<Counter initialValue={5} />);
  const incrementButton = screen.getByRole('button', { name: /increment/i });
  
  fireEvent.click(incrementButton);
  
  expect(screen.getByText('6')).toBeInTheDocument();
});

// ✅ 비즈니스 규칙 테스트
test('prevents overdraft when insufficient balance', () => {
  const account = new BankAccount(100);
  
  expect(() => account.withdraw(150)).toThrow('Insufficient balance');
  expect(account.getBalance()).toBe(100); // 잔액 변경 없음 확인
});
```


### 4. 고급 테스트 패턴

#### 4.1 상태 기반 vs 상호작용 기반 테스트

```typescript
// 🎯 상태 기반 테스트 (권장: 리팩토링에 강함)
describe('ShoppingCart', () => {
  test('adds item correctly', () => {
    const cart = new ShoppingCart();
    const item = createItem({ id: '1', price: 10 });
    
    cart.addItem(item, 2);
    
    expect(cart.getTotalItems()).toBe(2);
    expect(cart.getTotalPrice()).toBe(20);
    expect(cart.getItems()).toContainEqual(
      expect.objectContaining({ id: '1', quantity: 2 })
    );
  });
});

// ⚠️ 상호작용 기반 테스트 (필요한 경우에만)
describe('OrderService', () => {
  test('sends confirmation email after successful order', async () => {
    const mockEmailService = jest.mocked(emailService);
    const orderService = new OrderService(mockEmailService);
    
    await orderService.createOrder(validOrderData);
    
    expect(mockEmailService.sendConfirmationEmail).toHaveBeenCalledWith(
      expect.objectContaining({
        to: validOrderData.customerEmail,
        orderNumber: expect.any(String)
      })
    );
  });
});
```


#### 4.2 에러 시나리오 철저 테스트

```typescript
describe('PaymentProcessor', () => {
  // ✅ 다양한 실패 시나리오 커버
  test.each([
    ['invalid card', { cardNumber: '1234', error: 'Invalid card number' }],
    ['expired card', { cardNumber: '4111111111111111', expiryDate: '01/20', error: 'Card expired' }],
    ['insufficient funds', { cardNumber: '4111111111111111', error: 'Insufficient funds' }]
  ])('handles %s gracefully', async (scenario, { error, ...cardData }) => {
    const processor = new PaymentProcessor();
    
    await expect(processor.processPayment({
      amount: 100,
      ...cardData
    })).rejects.toThrow(error);
  });
  
  // ✅ 네트워크 에러 처리
  test('retries on network failure', async () => {
    const mockApiCall = jest.fn()
      .mockRejectedValueOnce(new NetworkError('Connection timeout'))
      .mockRejectedValueOnce(new NetworkError('Connection timeout'))
      .mockResolvedValueOnce({ success: true, transactionId: '123' });
    
    const processor = new PaymentProcessor({ apiCall: mockApiCall, maxRetries: 3 });
    
    const result = await processor.processPayment(validPaymentData);
    
    expect(result.success).toBe(true);
    expect(mockApiCall).toHaveBeenCalledTimes(3);
  });
});
```


### 5. 성능 및 안정성

#### 5.1 테스트 성능 최적화

```typescript
// ✅ 테스트 실행 속도 개선
describe('Large Test Suite', () => {
  // 비용이 큰 설정은 describe 레벨에서
  let expensiveResource: ExpensiveResource;
  
  beforeAll(async () => {
    expensiveResource = await createExpensiveResource();
  });
  
  afterAll(async () => {
    await expensiveResource.cleanup();
  });
  
  // 각 테스트마다 리셋만
  beforeEach(() => {
    expensiveResource.reset();
  });
  
  // 병렬 실행 가능한 테스트 설계
  test.concurrent('independent test 1', async () => {
    const result = await independentOperation1();
    expect(result).toBeDefined();
  });
  
  test.concurrent('independent test 2', async () => {
    const result = await independentOperation2();
    expect(result).toBeDefined();
  });
});
```


#### 5.2 플레이키 테스트 방지

```typescript
// ❌ 플레이키 테스트 패턴들
test('flaky test', async () => {
  setTimeout(() => expect(something).toBe(true), 100); // 타이밍 의존
  expect(Math.random() > 0.5).toBe(true); // 랜덤 결과
  expect(new Date().getTime()).toBeGreaterThan(startTime); // 시간 의존
});

// ✅ 안정적인 테스트 패턴
test('stable test', async () => {
  // 명확한 대기 조건
  await waitFor(() => {
    expect(screen.getByText('Loaded')).toBeInTheDocument();
  });
  
  // 가짜 타이머 사용
  jest.useFakeTimers();
  const callback = jest.fn();
  scheduleCallback(callback, 1000);
  jest.advanceTimersByTime(1000);
  expect(callback).toHaveBeenCalled();
  jest.useRealTimers();
  
  // 고정된 시드 사용
  const seededRandom = new SeededRandom('test-seed');
  const result = generateWithRandom(seededRandom);
  expect(result).toBe(expectedValue);
});
```


## 안티패턴 및 베스트 프랙티스

### 피해야 할 안티패턴[^1][^2]

```typescript
// ❌ 1. 과도한 모킹
describe('OverMockedTest', () => {
  test('everything is mocked', () => {
    const mockA = jest.fn();
    const mockB = jest.fn();
    const mockC = jest.fn();
    // 실제 비즈니스 로직은 테스트되지 않음
  });
});

// ❌ 2. 테스트에서 로직 사용
test('conditional assertions', () => {
  const result = calculate(input);
  if (result > 0) {
    expect(result).toBeGreaterThan(0);
  } else {
    expect(result).toBeLessThanOrEqual(0);
  }
});

// ❌ 3. 구현 세부사항 의존
test('tests implementation details', () => {
  const spy = jest.spyOn(component, 'privateMethod');
  component.publicMethod();
  expect(spy).toHaveBeenCalled();
});
```


### 권장 베스트 프랙티스[^3][^4]

```typescript
// ✅ 1. 명확한 테스트 이름
describe('UserRegistration', () => {
  describe('when user provides valid data', () => {
    test('creates user account and sends welcome email', () => {
      // 테스트 구현
    });
  });
  
  describe('when email is already taken', () => {
    test('returns validation error without creating duplicate', () => {
      // 테스트 구현
    });
  });
});

// ✅ 2. 테스트 격리
describe('IsolatedTests', () => {
  beforeEach(() => {
    jest.clearAllMocks();
    resetTestDatabase();
  });
  
  test('test 1 does not affect test 2', () => {
    // 독립적인 테스트
  });
});

// ✅ 3. 적절한 추상화
const createTestUser = (overrides = {}) => ({
  id: faker.datatype.uuid(),
  email: faker.internet.email(),
  name: faker.name.fullName(),
  ...overrides
});

test('user can update profile', () => {
  const user = createTestUser({ name: 'Original Name' });
  const result = updateUserProfile(user, { name: 'New Name' });
  expect(result.name).toBe('New Name');
});
```


## 결론

시니어 개발자의 테스트 설계는 **단순히 코드를 검증하는 것을 넘어서 시스템의 안정성과 유지보수성을 보장하는 아키텍처**를 구축하는 것이다.

**핵심 원칙**:

1. **테스트 피라미드** 준수로 비용 효율적인 테스트 전략 수립
2. **의존성 주입**과 **스마트 모킹**으로 테스트 가능한 코드 설계
3. **AAA 패턴** 및 **사용자 관점** 테스트로 의미 있는 검증
4. **에러 시나리오**와 **엣지 케이스** 철저 커버리지
5. **플레이키 테스트 방지**와 **성능 최적화**로 안정적인 테스트 스위트 구축

이러한 전략을 통해 개발팀은 **빠른 피드백**, **높은 코드 신뢰성**, **안전한 리팩토링**을 실현할 수 있으며, 궁극적으로 **품질 높은 소프트웨어 제품**을 지속적으로 배포할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://dzone.com/articles/testing-the-untestable-and-other-anti-patterns

[^2]: https://builders.travelperk.com/recipes-to-write-better-jest-tests-with-the-react-testing-library-part-1-670aaf3451d1

[^3]: https://www.linkedin.com/pulse/best-practices-testing-jest-part-1-lucas-mendonça-5bref

[^4]: https://geekyants.com/blog/writing-effective-unit-tests-best-practices

[^5]: https://archive.jestjs.io/docs/en/api

[^6]: https://dev.to/dvddpl/jest-testing-like-a-pro-tips-and-tricks-4o6f

[^7]: https://moldstud.com/articles/p-mastering-jest-matchers-advanced-techniques-for-assertion-testing

[^8]: https://jestjs.io/docs/api

[^9]: https://sylhare.github.io/2022/09/21/Jest-testing.html

[^10]: https://kettanaito.com/blog/practical-guide-to-custom-jest-matchers

[^11]: https://jestjs.io/docs/configuration

[^12]: https://developer.salesforce.com/docs/platform/lwc/guide/unit-testing-using-jest-patterns.html

[^13]: https://jestjs.io/docs/using-matchers

[^14]: https://jestjs.io

[^15]: https://www.tutorialspoint.com/jest/jest-advanced-testing.htm

[^16]: https://jestjs.io/ja/docs/29.5/using-matchers

[^17]: https://jestjs.io/docs/getting-started

[^18]: https://testomat.io/blog/tutorial-on-how-to-organize-an-advanced-jest-testing-framework/

[^19]: https://jestjs.io/docs/expect

[^20]: https://github.com/jestjs/jest/blob/master/packages/jest-core/package.json

[^21]: https://gamliela.com/blog/advanced-testing-with-jest

[^22]: https://www.browserstack.com/guide/jest-using-matchers

[^23]: https://qiita.com/YSasago/items/6f23055c971a091f9993

[^24]: https://8thlight.com/insights/advanced-unit-testing-in-react-with-jest-hooks-and-promises

[^25]: https://moldstud.com/articles/p-jest-testing-in-a-microservices-architecture-best-practices-and-tools

[^26]: https://moldstud.com/articles/p-top-10-essential-jest-testing-tips-for-every-qa-tester

[^27]: https://www.reddit.com/r/typescript/comments/16hzm8m/tricks_patterns_for_testing_with_jest/

[^28]: https://kinsta.com/blog/jest/

[^29]: https://devblogs.microsoft.com/ise/jest-mocking-best-practices/

[^30]: https://www.startearly.ai/post/a-developers-tutorial-to-jest-unit-testing

[^31]: https://zenn.dev/y_hsgw/articles/3ad9287863cc1a

[^32]: https://www.index.dev/interview-questions/jest

[^33]: https://rtbell.dev/blog/testing/jest-a-minute-a-better-way-to-structure-your-unit-tests

[^34]: https://articles.readytowork.jp/beginners-guide-to-jest-javascript-testing-8b9b5d2de547

[^35]: https://stackoverflow.com/questions/75377118/testing-mediator-design-pattern-with-jest

[^36]: https://www.deviqa.com/frameworks/jest-automation-testing-services/

[^37]: https://aqua-cloud.io/software-testing-pyramid/

[^38]: https://www.linkedin.com/pulse/elevating-backend-reliability-how-we-integrated-jest-testing-dayama-vxryf

[^39]: https://dev.to/craftedwithintent/understanding-the-testing-pyramid-and-testing-trophy-tools-strategies-and-challenges-k1j

[^40]: https://itnext.io/unveiling-6-anti-patterns-in-react-test-code-pitfalls-to-avoid-fd7e5a3a7360

[^41]: https://www.lambdatest.com/jest

[^42]: https://ducxinh.com/techblog/testing-pyramid:-a-comprehensive-guide-to-software-testing-strategy

[^43]: https://www.reddit.com/r/node/comments/dxow5i/what_are_some_of_the_most_common_antipatterns_you/

[^44]: https://circleci.com/blog/testing-pyramid/

[^45]: https://kentcdodds.com/blog/avoid-nesting-when-youre-testing

[^46]: https://github.com/julioneljunior/study-jest-tdd-clean-arch

[^47]: https://codilime.com/blog/why-you-should-use-the-testing-pyramid-in-test-automation/

[^48]: https://blog.bitsrc.io/what-not-to-assert-in-react-component-tests-66ab9ee79176

[^49]: https://jestjs.io/docs/architecture

[^50]: https://testomat.io/blog/testing-pyramid-role-in-modern-software-testing-strategies/

[^51]: https://www.testdevlab.com/blog/5-test-automation-anti-patterns-and-how-to-avoid-them

[^52]: https://frontendmasters.com/courses/enterprise-typescript/setup-jest-testing/

