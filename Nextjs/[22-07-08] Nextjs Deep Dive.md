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

Next js를 통해 만들어진 웹을 다운로드 받을때  
사용자가 특정 행동을 했을때만 실행되는 컴포넌트더라도 페이지에 import된 모든 항목을 처음 한번에 다운로드 하게 된다.

```tsx
import dynamic from "next/dynamic";

const Card = dynamic(() => import("./Card"), { ssr: false });
```

option을 통해 외부 API처럼 불필요한 SSR에서의 호출을 무효화 시킬 수도 있다.

![dynamic import](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/dynamic+import+network+check.png "dynamic import")
[Reference]

### Script and \_document

SSR을 최적화 하여 사용하기
외부 스크립트나 Web font같은 것들을 preload를 통해 build과정에서 추가할 수 있다. 이를 통해 크기가 큰 웹폰트나 외부 API 스크립트들을 따로 다운로드 받거나 헤더에 미리 추가하여 TTFB 시간을 획기적으로 단축할 수 있다.

```tsx
<Script src="some kakao api" strategy="beforeInteractive or lazyOnLoad" />
```

외부 API의 경우 바퀴의 재발명을 피하기 위한 경우가 많기 때문에 무거운 경우가 대다수,  
이를 굳이 처음부터 모두 로드할 필요는 없다. SSR의 경우 TTFB가 느려지는 이유가 되기도 함. 이를 Script strange로서 대처할 수 있다.
beforeInteractive: 인터렉션 이전에 api를 호출 Map API등에 적합
lazy on load : 모든 데이터, 소스들을 호출한 후에 로드를 시작함 페이스북 SDK같은 서비스에 적합

웹 폰트의 경우에도 외부 API를 통해 다운받지 않고 빌딩시 추가될 수 있도록 \_document파일에서 설정할 수 있다.

### fallback

static 빌드시 fallback 설정을 통해 다양한 방식을 제공할 수 있다.  
false: 지정되지 않은 path는 모두 404 페이지로
true: 지정되지 않은 path도 404 전환 x
'blocking': path는 빈배열로 전송(필수인지는 모르겠음)  
dynamic url을 static으로서 제공할 수 있다. 첫 방문시 static file을 제작하고 이후 방문시에는 static 을 그대로 리턴함,
지속적인 html제작으로 인한 용량 이슈 및 첫 방문시 성능 이슈를 확인해보아야 함.

![web fonts preload](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/web+font+ssr+preload.png "web fonts preload")

1. https://nomadcoders.co/ - next js
