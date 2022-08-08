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
