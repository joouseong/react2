# 주우성 202230236

## 9월 30일(5주차)
### Layout and Pages
Route 방식 비교

2. Next.js의 라우팅 방식: Pages Router vs App Router

항목 | Page Router | App Router
|---|---|---
도입 시기 | 초기부터 존재 | Next.js 13 부터 도입
루트 디렉토리 | pages/ | app/
파일 기반 | pages/about.js -> /about | app/about/page.tsx -> /about
특징 | 간단하고 익숙함(기존 React 방식과 유사) | 더 유연하고 강력한 기능 지원
대표 기능 | 동적 라우트, getStaticProps 등 | 레이아웃 중첩, 서버 컴포넌트, 로딩 UI, 병렬 라우트 등
추천 여부 | 유지보수 중(권장 X) | Next.js 15부터 기본 권장 방식

* pages router
    - export default function Page() 형식으로 구성
    - 각 파일은 하나의 페이지 컴포넌트
    - SSR/SSG 함수는 getStaticProps, getServerSideProps 등으로 처리

* app router
    - page.js 각 세그먼트의 페이지
    - layout.js 해당 세그먼트 이하의 모든 페이지에 공통 레이아웃 적용
    - 추가 기능: loading.js, error.js, not-found.js, route groups, parallel routes 등

**App Router의 강력한 기능들**
기능 | 설명
|---|---|
중첩 레이아웃 | 여러 레벨의 layout.js 파일을 통해 레이아웃을 계층적으로 구성 가능
서버 컴포넌트(RSC) | 서버에서만 렌더링되는 컴포넌트로 성능 최적화 가능(React Server Component)
로딩 UI | 페이지 전환 중 보여 줄 loading.js 파일 제공
여러 UI | 특정 경로에서만 발생하는 에러를 처리할 error.js 제공
병렬 라우팅 | 하나의 경로 안에서 탭 같은 독립적인 뷰를 병렬로 렌더링 가능

**프로젝트 별 추천 방식**
상황 | 추천 방식
|---|---|
새 프로젝트 시작 | App Router (app 디렉토리 기반)
기존 프로젝트 유지보수 | pages/ 계속 사용 가능하지만, 점차 마이그레이션 필요
React처럼 수동 라우팅이 필요한 경우 | React + react-router-dom 사용 가능(Next.js는 자동 라우팅이 기본)

### Linking and Navigating
**Introduction**
* Next.js에서 경로(route)는 기본적으로 서버에서 렌더링 됨
* 따라서 클라이언트는 새 경로를 표시하기 전 서버의 응답을 기다려야 하는 경우가 많음
* Next.js에는 prefetching, streaming 그리고 client-side transitions(클라이언트 사이드 전환)기능이 기본 제공되어 네비게이션 속도가 빠르고 반응성이 뛰어남

**1. How navigation works(네비게이션 작동 방식)**
* Next.js에서 네비게잇녀이 어떻게 작동하는지 이해하려면 다음 개념에 익숙해지는 것이 좋음
    - Server Rendering(서버 렌더링)
    - Prefetching(프리페칭)
    - Streaming(스트리밍)
    - Client-side transitions(클라이언트 축 전환)

**1-1. Server Rendering(서버 렌더링)**
* Next.js에서 레이아웃과 페이지는 기본적으로 React 서버 컴포넌트
* 초기 네비게이션 및 후속 네비게이션 할 때, 서버 컴포넌트 페이로드는 클라이언트로 전송되기 전 서버에서 생성됨
* 서버 렌더링에는 발생 시점에 따라 두가지 유형이 있음
    - 정적 렌더링(또는 사전 렌더링)은 빌드 시점이나 재검증 중에 발생하며, 결과는 캐시(cache)됨 <small>#재검증을 사용하면 전체 애플리케이션을 다시 빌드하지 않고도 캐시 항목을 업데이트 할 수 있음</small>
    - 동적 렌더링은 클라이언트 요청에 대한 응답으로 요청 시점에 발생

* 서버 렌더링의 단점은 클라이언트가 새 경로를 표시하기 전에 서버의 응답을 기다려야 한다는 것
* Next.js는 사용자가 방문할 가능성이 높은 경로를 미리 가져오고(prefetching), 클라이언트 축 전환(client-side transitions)을 수행하여 지연 문제를 해결

**# 알아두면 좋은 점**
* 최초 방문을 위해 HTML이 생성됨
* 일반적인 React 앱은 클라이언트 사이드 렌더링(CSR)만 사용하면, 처음 페이지를 방문할 때는 빈 HTML + JAvaScript 파일만 내려주고, 브라우저가 JS를 실행해야 화면이 렌더링 됨
* 하지만 Next.js에서는:
    - 사용자가 특정 URL을 처음 방문하면(initial visit) 서버가 해당 페이지의 HTML을 미리 생성해서 브라우저에 전달
    - 따라서 브라우저는 JS 실행 전에도 즉시 보이는 HTML 뼈대 + 콘텐츠를 표시할 수 있음
    - 이후에 React가 하이드레이션(hydration) 과정을 거쳐 상호작용이 가능해짐
* 즉 "초기 방문 시에도 HTML을 생성해서 내려주기 때문에, 사용자 경험(UX)이 좋아지고 SEO(Search Engine Optimization:검색 엔진 최적화)에도 유리하다"는 의미

**1-2. Prefetching(프리페칭: 미리 가져오기)**
* 프리페칭은 사용자가 해당 경로로 이동하기 전에 백그라운드에서 해당 경로를 로드하는 프로세스
* 사용자가 링크를 클릭하기 전에 다음 경로를 렌더링하는 데 필요한 데이터가 클라이언트 측에 이미 준비되어 있기 때문에 애플리케이션에서 경로 간 이동이 즉각적으로 느껴짐
* Next.js는 `<Link>`컴포넌트와 연결된 경로를 자동으로 사용자 뷰포트에 미리가져옴
* `<a>`를 사용하면 프리페칭을 하지 않음

