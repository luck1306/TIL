created at : 20250706 14:07
### Babel
개요 : js 컴파일러(더 정확히 말하면 변환기)이다. 주요 기능은 최신 js 문법으로 작성된 코드를 하위호환지원을 위해 변환한다.(최신 언어 사양을 완전히 지원하지 않는 옛날 브라우저나 환경을 위해)
원리 : 사용자가 작성한 신버전의 js 코드를 입력받아 동등한 기능의 옛버전 js코드를 산출한다.
### Webpack
개요 : 모듈 집합기(bundler)로, 주요한 기능은 웹 어플리케이션(js file, css, image, font, 등등)의 다양한 에셋 전부 가져다 모듈로서 다루고, 배포 목적의 하나이상의 최적화 묶음(bundle)에 다양한 에셋을 합친다.
원리 : 사용자 모듈 사이의 의존관계를 분석하고, loader(babel-loader 같은)로처리한다. 그리고 이를 정적 에셋으로 묶으면 브라우저에 효율적으로 보낼 수 있다. 

---
### 구성 순서
1. 폴더생성
2. 다음 의존성 설치 (개발 의존성)
	- webpack
	- webpack-cli
	- webpack-dev-server
	- @babel/cli
	- @babel/core
	- @babel/preset-env
	- @babel/preset-react
	- babel-loader
	- css-loader
	- file-loader
	- style-loader
	- html-webpack-plugin
3. 다음 의존성 설치 (프로덕션 의존성)
	- react
	- react-domki
4. webpack.config.js 작성
```js
//example
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
	mode: "development",
	entry: "./src/index.js", // 번들 처리를 시작할 지점
	output: { // 번들된 파일의 출력 설정
		filename: [name].bundle.js, // 출력 파일 이름
		path: paht.resolve(__dirname, "build"), // 출력 디렉토리
		clean: true, // 이전 빌드 결과물 삭제
	},
	module: {
		rules: [
			{
				test: /\.(js|jsx)$/, // 정규식에 맞는 파일을 처리
				exclude: /node_modules/, // 정규식에 맞는 파일을 제외
				use: { // loader 속성을 필수로 지정
					loader: "bable-loader", // 옛 브라우저가 이해할수 있도록 변환
					options: { // loader의 옵션 (이 options 작성 시 .babelrc 작성할 필요 없음)
						presets: ["@babel/preset-env", "@babel/preset-react"],
					}
				},
			},
			{
				test: /\.css$/,
				use: ["style-loader", "css-loader"],
			},
			{
				test: /\.(png|jp(e*)g|gif)$/,
				type: "asset/resources",
				use: {
					loader: "file-loader",
					options: {
						name: "[name].[ext]",
						outputPath: "images/"
					},
				},
			},
		],
	},
	plugins: [
		new HtmlWebpackPlugin({
			template: "./public/index.html",
		}),
	],
	devServer: {
		port: 3000,
		hot: true, // Webpack의 Hot Module Replacement 기능 활성화
		host: "localhost",
		// open: true, // 서버 실행 시 브라우저 자동 열기
		// compress: true, // 압축
	}
}
```
5. Babel 설정 파일 작성[^1] (두 가지 파일 포맷 존재)
	- babel.config.json (보통 포로젝트 전체 설정)
	- .babelrc (보통 파일에 따라 설정)
```.babelrc
// .babelrc
{
	"presets": ["@babel/preset-env". "@babel/preset-react"]
}
```
6. 기본 React 파일 생성
	- src/index.js 생성
	- public/index.html 생성
```js
// src/index.js
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
	<React.StrictMode>
		<App />
	</React.StrictMode>
);
```
```html
<!-- public/index.html -->
<!DOCTYPE html>

<html lang="en">
	<head>
		<meta charset="utf-8" />
		<!-- <link rel="icon" href="%PUBLIC_URL%/favicon.ico" /> -->
		<meta name="viewport" content="width=device-width, initial-scale=1" />
		<meta name="theme-color" content="#000000" />
		<meta
		name="description"
		content="Web site created using create-react-app"
		/>
		<!-- <link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" /> -->
		<!--
		manifest.json provides metadata used when your web app is installed on a
		user's mobile device or desktop. See https://developers.google.com/web/fundamentals/web-app-manifest/
		-->
		<!-- <link rel="manifest" href="%PUBLIC_URL%/manifest.json" /> -->
		<!--
		Notice the use of %PUBLIC_URL% in the tags above.
		It will be replaced with the URL of the `public` folder during the build.
		Only files inside the `public` folder can be referenced from the HTML.
		Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
		work correctly both with client-side routing and a non-root public URL.
		Learn how to configure a non-root public URL by running `npm run build`.
		-->
		<title>React App</title>
	</head>
	<body>
		<noscript>You need to enable JavaScript to run this app.</noscript>
		<div id="root"></div>
		<!--
		This HTML file is a template.
		If you open it directly in the browser, you will see an empty page.
		You can add webfonts, meta tags, or analytics to this file.
		The build step will place the bundled scripts into the <body> tag.
		To begin the development, run `npm start` or `yarn start`.
		To create a production bundle, use `npm run build` or `yarn build`.
		-->
	</body>
</html>

```
7. package.jsom scripts 속성 예시
```json
// package.jsom
...
"scripts": {
	"build": "webpack --config webpack.config.js",
	"start": "webpack-dev-server"
},
...
```

#### 주의사항
모든 Component에 "import React from 'react'"를 반드시 삽입해야함.
"create-react-app"으로 프로젝트 생성 시에 상관쓰지 않아도 되는 문제지만
직접 react 모듈을 설치해 구성했기 때문에 그런 편의성은 없다.


[^1]: webpack.config.json의 "babel-loader"에 관한 module.rules.use.options 속성 미작성시