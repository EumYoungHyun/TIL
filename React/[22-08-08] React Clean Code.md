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

### 컴포넌트 구조 예시

```tsx
// 1. import 최소화룰 추구
import React, { PropsWithChildren, useState, useEffect } from "react";

// 2. Types
type ComponentProps = {
  someProperty: string;
};

// 3. Styles  - css in js
//   but 성능 저하 이슈 고민
const Wrapper = styled("div")(({ theme }) => ({
  color: theme.palette.white,
}));

// 4. constant 관리
const SOME_CONSTANT = "something";

// 5. Component
function Component({ someProperty }: PropsWithChildren<ComponentProps>) {
  // 5.1 선언부
  const [state, setState] = useState(true);
  const { something } = useSomething();

  // 5.2 함수부
  function handleToggleState() {
    setState(!state);
  }

  // 5.3 useEffect
  // ❌
  React.useEffect(() => {
    // ...
  }, []);

  // ✅
  useEffect(() => {
    // ...
  }, []);

  // 5.5 추가사항
  const { property } = something;

  return (
    <div>
      {/* 코드를 더 명확하게 하기 위해 동일한 라인에서 닫히는 구조를 명확히 구분지어줄 것. */}
      {/* ❌ */}
      <div>
        <div>
          <p>Lorem ipsum</p>
          <p>Pellentesque arcu</p>
        </div>
        <p>Lorem ipsum</p>
        <p>Pellentesque arcu</p>
      </div>
      <div>
        <p>
          Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Pellentesque
          arcu. Et harum quidem rerum facilis est et expedita distinctio.
        </p>
        <p>Pellentesque arcu</p>
        <p>
          Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Pellentesque
          arcu. Et harum quidem rerum facilis est et expedita distinctio.
        </p>
      </div>

      {/* ✅ */}
      <Wrapper>
        <div>
          <p>Lorem ipsum</p>
          <p>Pellentesque arcu</p>
        </div>

        <p>Lorem ipsum</p>
        <p>Pellentesque arcu</p>
      </Wrapper>

      <div>
        <div>
          <p>
            Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
            Pellentesque arcu. Et harum quidem rerum facilis est et expedita
            distinctio.
          </p>

          <p>Pellentesque arcu</p>

          <p>
            Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
            Pellentesque arcu. Et harum quidem rerum facilis est et expedita
            distinctio.
          </p>
        </div>
      </div>
    </div>
  );
}

// 6. Exports
export { Component };
export type { ComponentProps };
```

### children을 추가할 땐 PropsWithChildren을 이용

많은 효용가치가 있는지는 모르겠으나 좀 더 명시적으로 작성할 수 있음

```tsx
import React, { PropsWithChildren } from "react";

type ComponentProps = {
  someProperty: string;
};

// ✅
function Component({
  someProperty,
  children,
}: PropsWithChildren<ComponentProps>) {
  // ...
}
```

### jsx, tsx내의 콜백함수가 1줄 이상이라면 함수로 분할

```tsx
// ❌
<button
  onClick={() => {
    setState(!state);
    resetForm();
    reloadData();
  }}
/>

// ✅
<button onClick={() => setState(!state)} />

// ✅
const handleButtonClick = () => {
  setState(!state);
  resetForm();
  reloadData();
}

<button onClick={handleButtonClick} />
```

### key prop으로 index를 사용하지 않을 것

[Reference]

1. https://medium.com/wesionary-team/using-index-as-a-key-is-an-anti-pattern-in-react-8e5db3aea3a9
2. https://atomizedobjects.com/blog/react/everything-you-need-to-know-about-react-keys/

```tsx
// ❌
const List = () => {
  const list = ["item1", "item2", "item3"];

  return (
    <ul>
      {list.map((value, index) => {
        return <li key={index}>{value}</li>;
      })}
    </ul>
  );
};

// ✅
const List = () => {
  const list = [
    { id: "111", value: "item1" },
    { id: "222", value: "item2" },
    { id: "333", value: "item3" },
  ];

  return (
    <ul>
      {list.map((item) => {
        return <li key={item.id}>{item.value}</li>;
      })}
    </ul>
  );
};
```

### div대신 fragment를 사용할 것

[Reference]

1. https://atomizedobjects.com/blog/react/react-fragment/
2. https://atomizedobjects.com/blog/react/react-fragment-vs-div/
3. https://atomizedobjects.com/blog/react/what-are-the-differences-between-react-fragment-longhand-vs-shorthand/

```tsx
// ❌
const ActionButtons = ({ text1, text2 }) => {
  return (
    <div>
      <button>{text1}</button>
      <button>{text2}</button>
    </div>
  );
};

// ✅
const Button = ({ text1, text2 }) => {
  return (
    <>
      <button>{text1}</button>
      <button>{text2}</button>
    </>
  );
};
```

### prop을 분해해서 사용

```tsx
// ❌
const Button = (props) => {
  return <button>{props.text}</button>;
};

// ✅
const Button = (props) => {
  const { text } = props;

  return <button>{text}</button>;
};

// ✅
const Button = ({ text }) => {
  return <button>{text}</button>;
};
```