* 경로의 어느 정도를 프리페칭할지는 정적 경로인지 동적 경로인지에 따라 달라짐
    - 정적 경로: 전체 경로가 프리페칭
    - 동적 경로: 프리페치를 건너 뛰거나, loading.tsx가 있ㄴ느 경우 경로가 부분적으로 프리페칭됨

* Next.js는 동적 라우팅을 건너뛰거나 부분적으로 프리페칭하는 방법으로 사용자가 방문하지 않을 수도 있는 경로에 대한 서버의 불필요한 작업을 방지
* 그러나 네비게이션 전에 서버 응답을 기다리면 사용자에게 앱이 응답하지 않는다는 인상을 줄 수도 있음
* 동적 경로에 대한 네비게이션 환경을 개선하려면 스트리밍을 사용할 수 있음

**1-3. Streaming**
* 스트리밍을 사용하면 서버가 전체 경로가 렌더링될 때까지 기다리지 않고, 동적 경로의 일부가 준비되는 즛기 클라이언트에 전송할 수 있음
* 즉, 페이지의 일부가 아직 로드 중이더라도 사용자는 더 빨리 콘첸트를 볼 수 있음
* 동적 경로의 경우, 부분적으로 미리 가져올 수 있다는 뜻
* 즉, 공유 레이아웃과 로딩 스켈레톤을 미리 요청할 수 있음
* 스트리밍을 사용하려면 라우팅 폴더에 loading.tsx 파일을 생성

* Next.js는 내부적으로 page.tsx 콘텐츠를 `<Suspense>` 경계로 자동 래핑
* 미리 가져온 대체 UI는 경로가 로드되는 동안 표시되고, 준비가 되면 실제 콘텐츠로 대체됨

* 알아두면 좋은 점: `<Suspense>`를 사용하면 중첩된 컴포넌트에 대한 로딩 UI를 만들 수도 있음
* loading.tsx의 이점:
    - 사용자에게 즉각적인 네비게이션과 시각적 피드백을 제공
    - 공유 레이아웃은 상호 작용이 가능하며, 네비게이션은 중단할 수 있음
    - 개선된 핵심 웹 핵심 지표: TTFB, FCP, 및 TTI

* 네비게이션 환경을 더욱 개선하기 위해 Next.js는 `<Link>` 컴포넌트를 사용하여 클라이언트 축 전환을 수행

<small>
* Web Vitals: 웹사이트의 사용자 경험을 측정하고 개선하기 위한 구글의 핵심 성능 지표
* Core Web Vitals: 페이지 로딩 성능, 상호작용 반응성, 시각적 안정성을 측정하는 핵심 지표
</small>

**Core Web Vitals(웹 성능 지표)**
* Next.js 공식 문서에서 이야기하는 TTFB, FCP, TTI 과거에 주로 사용하던 레거시 지표
* "기본적인 준비가 되었는가?"를 측정
* 이 지표들은 웹페이지가 기술적으로 로드되는 순서대로 시간을 측정

* TTFB(Time To First Byte): 네트워크와 서버의 성능을 나타냄. 이 시간이 길면 서버가 느리거나 네트워크 연결에 문제가 있는 것
* FCP(First Contentful Paint): 사용자가 "아, 페이지가 로딩되기 시작하는구나"라고 인지하는 순간. 하얀 화면에서 무언가 처음 뜰 때까지의 시간
* TTI(Time To Interactive): 페이지가 완전히 똑똑해진 시점. 버튼을 눌렀을 때 버벅대지 않고 정상 작동할 수 있는 준비가 완료된 시간
* 참고: 최신 성능 측정에서는 TTI의 중요도가 낮아지고 TBT(Total Blocking Time)나 INP로 대체되는 추세

* LCP(Largest Contentful Paint): 뷰포트 내에서 가장 큰 페이지 요소(큰 텍스트 블록, 이미지 또는 비디오)를 표시하는 데 걸리는 시간 <small># 뷰포트: 웹페이지가 사용자가 별도의 스크롤 동작 없이 볼 수 있는 영역</small>
* FID(First Input Delay): 사용자가 웹페이지와 상호작용을 시도하는 첫 번째 순간부터 웹페이지가 응답하는 시간
* CLS(Cumulative Layout Shift): 방문자에게 콘텐츠가 얼마나 불안정한 지 측정한 값. 페이지에서 갑자기 발생하는 레이아웃의 변경이 얼마나 일어나는지를 측정. 즉, 레이아웃 이동(Layout Shift) 빈도를 측정

**# 레이아웃 이동이 발생하는 원인**
1. 치수가 없는 이미지
2. 크기가 정의되지 않은 광고, Embed 및 iframe
3. 동적 콘텐츠

**# Shared layout remain interactive and navigation is interruptible**
[Shared layouts remain interactive]
* Next.js App Router에서는 layout.tsx가 여러 페이지 간에 공유됨
    - 예: `/blog/page.tsx`와 `/blog/[slug]/page.tsx` 모두 `blog/layout.tsx`를 공유
* 페이지 이동 시 layout.tsx는 다시 리렌더링되지 않고 그대로 유지되기 때문에 사이드바, 네비게이션 메뉴, 음악 플레이어 같은 UI가 새 페이지 로딩 중에도 계속 동작

