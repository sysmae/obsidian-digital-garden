---
publish:  true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Zod 핸드북

## 주요 권고사항

핸드북 내용을 요약하면, **Zod**는 TypeScript 환경에서 런타임 유효성 검사와 정적 타입 추론을 모두 제공하는 *TypeScript-First* 스키마 선언·검증 라이브러리이다.

- 컴파일 타임(TypeScript)과 런타임(JavaScript) 간 타입 불일치로 인한 오류를 방지
- 데이터 구조를 선언하고 이를 바탕으로 타입과 유효성 검사 코드를 단일 선언문으로 통합
- 최소한의 번들 크기(약 2 KB, gzip)와 제로 외부 의존성


## 1. Zod의 개요

Zod는 `z` 네임스페이스 아래 다양한 빌트인 스키마 빌더를 제공하며, 스키마 선언만으로 TypeScript 타입을 자동 생성한다.

- **정적 추론**: `z.infer<typeof Schema>`로 스키마 기반 타입 추출
- **런타임 검증**: `Schema.parse(data)` 또는 `Schema.safeParse(data)`
- **변환(Transformation)**: `.transform()` 사용 시 입력 타입과 출력 타입 분리 추출 가능


## 2. 설치 및 환경 설정

```bash
npm install zod
# 또는
yarn add zod
```

- TypeScript 프로젝트: `tsconfig.json`에서 `"strict": true` 권장
- 최신 TypeScript(v5.5 이상)와 호환


## 3. 기본 스키마 빌더

### 3.1 원시 타입

- `z.string()`, `z.number()`, `z.boolean()`, `z.bigint()`, `z.date()`
- 빈 값 타입: `z.undefined()`, `z.null()`, `z.void()`


### 3.2 메서드 체인

- 조건부 검사:
    - 문자열: `.min(length, message)`, `.max(length, message)`, `.email()`, `.regex(regex, message)`
    - 숫자: `.min(value, message)`, `.max(value, message)`, `.int()`, `.positive()`, `.nonnegative()`


## 4. 복합 스키마

### 4.1 객체 스키마

```ts
const UserSchema = z.object({
  id: z.string().uuid(),
  name: z.string().min(1),
  email: z.string().email(),
});
```

- 중첩 객체: 각 필드를 별도 스키마로 분리 후 `object({...})` 내부에 참조
- 배열: `z.array(ItemSchema)`
- 튜플: `z.tuple([TypeA, TypeB, …])`
- 유니온／교차: `z.union([A, B])`, `z.intersection(A, B)`


### 4.2 레코드

```ts
const CartSchema = z.record(z.string().uuid(), z.number().int().positive());
```

- 키·값 타입 지정 가능


## 5. 고급 기능

### 5.1 커스텀 검사: `.refine()`

- 동기/비동기 검증 가능
- 예: `z.number().refine(n => n > 0, { message: '양수여야 합니다' })`


### 5.2 전처리: `.preprocess()`

- 입력 데이터 가공 후 스키마 적용
- 예:

```ts
const PhoneSchema = z.preprocess(
  val => (typeof val === 'string' ? val.replace(/\D/g, '') : val),
  z.string().length(10)
);
```


### 5.3 스키마 조합

- 확장: `BaseSchema.extend({ extra: z.string() })`
- 병합: `SchemaA.merge(SchemaB)` (불변)


## 6. 타입 추출 및 활용

```ts
type User = z.infer<typeof UserSchema>;
type In = z.input<typeof SomeSchema>;
type Out = z.output<typeof SomeSchema>;
```

- 입력(input)과 출력(output) 타입 구분 추출


## 7. 예제: 폼 검증과 API 검증

```ts
import { z } from 'zod';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';

const ProfileSchema = z.object({
  username: z.string().min(2),
  email: z.string().email(),
  age: z.number().min(18)
});

type Profile = z.infer<typeof ProfileSchema>;

const { register, handleSubmit, formState } = useForm<Profile>({
  resolver: zodResolver(ProfileSchema)
});
```

- React Hook Form 연동, `@hookform/resolvers/zod` 사용

```ts
async function fetchBook() {
  const res = await fetch('/api/book/1');
  const data = await res.json();
  return BookSchema.parse(data);
}
```

- API 응답 검증


## 8. 모범 사례

1. **스키마 분리**: 재사용 가능한 작은 스키마를 정의하여 중첩 및 조합으로 확장
2. **명시적 오류 메시지**: 모든 제약조건에 사용자 친화적 메시지 부여
3. **비동기 검사**: 이메일 중복 확인 등 서버 확인이 필요한 경우 `.refine()` 비동기 사용
4. **전처리**: `unknown` 타입을 안전하게 특정 타입으로 전환
5. **Strict 모드**: `strict()`를 통해 예상치 못한 추가 필드 차단

## 결론

Zod는 TypeScript 프로젝트에서 **단일 선언**으로 타입 추론·런타임 검증·데이터 변환을 수행하는 강력한 스키마 라이브러리다. 스키마 기반 아키텍처를 통해 코드베이스의 **유지보수성**과 **안정성**을 높일 수 있다. 얕은 객체 검증부터 복잡한 비동기 검사까지 폭넓게 지원하므로, 프론트엔드 폼 검증과 백엔드 API 검증 모두에 일관된 방식으로 적용할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://zod.dev

[^2]: https://vee-validate.logaretm.com/v4/integrations/zod-schema-validation/

[^3]: https://blog.bitsrc.io/7-powerful-use-cases-for-zod-schemas-b6df6d77bebc

[^4]: https://github.com/colinhacks/zod

[^5]: https://blog.logrocket.com/schema-validation-typescript-zod/

[^6]: https://www.open200.com/post/typesafe-everything-with-zod-by-example

[^7]: https://lurgi.tistory.com/85

[^8]: https://betterstack.com/community/guides/scaling-nodejs/zod-explained/

[^9]: https://zod.dev/basics

[^10]: https://www.totaltypescript.com/tutorials/zod

[^11]: https://dev.to/drprime01/how-to-validate-a-file-input-with-zod-5739

[^12]: https://dev.to/_domenicocolandrea/master-schema-validation-in-typescript-with-zod-28dc

[^13]: https://isaac-christian.tistory.com/entry/TypeScript-Zod-타입-검증-라이브러리

[^14]: https://www.freecodecamp.org/news/how-to-use-zod-for-react-api-validation/

[^15]: https://www.vinuteam.com/blog/27

[^16]: https://techblog.samsung.com/blog/article/43

[^17]: https://velog.io/@jinyoung985/TypeScript-zod-라이브러리란

[^18]: https://www.daleseo.com/zod-schema/

[^19]: https://velog.io/@tmdgp0212/Zod

