## type, resolver

> GraphQL을 사용하기 위해선 type들과 resolver들을 정의하여아 한다.

typeDef는 Query나 Mutation이 존재함을 정의한다.  
존재함을 가리는 여부라는 점 에서 분기점을 대신하는 존재같다.  
REST의 controller정도로 생각하면 될 것 같다.

resolver는 root, arguments, context, info 4가지 항목으로 이루어져 있는 기능이 담기는 곳, 즉 REST의 service로직이 담기는 곳이다.

resolver에서 사용되는 4가지 params

1. root는 computed field 기능을 호출할 때 호출했던 곳의 내용을 담고 있다. (parent라고도 함)

2. arguments는 매개변수를 통해 전달된 값들을 의미한다.

3. context는 모든 resolver에서 접근 가능한 정보를 넣는 object다.
   로그인 여부를 체크한 뒤 로그인 유저 정보를 넣거나
   client 모듈을 넣는등 resolver들에서 공통적으로 사용될
   다양한 정보나 기능을 넣으면 된다.

4. info는 사용자가 지정한 정보를 담고 있다.
