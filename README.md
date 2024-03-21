# webpack

------------

+ 따라하기
[webpack 프론트엔드 필수 개발환경 셋팅 - HTML + CSS + JS + 이미지](https://www.youtube.com/watch?v=zal9HVgrMaQ "youtube")

------------

## 1 초기 세팅

### 1.1 프로젝트 폴더 생성

```js
project
├── index.html
└── src
    └── index.js
```

### 1-2 npm init -y
>> npm 초기화

### 1.3 npm install webpack-cli --save-dev
>> webpack 설치

### 1.4 webpack.config.js
>> webpack 세팅을 위한 파일을 생성

```js
project
├── index.html
├── src
│   └── index.js
└── webpack.config.js
```

### 1.5 webpack.config.js
>> webpack 초기 셋팅

```js
// path 모듈 불러오기
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    // 번들링된 js
    filename: 'main.js',
    // 배포용 폴더 dist
    path: path.resolve(__dirname, 'dist')
  }
}
```
+ entry: 배포 전 작업용 자바스크립트 시작점
+ output: 최종 배포용 번들링 파일 설정

### 1.6 package.json
>> build 명령어 세팅

```js
"scripts": {
  "build": "webpack"
},
```

### 1.7 npm run build
>> dist 폴더 생성되는지 확인
>> dist 폴더 내부에 main.js 파일 생성되는지 확인

------------

## 2 html 빌더 설치

### 2.1 npm install html-webpack-plugin
>> html도 빌드될 수 있도록 플러그인을 설치

### 2.2 webpack.config.js
>> 플러그인 설정

```js
const path = require('path');
// html 플러그인을 불러오기
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  },
  // html 플러그인 설정
  plugins: [new HtmlWebpackPlugin({
    template: "./index.html" // index.html을 기본 템플릿으로 반영할 수 있도록 설정
  })]
}
```

### 2.3 npm run build
>> dist 폴더 index.html 생성되는지 확인

```js
project
├── dist
│   ├── index.html
│   └── main.js
├── index.html
├── package-lock.json
├── package.json
├── src
│   └── index.js
└── webpack.config.js
```

------------

## 3. 화면에 실시간 업데이트 반영 (dev server)

### 3.1 npm install webpack-dev-server -D
>> webpack-dev-server 설치
>> -D = --save-dav

### 3.2 webpack.config.js
>> dev server 설정

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist') 
  },
  plugins: [new HtmlWebpackPlugin({
    template: "./index.html"
  })],
  devServer: {
    // 개발 서버가 dist 폴더를 제공할 수 있도록 설정
    static: {
      directory: path.resolve(__dirname, 'dist')
    },
    port: 8080
  }
}
```

### 3.3 package.json
>> build 명령어 세팅

```js
"scripts": {
  "start": "webpack serve --open --mode=development",
  "build": "webpack --mode=production"
},
```
+ npm start : 개발 모드에서 사용하는 명령어이기 때문에 --mode=development로 모드를 설정해준다.
+ npm run build: 프로덕션 모드에서 사용하는 명령어이기 때문에 --mode=production로 모드를 설정해준다.

### 3.4 npm start

------------

## 4 internal 방식 스타일 시트

### 4.1 npm install -D css-loader style-loader
>> css-loader, style-loader 설치

+ css-loader : css 파일을 읽어주는 역할
+ style-loader : css를 style 태그로 만들어서 head 내부에 삽입

### 4.2 webpack.config.js

```js
...

module.exports = {
  entry: './src/index.js',
  output: {
    ...
  },
  plugins: [new HtmlWebpackPlugin({
    ...
  })],
  module: {
    rules: [
      {
        test: /\.css$/,
		// use 배열은 뒤에서부터 적용된다.
        use: ["style-loader", "css-loader"] 
      }
    ]
  },
  devServer: {
    ...
  }
}
```

### 4.3 src 폴더 안에 css를 생성 후 index.js에 import 해준다.

```
project
├── dist
│   ├── index.html
│   └── main.js
├── index.html
├── package-lock.json
├── package.json
├── src
│   ├── index.js
│   ├── style.css
│   └── util.js
└── webpack.config.js
```

#### src/index.js

```js
// css를 가져온다
import './style.css'

