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
- FCP/FP (First Contentful Paint, 컨텐츠 표시 시점): 하얀 화면이 아닌 뭔가 보이긴 하는 시점이라는 의미. 유저들이 페이지를 마주치자마자 다른 상호작용을 trigger하지 않는다는 가정하에, 이 지표가 저조하다면 가장 큰 불편함을 초래함. UX관점에서 SSR이 좋은 이유 중 하나.
- TTI (Time To Interactive, 페이지 상호작용): 페이지가 상호작용 가능하게 될 때까지의 시간(이벤트 발생 등). 이 지표가 저조하다면 클릭을 해도 반응이 없는 상황이 발생.

하지만 React, Vue같은 SPA의 등장 이후 렌더링이란 JS 코드화 된 DOM요소를 로드하는 과정의 의미가 포함되었다.

![CSR](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/CSR.png "CSR")
![SSR](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/SSR.png "SSR")

SPA의 개념에서의 렌더링에도 TTFB, FCP, TTI등은 중요한 지표가 된다.

### Server Rendering vs Static Rendering

![Server Rendering](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/server+rendering.png "Server Rendering")

[Reference]

1. https://velog.io/@longroadhome/FE-SSRServer-Side-Rendering-%EA%B7%B8%EB%A6%AC%EA%B3%A0-SSGStatic-Site-Generation-feat.-NEXT%EB%A5%BC-%EC%A4%91%EC%8B%AC%EC%9C%BC%EB%A1%9C
2. https://velog.io/@huurray/React-Hydration-%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC
3. https://shlrur.github.io/develog/2019/02/14/rendering-on-the-web/
4. https://tech.junhabaek.net/%EC%9B%B9-%EB%A0%8C%EB%8D%94%EB%A7%81%EC%9D%98-%EC%9C%A0%ED%98%95-1-only-ssr-static-ssr-b10c3916fb09