---

## 관심사 분리

렌더링과 비즈니스 로직을 분리하면 Component 코드가 더 읽기 쉬워진다.  
더러운 코드의 경우 여러개의 hook과 useEffect들을 사용해 페이지/화면/컨테이너 Component에 적용시킨다.  
그러면 최종 코드는 읽을 수 없을 정도로 커지기 시작한다.

### 커스텀 훅

책임을 분리하려면 useEffect 또는 여러 useStates를 구성 요소에 직접 넣는 대신 custom hook을 생성하는 것이 좋다.

```tsx
// ❌
const ScreenDimensions = () => {
  const [windowSize, setWindowSize] = useState({
    width: undefined,
    height: undefined,
  });

  useEffect(() => {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    }

    window.addEventListener("resize", handleResize);
    handleResize();

    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return (
    <>
      <p>Current screen width: {windowSize.width}</p>
      <p>Current screen height: {windowSize.height}</p>
    </>
  );
};

// ✅
const useWindowSize = () => {
  const [windowSize, setWindowSize] = useState({
    width: undefined,
    height: undefined,
  });

  useEffect(() => {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    }

    window.addEventListener("resize", handleResize);
    handleResize();

    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return windowSize;
};

const ScreenDimensions = () => {
  const windowSize = useWindowSize();

  return (
    <>
      <p>Current screen width: {windowSize.width}</p>
      <p>Current screen height: {windowSize.height}</p>
    </>
  );
};
```

### 고차함수로서의 컴포넌트 컨트롤러

\*\* 고차함수 이용법에 대해 알아볼것

고차 함수로 사용되는 Component에 state나 customhook이 오버로드될 시점에 컨트롤러를 생성해야 합니다.

```tsx
// utils/wrap.ts
import { FC, createElement } from "react";

export const wrap =
  <Props extends object, ViewProps extends object>(
    View: FC<Partial<ViewProps>>,
    controllers: Array<(props: Props) => Partial<ViewProps> | null | void>
  ) =>
  (args: Props) =>
    createElement(
      View,
      ...controllers
        .map(
          (useController) => useController(args) as Partial<ViewProps> | null
        )
        .filter(Boolean)
    );

// useTodoControllers.ts
import { useMemo } from "react";

import { useTodoList } from "../../adapters/todoAdapter";
import { useUserData } from "../../adapters/userAdapter";

export const useTodoController = () => {
  const { user } = useUserData();

  const {
    todos: { isLoading, data },
  } = useTodoList(user.id);

  return useMemo(() => ({ isLoading, todos: data }), [isLoading, data]);
};

// TodoList.tsx
import { useTodoController } from "./useTodoController";
import { TodoItem } from "./components/TodoItem";

import { wrap } from "../../utils/wrap";

import { Todo, TodoList as TodoListType } from "../../../domain/struct/todo";

type TodoListProps = {
  todos: TodoListType;
  isLoading: boolean;
};

const TodoListComponent = ({ todos, isLoading }: TodoListProps) => {
  return isLoading ? (
    <>Loading...</>
  ) : (
    <>
      <ul className="tasks-list">
        {todos.map((todo: Todo) => (
          <TodoItem key={todo.id} todo={todo} />
        ))}
      </ul>
    </>
  );
};

export const TodoList = wrap(TodoListComponent, [useTodoController]);
```

### 큰 규모의 컴포넌트 기피

가능하다면 컴퓨터를 단위별로 최소화 해야한다.
조건부 렌더링이나 데이터 컬럼 정의, 많은 커스텀 훅을 사용할 수 있는 경우에 해당한다.

```tsx
// ❌
const SomeSection = ({ isEditable, value }) => {
  if (isEditable) {
    return (
      <Section>
        <Title>Edit this content</Title>
        <Content>{value}</Content>
        <Button>Clear content</Button>
      </Section>
    );
  }

  return (
    <Section>
      <Title>Read this content</Title>
      <Content>{value}</Content>
    </Section>
  );
};

// ✅
const EditableSection = ({ value }) => {
  return (
    <Section>
      <Title>Edit this content</Title>
      <Content>{value}</Content>
      <Button>Clear content</Button>
    </Section>
  );
};

const DetailSection = ({ value }) => {
  return (
    <Section>
      <Title>Read this content</Title>
      <Content>{value}</Content>
    </Section>
  );
};

const SomeSection = ({ isEditable, value }) => {
  return isEditable ? (
    <EditableSection value={value} />
  ) : (
    <DetailSection value={value} />
  );
};
```

### 그룹 값으로 묶일 수 있는 state들은 최대한 묶는다

```tsx
// ❌
const [username, setUsername] = useState<string>("");
const [password, setPassword] = useState<string>("");

// ✅
interface UserInfo {
  username: string;
  password: string;
}

const [user, setUser] = useState<UserInfo>({});
```

### boolean prop에 한해 축약 표현법을 사용한다.

default는 false로 지정, type은 ?: 지정자로 지정

```tsx
// ❌
<Form hasPadding={true} withError={true} />

// ✅
<Form hasPadding withError />
```

