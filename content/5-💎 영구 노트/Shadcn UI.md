---
{"publish":true,"created":"2025-07-30T00:24:26.823+09:00","modified":"2025-08-01T00:19:45.531+09:00","cssclasses":""}
---

#2025-07-28 22:37

Status: 

Tags:[[3-🏷️ 태그/넥스트]] [[3-🏷️ 태그/리액트]] [[3-🏷️ 태그/타입스크립트]]

# ShadcnUI
컴포넌트 라이브러리를 어떻게 구축할지에 대한 방법론을 제공하는 오픈 소스 툴킷
라이브러리가 아니라 실제 컴포넌트 코드

## 구조

- 구조 동작: [[5-💎 영구 노트/Radix UI]] Primitive
- 스타일: [[5-💎 영구 노트/테일윈드]], CVA
- 안정성: [[3-🏷️ 태그/타입스크립트]]

## 셋팅법
https://ui.shadcn.com/docs/installation/next
cli로 init 해서 초기 셋팅하고
```bash
npx shadcn@latest add button
```
이런 식으로 원하는 컴포넌트 가져옴 -> components/ui 폴더에 해당하는 컴포넌트 생김
필요시 커스텀해서 해당 컴포넌트 가져와서 사용
```tsx
import { Button } from "@/components/ui/button"

export function ButtonDemo() {
  return (
    <div className="flex flex-wrap items-center gap-2 md:flex-row">
      <Button>Button</Button>
    </div>
  )
}
```

## asChild
컴포넌트의 기본 태그를 사용자 정의 요소로 대체

기본적으로 컴포넌트는 자체 정의된 html 요소 렌더링
사용자가 전달한 자식 요소를 그대로 렌더링하면서도 컴포넌트의 접근성(accessibility), 이벤트 핸들링, 스타일링 등이 모두 적용되게 하고 싶을때 사용

의미론적 태그 유지하고, 스타일 동작 일관성 보장, 불필요한 래퍼 제거등의 효과 있다.
아래 예시: button대신 a요소 렌더링 -> 링크 의미 + Radix 접근성 기능 모두 챙김
```tsx
import { Button } from "@/components/ui/button";
import Link from "next/link";

<Button asChild>
  <Link href="/about">소개로 이동</Link>
</Button>
```
## References
[[5-💎 영구 노트/Radix UI]]
[[1-📚 참고 노트/ai 답변/Shadcn UI 핸드북 요약]]
https://ui.shadcn.com/docs