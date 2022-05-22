## QueryClient로 캐시 관리하기

React Query를 사용하기 위해서는 App.tsx 파일에 QueryClient를 등록하게 된다.

```javascript
import { QueryClient, QueryClientProvider } from "react-query";

const queryClient = new QueryClient();
    .
    .
    .
const App () =>
    <QueryClientProvider client={queryClient}>
        .
        .
        .
    </QueryClientProvider>
```

여기서 사양되는 QuertClient는 React Query에서 가장 상위에 존재하는 Query객체이다.

QueryClient는 캐시, 쿼리들 이외에도 다양한 것들을 직접 관리한다.

이를 이용하여 다양한 쿼리들의 refetching을 QueryClient를 통해 쉽게 관리할 수 있다.

```javascript
const {
  isLoading: nowPlayingLoading,
  data: nowPlayingData,
  refetch: nowPlayingRefetch,
  isRefetching: isNowPlayingRefetching,
} = useQuery("nowPlaying", moviesApi.nowPlaying);

const {
  isLoading: trendingLoading,
  data: trendingData,
  refetch: trendingRefetch,
  isRefetching: isTrendingRefetching,
} = useQuery("trending", moviesApi.trending);

const {
  isLoading: upcomingLoading,
  data: upcomingData,
  refetch: upcomingRefetch,
  isRefetching: isUpcomingRefetching,
} = useQuery("upcoming", moviesApi.upcoming);

const refreshing =
  isNowPlayingRefetching || isTrendingRefetching || isUpcomingRefetching;

const onRefresh = async () => {
  nowPlayingRefetch();
  trendingRefetch();
  upcomingRefetch();
};
```

refresh코드에서 나열을 정리해보았다.  
refresh한번에 여러가지 쿼리들을 리프레시 해주어야 했고,  
그만큼 코드가 더러워졌다.  
해당 코드를 ReactClient를 통해 쉽게 리팩토링 해보았다.

```javascript
const queryClient = useQueryClient();

const {
  isLoading: nowPlayingLoading,
  data: nowPlayingData,
  isRefetching: isNowPlayingRefetching,
} = useQuery(["movies", "nowPlaying"], moviesApi.nowPlaying);

const {
  isLoading: trendingLoading,
  data: trendingData,
  isRefetching: isTrendingRefetching,
} = useQuery(["movies", "trending"], moviesApi.trending);

const {
  isLoading: upcomingLoading,
  data: upcomingData,
  isRefetching: isUpcomingRefetching,
} = useQuery(["movies", "upcoming"], moviesApi.upcoming);

const refreshing =
  isNowPlayingRefetching || isTrendingRefetching || isUpcomingRefetching;

const onRefresh = async () => {
  queryClient.refetchQueries(["movies"]);
};
```

각 쿼리에서 값을 불러올 필요가 없어졌고,  
쿼리키 개념을 추가해 movies를 포함한 모든 쿼리들을 한번에 리프레싱 할 수 있게되었다.  
위 코드도 그렇게 깔끔하진 않지만 이전보다 클린하게 작성할 수 있었다.
