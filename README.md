# 주우성 202230236
## 9월 16일(3주차)


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