[navigation is interruptible]
* Next.js는 페이지 이동 시 새로운 데이터를 불러오는데, 그 사이에 사용자가 다른 네비게이션 동작을 하면 이전 로딩을 취소 해 줌
* 즉, 네트워크 요청이나 렌더링이 진행 중이라도 사용자가 다시 클릭하면 이전 요청은 중단되고 새로운 요청만 실행됨

즉 레이아웃은 페이지 전환 중에도 계속 동작하고, 페이지 이동이 진행 중이어도 다른 이동 요청이 들어오면 취소 가능하다는 의미

**1-4. Client-side transitions(클라이언트 축 전환)**
* 일반적으로 서버 렌더링 페이지로 이동하면 전체 페이지가 로드
    - 이로 인해 state가 삭제되고, 스크롤 위치가 재설정되며, 상호작용이 차단됨
* Next.js는 `<Link>` 컴포넌트를 사용하는 클라이언트 축 전환을 통해 이를 방지. 페이지를 다시 로딩하는 대신 다음과 같은 방법으로 콘텐츠를 동적으로 업데이트
    - 공유 레이아웃과 UI를 유지
    - 현재 페이지를 미리 가져온(prefetching) 로딩 상태 또는 사용 가능한 경우 새 페이지로 바꿈
* 클라이언트 축 전환은 서버에서 렌더링된 앱을 클라이언트에서 렌더링된 앱처럼 느껴지게 하는 요소
* 또한 프리페칭 및 스트리밍과 함께 사용하면 동적 경로에서도 빠른 전환이 가능

**2. 전환을 느리게 만드는 요인은 무엇인가**
* Next.js는 최적화를 통해 네비게이션 속도가 빠르고 반응성이 뛰어남
* 하지만 특정 조건에서는 전환 속도가 여전히 느릴 수 있음

**2-1. 동적 경로 없는 loading.tsx**
* 동적 경로로 이동할 때 클라이언트는 결과를 표시하기 전에 서버의 응답을 기다려야 함
    - 이로 인해 사용자는 앱이 응답하지 않는다는 인상을 받을 수 있음
* 부분 프리페칭을 활성화하고, 즉시 네비게이션을 트리고하고, 경로가 렌더링되는 동안 로딩 UI를 표시하려면 동적 경로에 loading.tsx를 추가하는 것이 좋음
* 알아두면 좋은 정보: 개발 모드에서 Next.js 개발자 도구를 사용하여 경로가 정적인지 동적인지 확인할 수 있음

**2-2. 동적 세그먼트 없는 generateStaticParams**
* 동적 세그먼트는 사전 렌더링될 수 있지만, generateStaticParams가 누락되어 사전 렌더링되지 않는 경우, 해당 경로는 요청 시점에 동적 렌더링으로 대체됨
* generateStaticParams를 추가하여 빌드 시점에 경로가 정적으로 생성되도록 함

**# generateStaticParams를 사용하지 않는 경우**
``` tsx
// generateStaticParams가 없는 경우
// blog2의 동적 라우트로 각 포스트의 slug에 대응하는 페이지를 렌더링
// 이 라우트는 generateStaticParams를 사용하지 않으므로 빌드타임이 아닌 런타임에
// params가 전달됨. App Router에서는 params가 Promise로 전달될 수 있으니
// 안전하게 사용하려면 await params로 값을 해제해야 함

import { posts } from "../posts";

export default async function Posts({
    params,
}: {
    // 런타임에서 전달되는 params는 Promise 형태일 수 있음
    params: Promise<{slug: string}>;
}) {
    // params를 await하여 실제 slug값을 얻음
    // (generateStaticParams가 없는 경우 런타임에서 슬러그를 해석하기 때문)
    const { slug } = await params; //params 해제
    const post = posts.find((p) => p.slug === slug);

    // 포스트를 찾지 못하면 간단한 404 메세지 반환
    // 실제 프로젝트에서는 Next.js의 notFound()를 호출하거나
    // 커스텀 404 컴포넌트를 렌더링하는 편이 좋음
    if(!post) {
        // 404 처리
        return (
            <h1>게시글을 찾을 수 없습니다.</h1>
        )
    }

    return (
        <article>
            <h1>{post.title}</h1>
            <p>{post.content}</p>
        </article>
    )
}
```

**# generateStaticParams를 사용하는 경우**
``` tsx

```


---
## 9월 23일(4주차)
### Link Component
Link Component 기본 사용법
* API Reperence > Component > Link Component의 설명
* `<Link>`는 HTML `<a>` 요소를 확장하여 프리페칭(prefetching)과 라우트 간 클라이언트 사이드 네비게이션 기능을 제공하는 React 컴포넌트
* Next.js에서 라우트 간 이동을 위해 주로 사용되는 방법
``` tsx
import Link from 'next/link'

export default function Page() {
  return <Link href="/dashboard">Dashboard</Link>
}
```

다음과 같은 prop을 <Link>컴포넌트에 전달할 수 있음
Prop | Example | Type | Required
|---|---|---|---|
href | href="/dashboard" | String or Objext | yes
replace | replace={false} | Boolean | -
scroll | scroll={false} | Boolean | -
prefetch | prefetch={false} | Boolean, "auto", or null | -
onNavigate | onNavigate={(e) => {}} | Function | -
transitionTypes | transitionTypes={['slide-in']} | string[] | -

이동할 경로 또는 URL을 prop으로 전달

### Layout and Pages
Creating a nested route(중첩 라우트 만들기)
* 중첩 라우트는 다중 URL 세그먼트로 구성된 라우트
* 예를 들어, /blog/[slug]경로는 세 개의 세그먼트로 구성
    - /(Root Segment)
    - blog(Segment)
    - [slug](Leaf Segment)

    [Next.js에서]
    * 폴더는 URL 세그먼트에 매핑되는 경로 세그먼트를 정의하는데 사용됨 #즉 폴더가 URL 세그먼트가 된다는 의미
    * 파일(예:page 및 layout)은 세그먼트에 표시되는 UI를 만드는 데 사용됨
    * 폴더를 중첩하면 중첩된 라우트를 만들 수 있음

