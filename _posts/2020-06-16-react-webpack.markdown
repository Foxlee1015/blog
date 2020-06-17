---
layout: post
title: React webpack
date: 2020-06-16 01:00:00 +0300
description: "React-webpack"
tags: [react, webpack]
img: react.png
---

## 웹팩 설정하기
[참고][참고]
[초기설정 참고][초기설정 참고]
[Sass적용][Sass적용]


1. npm init -y

2. npm i react react-dom (package.jon 에서 dependencies에 추가됨)

3. npm i -D @babel/core babel-loader @babel/preset-react @babel/preset-env   (package.json 에서 devDependencies 에 추가됨, --save-dev = -D)
   1. @babel/core : 리액트는 es6 사용함 -> es5문법으로 변경
   2. babel-loader :
      * 자바스크립트 파일을 babel preset/plugin과 webpack을 사용하여 es5로 컴파일 해주는 plugin
      * jsx -> javascript 로 컴파일
      * html webpack plugin
   3. @babel/preset-react : jsx -> javascript
   4. @babel/preset-env
      * es6 -> es5
      * es6 뿐 아니라 브라우저에 따라 알아서 컴파일 

4. npm i -D webpack webpack-dev-server webpack-cli html-webpack-plugin clean-webpack-plugin html-loader
   1. webpack : 모든 리액트 파일을 하나의 자바스크립트로 번들
   2. webpack-dev-server : live reload
   3. webpack-cli : build 스크립트를 통해 webpack 커맨드를 사용
   4. html-webpack-plugin : webpack.config.js에서 html 플러그인

5. npm i -D node-sass style-loader css-loader sass-loader mini-css-extract-plugin
   1. node-sass provides binding for Node.js to LibSass, a Sass compiler.
   2. sass-loader is a loader for Webpack for compiling SCSS/Sass files.
   3. style-loader injects our styles into our DOM.
   4. css-loader interprets @import and @url() and resolves them.
   5. mini-css-extract-plugin extracts our CSS out of the JavaScript bundle into a separate file, essential for production builds.

* First, SCSS is converted to CSS (sass-loader), then run through css-loader to process @import(), url() etc, then style-loader (to be appended to the DOM) or Mini CSS Extract Plugin to externalise the CSS when doing a production build.


[참고]: https://velog.io/@jeff0720/React-%EA%B0%9C%EB%B0%9C-%ED%99%98%EA%B2%BD%EC%9D%84-%EA%B5%AC%EC%B6%95%ED%95%98%EB%A9%B4%EC%84%9C-%EB%B0%B0%EC%9A%B0%EB%8A%94-Webpack-%EA%B8%B0%EC%B4%88
[초기설정 참고]:https://velog.io/@pop8682/%EB%B2%88%EC%97%AD-React-webpack-%EC%84%A4%EC%A0%95-%EC%B2%98%EC%9D%8C%EB%B6%80%ED%84%B0-%ED%95%B4%EB%B3%B4%EA%B8%B0
[Sass적용]: https://developerhandbook.com/webpack/how-to-configure-scss-modules-for-webpack/ 
