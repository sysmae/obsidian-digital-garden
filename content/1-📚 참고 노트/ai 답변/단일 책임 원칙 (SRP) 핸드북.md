---
publish: true
---

단일 책임 원칙 이라는 주제에 관해 만들어진 이유에서 구조, 사용법 등을 통합한 하나의 핸드북 형식의 요약 만들어줘. 초보~중급. nextjs

# 단일 책임 원칙 (SRP) 핸드북

## Next.js와 TypeScript를 활용한 실무 적용 가이드

## 1. 단일 책임 원칙의 이해

### 1.1 SRP란 무엇인가?

**단일 책임 원칙(Single Responsibility Principle, SRP)**은 SOLID 원칙의 첫 번째 원칙으로, "하나의 모듈은 오직 하나의 액터에 대해서만 책임져야 한다"는 개념입니다[^1][^2]. 더 쉽게 표현하면, **하나의 클래스나 모듈은 변경될 이유가 단 하나여야 한다**는 의미입니다[^3][^4].

SRP는 단순히 "하나의 함수는 하나의 일만 해야 한다"는 의미로 오해받기 쉽지만, 실제로는 **비즈니스 관점에서의 책임 분리**를 다루는 더 고차원적인 원칙입니다[^5][^6].

### 1.2 SRP가 만들어진 배경과 역사

로버트 C. 마틴(Uncle Bob)은 1980년대 후반 유즈넷에서 소프트웨어 설론을 통해 이 원칙들을 모으기 시작했습니다[^7][^8]. SOLID 원칙의 역사는 다음과 같습니다:

- **1980년대 후반**: 로버트 마틴이 소프트웨어 설계 원칙 논의 시작
- **2000년**: "Design Principles and Design Patterns" 논문에서 SRP 소개[^9][^10]
- **2004년**: 마이클 페더스(Michael Feathers)가 SOLID라는 약어 제안[^7][^8]


### 1.3 로버트 마틴의 SOLID 원칙과 SRP

SOLID 원칙은 다음 5가지로 구성됩니다[^11][^12]:

- **S (SRP)**: 단일 책임 원칙 - 한 클래스는 하나의 책임만 가져야 한다
- **O (OCP)**: 개방-폐쇄 원칙 - 확장에는 열려있고 변경에는 닫혀있어야 한다
- **L (LSP)**: 리스코프 치환 원칙 - 상위 타입의 객체를 하위 타입으로 바꿔도 동작해야 한다
- **I (ISP)**: 인터페이스 분리 원칙 - 클라이언트는 사용하지 않는 인터페이스에 의존하면 안 된다
- **D (DIP)**: 의존성 역전 원칙 - 추상화에 의존하고 구체화에 의존하면 안 된다

SRP는 이 중에서도 가장 기본이 되는 원칙으로, 나머지 원칙들의 토대가 됩니다[^13][^14].

### 1.4 왜 SRP가 중요한가?

SRP가 중요한 이유는 다음과 같습니다[^15][^16]:

**1. 단순성 (Simplicity)**

- 하나의 목적만 가진 컴포넌트는 복합적인 기능을 가진 컴포넌트보다 훨씬 단순합니다

**2. 재사용성 (Reusability)**

- 명확하고 집중된 목적을 가진 간단한 컴포넌트는 복잡한 시스템의 다른 부분에서 올바르게 재사용하기 쉽습니다

**3. 테스트 가능성 (Testability)**

- 하나의 명확한 목적을 가진 컴포넌트는 다양한 책임을 가진 컴포넌트보다 테스트를 작성하기 훨씬 쉽습니다

**4. 유연성 (Flexibility)**

- 명확한 통합 지점을 가진 작고 간단한 컴포넌트는 의도하지 않은 결과의 가능성이 낮아 변경하기 더 쉽습니다


## 2. SRP의 핵심 구조와 개념

### 2.1 책임(Responsibility)의 정의

SRP에서 말하는 **책임(Responsibility)**은 "변경을 위한 이유"로 정의됩니다[^17][^1]. 즉, 클래스나 모듈이 변경되어야 하는 이유가 책임입니다.

```typescript
// ❌ 잘못된 예 - 여러 책임을 가진 클래스
class Invoice {
  generateInvoice() {
    // 인보이스 생성 로직
  }
  
  saveToDatabase() {
    // 데이터베이스 저장 로직  
  }
  
  sendEmail() {
    // 이메일 전송 로직
  }
}
```

위 예제에서 `Invoice` 클래스는 세 가지 책임을 가집니다:

1. 인보이스 생성
2. 데이터베이스 저장
3. 이메일 전송

각각은 서로 다른 이유로 변경될 수 있으므로 SRP를 위반합니다[^18][^19].

### 2.2 액터(Actor) 중심의 사고

로버트 마틴의 클린 아키텍처에서는 SRP를 **"하나의 모듈은 오직 하나의 액터에 대해서만 책임져야 한다"**고 정의합니다[^1][^10].

**액터(Actor)**는 시스템과 상호작용하는 시스템 외부의 존재로, 시스템의 이해관계자를 의미합니다[^1]. 예를 들어:

- 사용자 (최종 사용자)
- 외부 시스템 (결제 시스템, API 등)
- 비즈니스 담당자 (회계팀, 인사팀 등)


### 2.3 변경의 이유(Reason to Change)

SRP의 핵심은 **변경에 대한 책임**입니다[^1]. 소프트웨어는 지속적으로 변경되며, 각 변경은 특정 액터의 요구사항에 의해 발생합니다.

단일 객체가 단일 액터에 대해서만 관심사를 가지고 대응하도록 설계해야 하는 이유는:

- 변경에 대한 비용을 낮추기 위함
- 모듈의 응집도를 높이기 위함
- 예상치 못한 부작용을 방지하기 위함


### 2.4 응집도와 결합도의 관계

SRP는 **높은 응집도(High Cohesion)**와 **낮은 결합도(Low Coupling)**를 추구합니다[^20][^5]:

**응집도**: 모듈 내부의 기능적 연관도
**결합도**: 모듈과 모듈 간의 상호 결합 정도

```typescript
// ✅ 높은 응집도를 가진 예
class UserRepository {
  findById(id: string): User { }
  save(user: User): void { }
  delete(id: string): void { }
}

// ✅ 낮은 결합도를 위한 인터페이스 사용
interface EmailService {
  send(to: string, subject: string, body: string): void;
}

class UserService {
  constructor(
    private userRepository: UserRepository,
    private emailService: EmailService
  ) {}
}
```


## 3. SRP 위반의 징후와 문제점

### 3.1 SRP 위반 사례 분석

다음은 실무에서 자주 볼 수 있는 SRP 위반 사례입니다[^21][^19]:

```typescript
// ❌ SRP 위반 사례
class Employee {
  calculatePay(): number {
    // 회계팀에서 사용하는 급여 계산 로직
  }
  
  reportHours(): string {
    // 인사팀에서 사용하는 근무시간 보고 로직  
  }
  
  save(): void {
    // 데이터베이스 관리자가 관리하는 저장 로직
  }
}
```

이 클래스는 세 개의 서로 다른 액터(회계팀, 인사팀, DBA)에게 책임을 지므로 SRP를 위반합니다[^21].

### 3.2 코드 복잡성 증가

SRP를 위반하면 다음과 같은 복잡성이 증가합니다[^22][^16]:

1. **단일 클래스의 크기 증가**: 여러 책임을 가진 클래스는 수백 줄의 코드를 포함하게 됩니다
2. **이해하기 어려운 코드**: 서로 다른 관심사가 뒤섞여 코드 파악이 어려워집니다
3. **디버깅의 어려움**: 문제의 원인을 찾기 위해 전체 클래스를 분석해야 합니다

### 3.3 유지보수 비용의 증가

SRP 위반 시 유지보수 비용이 증가하는 이유[^23][^22]:

- **연쇄적 변경**: 하나의 기능 변경이 관련 없는 다른 기능에 영향을 미칠 수 있습니다
- **테스트 범위 확대**: 작은 변경에도 전체 기능을 테스트해야 합니다
- **코드 의존성 증가**: 서로 다른 책임들이 강하게 결합되어 분리가 어려워집니다


### 3.4 테스트의 어려움

SRP 위반 시 테스트가 어려워지는 이유[^15][^16]:

```typescript
// ❌ 테스트하기 어려운 예
class OrderProcessor {
  processOrder(order: Order) {
    // 주문 검증
    if (!this.validateOrder(order)) return false;
    
    // 결제 처리
    const payment = this.processPayment(order);
    
    // 재고 업데이트
    this.updateInventory(order);
    
    // 이메일 발송
    this.sendConfirmationEmail(order);
    
    // 로그 기록
    this.logOrder(order);
  }
}
```

이런 클래스를 테스트하려면:

- 결제 시스템 Mock이 필요
- 데이터베이스 연결이 필요
- 이메일 서비스 Mock이 필요
- 로깅 시스템 설정이 필요


## 4. Next.js와 React에서의 SRP 적용

### 4.1 컴포넌트 단위의 책임 분리

