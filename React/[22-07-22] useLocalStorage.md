## useLocalStorage

보안적인 문제가 걸리지 않으며, 백엔드 없이 작동할 수 있는것들은 로컬에 저장하며 쓰는게 편하고 좋다.

type을 명시해주고, 데이터를 쉽게 넣고 빼는 형태로서 localstorage 훅을 제작과정을 공부하였다.

* 사용 예시
```tsx
const focusSoundsInitialState: FocusSoundsState = {
  isEnabled: false,
  mode: "compilation",
  compilationConfiguration: defaultFocusSoundsComilationConfiguration,
  configuration: {
    fire: 0.8,
    night: 0.2,
    seaside: 0.2,
  },
}

// inside of the component
const [state, setState] = usePersistentStorageValue(
  "focus-sounds",
  focusSoundsInitialState
)

```
스토리지를 사용하는 key값과 초기값을 설정해준 상태로 훅을 불러오면 된다.



훅에는 useState로 상태를 관리한다. 콜백함수를 통해 기존에 명시된 이름으로 스토리지가 있는지 없는지를 판단하고 없다면 초기화, 있다면 기존 값을 불러온다. 

```tsx
import { useEffect, useState } from "react"

import { persistentStorage } from "./persistentStorage"

export function usePersistentStorageValue<T>(key: string, initialValue?: T) {
  const [value, setValue] = useState<T>(() => {
    const valueFromStorage = persistentStorage.getItem(key)

    if (
      typeof initialValue === "object" &&
      !Array.isArray(initialValue) &&
      initialValue !== null
    ) {
      return {
        ...initialValue,
        ...valueFromStorage,
      }
    }

    return valueFromStorage || initialValue
  })

  useEffect(() => {
    persistentStorage.setItem(key, value)
  }, [key, value])

  return [value, setValue] as const
}
```

localstorage의 다른 기능들도 사용할 수 있도록 영구 저장소의 추상화를 진행한다.

```tsx
interface PersistentStorage {
  getItem(key: string): string | null
  setItem(key: string, value: any): void
}

class LocalStorage implements PersistentStorage {
  getItem(key: string) {
    const item = localStorage.getItem(key)

    if (item === null) return undefined

    if (item === "null") return null
    if (item === "undefined") return undefined

    try {
      return JSON.parse(item)
    } catch {}

    return item
  }
  setItem(key: string, value: any) {
    if (value === undefined) {
      localStorage.removeItem(key)
    } else {
      localStorage.setItem(key, JSON.stringify(value))
    }
  }
}

class MockStorage implements PersistentStorage {
  getItem() {
    return null
  }
  setItem() {}
}

export const persistentStorage = window?.localStorage
  ? new LocalStorage()
  : new MockStorage()
```


window 객체를 확인할 수 없는 상황이라면 임시의 스토리지를 반환하고
클라이언트 측이라면 window를통해 기능을 제공한다

서버사이드렌더링의 경우 window 체크를 typeof window === null로 진행하면 된다.

[Reference]
https://radzion.com/blog/hook-storage
