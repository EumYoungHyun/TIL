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

표에 나와있듯이 Nextjs의 주 목적은 SPA를 SSR방식으로 제공하는데 목적을 두고 있다.

<br />

### 웹 렌더링 과정

SPA에서 언급하는 렌더링 이전에 좀 더 기초적인 렌더링 개념은

### Hydration

웹 페이지를 렌더링하는 과정에서 React같은 CSR 라이브러리는 번들, 청크된 js파일들을 가져와 DOM을 렌더링한다.

웹페이지가 그려지는 순서는 HTML CSS
![Server Rendering](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/server+rendering.png "Server Rendering")

![Server Rendering](https://eumericano.s3.ap-northeast-2.amazonaws.com/dev/server+rendering.png "Server Rendering")

[Reference]

1. https://velog.io/@longroadhome/FE-SSRServer-Side-Rendering-%EA%B7%B8%EB%A6%AC%EA%B3%A0-SSGStatic-Site-Generation-feat.-NEXT%EB%A5%BC-%EC%A4%91%EC%8B%AC%EC%9C%BC%EB%A1%9C
2. https://velog.io/@huurray/React-Hydration-%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC
3. https://shlrur.github.io/develog/2019/02/14/rendering-on-the-web/
