---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# API 핸드북

**핵심 요약:**
API(Application Programming Interface)는 소프트웨어 간 상호작용을 가능케 하는 **계약**이자 **접점**이다. 잘 설계된 API는 내부 구현을 추상화하여 시스템 간 통합을 단순화하고, 확장성과 유지보수성을 높인다.

## 1. API의 정의와 목적

API는 프로그램(또는 시스템) 간에 **요청(request)**과 **응답(response)**을 주고받는 **인터페이스**로, 내부 로직을 숨기고 필요한 기능만 노출시킨다[^1].

- **사용자 인터페이스(UI)**가 사람과 컴퓨터를 연결하듯, API는 **컴퓨터와 컴퓨터** 또는 **프로그램과 프로그램**을 연결한다[^1].
- 기능을 계약(contract) 형태로 문서화한 것이 **API 사양(API specification)**이며, 이를 구현한 시스템은 API를 **제공**(expose)한다고 표현한다[^1].


## 2. API 유형 분류

### 2.1 공개 범위에 따른 분류

| 유형 | 설명 | 보안·접근성 |
| :-- | :-- | :-- |
| 공개(API)(Public/Open) | 모든 개발자에게 공개, 대체로 API 키나 OAuth 필요 | 중간 수준 인증[^2] |
| 파트너 API(Partner) | 특정 제휴사용, 제한적 접근 | 강력한 인증·권한 부여[^3] |
| 내부 API(Private/Internal) | 조직 내부 전용 | 비공개, 높은 보안[^4] |
| 컴포지트 API(Composite) | 여러 API를 조합해 단일 호출로 처리 | 내·외부 혼합 |

### 2.2 아키텍처·프로토콜에 따른 분류

| 유형      | 특징                                  | 주요 사례                   |
| :------ | :---------------------------------- | :---------------------- |
| REST    | 무상태(stateless), 리소스 기반, HTTP 메서드 활용 | JSON/HTTP, HATEOAS[^5]  |
| SOAP    | XML 기반 메시징, 프로토콜 독립적                | 기업 통합 환경                |
| RPC     | 원격 프로시저 호출, 함수 형태 요청                | gRPC, JSON-RPC, XML-RPC |
| GraphQL | 클라이언트가 데이터 스키마 정의                   | 다양한 데이터 소스 통합           |

## 3. API 구조와 설계 원칙

### 3.1 설계 원칙

1. **최소성(Minimal) \& 완전성(Complete)**: 필요한 기능만 노출하고, 모든 요구 기능 제공[^6].
2. **명확하고 단순한 의미론**: 최소한의 놀람 원칙(principle of least surprise)[^6].
3. **직관성(Intuitive)**: 문법과 경로(path)가 예측 가능해야 함[^6].
4. **일관성(Consistency)**: 명명 규칙, 응답 포맷이 일관되어야 함[^7].
5. **보안(Security)**: 인증·인가, 데이터 암호화, 입력 검증 필수[^8].

### 3.2 디자인 패턴

- **리소스 기반 경로 설계**: `/users/{id}` 등 리소스명 사용. 동사 생략[^8].
- **버전 관리**: URI 또는 헤더에 버전 명시(`/v1/…`)
- **에러 처리**: 표준 HTTP 상태 코드 활용(400, 401, 404, 500 등)[^9].


## 4. API 사용법과 통합

1. **문서 확인**: 제공자 문서에서 엔드포인트, 요청·응답 예시 파악[^10].
2. **인증 방식 구현**: API 키, OAuth2, JWT 등 적절한 방식 적용.
3. **테스트 및 디버깅**: Postman·cURL로 요청·응답 확인.
4. **통합(Integration)**: SDK, 라이브러리 활용하여 비즈니스 로직에 연결.
5. **모니터링**: 호출량·응답 시간·에러율 추적.

## 5. 모범 사례 및 가이드라인

- **표준 준수**: OpenAPI(OpenAPI Specification) 등 표준 문서화.
- **자동화된 문서화**: Swagger, Redoc 등 도구 사용.
- **테스트 커버리지**: 단위·통합 테스트 작성.
- **버전 호환성**: 하위 호환 깨지지 않도록 설계.
- **성능 최적화**: 캐싱, 페이징, 데이터 압축 적용.

**핸드북 요약**
API는 시스템 간 통신의 **기반**이며, **명확한 설계**, **엄격한 보안**, **일관된 문서화**가 중요한 핵심 요소이다. 이 핸드북을 통해 API의 정의, 유형, 설계 원칙, 사용 방법, 모범 사례까지 체계적으로 이해하고 적용할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/API

[^2]: https://blog.axway.com/learning-center/apis/basics/different-types-apis

[^3]: https://www.techtarget.com/searchapparchitecture/tip/What-are-the-types-of-APIs-and-their-differences

[^4]: https://www.mulesoft.com/jp/api/types-of-apis

[^5]: https://blog.dreamfactory.com/rest-apis-an-overview-of-basic-principles

[^6]: https://wiki.qt.io/API_Design_Principles

[^7]: https://www.ibm.com/think/topics/api-design

[^8]: https://www.mulesoft.com/api-university/four-principles-designing-effective-apis

[^9]: https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design

[^10]: https://aws.amazon.com/what-is/api/

[^11]: https://konghq.com/blog/learning-center/different-api-types-and-use-cases

[^12]: https://www.itmanage.co.jp/column/application-programming-interface/

[^13]: https://ja.wikipedia.org/wiki/アプリケーションプログラミングインタフェース

[^14]: https://stoplight.io/api-types

[^15]: https://www.intec.co.jp/column/edi-13.html

[^16]: https://www.redhat.com/ja/topics/api/what-are-application-programming-interfaces

[^17]: https://kwcplus.kddi-web.com/blog/api-types-and-protocols

[^18]: https://www.postman.com/api-platform/api-design/

[^19]: https://www.sbbit.jp/article/cont1/62752

[^20]: https://www.ibm.com/docs/ja/order-management?topic=apis-types

