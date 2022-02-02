## Computed Field

Computed Field를 GraphQL type에서 미리 계산한 값들을 쿼리 호출과 함께 넘길 수 있다.

이를 통해 본인임을 체크하거나, 팔로우중인지, 팔로우 숫자는 몇명인지 등 type에 정의된 변수에 계산된 값들을 넣어준다.

Computed Field는 resolver로 구현해야 하며, resolver에서는 모델명을 명시한 후 해당 필드를 정의한다.

GraphQL 호출이 오면 처음에는 DB에서 해당 값을 탐색하고,
DB에 없다면 resolver를 탐색해 Computed Field를 탐색한다.

> Computed Field 예시

```javascript
// GraphQL 부분
type User {
    id: Int!
    username: String!
    email: String!

    totalFollowing: Int!
    isMe: Boolean!
  }

// Resolver 부분
User : {
    totalFollowing:
        ({ id }: User, _, { client }) =>
            client.user.count({
                where: { followers: { some: { id } } },
            }),
    isMe:
        ({ id }: User, _, { loggedInUser }) => {
            if (!loggedInUser) return false;
            return id === loggedInUser.id;
        },
}

```
