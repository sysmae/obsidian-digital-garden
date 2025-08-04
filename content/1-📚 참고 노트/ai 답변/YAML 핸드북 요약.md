---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# YAML 핸드북 요약

## 주요 요약

YAML은 **사람이 읽기 쉬운** 형태로 데이터 구조를 표현·직렬화하기 위해 2001년 Clark Evans, Ingy döt Net, Oren Ben-Kiki 등에 의해 고안되었다. XML의 복잡성을 줄이고, JSON보다 가독성을 높이며, 프로그래밍 언어의 내장 데이터 구조와 최대한 일치하도록 설계되었다. 구성 파일, 로그, 메시지 전달, 크로스·언어 데이터 교환 등 다양한 분야에서 널리 사용된다.

## 1. 탄생 배경 및 설계 목적

YAML의 원래 명칭은 “Yet Another Markup Language”였으나, 곧 “YAML Ain’t Markup Language”로 재귀적 약어화하면서 **데이터 직렬화** 언어임을 강조했다[^1].
설계 목표(우선순위 순)[^2]:

- 사람에게 읽기 쉬워야 함.
- 다양한 언어 간 데이터 호환이 가능해야 함.
- 동적 언어의 내장 데이터 구조(스칼라·시퀀스·매핑)와 일치해야 함.
- 구현과 사용이 간단해야 함.

1. XML의 과도한 기호와 태그 사용을 제거하고,
2. JSON의 계층 구조만 가능하다는 한계를 확장하며,
3. 들여쓰기 기반 문법으로 복잡도 완화와 가독성 개선을 달성했다.

## 2. 문서 구조 및 문법

### 2.1 기본 구성 요소

- **스칼라(Scalars)**: 문자열, 정수, 실수, 불리언 등
- **시퀀스(Sequences)**: 리스트(`- item`) 또는 대괄호(`[...]`)
- **매핑(Mappings)**: 키-값 쌍(`key: value`) 또는 중괄호(`{key: value}`)


### 2.2 들여쓰기와 구분 기호

- **들여쓰기**: 공백으로만, 탭 불허
- **문서 구분**: 시작 `---`, 선택적 종료 `...`
- **주석**: `#` 뒤부터 줄 끝까지


### 2.3 스칼라 표기

- **기본**: 인용부호 없이 가능
- **단일 인용**: `'don''t'`
- **이중 인용**: `"\n", "\t", "\u263A"` 등 C 스타일 이스케이프


### 2.4 블록 표기

- **리터럴 블록**(`|`): 줄바꿈 보존
- **폴딩 블록**(`>`): 줄바꿈을 공백으로 변환, 단락 구분은 빈 줄 유지


### 2.5 앵커·별칭

- **앵커**(`&id`): 노드에 라벨 지정
- **별칭**(`*id`): 앵커 참조로 중복 데이터 반복 제거


## 3. 고급 기능 및 확장성

### 3.1 태그와 데이터 타입

- **보편 태그**(`!!int`, `!!str`): 명시적 타입 표기
- **로컬 태그**(`!myType`): 사용자 정의 타입


### 3.2 멀티 도큐먼트 스트림

- 하나의 파일에 여러 도큐먼트(`--- ... --- ...`) 삽입 가능
- 스트림 형태 입출력 지원


### 3.3 관계형 모델

- **비계층적 참조**: 앵커·별칭을 통해 동일 데이터 여러 위치에서 참조 → 중복 방지, 가독성 향상


### 3.4 보안 고려사항

- 안전 로딩(`safe_load`) 사용 권장(임의 객체 생성 방지)
- 사용자 정의 태그 처리 시 모듈별 취약점 주의


## 4. 활용 시나리오 및 사례

| 분야 | 대표 사용 예 | 설명 |
| :-- | :-- | :-- |
| 구성 파일 | Kubernetes, Ansible, Docker Compose | 인프라·배포 설정, IaC 정의[^3] |
| 지속형 로깅 | 로그 파일 포맷 | 가독성 높은 로그 저장 |
| 메시징 | RabbitMQ 메시지 직렬화 | 프로세스 간 메시지 교환 |
| 크로스·언어 데이터 교환 | API 응답 포맷 | JSON 호환, YAML 독자 확장 지원 |

## 5. 파일 확장자 및 MIME 타입

- 권장 확장자: **.yaml** (과거 .yml 병용 가능)
- 공식 MIME 타입: **application/yaml**
- JSON은 YAML 1.2의 **엄격한 상위 집합**(strict superset)으로, JSON 문서는 유효한 YAML 문서이다[^4].


## 6. 구현 및 호환성

- 주요 언어별 파서 제공(파이썬 PyYAML, Ruby Psych, Java SnakeYAML 등)
- 대부분의 편집기에서 문법 강조·접기 지원
- 인라인·블록 스타일 혼합 가능 → 유연한 포맷팅


## 7. 유지 관리 및 발전

YAML 1.0(2004) → 1.1(2005) → 1.2(2009) → 1.2.2(2021)로 발전하며, **JSON 호환성 강화** 및 **명세 가독성 개선**에 집중[^1][^5].
현재는 커뮤니티 기반의 YAML Language Development Team이 관리하며, GitHub 리포지토리에서 지속적인 이슈·제안 논의가 이루어지고 있다.

**핸드북 활용법**

1. **구조 파악**: 프로젝트에 필요한 데이터 모델을 스칼라·시퀀스·매핑으로 정의
2. **가독성 유지**: 일관된 들여쓰기(스페이스)·주석 활용
3. **재사용성 확보**: 앵커·별칭으로 공통 설정 분리
4. **보안 강화**: 외부 입력 YAML은 안전 로더로 처리
5. **버전 관리**: 다중 문서 스트림으로 설정 버전 구분

이 요약을 통해 YAML의 **탄생 이유**, **핵심 문법**, **고급 기능**, **실무 활용**, **향후 발전 방향**을 3단계 심화하여 파악할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/YAML

[^2]: https://redocly.com/learn/yaml

[^3]: https://spacelift.io/blog/yaml

[^4]: https://www.loc.gov/preservation/digital/formats/fdd/fdd000645.shtml

[^5]: https://yaml.org/spec/1.2.2/

[^6]: https://circleci.com/blog/what-is-yaml-a-beginner-s-guide/

[^7]: https://ja.wikipedia.org/wiki/YAML

[^8]: https://www.redhat.com/en/topics/automation/what-is-yaml

[^9]: https://yaml.org/spec/history/2001-05-26.html

[^10]: https://learnxinyminutes.com/yaml/

[^11]: https://learning.sap.com/learning-journeys/build-side-by-side-extensions-on-sap-btp/explaining-json-yaml_db8975cf-695d-4ac2-8704-a9795116140c

[^12]: https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started

[^13]: https://qiita.com/kaityo256/items/e3884d0109223c324baf

[^14]: https://yaml.org/spec/1.0/

[^15]: https://yaml.org

[^16]: https://github.com/kaityo256/yaml_cv

[^17]: https://dev.to/morphzg/yaml-reference-and-important-concepts-21p9

[^18]: https://qiita.com/jerrywdlee/items/d0c36549136211937473

[^19]: https://developer.ibm.com/tutorials/yaml-basics-and-usage-in-kubernetes/

[^20]: https://discuss.kubernetes.io/t/history-changes-in-all-yaml-files-not-only-deplyments-or-stateful-sets/22750

