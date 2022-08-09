## React code convention and best practices

https://levelup.gitconnected.com/react-code-conventions-and-best-practices-433e23ed69aa

### 1. linting 과 자동 formatter를 사용할 것

- 대부분의 툴들은 lint와 formatter를 제공한다.  
  코드를 깔끔하게 관리하기 위해 사용해야할 1순위 툴들이다.  
  eslint와 prettier를 추천.

### 2. import order를 사용할 것

- eslint 구성에 몇 가지 import order를 추가.  
  이렇게 하면 import가 항상 동일한 순서로 이루어지고  
  사전 정의된 규칙에 따라 그룹화된다.

```json
{
  "import/order": [
    2,
    {
      "newlines-between": "always",
      "groups": [
        "builtin",
        "external",
        "internal",
        "parent",
        "sibling",
        "index",
        "unknown",
        "object",
        "type"
      ],
      "alphabetize": {
        "order": "asc",
        "caseInsensitive": true
      },
      "pathGroups": [
        {
          "pattern": "react*",
          "group": "external",
          "position": "before"
        }
      ]
    }
  ]
}
```

## 명명 규칙

### 컴포넌트, 인터페이스, 타입 선언에서는 PascalCase를 사용

```tsx
// React component
const BannersEditForm = () => {
  ...
}

// Typescript interface
interface TodoItem {
  id: number;
  name: string;
  value: string;
}

// Typescript type alias
type TodoList = TodoItem[];
```

### 변수, 배열, 객체, 함수 등 자바스크립트 데이터 타입에는 camelCase 사용

```tsx
const getLastDigit = () => { ... }

const userTypes = [ ... ]
```

### 폴더와 컴포넌트 이외의 파일들에는 camelCase 사용, 컴포넌트 파일명은 PascalCase 사용

```
src/utils/form.ts
src/hooks/useForm.ts
src/components/banners/edit/Form.tsx
```

### 배럴의 사용 or 모아서 관리

배럴은 여러 모듈의 내보내기를 하나의 편리한 모듈로 롤업할 수 있음.

공유 구성 요소, 유틸리티, 도우미 기능 등과 같은 엔터티에 적용, 작업을 더 쉽게 하기 위해 배럴을 사용하여 배럴을 자동 생성할 수 있다.

```js
// 일반적인 방법: index.js 이용
export * from "./DropDown";
export * from "./TextBox";
export * from "./CheckBox";
export * from "./DateTimePicker";
export * from "./Slider";
// or
import { DropDown } from "./src/controls/DropDown";
import { TextBox } from "./src/controls/TextBox";
import { CheckBox } from "./src/controls/CheckBox";
import { DateTimePicker } from "./src/controls/DateTimePicker";
import { Slider } from "./src/controls/Slider";

// 배럴:
import {
  DropDown,
  TextBox,
  CheckBox,
  DateTimePicker,
  Slider,
} from "./src/controls";
or;
import * as Controls from "./src/controls/index";
```

### export default 자제

export default는 export하는 항목과 사용하는 곳에서의 이름이 연결되지 않을 수 있다.
이렇게 되면 유연성을 얻을 수 있지만 여러 개발자가 동일한 모듈을 다른 이름이나 비설명적인 이름으로 가져오면 문제가 발생하기 쉽다.

명명된 export는 명시적이므로 소비자가 원래 작성자가 의도한 이름으로 가져오고 모호성을 제거해야 한다.

```tsx
// ❌
export default MyComponent;

// ✅
export { MyComponent };
export const MyComponent = ...;
export type MyComponentType = ...;
```