React에서 SRP는 **각 컴포넌트가 하나의 명확한 책임을 가져야 한다**는 의미입니다[^24][^25].

```tsx
// ❌ SRP 위반 - 여러 책임을 가진 컴포넌트
const BookList = () => {
  const [books, setBooks] = useState<Book[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const fetchBooks = async () => {
    try {
      const response = await fetch('/api/books');
      const data = await response.json();
      setBooks(data);
    } catch (err: any) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchBooks();
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      {books.map((book) => (
        <div key={book.id}>
          <img src={book.image} alt={book.title} />
          <h3>{book.title}</h3>
          <p>{book.author}</p>
        </div>
      ))}
    </div>
  );
};
```

```tsx
// ✅ SRP 적용 - 책임 분리
// 1. 데이터 fetching 책임
const useBooks = () => {
  const [books, setBooks] = useState<Book[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchBooks = async () => {
      try {
        const response = await fetch('/api/books');
        const data = await response.json();
        setBooks(data);
      } catch (err: any) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    
    fetchBooks();
  }, []);

  return { books, loading, error };
};

// 2. 개별 책 렌더링 책임
const BookCard: React.FC<{ book: Book }> = ({ book }) => (
  <div>
    <img src={book.image} alt={book.title} />
    <h3>{book.title}</h3>
    <p>{book.author}</p>
  </div>
);

// 3. 목록 렌더링 책임
const BookList: React.FC = () => {
  const { books, loading, error } = useBooks();

  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage message={error} />;

  return (
    <div>
      {books.map((book) => (
        <BookCard key={book.id} book={book} />
      ))}
    </div>
  );
};
```


### 4.2 Custom Hook을 통한 로직 분리

Custom Hook은 컴포넌트에서 로직을 분리하여 SRP를 적용하는 강력한 도구입니다[^26][^27]:

```tsx
// ✅ 폼 로직 분리
const useContactForm = (initialValues: ContactFormData) => {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState<Record<string, string>>({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  const handleChange = (field: keyof ContactFormData, value: string) => {
    setValues(prev => ({ ...prev, [field]: value }));
    if (errors[field]) {
      setErrors(prev => ({ ...prev, [field]: '' }));
    }
  };

  const validateForm = (): boolean => {
    const newErrors: Record<string, string> = {};
    
    if (!values.name.trim()) {
      newErrors.name = '이름은 필수입니다';
    }
    
    if (!values.email.trim()) {
      newErrors.email = '이메일은 필수입니다';
    } else if (!/\S+@\S+\.\S+/.test(values.email)) {
      newErrors.email = '올바른 이메일 형식이 아닙니다';
    }

    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = async (onSubmit: (data: ContactFormData) => Promise<void>) => {
    if (!validateForm()) return;
    
    setIsSubmitting(true);
    try {
      await onSubmit(values);
    } catch (error) {
      console.error('Submit failed:', error);
    } finally {
      setIsSubmitting(false);
    }
  };

  return {
    values,
    errors,
    isSubmitting,
    handleChange,
    handleSubmit
  };
};

// 컴포넌트는 UI 렌더링에만 집중
const ContactForm: React.FC = () => {
  const {
    values,
    errors,
    isSubmitting,
    handleChange,
    handleSubmit
  } = useContactForm({
    name: '',
    email: '',
    message: ''
  });

  const onSubmit = async (data: ContactFormData) => {
    await fetch('/api/contact', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    });
  };

  return (
    <form onSubmit={(e) => {
      e.preventDefault();
      handleSubmit(onSubmit);
    }}>
      <div>
        <input
          type="text"
          value={values.name}
          onChange={(e) => handleChange('name', e.target.value)}
          placeholder="이름"
        />
        {errors.name && <span className="error">{errors.name}</span>}
      </div>
      
      <div>
        <input
          type="email"
          value={values.email}
          onChange={(e) => handleChange('email', e.target.value)}
          placeholder="이메일"
        />
        {errors.email && <span className="error">{errors.email}</span>}
      </div>
      
      <div>
        <textarea
          value={values.message}
          onChange={(e) => handleChange('message', e.target.value)}
          placeholder="메시지"
        />
        {errors.message && <span className="error">{errors.message}</span>}
      </div>
      
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? '전송 중...' : '전송'}
      </button>
    </form>
  );
};
```


### 4.3 컴포지션 패턴 활용

React의 컴포지션 패턴은 SRP를 자연스럽게 적용할 수 있게 해줍니다[^28][^29]:

```tsx
// ✅ 컴포지션을 통한 책임 분리
interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  children: React.ReactNode;
}

const Modal: React.FC<ModalProps> = ({ isOpen, onClose, children }) => {
  if (!isOpen) return null;

  return (
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={(e) => e.stopPropagation()}>
        {children}
      </div>
    </div>
  );
};

const ModalHeader: React.FC<{ children: React.ReactNode }> = ({ children }) => (
  <div className="modal-header">{children}</div>
);

const ModalBody: React.FC<{ children: React.ReactNode }> = ({ children }) => (
  <div className="modal-body">{children}</div>
);

const ModalFooter: React.FC<{ children: React.ReactNode }> = ({ children }) => (
  <div className="modal-footer">{children}</div>
);

// 사용 예시
const UserProfileModal: React.FC<{ user: User; isOpen: boolean; onClose: () => void }> = ({ 
  user, 
  isOpen, 
  onClose 
}) => (
  <Modal isOpen={isOpen} onClose={onClose}>
    <ModalHeader>
      <h2>{user.name} Profile</h2>
      <button onClick={onClose}>×</button>
    </ModalHeader>
    
    <ModalBody>
      <img src={user.avatar} alt={user.name} />
      <p>Email: {user.email}</p>
      <p>Role: {user.role}</p>
    </ModalBody>
    
    <ModalFooter>
      <button onClick={onClose}>Close</button>
      <button>Edit Profile</button>
    </ModalFooter>
  </Modal>
);
```


### 4.4 서버 컴포넌트 vs 클라이언트 컴포넌트

Next.js 13+에서는 서버 컴포넌트와 클라이언트 컴포넌트의 책임을 명확히 분리해야 합니다[^30]:

```tsx
// ✅ 서버 컴포넌트 - 데이터 페칭 책임
async function UserListPage() {
  const users = await fetchUsers(); // 서버에서 데이터 페칭
  
  return (
    <div>
      <h1>Users</h1>
      <UserList users={users} />
    </div>
  );
}

// ✅ 클라이언트 컴포넌트 - 상호작용 책임  
'use client'

interface UserListProps {
  users: User[];
}

const UserList: React.FC<UserListProps> = ({ users }) => {
  const [searchTerm, setSearchTerm] = useState('');
  const [sortBy, setSortBy] = useState<'name' | 'email'>('name');

  const filteredUsers = users
    .filter(user => 
      user.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
      user.email.toLowerCase().includes(searchTerm.toLowerCase())
    )
    .sort((a, b) => a[sortBy].localeCompare(b[sortBy]));

  return (
    <div>
      <SearchInput 
        value={searchTerm} 
        onChange={setSearchTerm}
        placeholder="Search users..." 
      />
      
      <SortSelect 
        value={sortBy} 
        onChange={setSortBy}
        options={[
          { value: 'name', label: 'Name' },
          { value: 'email', label: 'Email' }
        ]}
      />
      
      <div>
        {filteredUsers.map(user => (
          <UserCard key={user.id} user={user} />
        ))}
      </div>
    </div>
  );
};
```


## 5. TypeScript와 함께하는 SRP

### 5.1 타입 정의를 통한 책임 명확화

TypeScript의 타입 시스템을 활용하면 각 모듈의 책임을 명확히 정의할 수 있습니다[^31][^32]:

```typescript
// ✅ 명확한 책임 분리를 위한 타입 정의
interface User {
  id: string;
  name: string;
  email: string;
  role: UserRole;
}

interface UserRole {
  id: string;
  name: string;
  permissions: Permission[];
}

interface Permission {
  id: string;
  action: string;
  resource: string;
}

// 사용자 데이터 관리 책임
interface UserRepository {
  findById(id: string): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
  save(user: User): Promise<User>;
  delete(id: string): Promise<void>;
}

// 사용자 인증 책임
interface AuthService {
  authenticate(email: string, password: string): Promise<AuthResult>;
  generateToken(user: User): string;
  validateToken(token: string): Promise<User | null>;
}

// 권한 검증 책임
interface AuthorizationService {
  hasPermission(user: User, action: string, resource: string): boolean;
  getUserPermissions(user: User): Permission[];
}
```


### 5.2 인터페이스 분리

TypeScript 인터페이스를 활용하여 각 클라이언트가 필요한 메서드만 의존하도록 할 수 있습니다:

