# What is the Nextjs

항상 Nextjs를 쓰면서도 왜 쓰는지에 대한 정확한 근거가 부족했다.  
SEO를 위해 사용했지만 왜 getProps, getPath같은 함수를 써야 하는지, SSG를 이용해 배포하지만 왜 이를 이용해야하는지 등
Nextjs에 대해 자세히 이해하고 이를 더 잘 활용할 방법에 대해 고민해보려 한다.

## Nextjs

SPA의 UX장점을 취하면서도 SEO등의 장점을 가진 SSR방식을 일부 취하는 프레임워크  
<br />
SSR 방식을 도입하기 위해 React 라이브러리에서 자체 제공하는 ReactDOMServer를 이용해 직접 설정을 구축하는 방법이 있지만
복잡한 환경과 설정으로 인해 높은 러닝커브, DX의 악화를 초래한다.

이를 프레임 워크로서 해법을 제시한 언어이다.

![SSR, CSR](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/SSR%2CCSR.png "SSR, CSR")

표에 나와있듯이 Nextjs의 주 목적은 SPA를 SSR방식으로 제공하는데 목적을 두고 있다. 즉, Nextjs은 완벽한 Server Render가 아니다. 웹이 발전하며 생겨난 CSR, SPA개념에 따라 SSR과 CSR의 장점을 어느정도 섞어놓은 프레임 워크일 뿐이다.

<br />

### 웹 렌더링 과정

![SSR의 렌더링](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/SSR+Rendering.png "SSR의 렌더링")

SPA에서 언급하는 렌더링 이전에 좀 더 기초적인 렌더링 개념은 HTML Parser와 CSS Parser를 이용해 Dom을 작성하고 레이아웃을 바꾸며 화면에 그리는 과정을 의미했다.

여기에 활용되는 지표들은 TTI, FCP, TTFP 등이 있다.  
이 지표들은 웹 렌더링의 성능을 표현하는데 사용되는 표현들이다.

- TTFB(Time To First Byte, 첫 번째 바이트까지의 시간):  
  링크를 클릭한 후 처음으로 들어오는 콘텐츠 비트 사이의 시간을 나타낸다.  
  이 수치는 서버의 성능을 나타내는 수치라고도 여겨질 수 있으며, SEO에도 영향을 준다.
- FCP/FP (First Contentful Paint, 컨텐츠 표시 시점):  
  하얀 화면이 아닌 뭔가 보이긴 하는 시점이라는 의미. 유저들이 페이지를 마주치자마자 다른 상호작용을 trigger하지 않는다는 가정하에, 이 지표가 저조하다면 가장 큰 불편함을 초래함. UX관점에서 SSR이 좋은 이유 중 하나.
- TTI (Time To Interactive, 페이지 상호작용):  
  페이지가 상호작용 가능하게 될 때까지의 시간(이벤트 발생 등). 이 지표가 저조하다면 클릭을 해도 반응이 없는 상황이 발생.

하지만 React, Vue같은 SPA의 등장 이후 렌더링이란 JS 코드화 된 DOM요소를 로드하는 과정의 의미가 포함되었다.

![CSR](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/CSR.png "CSR")
![SSR](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/SSR.png "SSR")

SPA의 개념에서의 렌더링에도 TTFB, FCP, TTI등은 중요한 지표가 된다.

### hydrate (수화)

수화란 직역하면 몸에 물을 보충하는 것을 뜻한다.  
리액트에서 기본적으로 사용되는 render메소드는 DOM에 리액트 컴포넌트를 렌더링 하는 메소드이다.

```javascript
ReactDOM.render(element, container[, callback])
```

render메소드에 추가된 매개변수들 container DOM에 element를 렌더링 작업을 진행한다. 이 과정에서 container의 children으로 element를 넣어주는데 기존에 렌더링이 되어있다면 렌더링은 건너뛰고 업데이트만 진행하게 된다. 마지막으로 이 과정들이 완료되고 난 후 콜백 함수를 실행하며 함수가 종료된다.  
이를 한 문장으로 정리한다면

> ReactDom의 render메소드는 컴포넌트를 렌더링한 후에 콜백을 실행한다.

그리고 React는 render와 비슷한 hydrate라는 메소드도 제공한다  
hydrate는 render메소드와 동일한 매개변수를 수용한다.  
하지만 render와는 다르게 렌더링은 하지 않고 이벤트 핸들러만 붙여주는 역할을 한다.

