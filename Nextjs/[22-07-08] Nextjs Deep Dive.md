# Deep Dive in Nextjs

### midddleware

next js를 이용해 백엔드로서 사용할 때
request -> server -> log() -> auth() -> controller() -> DB
등의 과정으로 데이터를 반환한다.

백엔드에서 log, auth인증 같은 middleware들을 사용한다.
이를 next에서 활용하면 page를 이동할 때마다 실행되는 middleware를 만들 수 있다

![middleware](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/middleware.png "middleware")

page 단위로 \_middleware.ts 파일을 만들어 middleware가 구동되는 시점을 체크하였다.

<img src="https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/home.png" height="450" />

![global middleware result](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/home+result.png "global middleware result")

<img src="https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/slide+page.png" height="450" />

![slide middleware result](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/slide+result.png "slide middleware result")

미들웨어에서는 파라미터로 request 정보와 event 정보를 확인할 수 있다.

```tsx
import type { NextRequest, NextFetchEvent } from "next/server";

export function middleware(req: NextRequest, event: NextFetchEvent) {
  // ... doing some middelwares do
}
```

미들웨어에서 리퀘스트와 호출된 이벤트 등을 확인할 수 있으며 request에서는 추가적으로 nextjs가 판단하기에 봇인지 아닌지도 확인할 수 있다.

### dynamic import

[Reference]

1. https://nomadcoders.co/ - next js