* 예를 들어 /blog에 대한 경로를 추가하려면 app 디렉토리에 blog라는 폴더를 만들고
* /blog에 공개적으로 엑세스할 수 있도록 하려면 page.tsx 파일을 추가

* 폴더를 계속 중첩하여 중첩된 경로를 만들 수 있음
* 예를 들어 특정 블로그 게시물에 대한 경로를 만드려면 blog 안에 새 [slug] 폴더를 만들고 page 파일을 추가
* 폴더 이름을 대괄호(예:[slug])로 묶으면 데이터에서 여러 페이지를 생성하는데 사용되는 동적 경로 세그먼트가 생성됨

[slug]의 이해
* slug는 사이트의 특정 페이지를 쉽게 읽을 수 있는 형태로 식별하는 URL의 일부
* 문서의 경로 /blog/[slug]의 [slug] 부분은 불러올 데이터의 key를 말함
* 따라서 데이터에는 slug key가 반드시 있어야 함
* [slug]는 반드시 slug일 필요는 없음. 단, [foo]라고 했다면 데이터에 반드시 foo key(필드)가 있어야 함.

* async function: 함수를 async로 선언해야 내부에서 await을 쓸 수 있음
* await을 사용하는 이유는 서버의 데이터를 읽어올 때 타임 딜레이에 의한 오류를 방지하기 위해서
* 매개변수 구조({params}): Next.js가 페이지를 호출할 때는 props 객체로 {params, searchParams, ...} 같은 값을 넘겨주는데, 여기서 params 만 구조 분해로 받고 있음
* 타입{params: Promise<{slug:string}>}: TypeScript 타입 선언
* params가 Promise(비동기 값)임을 명시하고 있음
* await params는 params가 가리키는 Promise를 해제(reslove)해서 실제 객체 {slug: "..."}를 얻음

* 데이터 소스가 크다면 .find는 O(n)이므로 DB쿼리로 바꿔야 함 
    - O(n)은 알고리즘의 시간 복잡도가 입력 데이터의 크기 n에 비례하여 시간이나 메모리 사용량이 선형적으로 증가하는 것을 의미

Nesting layouts(중첩 레이아웃)
* 기본적으로 폴더 계층 구조의 레이아웃도 중첩되어 있음
* 즉, 자식 prop을 통해 지식 레이아웃을 감싸게 됨
* 특정 경로 세그먼트(폴더) 안에 레이아웃을 추가하여 레이아웃을 중첩할 수 있음
* 예를 들어 /blog 경로에 대한 레이아웃을 만드려면 blog 폴더 안에 새 레이아웃 파일을 추가

Creating a dynamic segment(동적 세그먼트 만들기)
* 동적 세그먼트를 사용하면 데이터에서 생성된 경로를 만들 수 있음

Rendering with search params(검색 매개변수를 사용한 렌더링)
* 서버 컴포넌트 page에서는 searchParams prop을 사용하여 검색 매개변수에 엑세스할 수 있음

* 무엇을 언제 사용해야 하나
    - 페이지에 대한 데이터를 로드하기 위해 검색 매개변수가 필요한 경우(예: 페이지 매김, 데이터베이스에서 필터링) searchParams prop을 사용
    - 검색 매개변수가 클라이언트에서만 사용되는 경우(예: props를 통해 이미 로딩된 목록을 필터링하는 경우) useSearchParams를 사용
    - 콜백이나 이벤트 핸들러에서 new URLSearchParams(window.location.search)를 사용하여 리렌더링을 하지 않고도 검색 매개변수를 읽어올 수 있음

searchParams란
* URL의 쿼리 문자열을 읽는 방법
* 예시 URL: /product?category=shoes&page=2
* 여기서 category=shoes, page=2가 search parameters
* searchParams는 컴포넌트의 props로 전달되어, 내부적으로는 URLSearchParams 처럼 작동

왜 "동적 렌더링"이 되는가
* Next.js에서 페이지는 크게 정적 또는 동적으로 렌더링 될 수 있음
* searchParams는 요청이 들어와야만 값을 알 수 있기 때문에, Next.js는 이 페이지를 정적으로 미리 생성할 수 없고, 요청이 올 때마다 새로 렌더링해야 함
* 따라서 해당 페이지는 자동으로 동적 렌더링으로 처리됨
* 즉 searchParams를 사용하는 순간 Next.js는 "정적으로 미리 만들 수 없겠다" 라고 판단

동적 vs 정적 렌더링 비교
항목 | 동적 렌더링 | 정적 렌더링
|---|---|---|
예시 | /about, /blog (미리 생성됨) | /product?page=2 (요청 시 생성)
장점 | 빠름, 캐싱 가능 | 유연함, 쿼리나 요청 기반 응답 가능
searchParams 사용 | 불가능 | 가능

Route 방식 비교
1. React vs Next.js 라우팅 방식의 차이

항목 | React(기본) | Next.js
|---|---|---|
라우팅 방식 | 수동(사용자가 직접 설정) | 자동(폴더/파일 기반)
라우터 도구 | react-router-dom 같은 외부 라이브러리 필요 | 자체 내장된 파일 기반 라우팅 시스템
라우트 정의 방식 | 코드에서 직접 `<Route>`로 정의 | 파일/폴더 이름으로 라우트가 자동 매핑됨
예시 | `<Route path="/about" element={<ABOUT/>}>` | pages/about.js -> about 경로 자동 생성, app/about/page.tsx -> /about 경로 자동 생성