```typescript
// ✅ 클라이언트별 인터페이스 분리
interface Readable {
  read(): string;
}

interface Writable {
  write(data: string): void;
}

interface Compressible {
  compress(): void;
}

// 각 클래스는 필요한 인터페이스만 구현
class FileReader implements Readable {
  read(): string {
    // 파일 읽기 로직
    return "file content";
  }
}

class FileWriter implements Writable {
  write(data: string): void {
    // 파일 쓰기 로직
  }
}

class CompressibleFile implements Readable, Writable, Compressible {
  read(): string { return ""; }
  write(data: string): void { }
  compress(): void { }
}

// 클라이언트는 필요한 인터페이스만 의존
class DocumentProcessor {
  constructor(
    private reader: Readable,
    private writer: Writable
  ) {}
  
  process(): void {
    const content = this.reader.read();
    const processed = this.transformContent(content);
    this.writer.write(processed);
  }
  
  private transformContent(content: string): string {
    return content.toUpperCase();
  }
}
```


### 5.3 제네릭을 활용한 재사용성

제네릭을 사용하여 타입 안전성을 유지하면서 재사용 가능한 컴포넌트를 만들 수 있습니다:

```tsx
// ✅ 제네릭을 활용한 재사용 가능한 컴포넌트
interface ListProps<T> {
  items: T[];
  renderItem: (item: T, index: number) => React.ReactNode;
  keyExtractor: (item: T) => string | number;
  className?: string;
}

const List = <T,>({ 
  items, 
  renderItem, 
  keyExtractor, 
  className 
}: ListProps<T>) => (
  <div className={className}>
    {items.map((item, index) => (
      <div key={keyExtractor(item)}>
        {renderItem(item, index)}
      </div>
    ))}
  </div>
);

// 사용 예시
interface Product {
  id: number;
  name: string;
  price: number;
}

const ProductList: React.FC<{ products: Product[] }> = ({ products }) => (
  <List
    items={products}
    keyExtractor={(product) => product.id}
    renderItem={(product) => (
      <div>
        <h3>{product.name}</h3>
        <p>${product.price}</p>
      </div>
    )}
    className="product-grid"
  />
);
```


### 5.4 타입 안전성과 SRP

TypeScript의 타입 시스템을 활용하여 컴파일 타임에 SRP 위반을 방지할 수 있습니다:

```typescript
// ✅ 타입으로 책임 경계 명확화
type EmailData = {
  to: string;
  subject: string;
  body: string;
};

type SMSData = {
  phoneNumber: string;
  message: string;
};

type PushNotificationData = {
  deviceId: string;
  title: string;
  body: string;
};

// 각 알림 유형별로 별도의 서비스
interface EmailNotificationService {
  send(data: EmailData): Promise<void>;
}

interface SMSNotificationService {
  send(data: SMSData): Promise<void>;
}

interface PushNotificationService {
  send(data: PushNotificationData): Promise<void>;
}

// 통합 알림 서비스는 조합으로 구성
class NotificationService {
  constructor(
    private emailService: EmailNotificationService,
    private smsService: SMSNotificationService,
    private pushService: PushNotificationService
  ) {}

  async sendEmail(data: EmailData): Promise<void> {
    return this.emailService.send(data);
  }

  async sendSMS(data: SMSData): Promise<void> {
    return this.smsService.send(data);
  }

  async sendPush(data: PushNotificationData): Promise<void> {
    return this.pushService.send(data);
  }
}
```


## 6. 실무 적용 가이드

### 6.1 리팩토링 전략

SRP를 적용한 리팩토링은 점진적으로 진행해야 합니다. 다음 단계를 따라 진행하세요:

**1단계: 책임 식별**

```typescript
// 현재 코드에서 책임들을 식별
class OrderService {
  // 1. 주문 검증 책임
  validateOrder(order: Order): boolean { }
  
  // 2. 결제 처리 책임  
  processPayment(order: Order): PaymentResult { }
  
  // 3. 재고 관리 책임
  updateInventory(order: Order): void { }
  
  // 4. 알림 책임
  sendConfirmationEmail(order: Order): void { }
  
  // 5. 로깅 책임
  logOrderActivity(order: Order): void { }
}
```

**2단계: 책임별 분리**

```typescript
// ✅ 각 책임을 별도 클래스로 분리
class OrderValidator {
  validate(order: Order): ValidationResult {
    // 주문 검증 로직만 담당
  }
}

class PaymentProcessor {
  process(order: Order): Promise<PaymentResult> {
    // 결제 처리 로직만 담당
  }
}

class InventoryManager {
  updateStock(order: Order): Promise<void> {
    // 재고 관리 로직만 담당
  }
}

class NotificationService {
  sendOrderConfirmation(order: Order): Promise<void> {
    // 알림 발송 로직만 담당
  }
}

class OrderLogger {
  logActivity(order: Order, activity: string): void {
    // 로깅 로직만 담당
  }
}
```

**3단계: 조합으로 구성**

```typescript
// ✅ 의존성 주입을 통한 조합
class OrderService {
  constructor(
    private validator: OrderValidator,
    private paymentProcessor: PaymentProcessor,
    private inventoryManager: InventoryManager,
    private notificationService: NotificationService,
    private logger: OrderLogger
  ) {}

  async processOrder(order: Order): Promise<OrderResult> {
    // 각 단계별로 적절한 서비스에 위임
    const validation = this.validator.validate(order);
    if (!validation.isValid) {
      return { success: false, errors: validation.errors };
    }

    const payment = await this.paymentProcessor.process(order);
    if (!payment.success) {
      return { success: false, errors: ['Payment failed'] };
    }

    await this.inventoryManager.updateStock(order);
    await this.notificationService.sendOrderConfirmation(order);
    this.logger.logActivity(order, 'Order completed successfully');

    return { success: true };
  }
}
```


### 6.2 점진적 개선 방법

기존 프로젝트에 SRP를 적용할 때는 다음 방법을 사용하세요:

**1. 가장 문제가 되는 부분부터 시작**

```typescript
// 1순위: 가장 큰 클래스나 가장 자주 변경되는 클래스
// 2순위: 테스트하기 어려운 클래스
// 3순위: 여러 팀이 동시에 수정하는 클래스
```

**2. Strangler Fig 패턴 적용**

```typescript
// ✅ 기존 코드를 단계적으로 대체
class LegacyUserService {
  // 기존 메서드들...
  
  // 새로운 기능은 분리된 서비스 사용
  async createUser(userData: CreateUserRequest): Promise<User> {
    return this.newUserCreationService.create(userData);
  }
  
  constructor(
    private newUserCreationService: UserCreationService
  ) {}
}
```

**3. Feature Flag 활용**

```typescript
// ✅ 기능 플래그로 안전하게 전환
class UserService {
  async getUser(id: string): Promise<User> {
    if (this.featureFlags.isEnabled('new-user-service')) {
      return this.newUserRepository.findById(id);
    }
    return this.legacyGetUser(id);
  }
}
```


### 6.3 팀 단위 적용 방안

팀 전체가 SRP를 적용하려면 다음과 같은 방법을 사용하세요:

**1. 코딩 컨벤션 정립**

```typescript
// team-conventions.md
// 1. 하나의 파일은 하나의 클래스만 포함
// 2. 클래스명은 책임을 명확히 표현
// 3. 메서드는 5개를 넘지 않도록 노력
// 4. 메서드 길이는 20줄을 넘지 않도록 노력
```

**2. 아키텍처 가이드라인**

```
src/
├── components/          # UI 컴포넌트 (렌더링 책임)
├── hooks/              # 로직 훅 (상태 관리 책임)
├── services/           # 비즈니스 로직 (도메인 책임)
├── repositories/       # 데이터 접근 (영속성 책임)
├── utils/              # 순수 함수 (유틸리티 책임)
└── types/              # 타입 정의
```

**3. 리뷰 체크리스트 수립**

```markdown
## SRP 리뷰 체크리스트
- [ ] 클래스/컴포넌트가 단일 책임을 가지는가?
- [ ] 변경될 이유가 하나뿐인가?
- [ ] 다른 책임과 섞여있지는 않은가?
- [ ] Mock 없이 테스트할 수 있는가?
- [ ] 클래스명이 책임을 명확히 표현하는가?
```


### 6.4 코드 리뷰 체크리스트

효과적인 SRP 적용을 위한 상세한 리뷰 가이드:

```typescript
// ❌ 리뷰에서 지적해야 할 사항들

// 1. 여러 책임이 섞인 컴포넌트
const UserDashboard = () => {
  // 데이터 페칭 + UI 렌더링 + 비즈니스 로직이 모두 섞여있음
  const [users, setUsers] = useState([]);
  const [analytics, setAnalytics] = useState({});
  
  useEffect(() => {
    fetchUsers().then(setUsers);
    calculateAnalytics().then(setAnalytics);
  }, []);

  const exportToCSV = () => { /* 내보내기 로직 */ };
  const sendNotification = () => { /* 알림 로직 */ };
  
  return (
    <div>
      {/* 복잡한 UI 로직 */}
    </div>
  );
};

// ✅ 개선된 버전
const UserDashboard = () => {
  const { users } = useUsers();
  const { analytics } = useAnalytics();
  const { exportToCSV } = useExport();
  const { sendNotification } = useNotification();

  return (
    <DashboardLayout>
      <UserList users={users} />
      <AnalyticsPanel analytics={analytics} />
      <ActionPanel 
        onExport={exportToCSV}
        onNotify={sendNotification}
      />
    </DashboardLayout>
  );
};
```


