---
{"publish":true,"created":"2025-08-05T05:17:02.221+09:00","modified":"2025-08-05T05:23:04.486+09:00","cssclasses":""}
---

#2025-08-05 05:17

Status: 

Tags: [[3-🏷️ 태그/확장자]]

# JSON (JavaScript Object Notation)
- 데이터 교환을 위한 경량, 텍스트 기반 데이터 교환 포맷
- 키, 문자열은 반드시 "" 로 감싸야함
- 키:값 의 형태로 형식 고정해서 어디서나 형식 믿을 수 있게
- [[5-💎 영구 노트/API]] 호출에 거의 반표준으로 굉장히 많이 쓰임

| 타입           | JSON 표기법       | 설명                  | 대응 언어 객체            |
| :----------- | :------------- | :------------------ | :------------------ |
| 객체(Object)   | `{}`           | 키-값 쌍의 집합           | dict (Python) 등     |
| 배열(Array)    | `[]`           | 순서 있는 값의 목록         | list (Python) 등     |
| 문자열(String)  | `"..."`        | UTF-8 인코딩 문자열       | str (Python) 등      |
| 숫자(Number)   | `123`, `3.14`  | 정수·실수 지원 (NaN·∞ 불가) | int·float (Python)  |
| 불리언(Boolean) | `true`/`false` | 논리값                 | True/False (Python) |
| 널(Null)      | `null`         | 값 부재                | None (Python)       |

## References
 [[5-💎 영구 노트/YAML]]
 [[5-💎 영구 노트/API]]

[[1-📚 참고 노트/ai 답변/JSON 핸드북]]