document.getElementById("root").innerHTML = '안녕하세요✌️';
```

#### src/style.css

```js
body {
	font-size: 30px;
	color: blue;
}
```

#### index.html

```js
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>Document</title>
</head>
<body>
	<div id="root"></div>
	<!-- 스크립트를 삽입하지 않아도 웹팩에서 자동으로 스크립트를 삽입해준다. -->
	<!-- 아래 코드는 필요없다-->
	<!-- <script src="./dist/main.js"></script>  -->
</body>
</html>
```

### 4.4 npm start
>> head 영역에 script, style 이 자동으로 들어와 있는지 확인

------------

## 5 external 방식 스타일 시트

### 5.1 npm install -D mini-css-extract-plugin
>> mini-css-extract-plugin 설치

### 5.2 webpack.config.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
// 플러그인을 불러온다
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    ...
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./index.html"
    }),
    // 플러그인 추가
    new MiniCssExtractPlugin({
      filename: "common.css",
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        // 다시 설정한다.
        use: [MiniCssExtractPlugin.loader, "css-loader"]
      }
    ]
  },
  devServer: {
   ...
  }
}
```

### 5-3 외부 파일로 삽입되었는지 확인

+ internal 방식은 build 해도 CSS 파일이 따로 생성되지 않는다. 바로 head에 작성되기 때문이다. 
+ 반면, external 방식은 build 하면 별도의 CSS 파일이 생성된다.

------------

## 6 이미지 로더

### 6.1 npm install -D file-loader
>> file-loader 설치

+ Loader?
>> 웹팩은 자바스크립트 파일뿐만 아니라 모든 웹 자원들도 모듈로 인식한다.
>> 자바스크립트 파일에 이미지나 CSS 파일을 import해서 사용할 수 있게 해주는 게 바로 Loader다.
>> Loader는 빌드할 때 import한 자바스크립트 파일이 아닌 리소스들을 해석하고 변환해주는 역할을 한다.


### 6.2 webpack.config.js

```js
...

module.exports = {
  entry: './src/index.js',
  output: {
    ...
  },
  plugins: [
    ...
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"]
      },
      // file-loader 세팅
      {
        test: /\.(png|jpe?g|gif|svg|webp)$/i,
        use: ['file-loader'],
      }
    ]
  },
  devServer: {
   ...
  }
}
```

------------

## 7 사용하지 않는 build 파일 자동으로 지우기
>> build에 포함됐던 파일이 필요 없어졌을 때 삭제하고 다시 build하면 그대로 남아있는 것을 확인할 수 있다.
>> 사용하지 않는 build 파일을 자동으로 삭제해주는 플러그인을 설치해보자.

### 7.1 npm install -D clean-webpack-plugin

### 7.2 webpack.config.js

```js
...

module.exports = {
  entry: './src/index.js',
  output: {
    ...
  },
  plugins: [
    ...
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"]
      },
      // file-loader 세팅
      {
        test: /\.(png|jpe?g|gif|svg|webp)$/i,
        use: ['file-loader'],
      }
    ]
  },
  devServer: {
   ...
  }
}
```

------------

+ 더하기
[PostCss & Autoprefixer 기본 설정(w. webpack)](https://gwpaeng.tistory.com/415 "velog")

------------

## 8 autoprefixer 

### 8.1 npm i -D postcss autoprefixer postcss-loader

### 8.2 webpack.config.js 

```js
...

module.exports = {
  entry: './src/index.js',
  output: {
    ...
  },
  plugins: [
    ...
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader", "postcss-loader"]
      },
      // file-loader 세팅
      {
        test: /\.(png|jpe?g|gif|svg|webp)$/i,
        use: ['file-loader'],
      }
    ]
  },
  devServer: {
   ...
  }
}
```

### 8.3 package.json

```js
"browserslist": [
  "> 1%",
  "last 2 versions"
]
```

### 8.3 postcss.config.js

```js
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
}
```

------------

+ 참고 
[[Webpack] Webpack에 한번 더 간단히 정리해보자.](https://blog.doitreviews.com/development/2020-04-29-webpack-basic/ "blog")
[title](url "velog")
[title](url "velog")


------------