## 7. SRP의 장점과 한계

### 7.1 SRP 적용의 이점

SRP를 적용했을 때 얻는 구체적인 이점들:

**1. 코드 가독성 향상**[^16]

```typescript
// Before: 무엇을 하는지 파악하기 어려움
class DataManager {
  processData() { /* 200줄의 복잡한 로직 */ }
}

// After: 각각의 역할이 명확함
class DataValidator { validate() { } }
class DataTransformer { transform() { } }
class DataSaver { save() { } }
```

**2. 테스트 용이성**[^15]

```typescript
// ✅ 각 클래스를 독립적으로 테스트 가능
describe('UserValidator', () => {
  it('should validate user email format', () => {
    const validator = new UserValidator();
    const result = validator.validateEmail('test@example.com');
    expect(result.isValid).toBe(true);
  });
});

describe('UserRepository', () => {
  it('should save user to database', async () => {
    const repository = new UserRepository(mockDatabase);
    const user = { name: 'John', email: 'john@example.com' };
    await repository.save(user);
    expect(mockDatabase.insert).toHaveBeenCalledWith(user);
  });
});
```

**3. 재사용성 증대**[^33]

```typescript
// ✅ 다른 컨텍스트에서도 재사용 가능
const emailValidator = new EmailValidator();

// 여러 곳에서 사용 가능
const userForm = new UserForm(emailValidator);
const subscriptionForm = new SubscriptionForm(emailValidator);
const contactForm = new ContactForm(emailValidator);
```

**4. 병렬 개발 가능**[^15]

```typescript
// 팀 A: 사용자 인증 담당
class AuthService { }

// 팀 B: 사용자 프로필 담당  
class UserProfileService { }

// 팀 C: 알림 담당
class NotificationService { }

// 각 팀이 독립적으로 개발 가능
```


### 7.2 과도한 분리의 위험성

SRP를 과도하게 적용할 때 발생할 수 있는 문제점들[^16][^34]:

**1. 과분화(Over-abstraction)**

```typescript
// ❌ 지나친 분리의 예
class StringLengthChecker {
  check(str: string): number {
    return str.length;
  }
}

class StringUpperCaseConverter {
  convert(str: string): string {
    return str.toUpperCase();
  }
}

class StringValidator {
  constructor(
    private lengthChecker: StringLengthChecker,
    private upperCaseConverter: StringUpperCaseConverter
  ) {}
  
  validate(str: string): boolean {
    const length = this.lengthChecker.check(str);
    const upperStr = this.upperCaseConverter.convert(str);
    return length > 0 && upperStr === str;
  }
}

// ✅ 적절한 수준의 분리
class StringValidator {
  validate(str: string): boolean {
    return str.length > 0 && str === str.toUpperCase();
  }
}
```

**2. 복잡한 의존성 그래프**

```typescript
// ❌ 너무 많은 의존성
class OrderService {
  constructor(
    private validator: OrderValidator,
    private priceCalculator: PriceCalculator,
    private taxCalculator: TaxCalculator,
    private discountCalculator: DiscountCalculator,
    private shippingCalculator: ShippingCalculator,
    private paymentProcessor: PaymentProcessor,
    private inventoryUpdater: InventoryUpdater,
    private emailSender: EmailSender,
    private smsSender: SMSSender,
    private logger: Logger
  ) {} // 10개의 의존성!
}

// ✅ 관련 기능들을 적절히 그룹화
class OrderService {
  constructor(
    private orderValidator: OrderValidator,
    private priceService: PriceService, // 가격 관련 기능들을 묶음
    private fulfillmentService: FulfillmentService, // 주문 처리 기능들을 묶음
    private notificationService: NotificationService // 알림 기능들을 묶음
  ) {}
}
```


### 7.3 성능과의 균형

SRP 적용 시 성능을 고려해야 하는 상황들:

**1. 컴포넌트 분리와 렌더링 성능**

```tsx
// ⚠️ 과도한 분리로 인한 성능 이슈
const UserCard = ({ user }: { user: User }) => (
  <Card>
    <UserAvatar user={user} />      {/* 별도 컴포넌트 */}
    <UserName user={user} />        {/* 별도 컴포넌트 */}
    <UserEmail user={user} />       {/* 별도 컴포넌트 */}
    <UserRole user={user} />        {/* 별도 컴포넌트 */}
    <UserActions user={user} />     {/* 별도 컴포넌트 */}
  </Card>
);

// ✅ 성능을 고려한 적절한 분리
const UserCard = ({ user }: { user: User }) => (
  <Card>
    <UserBasicInfo user={user} />   {/* 기본 정보 그룹 */}
    <UserActions user={user} />     {/* 액션 버튼들 */}
  </Card>
);
```

**2. 번들 크기 고려**

```typescript
// ⚠️ 너무 많은 작은 모듈들
import { validateEmail } from './validators/email-validator';
import { validatePhone } from './validators/phone-validator';
import { validateName } from './validators/name-validator';
// ... 20개의 작은 validator imports

// ✅ 관련 기능들을 묶어서 번들 최적화
import { FormValidators } from './validators';
const { validateEmail, validatePhone, validateName } = FormValidators;
```


### 7.4 실무에서의 절충점

실무에서 SRP를 적용할 때 고려해야 할 절충점들[^34][^22]:

**1. 프로젝트 규모에 따른 조정**

```typescript
// 소규모 프로젝트: 단순한 구조
class UserService {
  async createUser(userData: CreateUserRequest): Promise<User> {
    // 검증, 생성, 저장을 하나의 메서드에서 처리
    if (!this.isValidEmail(userData.email)) {
      throw new Error('Invalid email');
    }
    
    const user = { ...userData, id: generateId() };
    await this.database.users.insert(user);
    return user;
  }
}

// 대규모 프로젝트: 상세한 분리
class UserCreationService {
  constructor(
    private validator: UserValidator,
    private repository: UserRepository,
    private eventPublisher: EventPublisher
  ) {}
  
  async create(userData: CreateUserRequest): Promise<User> {
    await this.validator.validate(userData);
    const user = await this.repository.save(userData);
    await this.eventPublisher.publish(new UserCreatedEvent(user));
    return user;
  }
}
```

**2. 팀의 경험 수준 고려**

```typescript
// 초급 개발자가 많은 팀: 명확하고 단순한 구조
class UserController {
  // 하나의 파일에 관련 기능들을 모아서 이해하기 쉽게
  async getUser(id: string) { }
  async createUser(data: any) { }
  async updateUser(id: string, data: any) { }
  async deleteUser(id: string) { }
}

// 숙련된 팀: 세밀한 분리
class GetUserUseCase { }
class CreateUserUseCase { }
class UpdateUserUseCase { }
class DeleteUserUseCase { }
```

**3. 변경 빈도에 따른 조정**

```typescript
// 자주 변경되는 부분: 세밀하게 분리
class PricingRules { }
class DiscountCalculator { }
class TaxCalculator { }

// 거의 변경되지 않는 부분: 적절히 묶어서 관리
class UserBasicOperations {
  findById(id: string) { }
  findByEmail(email: string) { }
  save(user: User) { }
}
```


## 8. 실습 예제와 베스트 프랙티스

### 8.1 Before \& After 코드 예제

실무에서 자주 마주치는 SRP 위반 사례와 개선 방법을 살펴보겠습니다.

**예제 1: 사용자 관리 대시보드**

