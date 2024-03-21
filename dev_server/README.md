# webpack

------------

Tutorials : [웹팩 핸드북](https://joshua1988.github.io/webpack-guide/tutorials/webpack-dev-server.html "웹팩 핸드북")

# Webpack Dev Server

> 빈 폴더에서 아래 명령어로 package.json 파일을 생성
```javascript
npm init -y
```
> 아래 명령어로 실습에 필요한 라이브러리 설치
```javascript
npm i webpack webpack-cli webpack-dev-server html-webpack-plugin -D
```
> package.json 파일에서 아래와 같이 scripts 속성에 커스텀 명령어를 추가
```javascript
{
  // ...
  "scripts": {
    "dev": "webpack serve"
  },
}
```
> 프로젝트 루트 레벨에 index.html 파일 생성 후 내용 추가
```javascript
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Webpack Dev Server</title>
  </head>
  <body>
    <!-- 빌드 결과물이 정상적으로 로딩되면 아래 div 태그의 텍스트가 변경됨 -->
    <div class="container">
      TBD..
    </div>
    <!-- HTML Webpack Plugin에 의해 웹팩 빌드 내용이 아래에 추가됨 -->
  </body>
</html>
```
> 프로젝트 루트 레벨에 index.js 파일 생성 및 아래 내용 추가
```javascript
var div = document.querySelector('.container');
div.innerText = 'Webpack loaded!!';
```
> 웹팩 설정 파일 webpack.config.js 생성 후 아래 내용 추가
```javascript
var path = require('path');
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  mode: 'none',
  entry: './index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  devServer: {
    port: 9000,
  },
  plugins: [
    new HtmlWebpackPlugin({
      // index.html 템플릿을 기반으로 빌드 결과물을 추가해줌
      template: 'index.html',
    }),
  ],
};
```
> 명령어 입력 창에 npm run dev 를 입력하여 웹팩 데브 서버 실행

> localhost:9000에 접속 후 아래와 같이 화면이 뜨는지 확인
```javascript
Webpack loaded!!
```

------------

Developer Tools : [웹팩 핸드북](https://joshua1988.github.io/webpack-guide/devtools/webpack-dev-server.html "웹팩 핸드북")

# Webpack Dev Server

> 웹팩 데브 서버는 웹 애플리케이션을 개발하는 과정에서 유용하게 쓰이는 도구입니다. 
> 웹팩의 빌드 대상 파일이 변경 되었을 때 매번 웹팩 명령어를 실행하지 않아도 코드만 변경하고 저장하면 웹팩으로 빌드한 후 브라우저를 새로고침 해줍니다.
> 매번 명령어를 치는 시간과 브라우저를 새로 고침하는 시간 뿐만 아니라 웹팩 빌드 시간 또한 줄여주기 때문에 웹팩 기반의 웹 애플리케이션 개발에 필수로 사용됩니다.

## 웹팩 데브 서버의 특징
> 웹팩 데브 서버는 일반 웹팩 빌드와 다른점이 있습니다. 먼저 명령어를 보겠습니다.
```javascript
"scripts": {
  "dev": "webpack serve",
  "build": "webpack"
}
```
> 웹팩 데브 서버를 실행하여 웹팩 빌드를 하는 경우에는 빌드한 결과물이 파일 탐색기나 프로젝트 폴더에서 보이지 않습니다. 
> 좀 더 구체적으로 얘기하자면 웹팩 데브 서버로 빌드한 결과물은 메모리에 저장되고 파일로 생성하지는 않기 때문에 
> 컴퓨터 내부적으로는 접근할 수 있지만 사람이 직접 눈으로 보고 파일을 조작할 순 없습니다.
> 따라서, 웹팩 데브 서버는 개발할 때만 사용하다가 개발이 완료되면 웹팩 명령어를 이용해 결과물을 파일로 생성해야 합니다.
>> 컴퓨터 구조 관점에서 파일 입출력보다 메모리 입출력이 더 빠르고 컴퓨터 자원이 덜 소모됩니다 😄

### 프록시(Proxy) 설정
> 프록시 설정은 실무에서 가장 흔하게 사용하는 속성입니다. 아래와 같이 선언합니다.

```javascript
// webpack.config.js
module.exports = {
  devServer: {
    proxy: {
      '/api': 'http://localhost:3000'
    }
  }
};
```
> 위와 같이 설정하고 나면 로컬 웹팩 데브 서버에서 발생하는 API 요청에 변화가 생깁니다. 그림으로 살펴보겠습니다. 
> 먼저 프록시를 쓰지 않았을 때의 기본적인 웹팩 데브 서버와 API 서버의 통신 구조입니다.
   
```javascript
                    CORS(Cross-Origin Resource Sharing)
                      ▲          X             \  
                    /                            \
                  /        로그인 API 호출        \
                /        domain.com/api/login      ▼
    localhost:8080                                domain.com
axios.get('domain.com/api/login');
    클라이언트 측                                   서버 측
```
Link : [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS "CORS")

> 여기서 CORS라는 용어가 나옵니다. 이 용어는 브라우저 보안과 관계가 있는데요. 
> 쉽게 얘기하면 다른 도메인 간에는 자바스크립트로 자원을 요청할 수 없다는 의미입니다. 
> 위 그림에서도 서버에 로그인 관련 API 요청을 했는데 CORS 오류가 나는 걸 볼 수 있습니다.
> 뷰, 리액트와 같은 프런트엔드 프레임워크를 쓰면 개발 편의상 로컬에 웹팩 데브 서버를 띄워놓고 개발하는 경우가 많습니다. 
> 이 때, 이러한 문제를 해결하기 위해서 아래와 같이 프록시 속성을 설정하면 서버에서 해당 요청을 받아줍니다.

```javascript
module.exports = {
  devServer: {
    proxy: {
      '/api': 'domain.com'
    }
  }
};
```

```javascript
                    프록시가 설정된 Webpack Dev Server
                                 ▲            \  
                                 /              \     로그인 API 호출
localhost:8080/api/login        /                \      domain.com/api/login     
                               /                 ▼
                        localhost:8080           domain.com
                    axios.get('/api/login');
                        클라이언트 측              서버 측
```

> CORS가 브라우저 보안과 관련있기 때문에 브라우저에서 벗어나 서버에서 서버로 요청합니다. 
> 실제로 브라우저에서는 localhost:8080/api/login 으로 요청했지만 
> 중간에 프록시 서버의 활약으로 domain.com 서버에서는 같은 도메인(domain.com)에서 온 요청으로 인식하여 CORS 에러가 나지 않습니다.

>> 위 프록시 설정은 최대한 간단히 설명하기 위해 옵션을 하나 뺐습니다. 
>> 위와 같이 도메인 이름이 IP 주소가 아니라 가상 이름(domain.com)으로 되어 있는 경우 아래 옵션을 추가해 주셔야 합니다.

```javascript
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: 'domain.com',
        changeOrigin: true
      }
    }
  }
};
```

# HMR(Hot Module Replacement)
> HMR은 브라우저를 새로 고치지 않아도 웹팩으로 빌드한 결과물이 웹 애플리케이션에 실시간으로 반영될 수 있게 도와주는 설정입니다. 
> 브라우저 새로 고침을 위한 LiveReload 대신에 사용할 수 있으며 웹팩 데브 서버와 함께 사용할 수도 있습니다

## HMR 설정하기
> 리액트, 앵귤러, 뷰와 같이 대부분의 프레임워크에서 이미 HMR을 사용할 수 있는 로더들을 지원하고 있지만 
> 만약 개별적으로 설정하고 싶다면 아래와 같은 방식으로 설정할 수 있습니다.
```javascript
module.exports = {
  devServer: {
    hot: true
  }
}
```
> 데브 서버에 옵션으로 hot:true를 추가하고 자바스크립트나 CSS 스타일시트를 변경하면 해당 모듈이 바로 업데이트가 됩니다. 
> 그리고 화면에서는 브라우저가 다시 로딩되지 않고도 변경된 내용을 확인할 수 있습니다.

# 소스 맵
> 소스 맵(Source Map)이란 배포용으로 빌드한 파일과 원본 파일을 서로 연결시켜주는 기능입니다. 
> 보통 서버에 배포를 할 때 성능 최적화를 위해 HTML, CSS, JS와 같은 웹 자원들을 압축합니다. 
> 그런데 만약 압축하여 배포한 파일에서 에러가 난다면 어떻게 디버깅을 할 수 있을까요?
> 정답은 바로 소스 맵을 이용해 배포용 파일의 특정 부분이 원본 소스의 어떤 부분인지 확인하는 것입니다. 
> 이러한 편의성을 제공하는 것이 소스 맵입니다.

## 소스 맵 설정하기
> 웹팩에서 소스 맵을 설정하는 방법은 아래와 같습니다.
```javascript
// webpack.config.js
module.exports = {
  devtool: 'cheap-eval-source-map'
}
```
> devtool 속성을 추가하고 소스 맵 설정 옵션 중 하나를 선택해 지정해주면 됩니다.

### 소스 맵 설정 옵션
> 위에서 정의한 소스 맵 설정 옵션 이외에도 많은 옵션들이 있습니다. 자세한 옵션 속성과 비교는 다음 링크로 확인하세요.

소스 맵 설정 옵션 비교표 : [Devtool](https://webpack.js.org/configuration/devtool/#devtool "Devtool")

------------

Advanced : [웹팩 핸드북](https://joshua1988.github.io/webpack-guide/advanced/mode-config.html "웹팩 핸드북")

# Mode Config

## 웹팩 실행 모드 - mode
> 웹팩 버전 4부터 mode라는 개념이 추가되었습니다. 아래 코드를 보겠습니다.
```javascript
module.exports = {
  mode: 'none',
  entry: '',
  // ...
}
```
> 웹팩 설정을 정의할 때 위와 같이 mode라는 속성을 정의하면 웹팩의 실행 모드가 설정됩니다. 웹팩의 실행 모드에는 다음 3가지가 있습니다.

+ none : 모드 설정 안함
+ development : 개발 모드
+ production : 배포 모드

> 각 실행 모드에 따라 웹팩의 결과물 모습이 달라집니다. 
> 개발 모드인 경우에는 개발자들이 좀 더 보기 편하게 웹팩 로그나 결과물이 보여지고, 배포 모드인 경우에는 성능 최적화를 위해 기본적인 파일 압축 등의 빌드 과정이 추가됩니다.
>> 모드의 기본 값을 설정하지 않으면 production 모드로 자동 설정됩니다.

### 실행 모드에 따라 웹팩 설정 달리하기
> 웹팩으로 실제 웹 애플리케이션을 개발할 때는 보통 아래와 같이 2가지 케이스로 구분하여 작업해야 합니다.

+ 개발할 때 사용할 웹팩 설정
+ 개발이 끝나고 배포할 때 사용할 웹팩 설정
+ 웹팩 설정 파일이 1개인 상태에서 실행 모드에 따라 특정 설정을 적용하는 방법은 다음과 같습니다.

```javascript
// webpack.config.js
module.exports = (env) => {
  let entryPath = env.mode === 'production'
    ? './public/index.js'
    : './src/index.js';

  return {
    entry: entryPath,
    output: {},
    // ...
  }
}
```
```javascript
// package.json
{
  "build": "webpack",
  "development": "npm run build --env mode=development",
  "production": "npm run build --env mode=production"
}
```
> 위 코드를 보면 먼저 웹팩 설정 파일의 방식은 객체에서 함수 형식으로 바뀌었습니다.

```javascript
// 기존
module.exports = {};
// 현재
module.exports = () => {};
```
> 그리고 함수에 넘겨준 env 인자는 환경 변수를 의미하며 웹팩을 실행할 때 실행 옵션으로 넘겨줄 수 있습니다.

```javascript
webpack --env.a=10
```
> 만약 NPM 커스텀 명령어로 관리를 한다면 아래와 같이 할 수 있습니다.

```javascript
{
  "build": "webpack --env a=10"
}
```
> 위와 같은 방식으로 실행 모드에 따라 웹팩의 설정을 각각 다르게 적용할 수 있습니다.

# Webpack Merge

> 웹팩 머지는 단어 그대로 여러 개의 웹팩 설정 파일을 하나로 병합해주는 라이브러리입니다. 
> 실행 모드에 따라 웹팩 설정하기에서도 언급했지만 일반적으로 웹 애플리케이션을 제작할 떄는 웹팩 설정을 개발(Development)용과 배포(Production)용으로 나누어 적용합니다.
> 앞에서 배운 것처럼 실행 모드에 따라 조건 문으로 설정을 구분할 수 있으나 실제로 파일을 아예 나눠놓는 게 더 권장하는 방식입니다. 
> 웹팩 머지는 이러한 상황에서 더 빛을 발휘할 수 있습니다.

## 웹팩 설정 파일 구분 전략
> 웹팩 머지를 효율적으로 사용하는 방법은 개발용과 배포용 설정 파일에서 공통으로 쓰이는 부분을 먼저 분리하는 것입니다. 
> 파일 체계는 아래와 같은 형식으로 구성합니다.

```javascript
webpack.common.js
webpack.dev.js
webpack.prod.js
```
> 각 파일의 모습은 다음과 같습니다.

```javascript
// webpack.common.js
module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin(),
  ],
}
```
> 공통 설정 파일에는 엔트리, 아웃풋, 플러그인과 같이 실행 모드에 관계 없이 항상 들어가야 하는 코드를 추가합니다.
```javascript
// webpack.dev.js
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'development',
  devtool: 'inline-source-map',
  devServer: { contentBase: './dist' }
});
```
> 개발용 설정 파일에는 개발자 도구나 웹팩 데브 서버와 같은 설정을 추가합니다. 
> 그리고 webpack-merge 라이브러리를 설치 및 로딩하고 나서 웹팩 공통 설정 파일인 webpack.common.js 파일을 로딩해서 같이 병합해줍니다.
```javascript
// webpack.prod.js
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production'
});
```
> 배포용 설정 파일에는 배포하기 전 웹 리소스 최적화를 위한 설정들을 추가해줍니다.

## 참고 자료 - 웹팩 머지
> 웹팩 머지 라이브러리나 설정 파일 구분에 대해서 더 자세히 알고 싶다면 아래의 링크를 참고하세요.

+ webpack-merge 깃헙 리포지토리 : [webpack-merge](https://github.com/survivejs/webpack-merge "webpack-merge")
+ 배포를 위한 웹팩 설정 가이드 : [guides](https://webpack.js.org/guides/production/ "guides")

------------

# FAQ

+ 웹팩 버전 3과 4의 차이점이 무엇인가요?
> mode 설정 옵션이 추가되었습니다.
> 웹팩 버전3은 시스템 전역 레벨에서 명령어와 웹팩을 실행하는데 반해 버전 4는 프로젝트 단위로 웹팩과 명령어를 실행합니다.

------------
------------
------------
------------