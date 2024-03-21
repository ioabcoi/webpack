# webpack

Link : [웹팩 핸드북](https://joshua1988.github.io/webpack-guide/concepts/overview.html "웹팩 핸드북")

------------

# 웹팩이란?
> 웹팩이란 최신 프런트엔드 프레임워크에서 가장 많이 사용되는 모듈 번들러(Module Bundler)입니다. 
> 모듈 번들러란 웹 애플리케이션을 구성하는 자원(HTML, CSS, Javscript, Images 등)을 모두 각각의 모듈로 보고 이를 조합해서 병합된 하나의 결과물을 만드는 도구를 의미합니다. 

## 모듈이란?
> 모듈이란 프로그래밍 관점에서 특정 기능을 갖는 작은 코드 단위를 의미합니다. 자바스크립트로 치면 아래와 같은 코드가 모듈입니다.
```javascript
// math.js
function sum(a, b) {
    return a + b;
}

function substract(a, b) {
    return a - b;
}

const pi = 3.14;

export { sum, substract, pi }
```
> 이 math.js 파일은 아래와 같이 3가지 기능을 갖고 있는 모듈입니다.
1. 두 숫자의 합을 구하는 sum() 함수
2. 두 숫자의 차를 구하는 substract() 함수
3. 원주율 값을 갖는 pi 상수
> 이처럼 성격이 비슷한 기능들을 하나의 의미 있는 파일로 관리하면 모듈이 됩니다.
> 위의 export 코드는 ES6의 Modules 문법을 사용하였습니다. 문법을 잘 모르시는 분들은 여기를 참고하세요.

## 웹팩에서의 모듈
> 웹팩에서 지칭하는 모듈이라는 개념은 위와 같이 자바스크립트 모듈에만 국한되지 않고 웹 애플리케이션을 구성하는 모든 자원을 의미합니다. 
> 웹 애플리케이션을 제작하려면 HTML, CSS, Javascript, Images, Font 등 많은 파일들이 필요하죠. 이 파일 하나하나가 모두 모듈입니다.

## 모듈 번들링이란?
> 아래 그림과 같이 웹 애플리케이션을 구성하는 몇십, 몇백개의 자원들을 하나의 파일로 병합 및 압축 해주는 동작을 모듈 번들링이라고 합니다.
> 빌드, 번들링, 변환 이 세 단어 모두 같은 의미입니다.

## [온라인 강의로 쉽게 배우기](https://www.inflearn.com/course/프런트엔드-웹팩 '프런트엔드-웹팩')

------------

# 웹팩의 등장 배경

> 웹팩이 등장한 이유는 크게 3가지입니다.
1. 파일 단위의 자바스크립트 모듈 관리의 필요성
2. 웹 개발 작업 자동화 도구 (Web Task Manager)
3. 웹 애플리케이션의 빠른 로딩 속도와 높은 성능

## 파일 단위의 자바스크립트 모듈 관리
> 입문자 관점에서 고안된 자바스크립트는 아래와 같이 편리한 유효 범위를 갖고 있습니다.
```javascript
var a = 10;
console.log(a); // 10

function logText() {
  console.log(a); // 10
}
```
> 자바스크립트의 변수 유효 범위는 기본적으로 전역 범위를 갖습니다. 최대한 넓은 변수 범위를 갖기 때문에 어디에서도 접근하기가 편리하죠.
> 그런데 이러한 장점이 실제로 웹 애플리케이션을 개발할 때는 아래와 같은 문제점으로 변합니다.
```javascript
<!-- index.html -->
<html>
    <head>
     <!-- ... -->
    </head>
    <body>
        <!-- ... -->
        <script src="./app.js"></script>
        <script src="./main.js"></script>
    </body>
</html>
// app.js
var num = 10;
function getNum() {
    console.log(num);
}
// main.js
var num = 20;
function getNum() {
    console.log(num);
}
```
> 위와 같이 index.html에서 두 자바스크립트 파일을 로딩하여 사용한다고 해봅시다. 스크립트에 아래와 같이 코드를 실행하면 어떤 결과가 나올까요?
```javascript
<!-- index.html -->
<html>
    <head>
        <!-- ... -->
    </head>
    <body>
        <!-- ... -->
        <script src="./app.js"></script>
        <script src="./main.js"></script>
        <script>
            getNum(); // 20
        </script>
    </body>
</html>
```
> 결과는 20입니다. app.js에서 선언한 num 변수는 main.js에서 다시 선언하고 20을 할당했기 때문이죠.
> 이러한 문제점은 실제로 복잡한 애플리케이션을 개발할 때 발생합니다. 변수의 이름을 모두 기억하지 않은 이상 변수를 중복 선언하거나 의도치 않은 값을 할당할 수 있죠.
> 이처럼 파일 단위로 변수를 관리하고 싶은 욕구, 자바스크립트 모듈화에 대한 욕구를 예전까진 AMD, Common.js와 같은 라이브러리로 풀어왔습니다.

## 웹 개발 작업 자동화 도구
> 이전부터 프런트엔드 개발 업무를 할 때 가장 많이 반복하는 작업은 텍스트 편집기에서 코드를 수정하고 저장한 뒤 브라우저에서 새로 고침을 누르는 것이었습니다. 그래야 화면에 변경된 내용을 볼 수 있었죠.
> 이외에도 웹 서비스를 개발하고 웹 서버에 배포할 때 아래와 같은 작업들을 해야 했습니다.
+ HTML, CSS, JS 압축
+ 이미지 압축
+ CSS 전처리기 변환
> 이러한 일들을 자동화 해주는 도구들이 필요했습니다. 그래서 Grunt와 Gulp 같은 도구들이 등장했습니다.

## 웹 애플리케이션의 빠른 로딩 속도와 높은 성능
> 일반적으로 특정 웹 사이트를 접근할 때 5초 이내로 웹 사이트가 표시되지 않으면 대부분의 사용자들은 해당 사이트를 벗어나거나 집중력을 잃게 됩니다.
> 그래서 웹 사이트의 로딩 속도를 높이기 위해 많은 노력들이 있었습니다. 그 중 대표적인 노력이 브라우저에서 서버로 요청하는 파일 숫자를 줄이는 것입니다. 
> 이를 위해 앞에서 살펴본 웹 태스크 매니저를 이용해 파일들을 압축하고 병합하는 작업들을 진행했습니다.
> 뿐만 아니라 초기 페이지 로딩 속도를 높이기 위해 나중에 필요한 자원들은 나중에 요청하는 레이지 로딩(Lazy Loading)이 등장했죠.
> 웹팩은 기본적으로 필요한 자원은 미리 로딩하는게 아니라 그 때 그 때 요청하자는 철학을 갖고 있습니다.

## 웹팩으로 해결하려는 문제?
> 웹팩의 등장 배경에서도 살펴봤지만 웹팩에서 해결하고자 하는 기존의 문제점은 다음 4가지 입니다.
+ 자바스크립트 변수 유효 범위
+ 브라우저별 HTTP 요청 숫자의 제약
+ 사용하지 않는 코드의 관리
+ Dynamic Loading & Lazy Loading 미지원

## 자바스크립트 변수 유효 범위 문제
> 웹팩은 변수 유효 범위의 문제점을 ES6의 Modules 문법과 웹팩의 모듈 번들링으로 해결합니다.

## 브라우저별 HTTP 요청 숫자의 제약
> TCP 스펙에 따라 브라우저에서 한 번에 서버로 보낼 수 있는 HTTP 요청 숫자는 제약되어 있습니다. 아래의 표는 최신 브라우저 별 최대 HTTP 요청 횟수입니다.
+ 브라우저 : 최대 연결 횟수
+ 익스플로러 7 : 2
+ 익스플로러 8 ~ 9 : 6
+ 익스플로러 10, 11 : 8, 13
+ 크롬 : 6
+ 사파리 : 6
+ 파이어폭스 : 6
+ 오페라 : 6
+ 안드로이드, iOS	: 6
> 따라서, HTTP 요청 숫자를 줄이는 것이 웹 애플리케이션의 성능을 높여줄 뿐만 아니라 사용자가 사이트를 조작하는 시간을 앞당겨 줄 수 있죠.
>> ⚠️ 알아두면 좋아요!
>> 클라이언트에서 서버에 HTTP 요청을 보내기 위해서는 먼저 TCP/IP가 연결되어야 합니다.
> 웹팩을 이용해 여러 개의 파일을 하나로 합치면 위와 같은 브라우저별 HTTP 요청 숫자 제약을 피할 수 있습니다.

## Dynamic Loading & Lazy Loading 미지원
> Require.js와 같은 라이브러리를 쓰지 않으면 동적으로 원하는 순간에 모듈을 로딩하는 것이 불가능 했습니다. 
> 그러나 이젠 웹팩의 Code Splitting 기능을 이용하여 원하는 모듈을 원하는 타이밍에 로딩할 수 있습니다.

------------

# Node.js와 NPM
> 웹팩을 사용하기 위해서는 Node.js와 NPM이 컴퓨터에서 설치되어 있어야 합니다. 그리고 이 도구들에 대해 어느 정도 배경 지식이 있으면 웹팩을 다루는데 도움이 됩니다.

## Node.js
> Node.js는 브라우저 밖에서도 자바스크립트를 실행할 수 있는 환경을 의미합니다. Node.js가 나오기 전까지는 자바스크립트가 브라우저의 동작을 제어하는데 사용되었고 브라우저에서만 실행할 수 있었지만 이제는 Node.js로 자바스크립트를 브라우저 밖에서도 실행할 수 있게 되었습니다.

## Node.js 맛보기
> Node.js를 이해할 수 있는 가장 간단한 방법은 아래와 같은 파일을 만들고 실행해보는 것입니다.
```javascript
// app.js
console.log('hi');
```
> 위와 같이 app.js라는 파일을 하나 만들고 내용을 추가한 뒤 명령어 실행 창에서 아래 명령어를 입력합니다.
```javascript
node app.js
```
> 그러면 자바스크립트 파일이 실행되면서 명령어 입력 창에 hi라는 텍스트가 출력됩니다.

## NPM
> NPM(Node Package Manager)는 명령어로 자바스크립트 라이브러리를 설치하고 관리할 수 있는 패키지 매니저입니다. NPM 공식 사이트에서도 안내가 되어 있지만 전 세계 자바스크립트 개발자들이 모두 자바스크립트 라이브러리를 공개된 저장소에 올려놓고 npm 명령어로 편하게 다운로드 받을 수 있습니다.
> NPM은 Node.js를 설치하면 같이 설치됩니다.

### NPM 맛보기
> NPM을 맛보기 위해 새 폴더를 하나 만들고 그 폴더에서 아래의 명령어를 실행합니다.
```javascript
npm init -y
```
> 명령어가 실행되면 package.json 파일이 생성됩니다. 그리고 생성된 파일의 모습은 아래와 같습니다.
```javascript
{
    "name": "demo",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [],
    "author": "",
    "license": "ISC"
}
```
> 위 구조는 가장 기본적인 구조이며 실제 애플리케이션을 만들 때 자주 사용되는 속성은 다음과 같습니다.

+ scripts
+ dependencies
+ devDependencies

## NPM 설치 명령어

### NPM 지역 설치
```javascript
npm install jquery --save-prod
# 위 명령어와 동일한 효과
npm i jquery
```

#### NPM 지역 설치 옵션 2가지
```javascript
npm install jquery --save-prod
npm install jquery --save-dev
# 축약 버전
npm i jquery
npm i jquery -D
```
> 여기서 설치 옵션에 아무것도 넣지 않은 npm i jquery는 package.json의 dependencies에 등록됩니다.
```javascript
// package.json
{
    "dependencies": {
        "jquery": "^3.4.1"
    }
}
```
> 설치 옵션으로 -D를 넣은 경우에는 해당 라이브러리가 package.json의 devDependencies에 등록됩니다.
```javascript
// package.json
{
    "devDependencies": {
        "jquery": "^3.4.1"
    }
}
```

#### 개발용 라이브러리와 배포용 라이브러리 구분하기
> NPM 지역 설치를 할 때는 해당 라이브러리가 배포용(dependencies)인지 개발용(devDependencies)인지 꼭 구분해주어야 합니다. 
> 예를 들어, jquery와 같이 화면 로직과 직접적으로 관련된 라이브러리는 배포용으로 설치해야 합니다. 아래와 같이 말이죠.

```javascript
# 배포용 라이브러리 설치
npm i jquery
```
```javascript
{
    "dependencies": {
        "jquery": "^3.4.1"
    }
}
```
> 이렇게 설치된 배포용 라이브러리는 npm run build로 빌드를 하면 최종 애플리케이션 코드 안에 포함됩니다.
> 그런데 만약 반대로 설치 옵션에 -D를 주었다면 해당 라이브러리는 빌드하고 배포할 때 애플리케이션 코드에서 빠지게 됩니다. 
> 따라서, 최종 애플리케이션에 포함되어야 하는 라이브러리는 -D로 설치하면 안 됩니다. 
> 개발할 때만 사용하고 배포할 때는 빠져도 좋은 라이브러리의 예시는 다음과 같습니다.

+ webpack : 빌드 도구
+ eslint : 코드 문법 검사 도구
+ imagemin : 이미지 압축 도구

### NPM 전역 설치
```javascript
npm install gulp --global
# 위 명령어와 동일한 효과
npm install gulp --g
gulp
```

## NPM 커스텀 명령어
> NPM 커스텀 명령어란 사용자가 임의로 명령어의 이름과 동작을 정의해서 사용할 수 있는 기능을 의미합니다. 아래 코드를 봅시다.
```javascript
// package.json
{
    ...
    "scripts": {
        "hello": "echo hi"
    }
}
```
> NPM 패키지 관리 파일인 package.json에 위와 같이 scripts라는 속성을 추가할 수 있습니다. 그리고 아래의 명령어를 실행하면 콘솔에 hi 가 출력됩니다.
```javascript
npm run hello
```
> 이처럼 명령어 실행 창에 매번 긴 명령어를 입력할 필요 없이 커스텀 명령어를 이용해 원하는 동작을 실행할 수 있습니다.
> NPM 커스텀 명령어는 모두 npm run 명령어 이름 형식으로 실행할 수 있습니다.

### NPM 커스텀 명령어 실제 사례
> NPM 커스텀 명령어는 웹팩 같은 도구 뿐만 아니라 Node.js 등을 사용할 때도 유용합니다.
> 앞에서 배운 내용으로 실제 커스텀 명령어 사례를 몇 가지 살펴보겠습니다.
```javascript
"scripts": {
    "dev": "node server.js",
    "build": "webpack --mode=none",
}
```
> 위 코드는 서버를 실행하는 dev 명령어와 웹팩으로 빌드하는 build 명령어를 정의한 코드입니다. 
> 사용자는 매번 node server.js와 webpack --mode=none를 칠 필요 없이 npm run dev와 npm run build를 입력하면 됩니다.
> 아래와 같이 실행하려는 명령어가 길수록 더 빛을 발휘합니다.
```javascript
"scripts": {
    "build": "cross-env NODE_ENV=production webpack --progress --hide-modules"
}
```
> 매번 긴 명령어를 실행하는 것보다 커스텀 명령어를 사용하는 것이 더 편하겠죠? 😃

### NPM 커스텀 명령어 실전 활용
> 커스텀 명령어의 또 다른 장점은 해당 명령어에 실행 옵션 뿐만 아니라 다른 커스텀 명령어를 조합할 수 있다는 점입니다.
```javascript
"scripts": {
    "build": "webpack",
    "deploy": "npm run build -- --mode production"
    }
```
> 위와 같은 scripts 속성을 정의하고 아래 명령어를 입력하면 어떻게 될까요?
```javascript
npm run deploy
```
> 먼저 build에 정의한 webpack 명령어가 실행되면서 명령어 뒤쪽에 붙은 실행 옵션들이 수행됩니다. 
> 이후 웹팩 챕터에서 더 자세히 살펴보겠지만 여기서는 webpack이라는 도구의 mode에 production이라는 값을 넘겨준 것입니다.

------------

# 웹팩 맛보기 튜토리얼

## 개발 환경 구성
+ [Node.js LTS Version(버전 10 이상)](https://nodejs.org/en/ "node.js")
+ NPM 버전 6 이상

## 실습 절차 - 웹 페이지 자원 구성
1. 빈 폴더에서 아래 명령어로 package.json 파일을 생성
```javascript
npm init -y
```
2. 아래 명령어로 해당 폴더에 웹팩 관련 라이브러리와 lodash 라이브러리 설치
```javascript
npm i webpack webpack-cli -D
npm i lodash
```
3. 폴더에 index.html 파일을 생성하고 아래 내용 추가
```javascript
<html>
    <head>
        <title>Webpack Demo</title>
        <script src="https://unpkg.com/lodash@4.16.6"></script>
    </head>
    <body>
        <script src="src/index.js"></script>
    </body>
</html>
```
4. 프로젝트 루트 레벨에 src 폴더를 생성하고 그 안에 index.js 파일 생성.
```javascript
function component() {
    var element = document.createElement('div');

    /* lodash is required for the next line to work */
    element.innerHTML = _.join(['Hello','webpack'], ' ');

    return element;
}

document.body.appendChild(component());
```

## 실습 절차 - 웹팩 빌드를 위한 구성 및 빌드
5. 웹팩 빌드 및 빌드 결과물로 실행하기 위해 각 파일에 아래 내용 반영
```javascript
// index.js
import _ from 'lodash';

function component() {
    var element = document.createElement('div');

    /* lodash is required for the next line to work */
    element.innerHTML = _.join(['Hello','webpack'], ' ');

    return element;
}

document.body.appendChild(component());
```
```javascript
<!-- index.html -->
<html>
    <head>
        <title>Webpack Demo</title>
        <!-- <script src="https://unpkg.com/lodash@4.16.6"></script> -->
    </head>
    <body>
        <!-- <script src="src/index.js"></script> -->
        <script src="dist/main.js"></script>
    </body>
</html>
```
6. 웹팩 빌드 명령어를 실행하기 위해 package.json 파일에 아래 내용 추가
```javascript
"scripts": {
    "build": "webpack --mode=none"
}
```
7. npm run build 명령어 실행 후 index.html 파일을 라이브서버로 실행
8. 프로젝트 폴더 루트 레벨에 webpack.config.js 파일 생성 후 아래 내용 추가
```javascript
// webpack.config.js
// `webpack` command will pick up this config setup by default
var path = require('path');

module.exports = {
    mode: 'none',
    entry: './src/index.js',
    output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'dist')
    }
};
```
9. package.json 파일을 아래와 같이 수정
```javascript
"scripts": {
    "build": "webpack"
}
```
10. 다시 npm run build 명령어를 실행하여 빌드가 잘 되는지 확인

------------
------------

# 웹팩의 4가지 주요 속성

+ Entry 속성은 웹팩을 실행할 대상 파일. 진입점
+ Output 속성은 웹팩의 결과물에 대한 정보를 입력하는 속성. 일반적으로 filename과 path를 정의
+ Loader 속성은 CSS, 이미지와 같은 비 자바스크립트 파일을 웹팩이 인식할 수 있게 추가하는 속성. 로더는 오른쪽에서 왼쪽 순으로 적용
+ Plugin 속성은 웹팩으로 변환한 파일에 추가적인 기능을 더하고 싶을 때 사용하는 속성. 웹팩 변환 과정 전반에 대한 제어권을 갖고 있음

------------

## Entry

### Entry 속성
> 웹팩에서 웹 자원을 변환하기 위해 필요한 최초 진입점이자 자바스크립트 파일 경로를 의미합니다.
```javascript
module.exports = {
    entry: './src/index.js',
};
```

### Entry 파일에는 어떤 내용이 들어가야 하나?
> entry 속성에 지정된 파일에는 웹 애플리케이션의 전반적인 구조와 내용이 담겨져 있어야 합니다. 
> 웹팩이 해당 파일을 가지고 웹 애플리케이션에서 사용되는 모듈들의 연관 관계를 이해하고 분석하기 때문에 
> 애플리케이션을 동작시킬 수 있는 내용들이 담겨져 있어야 합니다.
```javascript
// index.js
import LoginView from './LoginView.js';     // 사용자의 로그인 화면
import HomeView from './HomeView.js';       // 로그인 후 진입하는 메인 화면
import PostView from './PostView.js';       // 게시글을 작성하는 화면

function initApp() {
LoginView.init();
HomeView.init();
PostView.init();
}

initApp();
```
> 해당 서비스가 싱글 페이지 애플리케이션이라고 가정하고 작성한 코드
> 웹 서비스에 필요한 화면들이 모두 index.js 파일에서 불려져 사용되고 있기 때문에 
> 웹팩을 실행하면 해당 파일들의 내용까지 해석하여 파일을 빌드해줄 것입니다.

### Entry 유형
> 엔트리 포인트는 1개가 될 수도 있지만 아래와 같이 여러 개가 될 수도 있습니다.
```javascript
entry: {
    login: './src/LoginView.js',
    main: './src/MainView.js'
}
```    
> 엔트리 포인트를 분리하는 경우는 싱글 페이지 애플리케이션이 아닌 특정 페이지로 진입했을 때 서버에서 해당 정보를 내려주는 형태의 멀티 페이지 애플리케이션에 적합합니다.

------------

## Output

### output 속성
> 웹팩을 돌리고 난 결과물의 파일 경로를 의미합니다. 
> 객체 형태로 옵션들을 추가해야 합니다.
```javascript
module.exports = {
    output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'dist')
    }
};
```

### Output 속성 옵션 형태
> 최소한 filename은 지정해줘야 하며 일반적으로 아래와 같이 path 속성을 함께 정의합니다.
```javascript
// webpack.config.js
var path = require('path');

module.exports = {
    output: {
        filename: 'main.js',                        // 웹팩으로 빌드한 파일의 이름
        path: path.resolve(__dirname, './dist')     // 해당 파일의 경로
        // path.resolve() 코드는 인자로 넘어온 경로들을 조합하여 유효한 파일 경로를 만들어주는 Node.js API
    }
}
```
> 이 API가 하는 역할을 좀 더 이해하기 쉽게 표현하면 아래와 같습니다.
```javascript
// webpack.config.js
var path = require('path');

module.exports = {
    output: './dist/bundle.js'
}
```

[path 라이브러리의 자세한 사용법](https://nodejs.org/api/path.html "path") 

### Output 파일 이름 옵션

> 앞에서 살펴본 filename 속성에 여러 가지 옵션을 넣을 수 있습니다.
1. 결과 파일 이름에 entry 속성을 포함하는 옵션
```javascript
module.exports = {
    output: {
        filename: '[name].bundle.js'
    }
};
```
2. 결과 파일 이름에 웹팩 내부적으로 사용하는 모듈 ID를 포함하는 옵션
```javascript
module.exports = {
    output: {
        filename: '[id].bundle.js'
    }
};
```
3. 매 빌드시 마다 고유 해시 값을 붙이는 옵션
```javascript
module.exports = {
    output: {
        filename: '[name].[hash].bundle.js'
    }
};
```
4. 웹팩의 각 모듈 내용을 기준으로 생생된 해시 값을 붙이는 옵션
```javascript
module.exports = {
    output: {
        filename: '[chunkhash].bundle.js'
    }
};
```
> 이렇게 생성된 결과 파일의 이름에는 각각 엔트리 이름, 모듈 아이디, 해시 값 등이 포함됩니다.

------------

## Loader

> 로더(Loader)는 웹팩이 웹 애플리케이션을 해석할 때 자바스크립트 파일이 아닌 웹 자원(HTML, CSS, Images, 폰트 등)들을 변환할 수 있도록 도와주는 속성입니다.
```javascript
// webpack.config.js
module.exports = {
    module: {
        rules: []
    }
}
```
> 엔트리나 아웃풋 속성과는 다르게 module라는 이름을 사용합니다.

### Loader가 필요한 이유
> 웹팩으로 애플리케이션을 빌드할 때 만약 아래와 같은 코드가 있다고 해보겠습니다.
```javascript
// app.js
import './common.css';
console.log('css loaded');

/* common.css */
p {
    color: blue;
}

// webpack.config.js
module.exports = {
    entry: './app.js',
    output: {
        filename: 'bundle.js'
    }
}
```
> 위 파일을 웹팩으로 빌드하게 되면 아래와 같은 에러가 발생합니다.
>> ERROR in ./common.css
>> Module parse failed: Unexpectied token ~~~
> 위 에러 메시지의 의미는 app.js 파일에서 임포트한 common.css 파일을 해석하기 위해 적절한 로더를 추가해달라는 것입니다.

### CSS Loader 적용하기
> 이 때 해당 폴더에 아래의 NPM 명령어로 CSS 로더를 설치하고 웹팩 설정 파일 설정을 바꿔주면 에러를 해결할 수 있습니다.
```javascript
npm i css-loader -D
```
```javascript
// webpack.config.js
module.exports = {
    entry: './app.js',
    output: {
        filename: 'bundle.js'
    },
    module: {
        rules: [
            {
            test: /\.css$/,
            use: ['css-loader']
            }
        ]
    }
}
```
> 위의 module 쪽 코드를 보면 rules 배열에 객체 한 쌍을 추가했습니다. 그리고 그 객체에는 2개의 속성이 들어가 있는데 각각 아래와 같은 역할을 합니다.
+ test : 로더를 적용할 파일 유형 (일반적으로 정규 표현식 사용)
+ use : 해당 파일에 적용할 로더의 이름
> 정리하자면 위 코드는 해당 프로젝트의 모든 CSS 파일에 대해서 CSS 로더를 적용하겠다는 의미입니다.
> 적용 후 빌드하면 정상적으로 실행되는 것을 알 수 있습니다.

### 자주 사용되는 로더 종류
> 앞에서 살펴본 CSS 로더 이외에도 실제 서비스를 만들 때 자주 사용되는 로더의 종류는 다음과 같습니다.
+ Babel Loader
+ Sass Loader
+ File Loader
+ Vue Loader
+ TS Loader
> 로더를 여러 개 사용하는 경우에는 아래와 같이 rules 배열에 로더 옵션을 추가해주면 됩니다.
```javascript
module.exports = {
    module: {
        rules: [
            { test: /\.css$/, use: 'css-loader' },
            { test: /\.ts$/, use: 'ts-loader' },
            // ...
        ]
    }
}
```

### 로더 적용 순서
> 특정 파일에 대해 여러 개의 로더를 사용하는 경우 로더가 적용되는 순서에 주의해야 합니다. 로더는 기본적으로 오른쪽에서 왼쪽 순으로 적용됩니다.
> CSS의 확장 문법인 SCSS 파일에 로더를 적용하는 예시를 보겠습니다.
```javascript
module: {
    rules: [
        {
        test: /\.scss$/,
        use: ['css-loader', 'sass-loader']
        }
    ]
}
```
> 위 코드는 scss 파일에 대해 먼저 Sass 로더로 전처리(scss 파일을 css 파일로 변환)를 한 다음 웹팩에서 CSS 파일을 인식할 수 있게 CSS 로더를 적용하는 코드입니다.
> 만약 웹팩으로 빌드한 자원으로 실행했을 때 해당 CSS 파일이 웹 애플리케이션에 인라인 스타일 태그로 추가되는 것을 원한다면 아래와 같이 style 로더도 추가할 수 있습니다.
```javascript
{
    test: /\.scss$/,
    use: ['style-loader', 'css-loader', 'sass-loader']
}
```
> 그리고, 위와 같이 배열로 입력하는 대신 아래와 같이 옵션을 포함한 형태로도 입력할 수 있습니다.
```javascript
module: {
    rules: [
        {
            test: /\.css$/,
            use: [
                { loader: 'style-loader' },
                {
                    loader: 'css-loader',
                    options: { modules: true }
                },
                { loader: 'sass-loader' }
            ]
        }
    ]
}
```

------------

## Plugin

> 플러그인(plugin)은 웹팩의 기본적인 동작에 추가적인 기능을 제공하는 속성입니다. 로더랑 비교하면 로더는 파일을 해석하고 변환하는 과정에 관여하는 반면, 플러그인은 해당 결과물의 형태를 바꾸는 역할을 한다고 보면 됩니다.
> 플러그인은 아래와 같이 선언합니다.
```javascript
// webpack.config.js
module.exports = {
    plugins: []
}
```
> 플러그인의 배열에는 생성자 함수로 생성한 객체 인스턴스만 추가될 수 있습니다. 예를 들어보겠습니다.
```javascript
// webpack.config.js
var webpack = require('webpack');
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    plugins: [
        new HtmlWebpackPlugin(),
        new webpack.ProgressPlugin()
    ]
}
```
> 위의 두 플러그인은 각각 아래와 같은 역할을 합니다.

+ [HtmlWebpackPlugin](https://joshua1988.github.io/webpack-guide/concepts/plugin.html#plugin "HtmlWebpackPlugin") : 웹팩으로 빌드한 결과물로 HTML 파일을 생성해주는 플러그인
+ [ProgressPlugin](https://webpack.js.org/plugins/progress-plugin/#root "ProgressPlugin") : 웹팩의 빌드 진행율을 표시해주는 플러그인

### 자주 사용하는 플러그인
+ [split-chunks-plugin](https://webpack.js.org/plugins/split-chunks-plugin/ "split-chunks-plugin") : 웹팩의 빌드 진행율을 표시해주는 플러그인
+ [clean-webpack-plugin](https://www.npmjs.com/package/clean-webpack-plugin "clean-webpack-plugin")
+ [image-webpack-loader](https://github.com/tcoopman/image-webpack-loader "image-webpack-loader")
+ [webpack-bundle-analyzer-plugin](https://github.com/webpack-contrib/webpack-bundle-analyzer "webpack-bundle-analyzer-plugin")

------------
------------
------------
------------
------------