```tsx
// ❌ SRP 위반 - 모든 것을 하나의 컴포넌트에서 처리
const UserDashboard = () => {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);
  const [searchTerm, setSearchTerm] = useState('');
  const [sortField, setSortField] = useState<'name' | 'email' | 'createdAt'>('name');
  const [sortDirection, setSortDirection] = useState<'asc' | 'desc'>('asc');
  const [selectedUsers, setSelectedUsers] = useState<string[]>([]);
  const [showDeleteModal, setShowDeleteModal] = useState(false);

  // 데이터 페칭
  useEffect(() => {
    const fetchUsers = async () => {
      try {
        setLoading(true);
        const response = await fetch('/api/users');
        if (!response.ok) throw new Error('Failed to fetch users');
        const data = await response.json();
        setUsers(data);
      } catch (err: any) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    fetchUsers();
  }, []);

  // 검색 필터링
  const filteredUsers = users.filter(user =>
    user.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
    user.email.toLowerCase().includes(searchTerm.toLowerCase())
  );

  // 정렬
  const sortedUsers = [...filteredUsers].sort((a, b) => {
    const aValue = a[sortField];
    const bValue = b[sortField];
    const comparison = aValue < bValue ? -1 : aValue > bValue ? 1 : 0;
    return sortDirection === 'asc' ? comparison : -comparison;
  });

  // 사용자 삭제
  const handleDeleteUsers = async () => {
    try {
      await Promise.all(
        selectedUsers.map(id => fetch(`/api/users/${id}`, { method: 'DELETE' }))
      );
      setUsers(users.filter(user => !selectedUsers.includes(user.id)));
      setSelectedUsers([]);
      setShowDeleteModal(false);
    } catch (error) {
      console.error('Failed to delete users:', error);
    }
  };

  // CSV 내보내기
  const exportToCSV = () => {
    const headers = ['Name', 'Email', 'Role', 'Created At'];
    const csvContent = [
      headers.join(','),
      ...sortedUsers.map(user => 
        [user.name, user.email, user.role, user.createdAt].join(',')
      )
    ].join('\n');
    
    const blob = new Blob([csvContent], { type: 'text/csv' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'users.csv';
    a.click();
    URL.revokeObjectURL(url);
  };

  if (loading) return <div className="loading">Loading users...</div>;
  if (error) return <div className="error">Error: {error}</div>;

  return (
    <div className="user-dashboard">
      <h1>User Management</h1>
      
      <div className="controls">
        <input
          type="text"
          placeholder="Search users..."
          value={searchTerm}
          onChange={(e) => setSearchTerm(e.target.value)}
        />
        
        <select 
          value={sortField} 
          onChange={(e) => setSortField(e.target.value as any)}
        >
          <option value="name">Name</option>
          <option value="email">Email</option>
          <option value="createdAt">Created At</option>
        </select>
        
        <button onClick={() => setSortDirection(sortDirection === 'asc' ? 'desc' : 'asc')}>
          {sortDirection === 'asc' ? '↑' : '↓'}
        </button>
        
        <button onClick={exportToCSV}>Export CSV</button>
        
        {selectedUsers.length > 0 && (
          <button onClick={() => setShowDeleteModal(true)}>
            Delete Selected ({selectedUsers.length})
          </button>
        )}
      </div>

      <table className="user-table">
        <thead>
          <tr>
            <th>
              <input
                type="checkbox"
                checked={selectedUsers.length === sortedUsers.length}
                onChange={(e) => {
                  if (e.target.checked) {
                    setSelectedUsers(sortedUsers.map(u => u.id));
                  } else {
                    setSelectedUsers([]);
                  }
                }}
              />
            </th>
            <th>Name</th>
            <th>Email</th>
            <th>Role</th>
            <th>Created At</th>
          </tr>
        </thead>
        <tbody>
          {sortedUsers.map(user => (
            <tr key={user.id}>
              <td>
                <input
                  type="checkbox"
                  checked={selectedUsers.includes(user.id)}
                  onChange={(e) => {
                    if (e.target.checked) {
                      setSelectedUsers([...selectedUsers, user.id]);
                    } else {
                      setSelectedUsers(selectedUsers.filter(id => id !== user.id));
                    }
                  }}
                />
              </td>
              <td>{user.name}</td>
              <td>{user.email}</td>
              <td>{user.role}</td>
              <td>{new Date(user.createdAt).toLocaleDateString()}</td>
            </tr>
          ))}
        </tbody>
      </table>

      {showDeleteModal && (
        <div className="modal-overlay" onClick={() => setShowDeleteModal(false)}>
          <div className="modal" onClick={(e) => e.stopPropagation()}>
            <h3>Delete Users</h3>
            <p>Are you sure you want to delete {selectedUsers.length} user(s)?</p>
            <div className="modal-actions">
              <button onClick={() => setShowDeleteModal(false)}>Cancel</button>
              <button onClick={handleDeleteUsers}>Delete</button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
};
```

```tsx
// ✅ SRP 적용 - 책임별로 분리된 구조

// 1. 데이터 관리 책임
const useUsers = () => {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        setLoading(true);
        const response = await fetch('/api/users');
        if (!response.ok) throw new Error('Failed to fetch users');
        const data = await response.json();
        setUsers(data);
      } catch (err: any) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    fetchUsers();
  }, []);

  const deleteUsers = async (userIds: string[]) => {
    await Promise.all(
      userIds.map(id => fetch(`/api/users/${id}`, { method: 'DELETE' }))
    );
    setUsers(users.filter(user => !userIds.includes(user.id)));
  };

  return { users, loading, error, deleteUsers };
};

// 2. 필터링 및 정렬 책임
const useUserFiltering = (users: User[]) => {
  const [searchTerm, setSearchTerm] = useState('');
  const [sortField, setSortField] = useState<keyof User>('name');
  const [sortDirection, setSortDirection] = useState<'asc' | 'desc'>('asc');

  const filteredAndSortedUsers = useMemo(() => {
    const filtered = users.filter(user =>
      user.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
      user.email.toLowerCase().includes(searchTerm.toLowerCase())
    );

    return [...filtered].sort((a, b) => {
      const aValue = a[sortField];
      const bValue = b[sortField];
      const comparison = aValue < bValue ? -1 : aValue > bValue ? 1 : 0;
      return sortDirection === 'asc' ? comparison : -comparison;
    });
  }, [users, searchTerm, sortField, sortDirection]);

  return {
    searchTerm,
    setSearchTerm,
    sortField,
    setSortField,
    sortDirection,
    setSortDirection,
    filteredAndSortedUsers
  };
};

// 3. 선택 관리 책임
const useUserSelection = (users: User[]) => {
  const [selectedUsers, setSelectedUsers] = useState<string[]>([]);

  const toggleUser = (userId: string) => {
    setSelectedUsers(prev =>
      prev.includes(userId)
        ? prev.filter(id => id !== userId)
        : [...prev, userId]
    );
  };

  const toggleAll = () => {
    setSelectedUsers(prev =>
      prev.length === users.length ? [] : users.map(u => u.id)
    );
  };

  const clearSelection = () => setSelectedUsers([]);

  return {
    selectedUsers,
    toggleUser,
    toggleAll,
    clearSelection
  };
};

// 4. CSV 내보내기 책임
const useCSVExport = () => {
  const exportToCSV = (users: User[]) => {
    const headers = ['Name', 'Email', 'Role', 'Created At'];
    const csvContent = [
      headers.join(','),
      ...users.map(user => 
        [user.name, user.email, user.role, user.createdAt].join(',')
      )
    ].join('\n');
    
    const blob = new Blob([csvContent], { type: 'text/csv' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'users.csv';
    a.click();
    URL.revokeObjectURL(url);
  };

  return { exportToCSV };
};

// 5. UI 컴포넌트들 - 각각 단일 책임
const SearchInput: React.FC<{
  value: string;
  onChange: (value: string) => void;
}> = ({ value, onChange }) => (
  <input
    type="text"
    placeholder="Search users..."
    value={value}
    onChange={(e) => onChange(e.target.value)}
    className="search-input"
  />
);

const SortControls: React.FC<{
  sortField: keyof User;
  sortDirection: 'asc' | 'desc';
  onSortFieldChange: (field: keyof User) => void;
  onSortDirectionChange: () => void;
}> = ({ sortField, sortDirection, onSortFieldChange, onSortDirectionChange }) => (
  <div className="sort-controls">
    <select 
      value={sortField} 
      onChange={(e) => onSortFieldChange(e.target.value as keyof User)}
    >
      <option value="name">Name</option>
      <option value="email">Email</option>
      <option value="createdAt">Created At</option>
    </select>
    
    <button onClick={onSortDirectionChange}>
      {sortDirection === 'asc' ? '↑' : '↓'}
    </button>
  </div>
);

const UserTable: React.FC<{
  users: User[];
  selectedUsers: string[];
  onToggleUser: (userId: string) => void;
  onToggleAll: () => void;
}> = ({ users, selectedUsers, onToggleUser, onToggleAll }) => (
  <table className="user-table">
    <thead>
      <tr>
        <th>
          <input
            type="checkbox"
            checked={selectedUsers.length === users.length && users.length > 0}
            onChange={onToggleAll}
          />
        </th>
        <th>Name</th>
        <th>Email</th>
        <th>Role</th>
        <th>Created At</th>
      </tr>
    </thead>
    <tbody>
      {users.map(user => (
        <UserRow
          key={user.id}
          user={user}
          isSelected={selectedUsers.includes(user.id)}
          onToggle={() => onToggleUser(user.id)}
        />
      ))}
    </tbody>
  </table>
);

const UserRow: React.FC<{
  user: User;
  isSelected: boolean;
  onToggle: () => void;
}> = ({ user, isSelected, onToggle }) => (
  <tr>
    <td>
      <input
        type="checkbox"
        checked={isSelected}
        onChange={onToggle}
      />
    </td>
    <td>{user.name}</td>
    <td>{user.email}</td>
    <td>{user.role}</td>
    <td>{new Date(user.createdAt).toLocaleDateString()}</td>
  </tr>
);

const DeleteConfirmModal: React.FC<{
  isOpen: boolean;
  userCount: number;
  onClose: () => void;
  onConfirm: () => void;
}> = ({ isOpen, userCount, onClose, onConfirm }) => {
  if (!isOpen) return null;

  return (
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal" onClick={(e) => e.stopPropagation()}>
        <h3>Delete Users</h3>
        <p>Are you sure you want to delete {userCount} user(s)?</p>
        <div className="modal-actions">
          <button onClick={onClose}>Cancel</button>
          <button onClick={onConfirm}>Delete</button>
        </div>
      </div>
    </div>
  );
};

// 6. 메인 컴포넌트 - 조합만 담당
const UserDashboard: React.FC = () => {
  const { users, loading, error, deleteUsers } = useUsers();
  const {
    searchTerm,
    setSearchTerm,
    sortField,
    setSortField,
    sortDirection,
    setSortDirection,
    filteredAndSortedUsers
  } = useUserFiltering(users);
  const {
    selectedUsers,
    toggleUser,
    toggleAll,
    clearSelection
  } = useUserSelection(filteredAndSortedUsers);
  const { exportToCSV } = useCSVExport();
  const [showDeleteModal, setShowDeleteModal] = useState(false);

  const handleDeleteConfirm = async () => {
    await deleteUsers(selectedUsers);
    clearSelection();
    setShowDeleteModal(false);
  };

  if (loading) return <div className="loading">Loading users...</div>;
  if (error) return <div className="error">Error: {error}</div>;

  return (
    <div className="user-dashboard">
      <h1>User Management</h1>
      
      <div className="controls">
        <SearchInput value={searchTerm} onChange={setSearchTerm} />
        
        <SortControls
          sortField={sortField}
          sortDirection={sortDirection}
          onSortFieldChange={setSortField}
          onSortDirectionChange={() => setSortDirection(prev => prev === 'asc' ? 'desc' : 'asc')}
        />
        
        <button onClick={() => exportToCSV(filteredAndSortedUsers)}>
          Export CSV
        </button>
        
        {selectedUsers.length > 0 && (
          <button onClick={() => setShowDeleteModal(true)}>
            Delete Selected ({selectedUsers.length})
          </button>
        )}
      </div>

      <UserTable
        users={filteredAndSortedUsers}
        selectedUsers={selectedUsers}
        onToggleUser={toggleUser}
        onToggleAll={toggleAll}
      />

      <DeleteConfirmModal
        isOpen={showDeleteModal}
        userCount={selectedUsers.length}
        onClose={() => setShowDeleteModal(false)}
        onConfirm={handleDeleteConfirm}
      />
    </div>
  );
};
```


