# Next.js 13 App Router + API

이 예제는 React 프런트엔드 및 Next.js 백엔드 API를 포함하는 Typescript로 작성된 풀스택 Next.js 애플리케이션입니다.

## 원문링크 : [링크](https://jasonwatmore.com/next-js-13-app-router-mongodb-user-rego-and-login-tutorial-with-example)

## Next.js 튜토리얼 프런트엔드 앱

Next.js 클라이언트 앱은 React로 빌드되었으며 다음 페이지를 포함합니다:

- `/account/login` - Next.js 앱에 로그인하기 위한 공개 페이지입니다.
- `/account/register` - 앱에 새 사용자 계정을 등록하기 위한 공개 페이지.
- `/` - 로그인한 사용자에게 보내는 간단한 환영 메시지가 포함된 보안 홈 페이지.
- `/users` - 사용자 추가, 편집 또는 삭제 옵션과 함께 Next.js 앱의 모든 사용자 목록을 표시하는 보안 페이지.
- `/users/add` - 새 사용자를 추가하기 위한 보안 페이지.
- `/users/edit/[id]` - 기존 사용자를 편집하기 위한 보안 페이지.

보안 페이지는 인증되지 않은 사용자를 로그인 페이지로 리디렉션하는 [보안 레이아웃 컴포넌트](https://jasonwatmore.com/#secure-layout-tsx)에 의해 보호됩니다.

## Next.js 튜토리얼 백엔드 API

Next.js API에는 다음 경로/엔드포인트가 포함되어 있습니다:

- `/api/account/login` - POST - 사용자 이름과 암호를 인증하고 HTTP 전용 `authorization` 쿠키에 반환되는 JWT 토큰을 생성하기 위한 퍼블릭 라우트
- `/api/account/logout` - POST - HTTP 전용 `authorization` 쿠키를 삭제하는 로그아웃을 위한 퍼블릭 라우트
- `/api/account/register` - POST - Next.js 앱에 새 사용자를 등록하기 위한 퍼블릭 라우트
- `/api/users` - GET - 모든 사용자를 반환하는 보안 라우트
- `/api/users` - POST - 새 사용자를 생성하기 위한 보안 라우트
- `/api/users/current` - GET - 현재 로그인한 사용자를 반환하는 보안 라우트
- `/api/users/[id]` - GET - 지정된 ID를 가진 사용자를 반환하는 보안 라우트
- `/api/users/[id]` - PUT - 사용자 업데이트를 위한 보안 라우트
- `/api/users/[id]` - DELETE - 사용자 삭제를 위한 보안 라우트

보안 라우트는 request의 `authorization` 쿠키에서 JWT 토큰을 확인하는 [JWT 미들웨어](https://jasonwatmore.com/#jwt-middleware-ts)에 의해 보호됩니다.

## Next.js App Router

새로운 [앱 라우터](https://nextjs.org/docs/app)는 폴더와 파일이 Next.js 애플리케이션에서 페이지 및 API 경로를 정의하는 데 사용되는 파일 시스템 기반 라우터라는 점에서 기존 [페이지 라우터](https://nextjs.org/docs/pages)와 유사합니다.

Next.js 페이지 라우터 vs 앱 라우터로 라우트를 정의하는 데 다른 파일 명명 규칙이 사용됩니다. 예를 들어 `/account/login` 라우트로 로그인 양식을 생성하려는 경우 페이지 라우터를 사용하여 `/pages/account/login.jsx` 파일을 생성하는 반면 앱 라우터에서는 `/app/account/login/page.jsx` 파일을 생성합니다 (또는 TypeScript의 .tsx)

앱 라우터는 [리액트 서버 컴포넌트](https://nextjs.org/docs/getting-started/react-essentials#server-components), [레이아웃](https://nextjs.org/docs/app/building-your-application/routing/pages-and-layouts#layouts), [라우트 그룹](https://nextjs.org/docs/app/building-your-application/routing/route-groups) 및 [개인 폴더](https://nextjs.org/docs/app/building-your-application/routing/colocation#private-folders)를 포함한 새로운 기능에 대한 지원을 추가합니다.

- 리액트 서버 컴포넌트는 서버에서 렌더링되며 기존의 서버 측 렌더링 웹 페이지(예: .NET MVC, PHP 등)와 유사하게 작동한다고 생각합니다.
- 레이아웃을 사용하면 여러 페이지에 대해 공통 레이아웃 코드를 한 곳에 배치할 수 있습니다. 이는 페이지 라우터 및 필수 해결 방법에서 지원되지 않았습니다.
- 라우트 그룹을 사용하면 URL 경로에 포함된 폴더 이름 없이 폴더의 페이지를 그룹화할 수 있습니다. 이렇게 하려면 폴더 이름을 괄호로 묶습니다(예: `(secure)`, `(public)`).
- 개인 폴더를 사용하면 라우팅 시스템에서 무시되는 `/app` 디렉터리에 폴더를 만들 수 있습니다. 이렇게 하려면 폴더 이름 앞에 밑줄을 붙입니다(예: `_components`, `_services`).

앱 라우터를 사용하려면 `/app` 폴더에 있는 파일을 배치하고, 페이지 라우터를 사용하려면 `/pages` 폴더에 있는 파일을 배치합니다. 동일한 프로젝트에서 둘 다 사용할 수 있지만 이는 페이지 라우터에서 앱 라우터로 프로젝트를 마이그레이션하는 경우에만 필요합니다.

## 몽고DB와 몽구스 ODM

MongoDB는 사용자 데이터를 저장하기 위해 API에서 사용하는 데이터베이스이며, Mongoose ODM(Object Data Modeling) 라이브러리는 MongoDB와 상호 작용하는 데 사용되며 여기에는 컬렉션의 스키마 정의, 데이터베이스 연결 및 모든 CRUD 작업 수행이 포함됩니다. 몽구스에 대한 자세한 내용은 https://mongoosejs.com/을 참조하세요.

## React Hook Form Library

예제의 양식은 React Hooks를 사용하여 React에서 양식을 작성, 검증 및 처리하기 위한 라이브러리인 React Hook Form으로 작성되었습니다. 한동안 React 및 Next.js 프로젝트에 사용했는데 사용 가능한 다른 옵션보다 사용하기 쉽고 코드가 덜 필요하다고 생각합니다. 자세한 내용은 https://react-hook-form.com을 참조하십시오.

## Zustand State Management

Zusstand는 응용 프로그램의 여러 컴포넌트에서 액세스할 수 있는 공유 상태를 관리하기 위해 [사용자 서비스](https://jasonwatmore.com/#user-service-ts) 및 [경고 서비스](https://jasonwatmore.com/#alert-service-ts)에서 사용하는 널리 사용되는 경량 React 상태 관리 라이브러리입니다. 자세한 내용은 https://docs.pmnd.rs/zustand/getting-started/introduction을 참조하세요.

## Joi Schema Validation

Joi는 HTTP 요청에서 Next.js API로 전송된 데이터(예: [로그인 라우트](https://jasonwatmore.com/#api-account-login-route-ts) 및 [등록 라우트](https://jasonwatmore.com/#api-account-register-route-ts))의 유효성을 검사하는 데 사용되는 유연한 JavaScript 스키마 유효성 검사 라이브러리입니다. 자세한 내용은 Joi 또는 https://joi.dev/ 로 [스키마 유효성 검사 요청을 참조](https://jasonwatmore.com/post/2020/07/22/nodejs-express-api-request-schema-validation-with-joi)하세요.

## Bootstrap CSS

Bootstrap 5 CSS는 Next.js 튜토리얼 앱의 스타일을 지정하는 데 사용되며 루트 레이아웃 컴포넌트로 가져옵니다. 자세한 내용은 https://getbootstrap.com/을 참조하세요.

## GitHub의 코드

예제 프로젝트는 https://github.com/cornflourblue/next-js-13-app-router-mongodb-rego-login-example 의 GitHub에서 사용할 수 있습니다.

# Tools required for this tutorial

Next.js MongoDB 애플리케이션을 로컬에서 개발하고 실행하려면 다음이 필요합니다.

- [Node.js 및 npm](https://nodejs.org/en/download/) - Node 런타임 및 cli-tools를 포함합니다.
- [Visual Studio Code](https://code.visualstudio.com/) - Windows, Mac 및 Linux에서 실행되는 코드 편집기입니다. 선호하는 코드 편집기가 다른 경우에도 괜찮습니다.
- MongoDB - Next.js 앱이 연결하려면 실행 중인 MongoDB 서버 인스턴스에 액세스해야 합니다. 원격(예: Azure, AWS 등) 또는 로컬 머신일 수 있습니다. MongoDB 커뮤니티 서버는 무료이며 https://www.mongodb.com/try/download/community 에서 다운로드할 수 있습니다. Docker 컨테이너에서 실행할 수도 있습니다. MongoDB의 공식 도커 이미지는 https://hub.docker.com/_/mongo 에서 사용할 수 있습니다.
- 앱이 연결할 수 있도록 MongoDB가 시작되었는지 확인합니다. https://docs.mongodb.com/manual/administration/install-community/ 의 각 OS 설치 페이지에서 지침을 확인할 수 있습니다.

# Next.js MongoDB 로그인 예제를 로컬로 실행

다음 단계에 따라 로컬 컴퓨터에서 Next.js 13 앱 라우터 MongoDB 인증 앱을 다운로드하고 실행합니다.

1. https://github.com/cornflourblue/next-js-13-app-router-mongodb-rego-login-example 에서 Next.js 프로젝트 소스 코드 다운로드 또는 복제
2. 프로젝트 루트 폴더(package.json이 있는 위치)의 명령줄에서 `npm install` 또는 `npm i`를 실행하여 필요한 모든 npm 패키지를 설치합니다.
3. 프로젝트 루트 폴더의 명령줄에서 `npm run dev`를 실행하여 앱을 시작하면 앱이 컴파일되고 Next.js 서버가 시작됩니다.
4. URL http://localhost:3000

참고: Next.js CLI 명령 `npx next dev`를 사용하여 앱을 직접 시작할 수도 있습니다. Next.js CLI에 대한 자세한 내용은 https://nextjs.org/docs/app/api-reference/next-cli 를 참조하세요.

## 프로덕션 환경에서 실행하기 전에

프로덕션 환경에서 앱을 실행하기 전에 [.env](https://jasonwatmore.com/#dotenv) 파일에서 `JWT_SECRET` 환경 변수를 업데이트해야 합니다. 이 환경 변수는 인증을 위해 JWT 토큰을 서명하고 확인하는 데 사용되며 다른 사람이 동일한 암호로 JWT를 생성할 수 없도록 임의의 문자열로 변경합니다. Next.js 앱 또는 API에 대한 무단 액세스 권한을 얻습니다. 빠르고 쉬운 방법은 두 개의 GUID를 결합하여 긴 임의의 문자열을 만드는 것입니다 (예: https://www.guidgenerator.com/)

---

## 중략

# 폴더 구조 (Documentation)

최상위 Next.js 앱 라우터 폴더(/app) 내에서 프로젝트는 다음 폴더로 구성됩니다.

- _components  
  페이지 또는 다른 리액트 컴포넌트에서 사용하는 리액트 컴포넌트.  
  전역 컴포넌트는 루트 `/_components` 폴더에 있고 기능별 컴포넌트는 하위 폴더(예: `/_components/users`)에 있습니다.  
  폴더 이름은 Next.js 라우팅 시스템에서 무시되는 개인 폴더로 표시하기 위해 밑줄(`_`) 접두사가 붙습니다.

- _helpers
  다른 폴더에 맞지 않고 자체 폴더를 갖는 것을 정당화하지 않는 모든 것. 프런트엔드 React 헬퍼는 `/_helpers/client` 폴더에 있고, 서버 측 헬퍼는 `/_helpers/server` 폴더에 있으며, API 특정 헬퍼는 `/_helpers/server/api` 폴더에 있습니다. 폴더 이름은 Next.js 라우팅 시스템에서 무시되는 개인 폴더로 표시하기 위해 밑줄(`_`) 접두사가 붙습니다.

- _services
  서비스는 클라이언트측 로직을 캡슐화하고 리액트 프런트엔드 앱과 Next.js 백엔드 API 간의 HTTP 통신을 처리합니다.  
  각 서비스는 특정 콘텐츠 유형(예: [사용자 서비스](https://jasonwatmore.com/#user-service-ts))에 대한 논리를 캡슐화하고 다양한 작업(예: 인증 및 CRUD 작업)을 수행하기 위한 속성 및 메서드를 노출합니다.  
  또한 서비스는 [경고 서비스](https://jasonwatmore.com/#alert-service-ts)를 사용하여 경고 표시 및 지우기와 같이 HTTP 요청과 관련되지 않은 작업을 수행할 수 있습니다. 폴더 이름은 Next.js 라우팅 시스템에서 무시되는 개인 폴더로 표시하기 위해 밑줄(`_`) 접두사가 붙습니다.

- (public)
  [로그인 페이지](https://jasonwatmore.com/#account-login-page-tsx) 및 [등록 페이지](https://jasonwatmore.com/#account-register-page-tsx)를 포함한 공개 페이지.  
  폴더 이름은 URL 경로에 포함되지 않도록 경로 그룹으로 표시하기 위해 괄호()로 묶여 있습니다.

- (secure)
  인증된 사용자만 액세스할 수 있는 보안 페이지입니다. 인증되지 않은 사용자는 [보안 레이아웃](https://jasonwatmore.com/#secure-layout-tsx)에 의해 자동으로 로그인 페이지로 리디렉션됩니다. 폴더 이름은 URL 경로에 포함되지 않도록 경로 그룹으로 표시하기 위해 괄호`()`로 묶여 있습니다.

- api
  Next.js 로그인 자습서 앱용 API 라우트 핸들러입니다. 라우트 핸들러는 미들웨어 및 전역 오류 처리에 대한 지원을 추가하기 위해 모두 사용자 정의 [API 핸들러](https://jasonwatmore.com/#api-handler-ts) 기능으로 래핑됩니다.
  참고: 새로운 앱 라우터 미들웨어로 이것을 시도했지만 try/catch 블록(익스프레스 미들웨어와 같은)에서 NextResponse.next()를 래핑하여 오류 처리를 지원하지 않는 것 같습니다.

## TypeScript 코드 구조

파일을 열 때 내보낸 모든 모듈을 쉽게 볼 수 있도록 상단에 `export` 문이 있는 TypeScript 파일을 구성했습니다. 내보내기 문 다음에는 각 TS 모듈에 대한 기능 및 기타 구현 코드가 옵니다.

## 배럴 파일

일부 폴더(예: \_components, \_helpers, \_services)의 `index.ts` 파일은 각 모듈의 전체 경로 대신 폴더 경로만 사용하여 가져올 수 있도록 해당 폴더에서 모든 모듈을 다시 내보내는 배럴 파일입니다. 단일 가져오기에서 여러 모듈 가져오기를 활성화합니다(예: `import { Alert, Nav } from '_components';`).

## 가져오기의 기본 URL

`baseUrl`은 [tsconfig.json](https://jasonwatmore.com/#tsconfig-json) 파일에서 `"./app"`로 설정되어 프로젝트의 /app 폴더를 기준으로 모든 import 문(점 '.' 접두어 없음)을 만들고 `import { useUserService } from '../../../_services';`와 같은 긴 상대 경로가 필요하지 않습니다.

## Next.js 프로젝트 구조

```ts
- app
  - _components
  - _helpers
    - client
    - server
  - _services
    - useUserService.ts
  - (public)
    - login/page.tsx
    - register/page.tsx
  - (private)
    - page.tsx
  - account
    - login/route.ts
    - logout/route.ts
    - register/route.ts
    - users
      - route.ts
      - [id]/route.ts
  - globals.css
  - layout.tsx
- .env
- .eslintrc.json
- next.config.js
- package.json
- tsconfig.json
```

# Next.js API 핸들러

**Path: /app/\_helpers/server/api/api-handler.ts**

API 핸들러는 `/app/api` 폴더에 있는 모든 API 경로 처리기(예: [로그인 라우트 핸들러](https://jasonwatmore.com/#api-account-login-route-ts), [등록 라우트 핸들러](https://jasonwatmore.com/#api-account-register-route-ts))에 대한 래퍼 기능입니다.

Next.js API 요청 파이프라인에 전역 미들웨어를 추가할 수 있고 전역 예외 처리에 대한 지원을 추가합니다. 래퍼 함수는 `핸들러`가 지원하는 각 HTTP 메서드(예: `GET`, `POST`, `PUT`, `DELETE` 등)에 대한 메서드를 포함하는 핸들러 개체를 허용합니다. 지원되지 않는 HTTP 메서드에 대한 요청이 수신되면 `405 메서드가 허용되지 않음` 응답이 반환됩니다.

## NextRequest json() 메서드는 한 번만 호출할 수 있습니다.

`req.json()` 메서드는 API 요청의 JSON 본문을 읽기 위해 한 번만 호출할 수 있으므로 두 번 읽어야 하는 경우(예: 미들웨어 및 경로 핸들러에서) 문제가 발생합니다. 이 제한을 해결하기 위해 `json()` 메서드를 첫 번째 호출에서 JSON 본문의 저장된 복사본을 반환하는 함수로 대체했습니다(일명 원숭이 패치).

## 글로벌 API 미들웨어

[JWT 미들웨어](https://jasonwatmore.com/#jwt-middleware-ts)는 보안 API 라우트에 대한 인증 토큰 쿠키를 확인합니다.

[유효성 검사 미들웨어](https://jasonwatmore.com/#validate-middleware-ts)는 스키마(제공된 경우)에 대해 JSON 요청 본문의 유효성을 검사합니다. 유효성 검사 스키마는 Joi 라이브러리로 생성되며 필요한 경우 라우트 핸들러 기능(예: [로그인 라우트 핸들러](https://jasonwatmore.com/#api-account-login-route-ts))에 `.schema` 속성으로 첨부됩니다.

## 전역 API 예외 핸들러

오류 핸들러는 단일 위치에서 모든 API 예외를 처리하는 데 사용됩니다.

## 새로운 Next.js 13 앱 라우터 미들웨어를 사용하지 않는 이유는?

[새로운 Next.js 앱 라우터 미들웨어](https://nextjs.org/docs/app/building-your-application/routing/middleware)로 모든 API 처리기 기능을 구현하려고 했지만 아래와 같은 `try/catch` 블록으로 전역 오류 처리를 지원하지 않는 것 같습니다.

# Next.js API 전역 오류 처리기

**Path: /app/\_helpers/server/api/error-handler.ts**

전역 오류 핸들러는 모든 API 오류를 포착하고 Next.js 자습서 API 전체에서 중복된 오류 처리 코드의 필요성을 제거하는 데 사용됩니다. [API 핸들러](https://jasonwatmore.com/#api-handler-ts) 래퍼 함수의 요청 파이프라인에 추가됩니다.

규칙에 따라 `'string'` 유형의 오류는 사용자 지정(앱별) 오류로 처리됩니다.  
이는 사용자 지정 오류가 발생하는 경우 문자열만 throw하면 되므로(예: `throw '사용자 이름 또는 비밀번호가 올바르지 않습니다'`) 사용자 지정 오류를 발생시키는 코드를 단순화합니다. `'찾을 수 없음'`이라는 단어로 끝납니다. `404` 응답 코드가 반환되고, 그렇지 않으면 표준 `400` 오류 응답이 반환됩니다.

오류가 이름이 `'JsonWebTokenError'`인 개체인 경우 JWT 토큰 유효성 검사가 실패하여 `'Unauthorized'` 메시지와 함께 HTTP 401 무단 응답 코드가 반환됨을 의미합니다. 사용자를 자동으로 로그아웃하기 위해 인증 쿠키도 삭제됩니다(있는 경우).

다른 모든(처리되지 않은) 예외는 콘솔에 기록되고 `500` 서버 오류 응답 코드를 반환합니다.

```ts
import { NextResponse } from "next/server";
import { cookies } from "next/headers";

export function errorHandler(err: Error | string) {
  if (typeof err === "string") {
    // 커스텀 애플리케이션 오류
    const is404 = err.toLowerCase().endsWith("not found");
    const status = is404 ? 404 : 400;

    return NextResponse.json({ message: err }, { status });
  }

  if (err.name === "JsonWebTokenError") {
    // JWT 에러 - 자동 로그아웃 및 쿠키 삭제
    cookies().delete("authorization");

    return NextResponse.json({ message: "Unauthorized" }, { status: 401 });
  }

  // 500 서버 에러
  console.error(err);

  return NextResponse.json({ message: err.message }, { status: 500 });
}
```

# Next.js API JWT 미들웨어

Path: /app/\_helpers/server/api/jwt-middleware.ts

JWT 미들웨어는 요청이 보안 API 라우트에 대한 것인 경우(퍼블릭 라우트는 우회됨) [auth helper](https://jasonwatmore.com/#auth-ts)를 사용하여 JWT 토큰을 확인합니다. 토큰이 유효하지 않으면 오류가 발생하여 [전역 오류 처리기](https://jasonwatmore.com/#error-handler-ts)가 '401 Unauthorized' 응답을 반환합니다.

미들웨어는 [API 핸들러](https://jasonwatmore.com/#api-handler-ts) 래퍼 함수의 Next.js 요청 파이프라인에 추가됩니다.

현재 사용자 ID는 요청 헤더에 추가되어 요청의 다른 코드 (예: [users repo](https://jasonwatmore.com/))

```ts
import { NextRequest } from "next/server";
import { auth } from "_helpers/server";

function isPublicPath(req: NextRequest) {
  // 인증이 필요하지 않은 퍼블릭 라우트
  const publicPaths = [
    "POST:/api/account/login",
    "POST:/api/account/logout",
    "POST:/api/account/register",
  ];

  return publicPaths.includes(`${req.method}:${req.nextUrl.pathname}`);
}

export async function jwtMiddleware(req: NextRequest) {
  if (isPublicPath(req)) return;

  // Request 쿠키에서 토큰 확인
  const id = auth.verifyToken();

  req.headers.set("userid", id);
}
```

# Next.js API 유효성 검사 미들웨어

Path: /app/\_helpers/server/api/validate-middleware.ts

유효성 검사 미들웨어는 Joi 라이브러리를 사용하여 스키마에 정의된 규칙에 대해 요청 본문의 JSON 데이터 유효성을 검사합니다. 유효성 검사에 실패하면 쉼표로 구분된 모든 오류 메시지 목록과 함께 오류가 발생합니다.

유효성 검사 스키마는 필요한 경우 경로 처리기 기능(예: [register route handler](https://jasonwatmore.com/#api-account-register-route-ts))의 `schema` 속성에 할당됩니다. `req` 및 `schema` 매개변수는 Next.js API에 대한 각 요청에 대해 `validateMiddleware()`를 호출할 때 [API Handler](https://jasonwatmore.com/#api-handler-ts)에 의해 전달됩니다. 스키마가 없으면 미들웨어가 무시됩니다.

```ts
import joi from "joi";

export async function validateMiddleware(
  req: Request,
  schema: joi.ObjectSchema
) {
  if (!schema) return;

  const options = {
    abortEarly: false, // 모든 오류 포함
    allowUnknown: true, // 알 수 없는 props 무시
    stripUnknown: true, // 알 수 없는 props 제거
  };

  const body = await req.json();
  const { error, value } = schema.validate(body, options);

  if (error) {
    throw `Validation error: ${error.details.map((x) => x.message).join(", ")}`;
  }

  // 삭제된 요청 본문을 반환하도록 req.json() 업데이트

  req.json = () => value;
}
```

# Auth Helper

Path: /app/\_helpers/server/auth.ts

auth helper는 request `authorization` 쿠키에서 JWT 토큰을 확인하는 데 사용됩니다.

`verifyToken` 함수는 `jsonwebtoken` 라이브러리를 사용하여 JWT 인증 토큰을 확인합니다. 예제에서는 [JWT 미들웨어](https://jasonwatmore.com/#jwt-middleware-ts)에서 호출됩니다. 성공하면 인증 토큰에서 디코딩된 사용자 `ID`가 반환됩니다. 실패하면 `jwt.verify()` 메서드에서 예외(`'JsonWebTokenError'`)가 발생합니다.

`isAuthenticated` 함수는 현재 인증 상태를 부울(`true` 또는 `false`)로 반환하기 위해 try/catch 블록에서 `verifyToken()`을 실행하는 간단한 래퍼입니다. [보안 레이아웃](https://jasonwatmore.com/#secure-layout-tsx) 및 [계정 레이아웃 컴포넌트](https://jasonwatmore.com/#account-layout-tsx)에 사용됩니다.

```ts
import jwt from "jsonwebtoken";
import { cookies } from "next/headers";

function verifyToken() {
  const token = cookies().get("authorization")?.value ?? "";
  const decoded = jwt.verify(token, process.env.JWT_SECRET!);
  const id = decoded.sub as string;
  return id;
}

function isAuthenticated() {
  try {
    verifyToken();
    return true;
  } catch {
    return false;
  }
}

export const auth = {
  isAuthenticated,
  verifyToken,
};
```

# MongoDB 데이터 컨텍스트

Path: /app/\_helpers/server/db.ts

MongoDB 데이터 컨텍스트는 Mongoose를 사용하여 MongoDB에 연결하는 데 사용되며 애플리케이션의 모든 데이터베이스 모델 개체(현재는 `사용자`만 포함)를 포함하는 개체를 내보냅니다. 단일 지점에서 데이터베이스의 모든 부분을 쉽게 읽고 쓸 수 있는 방법을 제공합니다.

데이터베이스 연결 문자열(`MONGODB_URI`) 환경 변수는 [.env](https://jasonwatmore.com/#dotenv) 파일에 정의되어 있습니다.

```ts
import mongoose from "mongoose";

const Schema = mongoose.Schema;

mongoose.connect(process.env.MONGODB_URI!);
mongoose.Promise = global.Promise;

export const db = {
  User: userModel(),
};

// mongoose models with schema definitions

function userModel() {
  const schema = new Schema(
    {
      username: { type: String, unique: true, required: true },
      hash: { type: String, required: true },
      firstName: { type: String, required: true },
      lastName: { type: String, required: true },
    },
    {
      // add createdAt and updatedAt timestamps
      timestamps: true,
    }
  );

  schema.set("toJSON", {
    virtuals: true,
    versionKey: false,
    transform: function (doc, ret) {
      delete ret._id;
      delete ret.hash;
    },
  });

  return mongoose.models.User || mongoose.model("User", schema);
}
```

# MongoDB Users Repo

Path: /app/\_helpers/server/users-repo.ts

사용자 저장소는 MongoDB에 저장된 사용자 데이터에 대한 모든 액세스를 캡슐화하고, 사용자 데이터를 읽고 관리하기 위한 인증 방법과 표준 CRUD 작업을 공개합니다. 저장소는 Next.js 사용자 및 계정 API 경로 핸들러(예: 계정 로그인 경로, 사용자 [id] 경로)에 의해 서버 측에서 사용됩니다.

## User authentication

`인증 방법`은 제공된 사용자 이름과 비밀번호를 확인합니다. 성공하면 JWT(JSON 웹 토큰)가 `jsonwebtoken` npm 패키지로 생성되고 토큰은 비밀 키(JWT_SECRET)를 사용하여 디지털 서명되므로 변조할 수 없으며 jwt 비밀은 .env 파일에 정의됩니다.

JWT는 `로그인 라우트 핸들러`에 의해 HTTP 전용 'authorization' 쿠키로 클라이언트에 반환되며, 쿠키는 브라우저에서 API로의 후속 요청과 함께 자동으로 전송됩니다.

## Password encryption

Bcrypt는 Next.js 튜토리얼에서 `bcryptjs` 라이브러리를 사용하여 비밀번호를 해시하고 확인하는 데 사용됩니다. 자세한 내용은 Node.js - Bcrypt로 비밀번호 해시 및 확인을 참조하세요.

```ts
import jwt from "jsonwebtoken";
import bcrypt from "bcryptjs";
import { headers } from "next/headers";
import { db } from "./db";

const User = db.User;

export const usersRepo = {
  authenticate,
  getAll,
  getById,
  getCurrent,
  create,
  update,
  delete: _delete,
};

async function authenticate({
  username,
  password,
}: {
  username: string;
  password: string;
}) {
  const user = await User.findOne({ username });

  if (!(user && bcrypt.compareSync(password, user.hash))) {
    throw "Username or password is incorrect";
  }

  // create a jwt token that is valid for 7 days
  const token = jwt.sign({ sub: user.id }, process.env.JWT_SECRET!, {
    expiresIn: "7d",
  });

  return {
    user: user.toJSON(),
    token,
  };
}

async function getAll() {
  return await User.find();
}

async function getById(id: string) {
  try {
    return await User.findById(id);
  } catch {
    throw "User Not Found";
  }
}

async function getCurrent() {
  try {
    const currentUserId = headers().get("userId");
    return await User.findById(currentUserId);
  } catch {
    throw "Current User Not Found";
  }
}

async function create(params: any) {
  // validate
  if (await User.findOne({ username: params.username })) {
    throw 'Username "' + params.username + '" is already taken';
  }

  const user = new User(params);

  // hash password
  if (params.password) {
    user.hash = bcrypt.hashSync(params.password, 10);
  }

  // save user
  await user.save();
}

async function update(id: string, params: any) {
  const user = await User.findById(id);

  // validate
  if (!user) throw "User not found";
  if (
    user.username !== params.username &&
    (await User.findOne({ username: params.username }))
  ) {
    throw 'Username "' + params.username + '" is already taken';
  }

  // hash password if it was entered
  if (params.password) {
    params.hash = bcrypt.hashSync(params.password, 10);
  }

  // copy params properties to user
  Object.assign(user, params);

  await user.save();
}

async function _delete(id: string) {
  await User.findByIdAndRemove(id);
}
```

# 경고 서비스 React Hook

Path: /app/\_services/useAlertService.ts

경고 서비스는 Next.js 튜토리얼 앱의 컴포넌트와 알림을 표시하는 경고 컴포넌트 사이의 브리지 역할을 하는 React 후크입니다. 여기에는 경고를 표시하고 지우는 메서드와 현재 경고에 액세스하기 위한 경고 속성이 포함되어 있습니다.

## 경고 표시

다양한 유형의 경고를 표시하는 메서드(success() 및 error()) 중 하나를 호출하여 애플리케이션의 모든 컴포넌트에서 경고 알림을 트리거할 수 있습니다.

## 경고 방법 매개변수

- 첫 번째 매개변수는 경고 `message` 문자열입니다.
- 두 번째는 라우트가 한 번 변경된 후(예: 등록 성공 후 리디렉션) 경고를 계속 표시하는 선택적 `showAfterRedirect` 부울 매개 변수입니다. 기본값은 `false`입니다.

## Zustand 상태 관리

Zustand는 공유 상태 데이터(경고)를 관리하기 위해 경고 서비스에서 내부적으로 사용되므로 애플리케이션의 모든 컴포넌트에서 액세스할 수 있습니다. 이는 React 상태 관리를 위한 Redux의 가벼운 대안입니다. 자세한 내용은 https://docs.pmnd.rs/zustand/getting-started/introduction을 참조하세요.

## TypeScript interfaces

TypeScript 인터페이스는 경고 개체(`IAlert`), zustand 경고 상태 저장소(`IAlertStore`) 및 경고 서비스(`IAlertService`)의 속성과 메서드를 정의하는 데 사용됩니다. 경고 서비스 인터페이스는 경고 저장소 인터페이스를 확장하여 서비스 인터페이스에 경고 속성을 포함합니다.

```ts
import { create } from "zustand";

export { useAlertService };

// alert state store
const alertStore = create<IAlertStore>(() => ({}));

function useAlertService(): IAlertService {
  const { alert } = alertStore();

  return {
    alert,
    success: (message: string, showAfterRedirect = false) => {
      const type = "alert-success";
      alertStore.setState({
        alert: { type, message, showAfterRedirect },
      });
    },
    error: (message: string, showAfterRedirect = false) => {
      const type = "alert-danger";
      alertStore.setState({
        alert: { type, message, showAfterRedirect },
      });
    },
    clear: () => {
      alertStore.setState((state) => {
        let alert = state.alert;

        // if showAfterRedirect is true the alert is kept for
        // one route change (e.g. after successful registration)
        if (alert?.showAfterRedirect) {
          alert.showAfterRedirect = false;
        } else {
          alert = undefined;
        }

        return { alert };
      });
    },
  };
}

// interfaces

interface IAlert {
  type: string;
  message: string;
  showAfterRedirect: boolean;
}

interface IAlertStore {
  alert?: IAlert;
}

interface IAlertService extends IAlertStore {
  success: (message: string, showAfterRedirect?: boolean) => void;
  error: (message: string, showAfterRedirect?: boolean) => void;
  clear: () => void;
}
```

# 사용자 서비스 React Hook

Path: /app/\_services/useUserService.ts

사용자 서비스는 클라이언트 측 로직을 캡슐화하고 사용자와 관련된 모든 것에 대해 React 프런트엔드와 Next.js 백엔드 API 간의 HTTP 통신을 처리하는 React 후크입니다.

여기에는 앱 로그인 및 로그아웃, 새 사용자 등록을 위한 메서드와 사용자 데이터를 검색, 생성 및 업데이트하기 위한 표준 CRUD 메서드가 포함되어 있습니다. HTTP 요청은 useFetch 후크를 통해 전송됩니다.

## Zustand 상태 관리

Zustand는 공유 상태 속성(`users`, `user`, `currentUser`)을 관리하기 위해 사용자 서비스에서 내부적으로 사용하는 경량 React 상태 관리 라이브러리이므로 애플리케이션의 모든 컴포넌트에서 액세스할 수 있습니다.

예를 들어 사용자 목록 페이지는 로드 시 userService.getAll()을 호출하고 userService.users 속성을 읽어 모든 사용자를 표시합니다. getAll() 메서드는 API에서 모든 사용자를 가져와 페이지의 다시 렌더링을 트리거하는 사용자 공유 상태 속성(userStore.setState() 사용)에 할당합니다.

`getById()` 및 `getCurrent()` 메서드는 `user` 및 `currentUser` 공유 상태 속성을 가져오고 설정하여 동일한 방식으로 작동합니다.

## TypeScript 인터페이스

TypeScript 인터페이스는 사용자 개체(`IUser`), zustand 사용자 상태 저장소(`IUserStore`) 및 사용자 서비스(`IUserService`)의 속성과 메서드를 정의하는 데 사용됩니다. 사용자 서비스 인터페이스는 사용자 저장소 인터페이스를 확장하여 서비스 인터페이스에 공유 상태 속성(사용자, 사용자 및 현재 사용자)을 포함합니다.

```ts
import { create } from "zustand";
import { useRouter, useSearchParams } from "next/navigation";

import { useAlertService } from "_services";
import { useFetch } from "_helpers/client";

export { useUserService };

// user state store
const initialState = {
  users: undefined,
  user: undefined,
  currentUser: undefined,
};
const userStore = create<IUserStore>(() => initialState);

function useUserService(): IUserService {
  const alertService = useAlertService();
  const fetch = useFetch();
  const router = useRouter();
  const searchParams = useSearchParams();
  const { users, user, currentUser } = userStore();

  return {
    users,
    user,
    currentUser,
    login: async (username, password) => {
      alertService.clear();
      try {
        const currentUser = await fetch.post("/api/account/login", {
          username,
          password,
        });
        userStore.setState({ ...initialState, currentUser });

        // get return url from query parameters or default to '/'
        const returnUrl = searchParams.get("returnUrl") || "/";
        router.push(returnUrl);
      } catch (error: any) {
        alertService.error(error);
      }
    },
    logout: async () => {
      await fetch.post("/api/account/logout");
      router.push("/account/login");
    },
    register: async (user) => {
      try {
        await fetch.post("/api/account/register", user);
        alertService.success("Registration successful", true);
        router.push("/account/login");
      } catch (error: any) {
        alertService.error(error);
      }
    },
    getAll: async () => {
      userStore.setState({ users: await fetch.get("/api/users") });
    },
    getById: async (id) => {
      userStore.setState({ user: undefined });
      try {
        userStore.setState({ user: await fetch.get(`/api/users/${id}`) });
      } catch (error: any) {
        alertService.error(error);
      }
    },
    getCurrent: async () => {
      if (!currentUser) {
        userStore.setState({
          currentUser: await fetch.get("/api/users/current"),
        });
      }
    },
    create: async (user) => {
      await fetch.post("/api/users", user);
    },
    update: async (id, params) => {
      await fetch.put(`/api/users/${id}`, params);

      // update current user if the user updated their own record
      if (id === currentUser?.id) {
        userStore.setState({ currentUser: { ...currentUser, ...params } });
      }
    },
    delete: async (id) => {
      // set isDeleting prop to true on user
      userStore.setState({
        users: users!.map((x) => {
          if (x.id === id) {
            x.isDeleting = true;
          }
          return x;
        }),
      });

      // delete user
      const response = await fetch.delete(`/api/users/${id}`);

      // remove deleted user from state
      userStore.setState({ users: users!.filter((x) => x.id !== id) });

      // logout if the user deleted their own record
      if (response.deletedSelf) {
        router.push("/account/login");
      }
    },
  };
}

// interfaces

interface IUser {
  id: string;
  firstName: string;
  lastName: string;
  username: string;
  password: string;
  isDeleting?: boolean;
}

interface IUserStore {
  users?: IUser[];
  user?: IUser;
  currentUser?: IUser;
}

interface IUserService extends IUserStore {
  login: (username: string, password: string) => Promise<void>;
  logout: () => Promise<void>;
  register: (user: IUser) => Promise<void>;
  getAll: () => Promise<void>;
  getById: (id: string) => Promise<void>;
  getCurrent: () => Promise<void>;
  create: (user: IUser) => Promise<void>;
  update: (id: string, params: Partial<IUser>) => Promise<void>;
  delete: (id: string) => Promise<void>;
}
```

# Next.js Login Form

Path: /app/(public)/account/login/page.tsx

로그인 페이지에는 Next.js 튜토리얼 앱에 로그인하기 위한 사용자 이름 및 비밀번호 필드가 포함된 React Hook Form 라이브러리로 구축된 양식이 포함되어 있습니다.

`useForm()` 후크 함수는 입력 등록, 양식 제출 처리, 양식 재설정, 양식 상태 액세스, 오류 표시 등을 포함한 양식 작업을 위한 메소드가 포함된 객체를 반환합니다. 전체 목록은 https://react-hook-를 참조하세요. form.com/docs/useform.

양식 유효성 검사 규칙은 각 양식 입력 필드를 등록하는 데 사용되는 React Hook Form `register()` 함수에 전달된 옵션으로 정의됩니다. 사용 가능한 유효성 검사 옵션의 전체 목록은 https://react-hook-form.com/docs/useform/register를 참조하세요.

`onSubmit` 함수는 양식이 제출되고 유효할 때 호출되며 `userService.login()`을 호출하여 API에 사용자 자격 증명을 제출합니다.

반환된 TSX/JSX 템플릿에는 모든 입력 필드와 유효성 검사 메시지가 포함된 양식이 포함되어 있습니다. 양식 필드는 JS 스프레드 연산자(예: `{...fields.username}`)를 사용하여 각 입력 요소에 바인딩됩니다.

```tsx
"use client";

import Link from "next/link";
import { useForm } from "react-hook-form";

import { useUserService } from "_services";

export default Login;

function Login() {
  const userService = useUserService();

  // get functions to build form with useForm() hook
  const { register, handleSubmit, formState } = useForm();
  const { errors } = formState;

  const fields = {
    username: register("username", { required: "Username is required" }),
    password: register("password", { required: "Password is required" }),
  };

  async function onSubmit({ username, password }: any) {
    await userService.login(username, password);
  }

  return (
    <div className="card">
      <h4 className="card-header">Login</h4>
      <div className="card-body">
        <form onSubmit={handleSubmit(onSubmit)}>
          <div className="mb-3">
            <label className="form-label">Username</label>
            <input
              {...fields.username}
              type="text"
              className={`form-control ${errors.username ? "is-invalid" : ""}`}
            />
            <div className="invalid-feedback">
              {errors.username?.message?.toString()}
            </div>
          </div>
          <div className="mb-3">
            <label className="form-label">Password</label>
            <input
              {...fields.password}
              type="password"
              className={`form-control ${errors.password ? "is-invalid" : ""}`}
            />
            <div className="invalid-feedback">
              {errors.password?.message?.toString()}
            </div>
          </div>
          <button disabled={formState.isSubmitting} className="btn btn-primary">
            {formState.isSubmitting && (
              <span className="spinner-border spinner-border-sm me-1"></span>
            )}
            Login
          </button>
          <Link href="/account/register" className="btn btn-link">
            Register
          </Link>
        </form>
      </div>
    </div>
  );
}
```

# Next.js Registration Form

Path: /app/(public)/account/register/page.tsx

등록 페이지에는 이름, 성, 사용자 이름 및 비밀번호 필드가 있는 React Hook Form 라이브러리로 구축된 간단한 등록 양식이 포함되어 있습니다.

`useForm()` 후크 함수는 입력 등록, 양식 제출 처리, 양식 재설정, 양식 상태 액세스, 오류 표시 등을 포함한 양식 작업을 위한 메소드가 포함된 객체를 반환합니다. 전체 목록은 https://react-hook-를 참조하세요. form.com/docs/useform.

양식 유효성 검사 규칙은 각 양식 입력 필드를 등록하는 데 사용되는 React Hook Form `register()` 함수에 전달된 옵션으로 정의됩니다. 사용 가능한 유효성 검사 옵션의 전체 목록은 https://react-hook-form.com/docs/useform/register를 참조하세요.

`onSubmit` 함수는 양식이 제출되고 유효할 때 호출되며 `userService.register()`를 호출하여 양식 데이터를 Next.js API에 제출합니다.

반환된 TSX/JSX 템플릿에는 모든 입력 필드와 유효성 검사 메시지가 포함된 양식이 포함되어 있습니다. 양식 필드는 JS 스프레드 연산자(예: `{...fields.username}`)를 사용하여 각 입력 요소에 바인딩됩니다.

```tsx
"use client";

import Link from "next/link";
import { useForm } from "react-hook-form";

import { useUserService } from "_services";

export default Register;

function Register() {
  const userService = useUserService();

  // get functions to build form with useForm() hook
  const { register, handleSubmit, formState } = useForm();
  const { errors } = formState;

  const fields = {
    firstName: register("firstName", { required: "First Name is required" }),
    lastName: register("lastName", { required: "Last Name is required" }),
    username: register("username", { required: "Username is required" }),
    password: register("password", {
      required: "Password is required",
      minLength: {
        value: 6,
        message: "Password must be at least 6 characters",
      },
    }),
  };

  async function onSubmit(user: any) {
    await userService.register(user);
  }

  return (
    <div className="card">
      <h4 className="card-header">Register</h4>
      <div className="card-body">
        <form onSubmit={handleSubmit(onSubmit)}>
          <div className="mb-3">
            <label className="form-label">First Name</label>
            <input
              {...fields.firstName}
              type="text"
              className={`form-control ${errors.firstName ? "is-invalid" : ""}`}
            />
            <div className="invalid-feedback">
              {errors.firstName?.message?.toString()}
            </div>
          </div>
          <div className="mb-3">
            <label className="form-label">Last Name</label>
            <input
              {...fields.lastName}
              type="text"
              className={`form-control ${errors.lastName ? "is-invalid" : ""}`}
            />
            <div className="invalid-feedback">
              {errors.lastName?.message?.toString()}
            </div>
          </div>
          <div className="mb-3">
            <label className="form-label">Username</label>
            <input
              {...fields.username}
              type="text"
              className={`form-control ${errors.username ? "is-invalid" : ""}`}
            />
            <div className="invalid-feedback">
              {errors.username?.message?.toString()}
            </div>
          </div>
          <div className="mb-3">
            <label className="form-label">Password</label>
            <input
              {...fields.password}
              type="password"
              className={`form-control ${errors.password ? "is-invalid" : ""}`}
            />
            <div className="invalid-feedback">
              {errors.password?.message?.toString()}
            </div>
          </div>
          <button disabled={formState.isSubmitting} className="btn btn-primary">
            {formState.isSubmitting && (
              <span className="spinner-border spinner-border-sm me-1"></span>
            )}
            Register
          </button>
          <Link href="/account/login" className="btn btn-link">
            Cancel
          </Link>
        </form>
      </div>
    </div>
  );
}
```

Next.js Account Layout

Path: /app/(public)/account/layout.tsx

계정 레이아웃 컴포넌트에는 `/app/(public)/account` 폴더의 모든 페이지에 대한 공통 레이아웃 코드가 포함되어 있으며, 일부 부트스트랩 클래스와 함께 `div`에 래핑된 `{children}` 요소 위에 `<Alert />` 컴포넌트가 포함되어 있습니다. 모든 계정 페이지의 너비 및 정렬.

이미 로그인한 사용자는 자동으로 홈 페이지로 리디렉션되도록 상단에서 인증 상태를 확인합니다.

```tsx
import { redirect } from "next/navigation";

import { auth } from "_helpers/server";
import { Alert } from "_components";

export default Layout;

function Layout({ children }: { children: React.ReactNode }) {
  // if logged in redirect to home page
  if (auth.isAuthenticated()) {
    redirect("/");
  }

  return (
    <>
      <Alert />
      <div className="col-md-6 offset-md-3 mt-5">{children}</div>
    </>
  );
}
```

# Add User Page

Path: /app/(secure)/users/add/page.tsx

사용자 추가 페이지는 사용자를 지정하지 않고 단순히 `user` 추가/편집 컴포넌트를 렌더링하므로 컴포넌트가 "추가" 모드로 설정됩니다.

```tsx
import { AddEdit } from "_components/users";

export default Add;

function Add() {
  return <AddEdit title="Add User" />;
}
```

# Edit User Page

Path: /app/(secure)/users/edit/[id]/page.tsx

사용자 편집 페이지는 지정된 `사용자`와 함께 사용자 추가/편집 컴포넌트를 렌더링하므로 해당 컴포넌트는 "편집" 모드로 설정됩니다. 사용자는 URL의 `id` 매개변수를 사용하여 `useEffect()` React 후크의 API에서 가져오며, id는 구성요소 매개변수를 통해 액세스할 수 있습니다.

```tsx
"use client";

import { useEffect } from "react";
import { useRouter } from "next/navigation";

import { AddEdit } from "_components/users";
import { Spinner } from "_components";
import { useUserService } from "_services";

export default Edit;

function Edit({ params: { id } }: any) {
  const router = useRouter();
  const userService = useUserService();
  const user = userService.user;

  useEffect(() => {
    if (!id) return;

    // fetch user for add/edit form
    userService.getById(id);
  }, [router]);

  return user ? <AddEdit title="Edit User" user={user} /> : <Spinner />;
}
```

# Users List Page

Path: /app/(secure)/users/page.tsx

사용자 목록 페이지에는 Next.js 튜토리얼 앱의 모든 사용자 목록이 표시되며 사용자를 추가, 편집 및 삭제하기 위한 버튼이 포함되어 있습니다.

모든 사용자는 사용자 서비스와 함께 `useEffect()` 후크에서 가져오고 `userService.users` 속성을 통해 액세스됩니다.

삭제 버튼은 `userService.deleteUser()` 메서드를 호출합니다. 이 메소드는 사용자가 API에서 삭제되는 동안 UI가 삭제 버튼에 스피너를 표시하도록 사용자 `isDeleting` 속성을 설정합니다. 현재 사용자가 자신의 기록을 삭제하면 앱에서 자동으로 로그아웃됩니다.

```tsx
"use client";

import Link from "next/link";
import { useEffect } from "react";

import { Spinner } from "_components";
import { useUserService } from "_services";

export default Users;

function Users() {
  const userService = useUserService();
  const users = userService.users;

  useEffect(() => {
    userService.getAll();
  }, []);

  return (
    <>
      <h1>Users</h1>
      <Link href="/users/add" className="btn btn-sm btn-success mb-2">
        Add User
      </Link>
      <table className="table table-striped">
        <thead>
          <tr>
            <th style={{ width: "30%" }}>First Name</th>
            <th style={{ width: "30%" }}>Last Name</th>
            <th style={{ width: "30%" }}>Username</th>
            <th style={{ width: "10%" }}></th>
          </tr>
        </thead>
        <tbody>
          <TableBody />
        </tbody>
      </table>
    </>
  );

  function TableBody() {
    if (users?.length) {
      return users.map((user) => (
        <tr key={user.id}>
          <td>{user.firstName}</td>
          <td>{user.lastName}</td>
          <td>{user.username}</td>
          <td style={{ whiteSpace: "nowrap" }}>
            <Link
              href={`/users/edit/${user.id}`}
              className="btn btn-sm btn-primary me-1"
            >
              Edit
            </Link>
            <button
              onClick={() => userService.delete(user.id)}
              className="btn btn-sm btn-danger btn-delete-user"
              style={{ width: "60px" }}
              disabled={user.isDeleting}
            >
              {user.isDeleting ? (
                <span className="spinner-border spinner-border-sm"></span>
              ) : (
                <span>Delete</span>
              )}
            </button>
          </td>
        </tr>
      ));
    }

    if (!users) {
      return (
        <tr>
          <td colSpan={4}>
            <Spinner />
          </td>
        </tr>
      );
    }

    if (users?.length === 0) {
      return (
        <tr>
          <td colSpan={4} className="text-center">
            <div className="p-2">No Users To Display</div>
          </td>
        </tr>
      );
    }
  }
}
```

# Next.js Secure Layout

Path: /app/(secure)/layout.tsx

보안 레이아웃 컴포넌트에는 `/app/(secure)` 폴더의 모든 페이지에 대한 공통 레이아웃 코드가 포함되어 있으며, 기본 `<Nav />` 컴포넌트와 두 개의 두 개의 요소로 래핑된 `{children}` 요소 위에 `<Alert />` 컴포넌트가 포함되어 있습니다. 부트스트랩 클래스가 있는 div를 사용하여 모든 보안 페이지의 패딩과 정렬을 설정합니다.

상단에서 인증 상태를 확인하여 인증되지 않은 사용자를 쿼리 매개변수(`returnUrl`)의 요청 경로와 함께 로그인 페이지로 자동 리디렉션합니다. 요청 경로는 Next.js 요청 헤더 `x-invoke-path`를 통해 서버 컴포넌트에서 액세스할 수 있습니다.

```tsx
import { headers } from "next/headers";
import { redirect } from "next/navigation";

import { auth } from "_helpers/server";
import { Alert, Nav } from "_components";

export default Layout;

function Layout({ children }: { children: React.ReactNode }) {
  // if not logged in redirect to login page
  if (!auth.isAuthenticated()) {
    const returnUrl = encodeURIComponent(headers().get("x-invoke-path") || "/");
    redirect(`/account/login?returnUrl=${returnUrl}`);
  }

  return (
    <div className="app-container bg-light">
      <Nav />
      <Alert />
      <div className="p-4">
        <div className="container">{children}</div>
      </div>
    </div>
  );
}
```

# Home Page

Path: /app/(secure)/page.tsx

홈 페이지는 로그인한 사용자 이름과 사용자 섹션에 대한 링크가 포함된 환영 메시지를 표시하는 간단한 React 컴포넌트입니다.

현재 사용자는 사용자 서비스와 함께 `useEffect()` 후크에서 가져오고 `userService.currentUser` 속성을 통해 액세스됩니다.

```tsx
"use client";

import Link from "next/link";
import { useEffect } from "react";

import { useUserService } from "_services";
import { Spinner } from "_components";

export default Home;

function Home() {
  const userService = useUserService();
  const user = userService.currentUser;

  useEffect(() => {
    userService.getCurrent();
  }, []);

  if (user) {
    return (
      <>
        <h1>Hi {user.firstName}!</h1>
        <p>You&apos;re logged in with Next.js & JWT!!</p>
        <p>
          <Link href="/users">Manage Users</Link>
        </p>
      </>
    );
  } else {
    return <Spinner />;
  }
}
```

# Next.js API Login Route

Path: /app/api/account/login/route.ts

로그인 라우트 핸들러는 로그인 경로 `/api/account/login`으로 전송된 HTTP 요청을 수신합니다. `usersRepo.authenticate()` 함수에 의해 인증된 사용자 이름과 비밀번호가 포함된 HTTP `POST` 요청을 지원합니다. 성공하면 JWT 인증 토큰이 HTTP 전용 `'authorization'` 쿠키에 설정됩니다.

경로 핸들러는 `POST()` 메소드가 포함된 객체를 `login()` 함수에 매핑된 `apiHandler()`에 전달하여 HTTP `POST` 요청을 지원합니다.

요청 스키마는 `joi` 데이터 유효성 검사 라이브러리로 정의되고 `login.schema` 속성에 할당됩니다. 스키마 유효성 검사는 API 처리기가 호출하는 유효성 검사 미들웨어에 의해 처리됩니다.

```ts
import { cookies } from "next/headers";
import joi from "joi";

import { usersRepo } from "_helpers/server";
import { apiHandler } from "_helpers/server/api";

module.exports = apiHandler({
  POST: login,
});

async function login(req: Request) {
  const body = await req.json();
  const { user, token } = await usersRepo.authenticate(body);

  // return jwt token in http only cookie
  cookies().set("authorization", token, { httpOnly: true });

  return user;
}

login.schema = joi.object({
  username: joi.string().required(),
  password: joi.string().required(),
});
```

# Next.js API Logout Route

Path: /app/api/account/logout/route.ts

로그아웃 경로는 단순히 `'authorization'` 쿠키를 삭제하여 사용자를 로그아웃시킵니다. 쿠키는 HTTP 전용입니다. 즉, 서버에서만 액세스할 수 있습니다. 이는 악의적인 클라이언트 측 코드가 쿠키와 같은 개인 데이터에 액세스하려고 시도할 수 있는 XSS(교차 사이트 스크립팅)를 방지하여 보안을 향상시킵니다.

로그아웃하려면 클라이언트 측 사용자 서비스가 이 API 경로에 요청을 보낸 다음 로그인 페이지로 리디렉션합니다.

```ts
import { cookies } from "next/headers";

import { apiHandler } from "_helpers/server/api";

module.exports = apiHandler({
  POST: logout,
});

function logout() {
  cookies().delete("authorization");
}
```

# Next.js API Register Route

Path: /app/api/account/register/route.ts

등록 핸들러는 등록 경로 `/api/account/register`로 전송된 HTTP 요청을 수신합니다. `usersRepo.create()` 메서드를 통해 Next.js 튜토리얼 앱에 등록된 사용자 세부 정보가 포함된 HTTP `POST` 요청을 지원합니다.

라우트 핸들러는 `POST()` 메서드가 포함된 객체를 `apiHandler()` 함수에 전달하여 HTTP `POST` 요청을 지원합니다.

요청 스키마는 `joi` 데이터 유효성 검사 라이브러리로 정의되고 `register.schema` 속성에 할당됩니다. 스키마 유효성 검사는 API 처리기가 호출하는 유효성 검사 미들웨어에 의해 처리됩니다.

```ts
import joi from "joi";

import { usersRepo } from "_helpers/server";
import { apiHandler } from "_helpers/server/api";

module.exports = apiHandler({
  POST: register,
});

async function register(req: Request) {
  const body = await req.json();
  await usersRepo.create(body);
}

register.schema = joi.object({
  firstName: joi.string().required(),
  lastName: joi.string().required(),
  username: joi.string().required(),
  password: joi.string().min(6).required(),
});
```

# Users [id] API Route

Path: /app/api/users/[id]/route.ts

`[id]` 매개변수(예: `/api/users/*`)와 같은 값을 사용하여 HTTP 요청을 처리하는 동적 API 라우트 핸들러입니다. 사용자 `id` 매개변수는 Next.js에 의해 경로 핸들러에 전달되는 `params` 객체에 첨부됩니다.

라우트 핸들러는 해당 메소드 이름이 포함된 객체를 `getById()`, `update()` 및 `_delete()` 함수에 매핑하는 apiHandler()에 전달하여 HTTP `GET`, `PUT` 및 `DELETE` 요청을 지원합니다.

`update()` 함수에 대한 요청 스키마는 `joi` 데이터 유효성 검사 라이브러리로 정의되고 `update.schema` 속성에 할당됩니다. 스키마 유효성 검사는 API 처리기가 호출하는 유효성 검사 미들웨어에 의해 처리됩니다.

```ts
import joi from "joi";

import { cookies } from "next/headers";

import { apiHandler } from "_helpers/server/api";
import { usersRepo } from "_helpers/server";

module.exports = apiHandler({
  GET: getById,
  PUT: update,
  DELETE: _delete,
});

async function getById(req: Request, { params: { id } }: any) {
  return await usersRepo.getById(id);
}

async function update(req: Request, { params: { id } }: any) {
  const body = await req.json();
  await usersRepo.update(id, body);
}

update.schema = joi.object({
  firstName: joi.string(),
  lastName: joi.string(),
  username: joi.string(),
  password: joi.string().min(6).allow(""),
});

async function _delete(req: Request, { params: { id } }: any) {
  await usersRepo.delete(id);

  // auto logout if deleted self
  if (id === req.headers.get("userId")) {
    cookies().delete("authorization");
    return { deletedSelf: true };
  }
}
```

# Current User API Route

Path: /app/api/users/current/route.ts

현재 사용자 핸들러는 현재 사용자 경로 `/api/users/current`로 전송된 HTTP 요청을 수신합니다. 해당 메소드 이름이 있는 객체를 `getCurrent()` 함수에 매핑하는 apiHandler()에 전달하여 HTTP `GET` 요청을 지원합니다.

`getCurrent()` 함수는 `usersRepo.getCurrent()`를 호출하여 MongoDB에서 현재 로그인된 사용자 세부 정보를 반환합니다.

```ts
import { usersRepo } from "_helpers/server";
import { apiHandler } from "_helpers/server/api";

module.exports = apiHandler({
  GET: getCurrent,
});

async function getCurrent() {
  return await usersRepo.getCurrent();
}
```

# Users API Route

Path: /app/api/users/route.ts

사용자 핸들러는 기본 사용자 경로 `/api/users`로 전송된 HTTP 요청을 수신합니다. 해당 메소드 이름이 포함된 객체를 `getAll()` 및 `create()` 함수에 매핑하는 apiHandler()에 전달하여 HTTP `GET` 및 `POST` 요청을 지원합니다.

`getAll()` 함수는 `usersRepo.getAll()`을 호출하여 MongoDB의 모든 사용자를 반환합니다.

`create()` 함수는 `usersRepo.create()`를 호출하여 MongoDB에 새 사용자를 생성합니다.

`create()` 함수에 대한 요청 스키마는 `joi` 데이터 유효성 검사 라이브러리로 정의되고 `create.schema` 속성에 할당됩니다. 스키마 유효성 검사는 API 처리기가 호출하는 유효성 검사 미들웨어에 의해 처리됩니다.

이 경로(및 Next.js API의 다른 모든 보안 경로)에 대한 보안은 전역 JWT 미들웨어에 의해 처리됩니다.

```ts
import joi from "joi";

import { usersRepo } from "_helpers/server";
import { apiHandler } from "_helpers/server/api";

module.exports = apiHandler({
  GET: getAll,
  POST: create,
});

async function getAll() {
  return await usersRepo.getAll();
}

async function create(req: Request) {
  const body = await req.json();
  await usersRepo.create(body);
}

create.schema = joi.object({
  firstName: joi.string().required(),
  lastName: joi.string().required(),
  username: joi.string().required(),
  password: joi.string().min(6).required(),
});
```

# Global CSS

Path: /app/globals.css

globals.css 파일에는 Next.js 예제 인증 앱에 대한 전역 사용자 정의 CSS 스타일이 포함되어 있습니다. 루트 레이아웃 컴포넌트를 통해 튜토리얼 앱으로 가져옵니다.

```css
.app-container {
  min-height: 350px;
}
```

# Next.js Root Layout

Path: /app/layout.tsx

루트 레이아웃 컴포넌트에는 Next.js 앱의 외부 <html> 및 <body> 태그가 포함되어 있으며, <head> 태그의 SEO 메타데이터 요소(예: 제목 및 설명)는 내보낸 메타데이터 개체로 제어됩니다. 전역 CSS 스타일시트를 파일 상단으로 가져옵니다.

```tsx
import "bootstrap/dist/css/bootstrap.min.css";
import "globals.css";

export const metadata = {
  title: "Next.js 13 - User Registration and Login Example",
};

export default Layout;

function Layout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

# Dotenv (.env) file

Path: /app/.env

`.env` 파일에는 `process.env` 개체를 통해 Next.js 앱의 코드에 액세스할 수 있는 환경 변수가 포함되어 있습니다.

기본적으로 환경 변수는 서버 측 코드에만 액세스할 수 있으며, 환경 변수를 React 클라이언트 앱에 액세스할 수 있도록 하려면 접두사 `NEXT_PUBLIC_`을 붙여야 합니다. 자세한 내용은 Next.js - dotenv(.env)에서 환경 변수에 액세스를 참조하세요.

여러 dotenv 파일을 생성하여 다양한 환경을 구성할 수 있습니다. `.env`는 모든 환경에 적용되는 기본 파일입니다. 또한 개발 관련 변수에 대해 `.env.development`를 생성하고, prod에 대해 `.env.production`을, 로컬 환경에 대해 `.env.local`을 생성할 수도 있습니다.

중요: `JWT_SECRET` 환경 변수는 인증을 위해 JWT 토큰을 서명하고 확인하는 데 사용되며, 다른 사람이 Next.js 앱 또는 API에 대한 무단 액세스를 얻기 위해 동일한 비밀을 사용하여 JWT를 생성할 수 없도록 자신의 임의 문자열로 이를 변경합니다. 빠르고 쉬운 방법은 두 개의 GUID를 결합하여 긴 임의의 문자열을 만드는 것입니다(예: https://www.guidgenerator.com/에서).

```env
JWT_SECRET=THIS IS USED TO SIGN AND VERIFY JWT TOKENS, REPLACE IT WITH YOUR OWN SECRET, IT CAN BE ANY STRING
MONGODB_URI=mongodb://127.0.0.1/next-js-registration-login-example
```

# ESLint Config

Path: /.eslintrc.json

ESLint RC 파일은 프로젝트에서 `npm run lint` 또는 `next lint`가 실행될 때 활성화되는 규칙을 구성합니다.

기본 규칙 세트는 `next/core-web-vitals`이며, 이는 `npx create-next-app@latest` 명령을 사용하여 Next.js 앱을 생성할 때 포함됩니다. `create-next-app`에 대한 자세한 내용은 https://nextjs.org/docs/app/api-reference/create-next-app를 참조하세요.

```json
{
  "extends": "next/core-web-vitals"
}
```

# Next.js Config

Path: /next.config.js

Next.js 구성 파일을 사용하여 Next.js 프로젝트에 대한 다양한 옵션을 구성할 수 있습니다.

`npx create-next-app@latest` 명령을 사용하여 Next.js 기본 프로젝트를 만들 때 파일이 자동으로 생성되었으며 아무런 변경도 하지 않았습니다. 사용 가능한 모든 구성 옵션에 대한 정보는 https://nextjs.org/docs/app/api-reference/next-config-js를 참조하세요.

```json
/** @type {import('next').NextConfig} */
const nextConfig = {}

module.exports = nextConfig
```

# Package.json

Path: /package.json

package.json 파일에는 Next.js 튜토리얼 앱을 실행하고 빌드하기 위한 스크립트와 `npm install` 또는 `npm i`를 실행할 때 설치되는 종속성을 포함한 프로젝트 구성 정보가 포함되어 있습니다. 전체 문서는 npm 문서 웹사이트에서 볼 수 있습니다.

package.json 스크립트에 사용되는 Next.js CLI 명령에 대한 자세한 내용은 https://nextjs.org/docs/app/api-reference/next-cli를 참조하세요.

```json
{
  "name": "next-js-13-app-router-mongodb-rego-login-example",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "@types/bcryptjs": "^2.4.2",
    "@types/jsonwebtoken": "^9.0.2",
    "@types/node": "20.4.5",
    "@types/react": "18.2.16",
    "@types/react-dom": "18.2.7",
    "bcryptjs": "^2.4.3",
    "bootstrap": "^5.3.0",
    "eslint": "8.45.0",
    "eslint-config-next": "13.4.12",
    "joi": "^17.9.2",
    "jsonwebtoken": "^9.0.1",
    "mongodb": "^5.7.0",
    "mongoose": "^7.4.1",
    "next": "13.4.12",
    "react": "18.2.0",
    "react-dom": "18.2.0",
    "react-hook-form": "^7.45.2",
    "typescript": "5.1.6",
    "zustand": "^4.3.9"
  }
}
```

# TypeScript Config

Path: /tsconfig.json

tsconfig 파일에는 TypeScript 프로젝트가 JavaScript로 컴파일되는 방식을 지정하는 컴파일러 옵션과 구성이 포함되어 있습니다.

TypeScript 옵션과 함께 `npx create-next-app@latest` 명령을 사용하여 기본 Next.js 프로젝트를 생성할 때 파일이 자동으로 생성되었습니다. 내가 변경한 유일한 것은 `baseUrl`을 추가한 것입니다.

`baseUrl` 옵션은 Next.js 튜토리얼 앱에 대한 절대 가져오기를 구성하는 데 사용됩니다. 기본 URL을 `"./app"`으로 설정하면 모든 TypeScript import 문(점 '.' 접두사 없음)이 프로젝트의 앱 폴더를 기준으로 하여 `import { userService } from '../../../_services';`.

Next.js also supports module path aliases(e.g. '@/\_services') in the tsconfig file, for more info see https://nextjs.org/docs/app/building-your-application/configuring/absolute-imports-and-module-aliases.

```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    // make all imports without a dot '.' prefix relative to the app directory
    "baseUrl": "./app"
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```
