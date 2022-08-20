## Why React Re-Renders (번역)

원문: https://www.joshwcomeau.com/react/why-react-re-renders/

---

솔직히 이야기해서, 저는 리액트의 re-rendering 프로세스를 제대로 이해하지 못한 채
리액트 전문가로서 몇 년 동안 일해왔습니다. 😅  
이는 다른 많은 리액트 개발자들도 비슷할거라고 생각합니다. 우리는 그럭저럭 살아갈 수 있을 만큼 리액트를 이해하고 있지만,  
만약 당신이 리액트 개발자 그룹에게 "리액트에서 re-rendering을 유발하는 것은 무엇인가?" 라는 물음을 한다해도,
깊은 이해를 기반으로한 대답을 얻기 쉽지 않을것입니다.

이 주제에 대해서는 많은 오해가 있고, 많은 불확실성으로 이어질 수 있습니다.  
React의 렌더링 주기를 이해하지 못하면, useMemo와 useCallback등을 어떻게 활용해야할지 이해할 수 없습니다.

이 튜토리얼에서는 React의 re-render 타이밍과 이유에 대한 멘탈모델을 구축하고,  
React devtools를 사용하여 특정 component가 다시 리랜더링된 이유를 설명하는 방법에 대해 알아볼 예정입니다.

> 이 튜토리얼은 리액트의 초급을 넘은 개발자를 대상으로 합니다.  
> 리액트의 기초지식이 부족한 개발자라면 이 페이지를 즐겨찾기 해 놓고 다음에 다시 방문해주세요!

---

### The Core React Loop

기초적인 지식으로 이 글을 시작합니다.  
React의 모든 re-rendering은 state의 변화에서 시작됩니다.  
React에서 Component를 re-render시키는 유일한 트리거는 State입니다.

이런 질문이 생길지 모르겠습니다.  
'props값이 변할떄도, context가 변할때도 Component가 re-render되는것 아닌가요?'

여기서 말하는 state의 변화란 가장 최상위 부모 Component가 가진 state의 변동을 의미합니다.  
예를 들어보죠.

```jsx
import React from "react";

function App() {
  return (
    <>
      <Counter />
      <footer>
        <p>Copyright 2022 Big Count Inc.</p>
      </footer>
    </>
  );
}

function Counter() {
  const [count, setCount] = React.useState(0);

  return (
    <main>
      <BigCountNumber count={count} />
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </main>
  );
}

function BigCountNumber({ count }) {
  return (
    <p>
      <span className="prefix">Count:</span>
      {count}
    </p>
  );
}

export default App;
```

이 예시에는 3개의 Component(App, Counter, BigCounter)가 존재합니다.  
React에서는 모든 state 변수는 특정 component의 instance로 연결됩니다.  
이 예시에서는, Counter라는 컴포넌트 안에 count라는 하나의 state를 가지고 있죠.  
state(count)가 변할때마다, Counter 컴포넌트는 re-render됩니다.  
그리고 Counter가 re-render됨에 따라 BigCountNumber 컴포넌트도 re-rendering 될 것 입니다.

아래에는 increment라는 버튼을 누를때 어떻게 작동하는지에 대한 그림입니다.

![Counter re-rendering](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/re-render/counter1.jpg "Counter re-rendering")

이제붜 오해들을 없애봅시다.

큰 오해 1. 모든 앱이 state가 변할때 re-render된다.

일부 개발자들이 리액트의 모든 state 변화가 프로그램 전체의 렌더링을 강요한다고 믿지만 사실이 아닙니다.  
re-render는 state 및 하위 항목(있는 경우)을 소유한 component에만 영향을 미칩니다.