### 문자열 prop에는 중괄호 사용을 피한다

```tsx
// ❌
<Title variant={"h1"} value={"Home page"} />

// ✅
<Title variant="h1" value="Home page" />
```

### inline-style 기피

CSS 모듈, JSS, tailwind, styled-components 등과 같은 서드파티를 사용하거나 사용자 지정 후크를 만드는 것이 좋다.

```tsx
// ❌
const Title = (props) => {
  return (
    <h1 style={{ fontWeight: 600, fontSize: "24px" }} {...props}>
      {children}
    </h1>
  );
};

// ✅
const useStyles = (props) => {
  return useMemo(
    () => ({
      header: { fontWeight: props.isBold ? 700 : 400, fontSize: "24px" },
    }),
    [props]
  );
};

const Title = (props) => {
  const styles = useStyles(props);

  return (
    <h1 style={styles.header} {...props}>
      {children}
    </h1>
  );
};
```

### 이중연산자를 이용한 조건부 렌더링 사용

```tsx
const { role } = user;

// ❌
if (role === ADMIN) {
  return <AdminUser />;
} else {
  return <NormalUser />;
}

// ✅
return role === ADMIN ? <AdminUser /> : <NormalUser />;
```

### string 사용시 enum 단위로 작성

```tsx
// ❌
if (role === "admin") {
  return <AdminUser />;
}

// ✅
enum Roles {
  admin = "admin",
  basic = "basic",
}

if (role === Roles.admin) {
  return <AdminUser />;
}
```

### type의 별칭 사용

```tsx
export type TodoId = number;
export type UserId = number;

export interface Todo {
  id: TodoId;
  name: string;
  completed: boolean;
  userId: UserId;
}

export type TodoList = Todo[];
```

### 서드파티 라이브러리의 직접적인 사용 기피

서드파티 라이브러리를 직접적으로 사용하기 보다는
중앙 집적화된 곳에서 다시 내보내는 형태로 사용

```tsx
// src/lib/store.ts
export { useDispatch, useSelector } from "react-redux";

//src/lib/query.ts
export { useQuery, useMutation, useQueryClient } from "react-query";
```

### 직접적인 구현보다 추상화에 의존하도록 작성

```tsx
// ❌ 서드파티 라이브러리의 직접적인 사용 기피
import moment from "moment";

const updateProduct = (product) => {
  const payload = {
    ...product,
    // ❌ mement의 interface가 실행되는 순간에 묶여있는 코드
    updatedAt: moment().toDate(),
  };

  return await fetch(`/product/${product.id}`, {
    method: "PUT",
    body: JSON.stringify(payload),
  });
};

// ✅ helper function 처럼 함수 기능 밖에서 돕도록 추상화 레벨을 만들어야 한다.

// utils/createDate.ts
import moment from "moment";

export const createDate = (): Date => moment().toDate();

// updateProduct.ts
import { createDate } from "./utils/createDate";

const updateProduct = (product) => {
  const payload = {
    ...product,
    // ✅ using the abstracted helper function
    updatedAt: createDate(),
  };

  return await fetch(`/product/${product.id}`, {
    method: "PUT",
    body: JSON.stringify(payload),
  });
};
```

### 선언적 프로그래밍 (declarative programming)

```
// ❌ 명령형 프로그래밍: 배열을 for문 안에서 처리되도록 작성.
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
let sum = 0;

for (let i = 0; i < arr.length; i++) {
  sum += arr[i];
}

// ✅ 선언적 프로그래밍: 각 아이템을 따로 처리할 필요가 없음.
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
const sum = arr.reduce((acc, v) => acc + v, 0);
```

### 서술적 면수명

```tsx
// ❌ 한글자 변수명 기피
const n = "Max";
// ✅
const name = "Max";

// ❌ 줄임말 기피
const sof = "Sunday";
// ✅
const startOfWeek = "Sunday";

// ❌ 의미없는 이름 기피
const foo = false;
// ✅
const appInit = false;
```

### 많은 수의 파라미터 기피

기존의 클린코드 책에서는 lat, lon처럼 당연히 따라오는 변수를 제외하고는 2개 이상의 파라미터를 주의하고, 3개 이상부터는 거의 금기시 하였다.  
많은 수의 파라미터가 필요할때는 객체로서 관리하여, 왜 이 파라미터들이 필요한지, 순서에 맞게 잘 들어가는지를 체크해볼 필요가 있다.

```tsx
// ❌
function createPerson(firstName, lastName, height, weight, gender) {
  // ...
}

// ✅
function createPerson({ firstName, lastName, height, weight, gender }) {
  // ...
}

// ✅
function createPerson(person) {
  const { firstName, lastName, height, weight, gender } = person;
  // ...
}
```

### object 최대한 분리해서 사용

```tsx
// ❌
return (
  <>
    <div> {user.name} </div>
    <div> {user.age} </div>
    <div> {user.profession} </div>
  </>
);

// ✅
const { name, age, profession } = user;

return (
  <>
    <div> {name} </div>
    <div> {age} </div>
    <div> {profession} </div>
  </>
);
```
