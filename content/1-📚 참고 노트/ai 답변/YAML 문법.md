---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# YAML 문법

이 페이지는 Ansible 플레이북(구성 관리 언어)이 표현되는 올바른 YAML 문법에 대한 기본 개요를 제공합니다.

YAML을 사용하는 이유는 XML이나 JSON 같은 다른 일반 데이터 형식보다 사람이 읽고 쓰기 쉽기 때문입니다. 또한 대부분의 프로그래밍 언어에서 YAML을 처리할 수 있는 라이브러리를 제공합니다.

플레이북 작성 방법을 실제로 확인하려면 [플레이북 작업하기](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks.html#working-with-playbooks)도 함께 참고하세요.

## YAML 기초

Ansible에서 거의 모든 YAML 파일은 리스트로 시작합니다. 리스트의 각 항목은 키/값 쌍(“해시” 또는 “딕셔너리”라고도 함)의 리스트입니다. 따라서 YAML에서 리스트와 딕셔너리를 작성하는 방법을 알아야 합니다.

모든 YAML 문서(Ansible과 관계없이)는 선택적으로 `---`로 시작하고 `...`로 끝날 수 있습니다. 이는 문서의 시작과 끝을 표시합니다.

- 리스트 항목: 동일한 들여쓰기 수준에서 `- `(대시 + 공백)로 시작하는 줄

```yaml
---
# 맛있는 과일 목록
- Apple
- Orange
- Strawberry
- Mango
...
```

- 딕셔너리: `키: 값` 형태 (`:` 다음에 공백 필수)

```yaml
# 직원 기록
martin:
  name: Martin D'vloper
  job: Developer
  skill: Elite
```


리스트의 항목이 딕셔너리이거나, 딕셔너리 값이 리스트일 수도 있습니다:

```yaml
# 직원 기록
- martin:
    name: Martin D'vloper
    job: Developer
    skills:
      - python
      - perl
      - pascal
- tabitha:
    name: Tabitha Bitumen
    job: Developer
    skills:
      - lisp
      - fortran
      - erlang
```


### 축약형(Flow) 컬렉션

흐름 컬렉션을 사용해 축약형으로 표현할 수 있습니다. Ansible 자체에서는 자주 사용하지 않습니다.

```yaml
---
martin: {name: Martin D'vloper, job: Developer, skill: Elite}
fruits: ['Apple', 'Orange', 'Strawberry', 'Mango']
```


### 불리언(Boolean) 값

불리언 값은 여러 형태로 지정할 수 있지만, 기본 yamllint 옵션과 호환되려면 소문자 `true`, `false`를 사용하는 것이 좋습니다.

```yaml
create_key: true
needs_agent: false
knows_oop: True
likes_emacs: TRUE
uses_cvs: false
```


### 여러 줄 값

값이 여러 줄에 걸치는 경우, `|` 또는 `>`를 사용합니다.

- Literal Block Scalar(`|`): 줄바꿈과 모든 공백을 포함

```yaml
include_newlines: |
            exactly as you see
            will appear these three
            lines of poetry
```

- Folded Block Scalar(`>`): 줄바꿈을 공백으로 대체

```yaml
fold_newlines: >
            this is really a
            single line of text
            despite appearances
```


줄바꿈을 일부만 유지하려면 빈 줄을 넣거나 `\n`를 사용할 수 있습니다.

```yaml
fold_some_newlines: >
    a
    b

    c
    d
      e
    f

fold_same_newlines: "a b\nc d\n  e\nf\n"
```


## 종합 예제

아래 예제는 Ansible과 직접 관련은 없지만, YAML 형식을 익히는 데 도움이 됩니다.

```yaml
---
# 직원 기록
name: Martin D'vloper
job: Developer
skill: Elite
employed: True
foods:
  - Apple
  - Orange
  - Strawberry
  - Mango
languages:
  perl: Elite
  python: Elite
  pascal: Lame
education: |
  4 GCSEs
  3 A-Levels
  BSc in the Internet of Things
```


## 주의 사항(Gotchas)

#### 매핑 키와 값에 콜론

스칼라 안에 `: `(콜론+공백)이 들어가면 매핑으로 인식되어 오류가 발생할 수 있습니다.

잘못된 예:

```yaml
foo: somebody said I should put a colon here: so I did
windows_drive: c:
```

올바른 예 (따옴표로 감싸기):

```yaml
foo: 'somebody said I should put a colon here: so I did'
windows_drive: 'c:'
```

또는 이중 따옴표:

```yaml
foo: "somebody said I should put a colon here: so I did"
windows_drive: "c:"
```


#### 이스케이프(escape)

이중 따옴표 안에서는 `\t`, `\n` 같은 이스케이프 문자를 사용할 수 있습니다.

```yaml
foo: "a \t TAB and a \n NEWLINE"
```


#### Ansible 변수

Ansible에서는 `{{ var }}` 형식으로 변수를 사용합니다. 값이 `{`로 시작하면 YAML이 딕셔너리로 인식하므로 전체 값을 따옴표로 감싸야 합니다.

```yaml
foo: "{{ variable }}"
foo: "{{ variable }}/additional/string"
foo2: "{{ variable }}\\backslashes\\are\\also\\special\\characters"
```


#### 특수 문자

스칼라의 시작 문자로 사용하면 안 되는 특수 문자: `[] {} > | * & ! % # \` @ ,` `?`, `:`, `-`는 스칼라 시작 문자로 허용되지만, 구현체마다 다를 수 있어 따옴표를 권장합니다.

#### Flow 컬렉션에서의 제한

흐름 컬렉션에서는 더 엄격한 규칙이 적용됩니다.

```yaml
flow mapping: { key: "you { should [ use , quotes here" }
```


#### 불리언과 숫자 문자열

YAML은 특정 문자열(예: `yes`, `no`, `1.0`)을 불리언 또는 숫자로 변환할 수 있습니다. 문자열로 유지하려면 따옴표를 사용하세요.

```yaml
non_boolean: "yes"
version: "1.0"
```

이제 Ansible 플레이북을 작성하기 위한 기본적인 YAML 문법을 이해하셨습니다. 질문이 있으면 [Ansible 커뮤니케이션 가이드](https://docs.ansible.com/ansible/latest/community/communication.html#communication)를 참고하세요.

<div style="text-align: center">⁂</div>

[^1]: https://docs.ansible.com/ansible/latest/index.html

