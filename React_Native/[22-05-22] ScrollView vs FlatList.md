## ScrollView 와 FlatList 비교

1. ScrollView: ScrollView는 내부의 하위 컴포넌트로서 스크롤할 수 있는 컴포넌트의 역할과 스크롤 가능한 컨테이너로 사용되는 내장형 React Native의 컴포넌트이다.

ScrollView는 모든 콘텐츠,즉 화면에 표시할 데이터를 모두 한번에 로드한다. 이것은 컴포넌트가 로드된 직후에 수행됩니다. 따라서 전체 콘텐츠(데이터 목록)가 모두 탑재되게 된다. 이제 이 데이터 목록에 많은 항목이 포함되어 있으면 자동으로 성능 문제가 발생한다. 따라서 화면에 표시할 항목이 100개 또는 1000개 있는 경우 ScrollView를 사용하는 것이 바람직하지 않다.

2. Flatlist: FlatList 컴포넌트는 스크롤 가능한 목록에 유사한 구조의 데이터를 표시하는 내장형 React Native의 컴포넌트이다. 현재 화면에 표시되고 있는 요소만 보여줍니다.

ScrollView와 달리 FlatList는 현재 화면에 표시되는 요소만 렌더링 한다(기본값: 10개 항목). 따라서 응용 프로그램의 성능에 영향을 미치지 않는다. FlatList 컴포넌트를 사용하여 많은 데이터 목록을 표시하는 것이 좋습니다.

https://www.geeksforgeeks.org/when-we-use-scrollview-over-flatlist-or-vice-versa/