```javascript
ReactDOM.hydrate(element, container[, callback])
```

SSR을 하는 경우에는 hydrate로 콜백만 붙여야 한다.  
SSR을 거쳐 이미 마크업이 채워져있는 경우에는 render를 사용하지 않고 hydrate를 이용해 성능적 손해를 피할 수 있다.

웹 페이지를 요청하는 순간부터 사용자에게 도착하는 과정을 보면 Rehydration의 과정을 더 쉽게 이해할 수 있다.

![Rehydration](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/rehydration.png "Rehydration")

최초의 웹 페이지 요청 이후 서버는 HTML을 반환해준다.  
이때 반환해주는 HTML은 Dehydrate된 (인터렉션이 안되는) DOM이다.  
veiw에 DOM이 그려지고, JS신호가 도착하고 실행하면서 리액트가 정적인 HTML과 store를 동적인 리액트 컴포넌트 트리와 store로 변환하는 과정이 일어나는데, 이걸 (Re)hydrate라고 한다.  
이때 hydrate 대신 render를 사용하면 화면을 다시 그리는 상황이 초래된다. SSR으로 웹을 제작할때 이 점을 주의하며 코딩을 해야 한다.

> 리액트에서 서버사이드 렌더링으로 만들어진 수분이 없는 정적인 HTML과 State로부터 수분을 보충하는 과정(동적인 상태로 변화)인 hydrate가 일어난다.

### Server Rendering

Server Rendering은 요청된 페이지에 대한 전체 HTML파일을 서버측에서 생성한다.  
browser에 전달되기 전에 페이지를를 모두 제작하기 때문에, client-side에서 data를 가져오거나 추가적인 round-trip이 발생하지 않는다.

![Server Rendering](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/server+rendering.png "Server Rendering")

Server Rendering에서는 일반적으로 FP(First Paint)와 FCP(First Contentful Paint)가 빠르다.  
페이지의 렌더링과 로직 연결을 서버에서 제작하므로 client측에 많은 연산을 요청하지 않게 된다.
이 덕에 빠른 TTI(Time to Interactive)를 얻을 수 있는 장점이 있다.
JS로 인해 생기는 로딩의 영향이 매우 낮아지며 First-party JS cost가 줄어들기 때문에 클라이언트 측에서 더 많은 여유 자원을 사용할 수 있게 되지만,  
서버에서 모든 HTML을 만들어 보내다 보니 TTFB(Time to First Byte)가 느려질 수 있다는 단점이 생긴다. 따라서 SEO에 악영향을 끼칠 수 있다.

Server Rendering은 서비스가 오직 text와 link로만 이루어진 페이지에 적절하다. 거의 모든 장치 및 네트워크 상태에서 잘 작동하며, streaming document parsing 같은 브라우저 최적화에 활용할 베이스가 될 수 있다.

Server Rendering과 client rendering 사이에서 어떤 방식이 올바른 application 방식이냐에 대한 오랜 논쟁이 있었고, 결론은 항상 같았다. '반영하고자 하는 페이지에 적합한 기술을 사용할 것' 그리고 이 해답은 hybrid rendering을 통해 이를 선택적으로 변경할 수 있다.  
Netflix는 비교적 정적인 page는 server rendering을 사용하고, 많은 상호작용이 많이 필요한 page에서는 JS를 prefetch하여 많은 client rendering이 필요한 page가 빨리 loading 되도록 제작되었다.

React나 React베이스의 Nextjs 등 인기있는 솔루션들은 hydration기법을 이용해 SSR과 CSR의 장점을 혼합한 형태로 서비스를 제공하므로 그 특징을 이해하고 잘 사용할 줄 알아야 한다.

### Static Rendering

Static rendering은 build 시에 일어나며, 빠른 FP(First Paint), FCP(First Contentful Paint), TTI(Time To Interactive)를 제공한다.  
Static Rendering은 각 URL에 대한 HTML 파일을 미리 생성해 놓기 때문에,
Server rendering과는 다르게, page의 HTML을 즉석에서 생성 할 필요가 없으며 일관성 있게 빠른 TTFB(Time To First Byte)를 얻을 수 있다.  
Response인 HTML을 미리 생성해 놓으면, static render는 여러 CDN에 배포함으로써 edge-caching의 이점을 가져갈 수 있다.