### 8.2 Next.js 프로젝트 구조화

SRP를 적용한 Next.js 프로젝트의 이상적인 구조:

```
src/
├── app/                    # Next.js 13+ 앱 라우터
│   ├── (auth)/            # 인증 관련 라우트 그룹
│   │   ├── login/
│   │   └── register/
│   ├── dashboard/         # 대시보드 라우트
│   │   ├── page.tsx       # 서버 컴포넌트 (데이터 페칭)
│   │   └── components/    # 해당 페이지 전용 컴포넌트
│   └── api/               # API 라우트
│       ├── users/
│       └── auth/
├── components/            # 재사용 가능한 UI 컴포넌트
│   ├── ui/               # 기본 UI 컴포넌트 (버튼, 인풋 등)
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   └── Modal.tsx
│   ├── forms/            # 폼 관련 컴포넌트
│   │   ├── ContactForm.tsx
│   │   └── UserForm.tsx
│   └── layout/           # 레이아웃 컴포넌트
│       ├── Header.tsx
│       ├── Footer.tsx
│       └── Sidebar.tsx
├── hooks/                # Custom hooks (로직 분리)
│   ├── useAuth.ts
│   ├── useUsers.ts
│   └── useLocalStorage.ts
├── services/             # 비즈니스 로직 서비스
│   ├── authService.ts
│   ├── userService.ts
│   └── notificationService.ts
├── repositories/         # 데이터 액세스 레이어
│   ├── userRepository.ts
│   ├── authRepository.ts
│   └── base/
│       └── apiClient.ts
├── types/               # TypeScript 타입 정의
│   ├── user.ts
│   ├── auth.ts
│   └── api.ts
├── utils/               # 순수 함수 유틸리티
│   ├── validation.ts
│   ├── formatting.ts
│   └── constants.ts
└── middleware.ts        # Next.js 미들웨어
```

**실제 구현 예시:**

```typescript
// types/user.ts - 타입 정의 책임
export interface User {
  id: string;
  name: string;
  email: string;
  role: UserRole;
  createdAt: string;
  updatedAt: string;
}

export type UserRole = 'admin' | 'user' | 'moderator';

export interface CreateUserRequest {
  name: string;
  email: string;
  password: string;
  role?: UserRole;
}

// repositories/userRepository.ts - 데이터 액세스 책임
import { apiClient } from './base/apiClient';
import { User, CreateUserRequest } from '@/types/user';

export class UserRepository {
  async findAll(): Promise<User[]> {
    const response = await apiClient.get('/users');
    return response.data;
  }

  async findById(id: string): Promise<User | null> {
    try {
      const response = await apiClient.get(`/users/${id}`);
      return response.data;
    } catch (error) {
      if (error.status === 404) return null;
      throw error;
    }
  }

  async create(userData: CreateUserRequest): Promise<User> {
    const response = await apiClient.post('/users', userData);
    return response.data;
  }

  async update(id: string, userData: Partial<User>): Promise<User> {
    const response = await apiClient.put(`/users/${id}`, userData);
    return response.data;
  }

  async delete(id: string): Promise<void> {
    await apiClient.delete(`/users/${id}`);
  }
}

// services/userService.ts - 비즈니스 로직 책임
import { UserRepository } from '@/repositories/userRepository';
import { User, CreateUserRequest } from '@/types/user';
import { validateEmail, validatePassword } from '@/utils/validation';

export class UserService {
  constructor(private userRepository: UserRepository) {}

  async getAllUsers(): Promise<User[]> {
    return this.userRepository.findAll();
  }

  async createUser(userData: CreateUserRequest): Promise<User> {
    // 비즈니스 규칙 검증
    if (!validateEmail(userData.email)) {
      throw new Error('Invalid email format');
    }

    if (!validatePassword(userData.password)) {
      throw new Error('Password must be at least 8 characters');
    }

    // 중복 이메일 검사
    const existingUser = await this.userRepository.findByEmail?.(userData.email);
    if (existingUser) {
      throw new Error('Email already exists');
    }

    return this.userRepository.create(userData);
  }

  async updateUser(id: string, userData: Partial<User>): Promise<User> {
    const existingUser = await this.userRepository.findById(id);
    if (!existingUser) {
      throw new Error('User not found');
    }

    // 이메일 변경 시 중복 검사
    if (userData.email && userData.email !== existingUser.email) {
      const emailExists = await this.userRepository.findByEmail?.(userData.email);
      if (emailExists) {
        throw new Error('Email already exists');
      }
    }

    return this.userRepository.update(id, userData);
  }
}

// hooks/useUsers.ts - 상태 관리 책임
import { useState, useEffect } from 'react';
import { User } from '@/types/user';
import { UserService } from '@/services/userService';
import { UserRepository } from '@/repositories/userRepository';

const userService = new UserService(new UserRepository());

export const useUsers = () => {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const fetchUsers = async () => {
    try {
      setLoading(true);
      setError(null);
      const userData = await userService.getAllUsers();
      setUsers(userData);
    } catch (err: any) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  const createUser = async (userData: CreateUserRequest): Promise<User> => {
    const newUser = await userService.createUser(userData);
    setUsers(prev => [...prev, newUser]);
    return newUser;
  };

  const deleteUser = async (id: string): Promise<void> => {
    await userService.deleteUser(id);
    setUsers(prev => prev.filter(user => user.id !== id));
  };

  useEffect(() => {
    fetchUsers();
  }, []);

  return {
    users,
    loading,
    error,
    createUser,
    deleteUser,
    refetch: fetchUsers
  };
};

// app/dashboard/page.tsx - 서버 컴포넌트 (데이터 페칭 책임)
import { UserRepository } from '@/repositories/userRepository';
import { UserService } from '@/services/userService';
import { UserDashboardClient } from './components/UserDashboardClient';

export default async function DashboardPage() {
  const userService = new UserService(new UserRepository());
  const initialUsers = await userService.getAllUsers();

  return (
    <div>
      <h1>User Dashboard</h1>
      <UserDashboardClient initialUsers={initialUsers} />
    </div>
  );
}

// app/dashboard/components/UserDashboardClient.tsx - 클라이언트 컴포넌트 (상호작용 책임)
'use client'

import { User } from '@/types/user';
import { UserList } from '@/components/UserList';
import { UserFilters } from '@/components/UserFilters';
import { useUserFiltering } from '@/hooks/useUserFiltering';
import { useUsers } from '@/hooks/useUsers';

interface Props {
  initialUsers: User[];
}

export const UserDashboardClient: React.FC<Props> = ({ initialUsers }) => {
  const { users, loading, error, deleteUser } = useUsers(initialUsers);
  const { filteredUsers, searchTerm, setSearchTerm, sortBy, setSortBy } = useUserFiltering(users);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <UserFilters
        searchTerm={searchTerm}
        onSearchChange={setSearchTerm}
        sortBy={sortBy}
        onSortChange={setSortBy}
      />
      
      <UserList
        users={filteredUsers}
        onDelete={deleteUser}
      />
    </div>
  );
};
```


### 8.3 재사용 가능한 컴포넌트 설계

SRP를 적용한 재사용 가능한 컴포넌트 설계 패턴:

```tsx
// ✅ 복합 컴포넌트 패턴 (Compound Component Pattern)
interface CardContextValue {
  variant: 'default' | 'outlined' | 'elevated';
}

const CardContext = createContext<CardContextValue | null>(null);

const Card: React.FC<{
  variant?: 'default' | 'outlined' | 'elevated';
  children: React.ReactNode;
  className?: string;
}> & {
  Header: typeof CardHeader;
  Body: typeof CardBody;
  Footer: typeof CardFooter;
} = ({ variant = 'default', children, className }) => {
  return (
    <CardContext.Provider value={{ variant }}>
      <div className={`card card--${variant} ${className || ''}`}>
        {children}
      </div>
    </CardContext.Provider>
  );
};

const CardHeader: React.FC<{
  children: React.ReactNode;
  className?: string;
}> = ({ children, className }) => (
  <div className={`card__header ${className || ''}`}>
    {children}
  </div>
);

const CardBody: React.FC<{
  children: React.ReactNode;
  className?: string;
}> = ({ children, className }) => (
  <div className={`card__body ${className || ''}`}>
    {children}
  </div>
);

const CardFooter: React.FC<{
  children: React.ReactNode;
  className?: string;
}> = ({ children, className }) => (
  <div className={`card__footer ${className || ''}`}>
    {children}
  </div>
);

Card.Header = CardHeader;
Card.Body = CardBody;
Card.Footer = CardFooter;

// 사용 예시
const UserProfile: React.FC<{ user: User }> = ({ user }) => (
  <Card variant="elevated">
    <Card.Header>
      <h3>{user.name}</h3>
      <span className="badge">{user.role}</span>
    </Card.Header>
    
    <Card.Body>
      <p>Email: {user.email}</p>
      <p>Member since: {formatDate(user.createdAt)}</p>
    </Card.Body>
    
    <Card.Footer>
      <Button variant="outline">Edit</Button>
      <Button variant="danger">Delete</Button>
    </Card.Footer>
  </Card>
);
```

```tsx
// ✅ Render Props 패턴
interface DataFetcherProps<T> {
  url: string;
  children: (state: {
    data: T | null;
    loading: boolean;
    error: string | null;
    refetch: () => void;
  }) => React.ReactNode;
}

const DataFetcher = <T,>({ url, children }: DataFetcherProps<T>) => {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const fetchData = useCallback(async () => {
    try {
      setLoading(true);
      setError(null);
      const response = await fetch(url);
      if (!response.ok) throw new Error('Failed to fetch data');
      const result = await response.json();
      setData(result);
    } catch (err: any) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }, [url]);

  useEffect(() => {
    fetchData();
  }, [fetchData]);

  return <>{children({ data, loading, error, refetch: fetchData })}</>;
};

// 사용 예시
const UserList: React.FC = () => (
  <DataFetcher<User[]> url="/api/users">
    {({ data: users, loading, error, refetch }) => {
      if (loading) return <Spinner />;
      if (error) return <ErrorMessage message={error} onRetry={refetch} />;
      if (!users?.length) return <EmptyState message="No users found" />;

      return (
        <div>
          {users.map(user => (
            <UserCard key={user.id} user={user} />
          ))}
        </div>
      );
    }}
  </DataFetcher>
);
```


### 8.4 실무 팁과 주의사항

**1. SRP 적용 시 자주 하는 실수들**

```tsx
// ❌ 실수 1: 너무 세분화된 컴포넌트
const UserName = ({ name }: { name: string }) => <span>{name}</span>;
const UserEmail = ({ email }: { email: string }) => <span>{email}</span>;
const UserRole = ({ role }: { role: string }) => <span>{role}</span>;

// ✅ 적절한 수준의 분리
const UserBasicInfo = ({ user }: { user: User }) => (
  <div className="user-basic-info">
    <span className="user-name">{user.name}</span>
    <span className="user-email">{user.email}</span>
    <span className="user-role">{user.role}</span>
  </div>
);

// ❌ 실수 2: 로직과 UI를 무리하게 분리
const useButtonClick = () => {
  const [clicked, setClicked] = useState(false);
  const handleClick = () => setClicked(true);
  return { clicked, handleClick };
};

// ✅ 간단한 상호작용은 컴포넌트 내부에서 처리
const Button = ({ onClick, children }: ButtonProps) => {
  const [isPressed, setIsPressed] = useState(false);
  
  return (
    <button
      onClick={onClick}
      onMouseDown={() => setIsPressed(true)}
      onMouseUp={() => setIsPressed(false)}
      className={isPressed ? 'pressed' : ''}
    >
      {children}
    </button>
  );
};
```

**2. 성능을 고려한 SRP 적용**

```tsx
// ✅ memo를 활용한 불필요한 리렌더링 방지
const UserCard = memo(({ user, onEdit, onDelete }: UserCardProps) => (
  <div className="user-card">
    <UserAvatar src={user.avatar} alt={user.name} />
    <UserInfo user={user} />
    <UserActions onEdit={onEdit} onDelete={onDelete} />
  </div>
));

// ✅ 콜백 메모이제이션
const UserList = ({ users }: { users: User[] }) => {
  const handleEdit = useCallback((id: string) => {
    // 편집 로직
  }, []);

  const handleDelete = useCallback((id: string) => {
    // 삭제 로직
  }, []);

  return (
    <div>
      {users.map(user => (
        <UserCard
          key={user.id}
          user={user}
          onEdit={() => handleEdit(user.id)}
          onDelete={() => handleDelete(user.id)}
        />
      ))}
    </div>
  );
};
```

**3. 테스트하기 쉬운 구조 만들기**

```tsx
// ✅ 테스트 친화적인 구조
export const UserService = {
  async getUsers(repository: UserRepository): Promise<User[]> {
    return repository.findAll();
  },

  async createUser(
    userData: CreateUserRequest,
    repository: UserRepository,
    validator: UserValidator
  ): Promise<User> {
    const validation = validator.validate(userData);
    if (!validation.isValid) {
      throw new Error(validation.errors.join(', '));
    }
    
    return repository.create(userData);
  }
};

// 테스트 예시
describe('UserService', () => {
  it('should create user when data is valid', async () => {
    const mockRepository = {
      create: jest.fn().mockResolvedValue({ id: '1', ...userData }),
      findAll: jest.fn()
    };
    
    const mockValidator = {
      validate: jest.fn().mockReturnValue({ isValid: true, errors: [] })
    };

    const result = await UserService.createUser(
      userData,
      mockRepository,
      mockValidator
    );

    expect(result.id).toBe('1');
    expect(mockRepository.create).toHaveBeenCalledWith(userData);
  });
});
```

**4. 팀 협업을 위한 문서화**

```typescript
/**
 * 사용자 관리를 위한 서비스
 * 
 * 책임:
 * - 사용자 데이터 검증
 * - 비즈니스 규칙 적용
 * - 사용자 생성/수정/삭제 오케스트레이션
 * 
 * 의존성:
 * - UserRepository: 데이터 영속성
 * - UserValidator: 데이터 검증
 * - EmailService: 알림 발송
 * 
 * @example
 * ```
 * const userService = new UserService(
 *   new UserRepository(),
 *   new UserValidator(),
 *   new EmailService()
 * );
 * 
 * const user = await userService.createUser({
 *   name: 'John Doe',
 *   email: 'john@example.com',
 *   password: 'securePassword'
 * });
 * ```
 */
export class UserService {
  constructor(
    private userRepository: UserRepository,
    private userValidator: UserValidator,
    private emailService: EmailService
  ) {}

  /**
   * 새 사용자를 생성합니다
   * 
   * @param userData 사용자 생성 데이터
   * @returns 생성된 사용자 정보
   * @throws {ValidationError} 데이터가 유효하지 않을 때
   * @throws {ConflictError} 이메일이 이미 존재할 때
   */
  async createUser(userData: CreateUserRequest): Promise<User> {
    // 구현...
  }
}
```


## 마무리

단일 책임 원칙(SRP)은 소프트웨어 설계의 가장 기본이 되는 원칙 중 하나입니다. 특히 Next.js와 TypeScript를 활용한 현대적인 웹 개발에서 SRP를 올바르게 적용하면 다음과 같은 이점을 얻을 수 있습니다:

**✅ 주요 이점**

- **유지보수성 향상**: 각 모듈의 책임이 명확해져 수정이 용이합니다
- **테스트 용이성**: 단일 책임을 가진 컴포넌트는 테스트하기 쉽습니다
- **재사용성 증대**: 명확한 목적을 가진 컴포넌트는 다른 곳에서도 활용하기 쉽습니다
- **팀 협업 개선**: 각 모듈의 역할이 분명해져 여러 개발자가 동시에 작업하기 좋습니다

**⚠️ 주의사항**

- 과도한 분리는 오히려 복잡성을 증가시킬 수 있습니다
- 프로젝트 규모와 팀 상황을 고려하여 적절한 수준의 분리를 적용해야 합니다
- 성능과 개발 효율성 사이의 균형을 유지해야 합니다

**🎯 핵심 기억사항**

