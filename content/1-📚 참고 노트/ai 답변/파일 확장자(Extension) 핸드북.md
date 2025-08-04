---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 파일 확장자(Extension) 핸드북

**핵심 요약:**
파일 확장자는 파일명 뒤에 붙는 접미사로, 파일 내부의 데이터 구조를 식별하고 운영체제 및 애플리케이션이 적절히 처리할 수 있도록 돕는다. 올바른 확장자 사용은 호환성·보안·효율성 측면에서 필수적이다.

## 1. 개념 및 목적

파일 확장자란 파일명 끝에 마침표(.)와 함께 붙는 1~4자 알파벳·숫자 문자열로, 파일의 **데이터 형식** 및 **의도된 용도**를 나타낸다[^1].

- **목적:**

1. 운영체제에 적합한 프로그램 연계
2. 사용자에게 파일 유형 직관 제공
3. 보안상 잠재적 위험 식별


## 2. 구조 및 분류

### 2.1 구조

- 기본 구조: `<파일명>.<확장자>`
- 다중 확장자: `archive.tar.gz` → `tar`(아카이브), `gz`(gzip 압축)[^1]
- 변형 예: `test.3.2.csv` (최종 마침표 뒤만 확장자로 인식)


### 2.2 분류

| 분류 | 확장자 예시 | 설명 |
| :-- | :-- | :-- |
| 문서 | .txt, .docx, .pdf | 일반 텍스트·워드·PDF 문서[^2] |
| 이미지 | .jpg, .png, .gif | 비손실·손실 압축 이미지[^2] |
| 오디오 | .mp3, .wav, .aac | 스트리밍·편집용 오디오[^2] |
| 비디오 | .mp4, .avi, .mkv | 멀티미디어 컨테이너 포맷[^2] |
| 압축 | .zip, .rar, .tar.gz | 아카이브 및 압축 파일[^2] |
| 실행 | .exe, .bat, .sh | 실행 스크립트·바이너리[^3] |

## 3. 운영체제별 처리 방식

### 3.1 Windows

- 확장자 기반 매핑: `.docx` → Word, `.xlsx` → Excel 자동 연계[^4]
- **기본 숨김 설정**으로 사용자에게 확장자 숨김 → 보안 위험 증가 가능
- 변경 시 경고 팝업


### 3.2 macOS·Linux

- **MIME 타입** 및 파일 헤더 기반 인식[^5]
- 확장자 미존재 시에도 내부 메타데이터로 파일 처리
- 확장자 변경 시 경고 및 재연결 시도


## 4. 활용 및 관리

### 4.1 유용성

- **정확한 프로그램 연결:** 불필요한 오류 방지
- **파일 분류·조직화:** 자동화 스크립트·백업 정책에 활용


### 4.2 보안

- **더블 확장자 주의:** `invoice.pdf.exe` 형태 악성코드 위장[^3]
- **알려지지 않은 확장자 검사:** 미확인 파일 실행 전 확인


### 4.3 변경 및 사용자 설정

- Windows: 파일 탐색기 → 보기(View) → ‘파일 이름 확장명’ 체크
- macOS: Finder → 환경설정 → ‘모든 파일 확장명 보기’ 체크


## 5. 심화 주제

### 5.1 커스텀 확장자

- 소프트웨어 개발 시 고유 포맷 식별용 정의 가능
- `manifest.json` 등 웹브라우저 확장 파일의 핵심 메타데이터[^6]


### 5.2 메타데이터와 확장자

- 일부 파일 포맷은 확장자 없이도 내부 헤더로 식별 가능
- 모바일 앱 패키징(`.apk`) 등 복합 구조 포맷

**핸드북 사용법 요약:**

1. 파일 생성 시 올바른 확장자 지정
2. 운영체제별 확장자 표시 옵션 활성화
3. 미확인 확장자 파일 실행 전 반드시 안전성 검토
4. 개발 및 배포 시 고유 확장자로 포맷 차별화

---

[^1] 웹:1
[^3] 웹:3
[^4] 웹:4
[^2] 웹:5

<div style="text-align: center">⁂</div>

[^1]: https://www.techtarget.com/whatis/definition/file-format

[^2]: https://www.ionos.com/digitalguide/server/know-how/file-extension-overview/

[^3]: https://www.oncrashreboot.com/computer-literacy-study-guide/file-management-and-compression-essentials/understanding-file-extensions/

[^4]: https://en.wikipedia.org/wiki/Filename_extension

[^5]: https://www.howtogeek.com/356448/what-is-a-file-extension/

[^6]: https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Anatomy_of_a_WebExtension

[^7]: https://file-extensions.com

[^8]: https://www.lenovo.com/ca/en/glossary/file-extension/

[^9]: https://www.indeed.com/career-advice/career-development/what-is-file-extension

[^10]: https://www.lenovo.com/us/en/glossary/file-extension/

[^11]: https://www.lenovo.com/my/en/glossary/file-extension/

[^12]: https://www.solvusoft.com/en/file-extensions/file-extension-usage/

[^13]: https://edu.gcfglobal.org/en/basic-computer-skills/understanding-file-extensions/1/

[^14]: https://en.wikipedia.org/wiki/File_format

[^15]: https://www.malwarebytes.com/ja/blog/news/2013/12/file-extensions-2

[^16]: https://support.microsoft.com/en-us/windows/common-file-name-extensions-in-windows-da4a4430-8e76-89c5-59f7-1cdbbc75cb01

[^17]: https://www.komprise.com/glossary_terms/file/

[^18]: https://www.lifewire.com/what-is-a-file-extension-2625879

[^19]: https://www.sciencedirect.com/topics/engineering/file-extension

