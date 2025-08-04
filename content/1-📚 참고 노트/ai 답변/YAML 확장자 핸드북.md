---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# YAML 확장자 핸드북

## 핵심 요약

YAML 파일의 공식 권장 확장자는 `.yaml`이며, 역사적·실용적 이유로 `.yml`도 널리 사용된다. 확장자 선택 자체가 내용에 영향을 주지 않으나 일관성과 표준 준수 차원에서 `.yaml` 사용이 권장된다.

## 1. YAML 확장자 개요

YAML(YAML Ain't Markup Language)은 가독성이 높은 데이터 직렬화 포맷이다.

- 공식 확장자: `.yaml` (2006년 권고)[^1]
- 대체 확장자: `.yml` (Windows 8.3 호환성 등 역사적 이유)

**핸드북 포인트**

- 확장자는 파서가 내부 구조를 해석하는 데 관여하지 않음
- 조직 내 일관된 확장자 사용 권장


## 2. 확장자 선택의 배경과 권장 기준

1. **표준 준수**
    - YAML 공식 FAQ: “Please use `.yaml` when possible.”[^2]
2. **역사적 이유**
    - 초기 Windows 파일명 제약으로 `.yml` 사용 확산
3. **실용적 차이 없음**
    - 파싱·처리 관점에서 `.yaml`과 `.yml` 동일

**핸드북 팁**

- 신규 프로젝트: `.yaml` 채택
- 레거시 환경: 내부 정책에 따라 `.yaml`→`.yml` 통일


## 3. 파일 구조 및 문법 관례

### 3.1 문서 구분

- 시작: `---`
- 종료(선택): `...`


### 3.2 기본 요소

| 구조            | 표현 예시        |                  |
| :------------ | :----------- | ---------------- |
| 맵(mapping)    | `key: value` |                  |
| 시퀀스(sequence) | `- item`     |                  |
| 다중 문장 문자열     | `            | `(리터럴), `>`(폴디드) |

### 3.3 들여쓰기

- 탭 금지, 공백(스페이스)만 사용
- 레벨별 2칸 또는 4칸 일관되게 사용


### 3.4 주석

- `#` 뒤 텍스트는 무시
- 라인별 주석만 지원


## 4. 주요 사용 사례

- 구성(configuration) 파일: Kubernetes, Ansible, CircleCI 등
- 데이터 직렬화(serialization) 및 교환
- 문서 템플릿: 다중 문서 스트림 지원


## 5. 확장자 관리 방안 및 실전 팁

1. **정책 수립**: 조직 내 가이드라인 문서화
2. **자동화 도구 설정**: 린터(linter), CI 파이프라인에서 확장자 검사
3. **교육 및 코드 리뷰**: 확장자 일관성 강조

## 6. 부록: 주요 참고 자료

1. YAML 위키피디아 – 확장자 권고 사항[^1]
2. YAML 공식 FAQ – 확장자 사용 지침[^2]

---

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/YAML

[^2]: https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html

[^3]: https://blog.devops.dev/understanding-yaml-a-beginners-guide-with-real-world-use-cases-62b7985dc09a

[^4]: https://dev.to/opensauced/open-source-abcs-yaml-yml-333m

[^5]: https://circleci.com/blog/what-is-yaml-a-beginner-s-guide/

[^6]: https://www.ibm.com/think/topics/yaml

[^7]: https://www.redhat.com/en/topics/automation/what-is-yaml

[^8]: https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started

[^9]: https://www.devopsschool.com/blog/what-is-yaml-and-how-it-works-an-overview-and-its-use-cases/

[^10]: https://www.reddit.com/r/ansible/comments/cnsspu/yml_vs_yaml_file_extention/

[^11]: https://docs.lokalise.com/en/articles/1400853-yaml

[^12]: https://www.reddit.com/r/explainlikeimfive/comments/nv49ug/eli5_what_are_yaml_files_used_for/

[^13]: https://stackoverflow.com/questions/22268952/what-is-the-difference-between-yaml-and-yml-extension

[^14]: https://www.home-assistant.io/docs/configuration/yaml/

[^15]: https://orhanergun.net/yaml-101-mastering-syntax-exploring-use-cases

[^16]: https://github.com/readthedocs/readthedocs.org/issues/7460

[^17]: https://yaml.org/spec/1.2.2/

[^18]: https://www.freecodecamp.org/news/what-is-yaml-the-yml-file-format/

[^19]: https://spacelift.io/blog/yaml

[^20]: https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml

