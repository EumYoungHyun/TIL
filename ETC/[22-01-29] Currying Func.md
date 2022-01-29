## currying 함수

graphql 로그인 필터 함수 작성 중 학습했다.

    B라는 함수에서 에러정보가 담긴 A라는 함수를 실행시켰다.
    A가 실행되면 B의 기능을 더 이상 실행할 수 없다는 뜻이고,
    자연스레 B까지 함께 종료되어야 한다.
    (ex) 에러 발생에 따른 함수 종류 등...)
    일반적인 함수라면 A값을 리턴한다고 하면
    B에서 A의 리턴값을 확인한 후, 에러임이 판단될때 B를 종료하는 로직을 짜야한다.
    그렇다면 A함수에서 오류임을 알게된 시점에서 B 함수 를 종료할 순 없을까?

위 해답은 2가지 방법이 있다.

1. throw new Error 를 통해 A함수를 강제 종료하는 방식
2. 커링함수를 사용

커링함수는 함수를 리턴하는 함수이다.

```javascript
function foo() {
  return function bar() {
    console.log("blar blar blar");
  };
}

const foo = () => () => console.log("blar blar blar");
```

여기서 bar를 한번에 실행하고자 한다면 foo()()로 실행하면 된다.

커링함수를 쓰면 매개변수가 매핑되는 함수에서
자동으로 변수가 담겨지도록 응용할 수 있다.  
ex) GraphQL의 함수들은 Query.someFunc() 같이 명령으로 실행되는 함수가 아니고,
호출이 왔을때 Query.someFunc 함수에 (root, arguments, context, info) 매개변수들을
담아주면서 실행시킨다.