* React는 기본적으로 라우팅 기능이 없기 때문에, 직접 라우터 라이브러리를 설치해 라우팅을 설정해야 함
* Next.js는 자체적으로 라우팅 시스템을 내장하고 있음

---
## 9월 16일(3주차)
[라우팅 그룹 및 비공개 폴더]
* 라우트 그룹을 사용하여 URL을 변경하지 않고 코드를 정리할 수 있음
* 라우팅되지 않는 파일들은 _folder라는 비공개 디렉토리에 함께 저장

Path | URL Pattern | Notes
|---|---|---|
app(maketing)/page.tsx | / | URL에서 제외된 그룹
app(shop)/cart/page.tsx | /cart | (shop)내에서 레이아웃 공유
app/blog/_components/Post.tsx | - | 라우팅 대상 아님: UI 유틸리티를 위한 안전한 공간
app/blog/data.ts | - | 라우팅 대상 아님: 유틸리티를 위한 안전한 공간

[병렬 및 가로채기 라우팅]
* 이러한 기능은 슬롯 기반 레이아웃이나 모달 라우팅과 같은 특정 UI 패턴에 적합
* 부모 레이아웃에서 렌더링되는 명명된 슬롯(named slots)에는 @slot을 사용
* 인터셉트 패턴을 사용하면 URL을 변경하지 않고도 현재 레이아웃 내에서 다른 경로를 렌더링할 수 있음
* 예를 들면 목록 위에 모달 형태로 상세 보기를 표시할 때 사용할 수 있음

Pattern(docs) | Meaning | 일반적인 사용 사례
|---|---|---|
@folder | 명명된 슬롯 (Named slot) | 사이드바 + 메인 콘텐츠
(.)folder | 동일 레벨 가로채기(Intercept) | 모달에서 형제 라우트 미리보기
(..)folder | 한 레벨 위에서 가로채기 | 부모의 자식 라우트를 오버레이로 열기
(..)(..)folder | 두 레벨 위에서 가로채기 | 깊게 중첩한 오버레이
(...)folder | 루트에서 가로채기 | 현재 뷰에 임의의 라우트 표시

Open Graph Protocol
* 웹사이트나 페이스북, 인스타그램, X, 카카오톡 등에 링크를 전달할 때 "미리보기"를 생성하는 프로토콜
* Open Graph Protocl이 대표적인 프로토콜
* 페이스북이 주도하는 표준화 규칙으로 대부분의 SNS 플랫폼에서 활용되고 있음
* 모든 플랫폼이 동일한 방식으로 오픈 그래프를 처리하는 것은 아님
* 웹페이지의 메타 태그에 선언

Organizing your project(프로젝트 구성하기)
* Next.js는 프로젝트 파일을 어떻게 구성하고 어디에 배치할지에 대한 제약이 없음
* 하지만 프로젝트를 체계적으로 구성하는데 도움이 되는 몇가지 기능을 제공

[component의 계층 구조]
* 특수 파일에 정의된 component는 특정 계층 구조로 렌더링 됨
    - layout.js
    - template.js
    - error,js(React 오류 경계)
    - loading.js(리엑트 서스펜스 경계)
    - not-found.js(React 오류 경계)
    - page.js 또는 중첩 layout.js
    ```
    <Layout>
      <Template>
        <ErrorBoundary fallback={<Error />}>
          <Suspense fallback={<Loading />}>
            <ErrorBoundary fallback={<NotFound />}>
              <Page />
            </ErrorBoundary>
          </Suspense>
        </ErrorBoundary>
      </Template>
    </Layout>
    ```

Layout과 template의 차이
* 유사한 기능을 갖고 있지만 동작 방식에 차이가 있음

파일 | 특징 | 상태/DOM 유지 | 사용 사례
|---|---|---|---|
layout.tsx | 경로별 공유 레이아웃 | 유지됨(정적) | 네비게이션, 사이드바, 공통 레이아웃
template.tsx | 매번 새 인스턴스 생성 | 초기화됨(동적) | 페이지별로 초기화가 필요한 경우

[코로케이션] Colocation
* 파일 및 폴더를 기능별로 그룹화하여 프로젝트의 구조를 명확하게 정의하는 것
    - app 디렉토리에서 중첩된 폴더는 라우팅 구조를 정의
    - 각 폴더는 URL의 해당 세그먼트에 맵핑되는 라우팅 세그먼트를 나타냄
    - 그러나 폴더를 통해 라우트 구조가 정의되도 해당 라우트 세그먼트에 page.js 또는 route.js파일이 추가되기 전까지는 외부에서 해당 라우트에 접근할 수 없음.
* 경로가 공개적으로 접근 가능하게 설정되더라도 클라이언트에게는 반환 되거나 전송되는 콘텐츠만 전달됨
* 이것은 프로젝트 파일을 app 디렉토리의 라우팅 세그먼트 안에 안전하게 배치하여 실수로 라우팅 디지 않도록 할 수 있음

* 알아두면 좋은 점: 프로젝트 파일을 app 폴더에 함께 저장할 수는 있지만 꼭 그럴 필요는 없음, 원한다면 app 디렉터리 외부에 보관할 수도 있음
* 이런 문제들을 모두 경험하기 때문에 Next.js에서도 src 디렉토리 사용을 권장하고 있음

[비공개 폴더]
* 비공개 폴더는 폴더 앞에 밑줄을 붙여서 만들 수 있음 _folderName
* 이 것은 해달 폴더가 비공개로 구현되는 세부 사항이기 때문에 라우팅 시스템에서 고려되어서는 안 되며, 따라서 해당 폴더와 모든 하위 폴더가 라우팅에서 제외됨을 나타냄