1. **책임 = 변경의 이유**: 하나의 모듈은 하나의 이유로만 변경되어야 합니다
2. **액터 중심 사고**: 누가 이 변경을 요청하는지를 생각해보세요
3. **점진적 적용**: 기존 프로젝트에는 단계적으로 적용하세요
4. **실용적 접근**: 이론보다는 실무에 맞는 절충점을 찾으세요

SRP는 단순한 규칙이 아닌, 더 나은 소프트웨어를 만들기 위한 사고방식입니다. 이 핸드북을 참고하여 여러분의 Next.js 프로젝트에 SRP를 적용해보시기 바랍니다.

<div style="text-align: center">⁂</div>

[^1]: https://velog.io/@qjqdn1568/SOLID-단일-책임-원칙-SRP

[^2]: https://ko.wikipedia.org/wiki/단일_%EC%B1%85%EC%9E%84_%EC%9B%90%EC%B9%99

[^3]: https://codeburst.io/the-single-responsibility-principle-2c09c279fce9

[^4]: https://www.linkedin.com/pulse/single-responsibility-principle-examples-fernando-vezzali

[^5]: https://seokzin.tistory.com/entry/React-SOLID-원칙을-컴포넌트에-적용하기

[^6]: https://min-mon.tistory.com/entry/단일-책임-원칙-SRP-Single-Responsibility-Principle

[^7]: https://johngrib.github.io/wiki/jargon/solid/

[^8]: https://velog.io/@suwon-city-boy/SOLID-객체지향의-5대-원칙

[^9]: https://dev-gold.tistory.com/104

[^10]: https://www.msap.ai/docs/msa-expert-from-concepts-to-practice/part-1-msa-fundamentals/msa-design-principles/msa-single-responsibility-principle/

[^11]: https://en.wikipedia.org/wiki/SOLID

[^12]: https://ko.wikipedia.org/wiki/SOLID_(%EA%B0%9D%EC%B2%B4_%EC%A7%80%ED%96%A5_%EC%84%A4%EA%B3%84)

[^13]: https://stackify.com/solid-design-principles/

[^14]: https://www.toptal.com/software/single-responsibility-principle

[^15]: https://www.theserverside.com/tip/How-to-apply-the-single-responsibility-principle-in-Java

[^16]: https://www.ggorantala.dev/srp-advantages-and-disadvantages/

[^17]: https://en.wikipedia.org/wiki/Single-responsibility_principle

[^18]: https://dev.to/extinctsion/solid-single-responsibility-principlesrp-in-c-34d7

[^19]: https://www.geeksforgeeks.org/system-design/single-responsibility-in-solid-design-principle/

[^20]: https://kmkunk.tistory.com/95

[^21]: https://inpa.tistory.com/entry/OOP-💠-아주-쉽게-이해하는-SRP-단일-책임-원칙

[^22]: https://blog.logrocket.com/single-responsibility-principle-srp/

[^23]: https://bettershinny.tistory.com/9

[^24]: https://dev.to/mikhaelesa/single-responsibility-principle-in-react-10oc

[^25]: https://www.linkedin.com/pulse/single-responsibility-principle-srp-react-alexandre-pereira-bdfkf

[^26]: https://dev.to/barrymichaeldoyle/mastering-separation-of-concerns-in-react-with-custom-hooks-1e97

[^27]: https://clouddevs.com/next/creating-custom-hooks/

[^28]: https://dev.to/wallacefreitas/understanding-the-composition-pattern-in-react-3dfp

[^29]: https://krasimir.gitbooks.io/react-in-patterns/content/chapter-04/

[^30]: https://www.wisp.blog/blog/should-i-avoid-using-hooks-in-nextjs

[^31]: https://dev.to/deepeshk1204/best-practices-of-reactjs-with-typescript-24p4

[^32]: https://www.geeksforgeeks.org/typescript/typescript-with-react-benefits-and-best-practices/

[^33]: https://www.marktinderholt.com/software development/2024/07/01/single-responsability-principle.html

[^34]: https://hackernoon.com/making-the-single-responsibility-principle-practical

[^35]: https://arifinfrds.com/2025/02/11/solid-single-responsibility-principle-srp/

[^36]: https://dev.to/paulocappa/solid-day-1-s-single-responsibility-principle-srp-1f5k

[^37]: https://www.techtarget.com/whatis/definition/Single-Responsibility-Principle-SRP

[^38]: https://cloudnative.ly/the-single-responsibility-principle-srp-77895368c068

[^39]: https://www.freecodecamp.org/news/solid-principles-single-responsibility-principle-explained/

[^40]: https://hackernoon.com/solid-learn-about-the-single-responsibility-principle-with-examples

[^41]: https://blog.itcode.dev/posts/2021/08/13/single-responsibility-principle

[^42]: https://da-nyee.github.io/posts/design-pattern-solid-principles/

[^43]: https://www.reddit.com/r/programming/comments/18mj2pt/ive_vastly_misunderstood_the_single/

[^44]: https://www.digitalocean.com/community/conceptual-articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design

[^45]: https://dev.to/ivangavlik/single-responsibility-principle-explained-with-example-in-java-3bb2

[^46]: https://stackoverflow.com/questions/74835489/how-to-approach-single-responsibility-principle

[^47]: https://yakhyo.github.io/solid-python/solid_python/srp/

[^48]: https://www.linkedin.com/advice/1/what-benefits-applying-single-responsibility-principle

[^49]: https://www.baeldung.com/java-single-responsibility-principle

[^50]: https://www.sitepoint.com/the-single-responsibility-principle/

[^51]: https://blog.devops.dev/back-to-basics-understanding-the-single-responsibility-principle-in-c-77b1d59755bd

[^52]: https://www.geeksforgeeks.org/java/single-responsibility-principle-in-java-with-examples/

[^53]: https://www.youtube.com/watch?v=MPp4A4F6rQI

[^54]: https://getparthenon.com/blog/code-review-single-responsibility-principle/

[^55]: https://kimtaesoo99.tistory.com/216

[^56]: https://ramees.tistory.com/63

[^57]: https://tech.kakaobank.com/posts/2411-solid-truth-or-myths-for-developers/

[^58]: https://haedallog.tistory.com/147

[^59]: https://yoongrammer.tistory.com/96

[^60]: https://modulabs.co.kr/blog/oop-solid

[^61]: https://goldenrabbit.co.kr/2024/05/20/programming-객체지향-프로그래밍-5가지-설계-원칙-solid/

[^62]: https://wikidocs.net/167368

[^63]: https://velog.io/@teo/Javascript에서도-SOLID-원칙이-통할까

[^64]: https://dev.to/ruben_alapont/solid-principles-series-understanding-the-single-responsibility-principle-srp-in-nodejs-with-typescript-57e8

[^65]: https://www.manuelsanchezdev.com/blog/solid-principles-single-responsibility

[^66]: https://thetshaped.dev/p/single-responsibility-principle-srp-in-react-write-focused-components

[^67]: https://www.youtube.com/watch?v=vFDK1PZuYzU

[^68]: https://www.youtube.com/watch?v=tLPi3SPqUSE

[^69]: https://dev.to/mdawooddev/implementing-solid-principles-in-react-a-guide-for-scalable-development-57h8

[^70]: https://www.linkedin.com/pulse/from-theory-practice-applying-solid-principles-react-using-martin-zst5f

[^71]: https://itnext.io/solid-in-react-the-good-the-bad-and-the-awesome-79d6cc518d1f

[^72]: https://github.com/afdezcl/react-solid

[^73]: https://www.linkedin.com/pulse/single-responsibility-principle-srp-solid-applied-react-igor-matsuoka-lolaf

[^74]: https://cekrem.github.io/posts/single-responsibility-principle-in-react/

[^75]: https://www.geeksforgeeks.org/reactjs/how-to-use-single-responsibility-principle-in-reactjs/

[^76]: https://www.dhiwise.com/post/building-react-apps-with-the-single-responsibility-principle

[^77]: https://www.reddit.com/r/webdev/comments/uepe2h/how_does_reacts_use_of_jsx_sit_well_with_single/

[^78]: https://dev.to/fajarriv/solid-principle-in-nextjs-using-typescript-3l3k

[^79]: https://www.developerway.com/posts/components-composition-how-to-get-it-right

[^80]: https://dev.to/areknawo/separation-of-concerns-with-custom-react-hooks-3aoe

[^81]: https://www.sitepoint.com/react-with-typescript-best-practices/

[^82]: https://github.com/vercel/next.js/discussions/50549

[^83]: https://legacy.reactjs.org/docs/composition-vs-inheritance.html

[^84]: https://www.reddit.com/r/nextjs/comments/1j5no80/are_hooks_bad_in_nextjs/

[^85]: https://dev.to/ricardolmsilva/composition-pattern-in-react-28mj

[^86]: https://astconsulting.in/typescript/typescript-react-best-practices

[^87]: https://www.youtube.com/watch?v=GBh59sRi8qQ

[^88]: https://velog.io/@dolfalf/React프로젝트-Best-practice-정리

[^89]: https://velog.io/@houndhollis/Next.js-14-생각-없이-짠-코드-리팩토링-Custom-hook-편

[^90]: https://www.dhiwise.com/post/react-composition-patterns-crafting-reusable-code

