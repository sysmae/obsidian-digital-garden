---
{"publish":true,"created":"2025-07-28T22:37:29.244+09:00","modified":"2025-08-06T21:03:23.206+09:00","cssclasses":""}
---

#2025-07-28 22:37

Tags: [[3-🏷️ 태그/리액트]] [[3-🏷️ 태그/넥스트]]

# Radix UI
스타일 없는 접근성 중심의 컴포넌트 라이브러리
[[5-💎 영구 노트/헤드리스 UI]] 의 대표 라이브러리

Primitives Themes Colors Icons 4가지가 있으며 이 중에서 헤드리스 ui 관련 기능은 Primitives가 중심

코드 예시에서 볼 수 있듯이 필요한 요소들 구조가 잘 정의되어 있으므로 원하는 기능이 있을때 가져다가 사용하면 된다.
```jsx
<Dialog.Root>           // 상태 관리
  <Dialog.Trigger />    // 열기 버튼
  <Dialog.Portal>       // body 바깥 렌더링
    <Dialog.Overlay />  // 배경 오버레이
    <Dialog.Content>    // 실질 내용
      <Dialog.Title />
      <Dialog.Description />
      <Dialog.Close />
    </Dialog.Content>
  </Dialog.Portal>
</Dialog.Root>
```

Radix 를 제대로 사용하면 ARIA나 포커스관리, 키보드 지원 등 다양한 기능들을 자동으로 별도의 셋팅 추가 없이 사용할 수 있다.

## References
 [[5-💎 영구 노트/헤드리스 UI]]
 [[5-💎 영구 노트/Shadcn UI]]
 [[1-📚 참고 노트/ai 답변/Radix UI 핸드북]]
 https://www.radix-ui.com/primitives/docs/overview/getting-started