* app 디렉토리 파일은 기본적으로 안전하게 코로케이션 될 수 있으므로, 코로케이션에 비공개 폴더는 불필요,. 하지만 다음과 같은 경우에는 유용할 수 있음
    - UI 로직과 라우팅 로직을 분리
    - 프로젝트와 Next.js 생태계 전반에서 내부 파일을 일관되게 구성
    - 코드 편집기에서 파일을 정렬하고 그룹화
    - 향후 Next.js 파일 규칙과 관련된 잠재적인 이름 충돌을 방지
* 알아두면 좋은 점:
    - 프레임워크 규칙은 아니지만, 동일한 밑줄 패턴을 사용하여 비공개 폴더 외부의 파일을 "비공개"로 표현하는 것도 고려할 수 있음
    - 폴더 이름 앞에 %5F(밑줄로 URL 인코딩된 형태)를 접두사로 붙여 밑줄로 시작되는 URL 세그먼트를 만들 수 있음
    - 비공개 폴더를 사용하지 않는 경우, 예상치 못한 이름 충돌을 방지하기 위해 Next.js의 특수 파일 규칙을 아는 것이 좋음

[라우팅 그룹]
* 폴더를 괄호로 묶어 라우팅 그룹을 만들 수 있음(folderName)
* 이 것은 해당 폴더가 구성 목적으로 사용되는 것을 의미하며, 라우터의 URL 경로에 포함되지 않아야 함

* 라우팅 그룹은 다음과 같은 경우에 유용
    - 사이트 섹션, 목적 또는 팀별로 라우트를 구성
        - 예: 마케팅 페이지, 관리 페이지 등
    - 동일한 라우팅 세그먼트 수준에서 중첩 레이아웃 활성화: 
        - 공통 세그먼트 안에 여러 개의 루트 레이아웃을 포함시켜 여러 개의 중첩 레이아웃 만들기
        - 공통 세그먼트의 라우팅 하위 그룹에 레이아웃 추가

[src 디렉토리]
* Next.js는 애플리케이션 코드(app 포함)을 옵션으로 선택하는 src폴더 내에 저장할 수 있도록 지원
* 이를 통해 애플리케이션 코드와 주로 프로젝트 루트에 위치하는 프로젝트 설정 파일을 분리할 수 있음

### Layouts and Pages
Creating a page
* Next.js는 파일 시스템 기반 라우팅을 사용하기 때문에 폴더와 파일을 사용하여 경로를 정의할 수 있음
* page는 특정 경로에서 렌더링되는 UI
* page를 생성하려면 app 디렉토리에 page파일을 추가하고, React 컴포넌트를 default export 해야함

Creating a layout
* layout은 여러 페이지에서 공유되는 UI
* layout은 네비게이션에서 state 및 상호작용을 유지, 다시 렌더링 되지는 않음
* layout 파일에서 React 컴포넌트의 default export를 사용하여 layout을 정의할 수 있음
* layout 컴포넌트는 page 또는 다른 layout이 될 수 있는 children prop을 허용해야 함

* children은 컴포넌트 안에 감싸진 요소(컴포넌트)를 의미
* layout 컴포넌트를 만들 때 그 안에 들어갈 콘텐츠(children)를 받을 수 있게 해야하고, 그 컨텐츠는 page또는 layout 컴포넌트가 될 수도 있다는 의미

* 예를 들어, index 페이지를 자식으로 허용하는 레이아웃을 만드려면, app 디렉토리에 layout 파일을 추가
* 루트 레이아웃은 필수이며, html 및 body 태그를 포함해야 함

---
## 9월 9일(2주차)
### Installation
[프로젝트 수동 생성]
* 새로운 Next.js 앱을 수동으로 생성하려면 필요한 패키지를 설치해야 함
    > pnpm i next@latest react@latest react-dom@latest

[package.json 파일에 스크립트 추가]
* 등록한 스크립트는 애플리케이션 개발의 다양한 명령을 참조함
    - next dev: 개발 서버를 시작
    - next build: 프로덕션을 위한 애플리케이션 빌드
    - next start: 프로덕션 서버를 시작
    - next lint: ESLint를 실행
* 이제 turbopack이 기본 번들러. Webpack을 사용하려면 next dev --webpack 또는 next build -webpack을 실행하면 됨

[app 디렉토리 생성]
* Next.js는 file-system Routing을 사용. 즉 애플리케이션의 Routing은 파일을 어떻게 구성되는가에 따라 결정됨
* app 디렉토리를 생성하고, 그 안에 layout.tsx 파일을 생성. 이 파일은 루트 레이아웃이 됨
* 이 파일은 필수 파일이며 <html>과 <body> 태그를 포함해야 함

* 초기 콘텐츠로 사용할 홈 페이지 "app/page.tsx"를 생성
* 사용자가 애플리케이션의 루트(/)를 방문하면 layout.tsx() 및 page.tsx 두 문서 모두 렌더링 됨

오류 처리
* 문서의 지시대로만 처리하면 오류 발생
* 타입스크립트 환경이 아니기 때문
* 타입스크립트 환경에서 react와 react-dom을 사용할 수 있도록 타입 정의를 제공하는 패키지를 설치해야 함
    > pnpm add -D @types/react @types/react-dom
* 일반 설치와 -D 설치의 차이점

| 구분 | 일반설치(pnpm add <pkg>) | 개발용 설치(pnpm add -D <pkg>)|
|---|---|---|
| 등록위치 | package.json 내 dependencies | package.json 내 devDependencies|
| 용도 | 실제 서비스 구동에 반드시 필요한 패키지 | 코드 빌드, 테스트, 린팅 등 개발할 때만 필요한 패키지|
| 배포 환경 | 빌드 결과물에 포함되거나 프로덕션 서버에 설치됨 | --production 옵션 등으로 빌드/배포 시 제외 됨|
| 대표 예시 | React, Vue, Express, Axios, Lodash 등 | TypeScript, ESLint, Pretter, Vite, Jest 등|