![Static Rendering](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/static+rendering.png "Static Rendering")

Static rendering는 가능한 모든 URL에 대해 HTML 파일들을 미리 만들어 놓아야 한다는 단점을 가지고 있다.  
이는 URL이 무엇을 뜻하는지 예측하기 힘들거나 고유 페이지가 많은 site의 경우 여러 문제가 있을 수 있습니다.

static rendering과 prerendering 사이의 차이를 이해하는 것이 중요하다.  
Static rendering page는 client-side JS를 많이 실행하지 않아도 interactive한 반면, prerendering은 First Paint(FP) 혹은 SPA의 First Contentful Paint(FCP)를 향상시킵니다. Prerendering은 page가 interactive 할 수 있도록 content를 client-side에서 생성합니다.

### Server Rendering vs Static Rendering

Server Rendering은 Silver bullet이 아니다. 동적인 특성으로 인해 compute overhead가 발생하기 쉽고 많은 server rendering solution들은 일찍 flush하지 않으며 TTFB를 지연시키거나 전송하는 data에 대해 중복 요청을 할 수 있다. Server rendering을 올바르게 사용하기 위해서는 component caching, memory 사용량 관리, memoization 기법 적용 등등 적합한 방식을 찾거나 구축해야 합니다.

Server rendering은 각 URL에 대한 HTML을 on-demand 방식으로 요청시 생성하므로 static rendering으로 생성된 contents를 제공하는 것보다 느리다.  
이를 보완하기 위해 server rendering + HTML caching 으로 server render time을 줄이는 방법이 있다.  
server rendering의 장점은 static rendering보다 좀 더 “live” 한 data를 사용하여 좀 더 완벽한 응답을 만든다는 점이다.  
즉, static rendering으로 개인화가 필요한 page를 만들기 쉽지 않다.

### SEO 고려사항

[기본 용어 설명]
Rendering

- SSR(Server Side Rendering): Client side 혹은 범용 application을 서버에서 HTML로 rendering 하는 방식.
- CSR(Client-Side Rendering): Browser에서 DOM을 사용해서 Application을 rendering 하는 방식.
- Rehydration: Server에서 rendering 한 HTML DOM 트리와 data를 client에서 재사용하도록 JavaScript 뷰를 부팅하는 과정.
- Prerendering: Build 시에 client-side application을 실행해서 초기 상태를 static HTML로 저장 하는 방식.

Performance

- TTFB(Time To First Byte): 링크를 클릭했을 때와 해당 링크 contents의 첫 번째 bit가 들어왔을 때 사이의 시간.
- FP(First Paint): 사용자가 첫 번째 pixel을 볼 수 있는 시간.
- FCP(First Contentful Paint): Article body 같은 요청한 내용이 보이는 시간.
- TTI(Time To Interactive): Page가 interactive(event 연결 등) 하게 된 시간.

[Reference]

1. https://velog.io/@longroadhome/FE-SSRServer-Side-Rendering-%EA%B7%B8%EB%A6%AC%EA%B3%A0-SSGStatic-Site-Generation-feat.-NEXT%EB%A5%BC-%EC%A4%91%EC%8B%AC%EC%9C%BC%EB%A1%9C
2. https://velog.io/@huurray/React-Hydration-%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC
3. https://shlrur.github.io/develog/2019/02/14/rendering-on-the-web/
4. https://tech.junhabaek.net/%EC%9B%B9-%EB%A0%8C%EB%8D%94%EB%A7%81%EC%9D%98-%EC%9C%A0%ED%98%95-1-only-ssr-static-ssr-b10c3916fb09
5. https://simsimjae.tistory.com/389
6. https://velog.io/@jwhan/%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8-%EC%82%AC%EC%9D%B4%EB%93%9C-%EB%A0%8C%EB%8D%94%EB%A7%81CSR-%EC%84%9C%EB%B2%84-%EC%82%AC%EC%9D%B4%EB%93%9C-%EB%A0%8C%EB%8D%94%EB%A7%81SSR-%EC%A0%95%EC%A0%81-%EC%82%AC%EC%9D%B4%ED%8A%B8-%EC%83%9D%EC%84%B1SSG-%EC%9D%98-%EC%A0%95%EC%9D%98%EC%99%80-%EC%9E%A5%EB%8B%A8%EC%A0%90
