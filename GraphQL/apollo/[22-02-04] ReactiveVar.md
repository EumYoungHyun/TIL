## ReactiveVar

리액트를 사용하던 중 ReactiveVar에 대해 알게됨.  
상태관리를 지원해주는 기능인데, apollo/client를 설치하면 그냥 사용할 수 있다.

사용법은 Recoil과 매우 비슷한데, 더 편한 느낌이 있다.

```javascript
// apollo.ts
// 선언

import { makeVar } from "@apollo/client";

export const isLoggedInVar = makeVar(false);
```

```javascript
// App.ts
//사용 및 변환
import { isLoggedInVar } from "apollo";

function App() {
    const isLoggedIn = useReactiveVar(isLoggedInVar);

    return (
        <div>
            <h2>{isLoggedInVar}</h2>
            <button onClick={() => isLoggedInVar(!isLoggedIn)}>
                Toggle Login
            </button>
        </div>
    );
}

export default App;
```

위의 예시는 login 여부를 체크하는 용도로 사용해보았다.  
기능이 적어 간단한 구조지만, 리덕스로 고생 좀 해본 개발자라면  
이를 더 간단하게 만들어준 ReactiveVar의 강력함을 한눈에 확인 할 수 있을것이다.

변환하는 부분은 함수로 따로 빼서 구현한다면,  
리덕스 처럼 dispatch나 이후 처리 등을 간단하게 구현할 수 있을 것 같다.

물론 이를 이용하기 위해 apollo를 설치하는건 많은 용량을 비효율적으로 사용하는 느낌이 있지만,  
어차피 지금 하고 있는 프로젝트는 apollo를 이용해 GraphQL을 컨트롤 하기 때문에,  
이를 이용해 프로젝트를 진행할 예정이다.