이전 버전의 Next.js 프로젝트로 인식
* js나 jsx로 프로젝트를 진행하려고 해도 오류 발생
* 이런 경우 추가로 react를 import 해야 함

[알아두면 좋은 정보]
* 루트 레이아웃을 만드는 것을 잊어버린 경우, Next.js는 개발 서버를 실행할 때 자동으로 이 파일을 생성
* 프로젝트 루트에 있는 src폴더 아래로 app 폴더 전체를 이동

[public 디렉토리 생성 (선택 사항)]
* 이미지, 글 꼴 등의 정적 리소스를 저장하기 위한 public 디렉토리를 프로젝트 루트에 생성
* public 디렉토리를 생성하면 기본 URL(/)로 public 디렉토리 내부의 리소스를 참조할 수 있음
* 예를 들어 public/profile.png는 /profile.png와 같이 참조 가능

[개발 서버 실행] 
1. 개발 서버를 시자갛려면 다음 명령을 실행 > pnpm dev
2. 명령을 실행한 후 locallhost:3000로 접속하면 App을 확인 가능(수동으로 접속해야 함)
3. app/page.tsx 파일을 편집하고 저장하면, 브라우저를 통해 업데이트된 결과 확인 가능

[TypeScript 설정] 최소 typeScript 버전: v5.1.0
* Next.js는 TypeScript를 기본적으로 지원
* 프로젝트에 TypeScript를 추가하려면 파일 확장자를 .ts 또는 .tsx로 바꾸고 next dev명령을 실행

[IDE 플러그인]
* Next.js에는 사용자 정의 TypeScript 플러그인과 유형 검사기가 포함되어 있음
* VS Code와 다른 코드 편집기에서 고급 유형 검사 및 자동 완성에 사용 가능

[import 및 모듈의 절대 경로 별칭 설정]
* Next.js에는 tsconfig.json 및 jsconfig.json 파일의 "paths" 및 "baseUrl" 옵션을 기본적으로 지원
* 이 옵션을 사용하면 프로젝트 디렉토리를 절대 경로로 별칭하여 모듈을 더 쉽고 깔끔하게 가져올 수 있음
* 별칭으로 import를 구성하려면 tsconfig.json 또는 jsconfig.json 파일의 baseUrl에 구성 옵션을 추가

[Next.js 앱 업그레이드]
* Next.js 버전을 최신 상태로 유지하는 것이 좋음
* 각 릴리스에는 새로운 기능과 함꼐 보안 패치, 버그 수정 및 성능 최적화가 포함되어 있으므로 최신버전을 유지하는 것이 좋음

자동 생성되는 항목
* package.json 파일에 scripts 자동 추가 / public 디렉토리
* TypeScript 사용(선택): tsconfig.json 파일 생성
* ESLint 설정(선택): eslintsrc.json 대신 eslint.config.mjs 파일 생성
* Tailwind CSS 사용(선택)
* src 디렉토리 사용(선택)
* App Router(선택), app/layout.tsx 파일 및 app/page.tsx
* Turbopack 사용(선택)
* import alias 사용(선택): No로 해도 tsconfig.json에 "path" 자동 생성
* 수동으로 프로젝트를 생성할 때 추가적으로 해야하는 작업을 자동으로 처리해줌

### 프로젝트 구조 및 구성
용어 정의
* 원문에는 route라는 단어가 자주 등장하고, 사전적 의미는 "경로"
* route는 "경로"를 의미하고, routing은 "경로를 찾아가는 과정"을 의미
* segment는 routing과 관련이 있는 directory의 별칭 정도로 이해하면 됨

[최상위 폴더]
* 최상위 디렉토리는 애플리케이션의 코드와 정적 자산을 구성하는 데 사용 됨

[최상위 파일]
* 최상위 파일은 애플리케이션 구성, 종속성 관리, 프록시 실행, 모니터링 도구 통합, 환경 변수 정의에 사용됨

[라우팅 파일]
* 경로를 노출할 페이지를 추가하고, 헤더, 네비게이션, 푸터와 같은 공유 UI 레이아웃을 추가할 수 있음
* 이 밖에 스켈레톤 로딩 화면을 추가하고, 오류 경계를 표시하는 오류화면을 추가하고, API 경로를 추가할 수 있음

[중첩 라우팅]
* 디렉토리는 URL 세그먼트를 정의
* 디렉토리를 중첩하면 세그먼트도 중첩
* 모든 수준의 레이아웃은 하위 세그먼트를 감쌈
* 페이지나 경로 파일이 존재하면 해당 경로는 공개됨

[동적 라우팅]
* 대괄호를 사용하여 세그먼트를 매개변수화 할 수 있음
* 단일 매개변수의 경우 [segment]를 사용
* 모든 값을 포괄하는 매개변수(catch-all)의 경우 [[...segment]]를 사용
* 선택적 포괄 매개변수의 경우 [[...segment]]를 사용
* params 속성을 통해 값에 접근할 수 있음

Next.js의 동적 라우팅
* Next.js의 동적 라우팅은 3가지로 구분됨
* 핵심적인 차이는 "하위 경로(Depth)를 어디까지 허용할 것인가"와 "동적 세그먼트가 없는 기본 경로를 처리할 수 있는가"에 있음

1. 일반 동적 라우팅
    * 디렉토리 구조: [slug]
    * 작동 방식: 1개의 특정 경로 세그먼트만 동적으로 매칭
2. Catch-all 라우팅
    * 디렉토리 구조: [...slug]
    * 작동 방식: 해당 경로 아래에 오는 모든 하위 경로(무한 Depth)를 하나의 배열로 전달하여 매칭
3. Optical Catch-all 라우팅
    * 디렉토리 구조: [[...slug]]
    * 작동 방식: Catch-all 라우팅과 같지만, 동적 파라미터가 없는 기본 경로(/ports) 까지도 매칭해 줌

---
## 9월 2일(1주차)
### pnpm vs npm
pnpm
* pnpm은 Performant(효율적인) NPM의 약자로 고성능 Node 패키지 매니저
* npm, yan과 같은 목적의 패키지 관리자이지만, 디스크 공간 낭비, 복잡한 의존성 관리, 느린 설치 속도 문제 개선을 위해 개발되었음
* 대표적인 특징
    1. 하드 링크 기반의 효율적인 저장 공간 사용: 패키지를 한 번만 설치하여 글로벌 저장소에 저장하고, 각 프로젝트의 node modules 디렉토리에는 설치된 패키지에 대한 하드 링크(또는 심볼럭 링크)가 생성됨
    2. 빠른 패키지 설치 속도: 이미 설치된 패키지는 다시 다운로드하지 않고 재사용하므로, 초기 설치뿐 만 아니라 종속성 설치 및 업데이트 할 때도 더 빠른 속도를 경험할 수 있음
    3. 엄격하고 효율적이 종속성 환리
    4. 다른 패키지 매니저의 비효율성을 개선한 패키지 관리자

pnpm으로 Next.js 프로젝트 생성
* $ pnpm create next-app@latest
    - npm의 npx 대신 pnpm create 사용
    - next-app 명령어 실제로 실행되는 것은 create-next-app
* $ cd my-app
* 서버 실행: $ pnpm dev

### Hard link vs Soft link
Hard link vs Symbolic link(Soft link)
* pnpm의 특징 중에 하드 링크를 사용해서 디스크 공간을 효율적으로 사용할 수 있다고 함
* 탐색기에서 npm과 pnpm 프로젝트의 node module의 용량을 확인하면 동일

[하드링크]
* 우리가 "파일"이라고 부르는 것은 세 부분으로 나뉘어 있음
    1. Directory Entry: 파일 이름과 해당 inode 번호를 매핑 정보가 있는 특수 파일
    2. inode: 파일 또는 디렉토리에 대한 모든 메타데이터를 저장하는 구조체(권한, 소유자 크기, 데이터 블록 위치 등)
    3. data blocks: 실제 데이터가 존재하는 영역
* 하드 링크를 생성하면 디렉토리 엔트리에 매핑 정보가 추가되어, 동일한 inode를 가리키게 함
* 따라서 원본과 하드링크는 완전히 동일한 파일
* 원본과 사본의 개념이 아님

* 디렉토리 엔트리에 있는 원본과 하드링크는 같은 inode를 창조하므로 데이터 블록을 100% 공유
* 따라서 원본이나 하드링크 중에서 하나만 삭제하면 디렉토리 엔트리에서 이름만 삭제됨
* link count가 0이 되지 않는 한 데이터는 남아 있음
* pnpm sotre에 저장된 페키지나, node modules/pnpm에 저장된 패키지나 동일한 파일을 참조하고 있음

* 그런데 탐색기에서 node modules의 속성을 보면 npm의 경우와 디스크 용량이 같아 보임
* 이 것은 탐색기의 특성상 그렇게 표시하기 때문
* pnpm으로 패키지를 설치하면 전역 store에 한 번만 저장
* 따라서 실제 디스크 사용은 중복되지 않음

[심볼릭 링크 (소프트 링크)]
* inode를 공유하지 않고, 경로 문자열을 저장해 두는 특수 파일
* 따라서 심볼릭 링크를 열면 내부에 적힌 "경로"를 따라가서 원본 파일을 찾음
* 원본이 삭제되면 심볼릭 링크는 끊어진 경로가 되므로 더 이상 사용할 수 없음
* 윈도우의 바로가기 파일과 비슷하게 생각할 수 있음

### Installation
* 빠르게 Next.js 프로젝트를 생성하고, 실행하려면 다음 순서대로 진행
    1. my-app이라는 이름의 Next.js 앱을 새로 생성
    2. cd my-app으로 프로젝트로 들어가서 개발 서버 시작
    3. http://localhost:3000 주소로 앱을 실행(서버 중지: Ctrl + C)
    ``` terminal
    pnpm create next-app@latest my-app --yes
    cd my-app
    pnpm dev
    ```
* --yes 옵션은 저장된 기본 설정이나 기본값을 사용하여 프롬프트를 건너 뜀
* 기본 설정에서는 TypeScript, Tailwind CSS, ESLint, App Router 및 Turbopack이 활성화되며, 가져오기 별칭(@/*) 사용 그리고 AGENTS.md 파일(CLADE.md에서 참조)을 통해 코딩 에이전트가 최신 Next.js 코드를 작성하도록 안내함

[시스템 요구 사항]
* 시작하기 전에 시스템이 다음 요구 사항을 충족하는지 확인
    - 최소 Node.js 버전: Node.js 20.9 이상
    - 운영체제: maOS, Windows(WSL 포함) Linux
[지원되는 브라우저]
* Next.js는 별도의 설정 없이 최신 브라우저를 지원
* VS Code에서는 정식 통합 브라우저를 지원하기 시작

[CUI를 사용한 프로젝트 생성]
* Next.js 앱을 가장 빠르게 생성하는 방법은 create-next-app을 사용하는 것
* create-next-app은 모든 설정을 자동으로 해줌
* 프로젝트를 생성하려면 pnpm create next-app 명령어 실행