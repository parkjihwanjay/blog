---
title: Webpack, Babel 설정
date: 2020-09-06 12:09:92
category: 프론트엔드
thumbnail: { thumbnailSrc }
draft: false
---

# Webpack, Babel설정하기

지금까지 babel과 webpack의 개념을 알아봤으니 실제로 설정을 해볼게요.

### Babel 설정하기

먼저, 필요한 모듈들을 깔겠습니다.

babel의 모듈들은 7버전부터 @babel로 시작하며 npm으로 받을 수 있습니다.

- @babel/core는 babel의 핵심 기능입니다.

- @babel/preset-env는 가장 많이 사용하는 preset으로 필수적인 plugin들이 모여있습니다.

- @babel/cli는 터미널에서 bable를 실행하는 모듈입니다.

```markdown
// npm 프로젝트를 시작하는 모든 옵션을 yes로 설정하겠다.
$ npm init -y
// 해당 모듈을 devdependency로 깔겠다.
// @babel/cli는 굳이 안깔아도 됩니다.
$ npm i @babel/core @babel/cli @babel/preset-env -D
```

다음으로, babel 설정을 하겠습니다.

Babel 설정은 CLI로 해도 되고 여러 방법이 있지만 저는 config 파일에 설정해보겠습니다.

babel.config.json 파일은 전역에 적용하는 설정,

.babelrc.json은 프로젝트의 일부에 적용하는 설정이라고 아시면 됩니다.

babel 공식 페이지에서는 babel.config.json 사용을 권장하고 있습니다.

babel 자체가 babel.config.json 파일을 사용한다고 하네요.

저는 babel.config.json 파일에 설정해보겠습니다.

프로젝트 root에 babel.config.json 파일을 만들어주세요.

**babel.config.json**

```javascript
module.exports = function(api) {
  // plugin이나 preset을 캐시하여 다시 실행하지 않도록
  api.cache(true)

  // preset 추가
  const presets = [
    [
      '@babel/preset-env',
      {
        targets: '> 0.25%, not dead',
        // 0.25% 이상의 시장 점유율을 가지는 브라우저에 대해 지원하겠다.
        // not dead : 보안 update를 하는 브라우저에 대해 지원하겠다.
        'core-js': 3,
        // polyfill은 core-js를 사용하겠다.
        useBuiltIns: 'usage',
        // 모든 polyfill을 가져오는게 아니라 필요한 Polyfill를 import해서 사용하겠다.
        modules: true,
        // ES6의 module syntax를 지원하겠다.
      },
    ],
  ]

  return {
    presets,
  }
}
```

위의 옵션들은 babel 공식 홈페이지에 잘 나와있습니다.

저는 프로젝트에서 polyfill을 core-js를 사용하기 때문에 core-js도 깔아줍시다.

```
$ npm i core-js
```

polyfill은 runtime에서 필요하기 때문에 dependency에 깔아줍니다.

이렇게 하면 기본적인 babel 설정은 끝났습니다.

### Webpack 설정하기

이제 모듈 번들러인 webpack을 설정하겠습니다.

먼저 필요한 모듈을 깔아줍니다.

```
// webpack-cli는 안까셔도 됩니다.
$ npm i webpack webpack-cli html-webpack-plugin clean-webpack-plugin webpack-merge webpack-dev-server -D
```

- Html-webpack-plugin
  - 웹팩으로 빌드한 결과물로 HTML 파일을 생성해주는 플러그인
  - 번들링한 자바스크립트 파일을 자동으로 연결해줍니다.
- Clean-webpack-plugin
  - build할때 기존에 build된 파일들을 제거해줍니다.
- webpack-merge
  - development와 production config 파일을 따로 만들때 공통된 속성을 묶을 때 필요합니다.
- webpack-dev-server
  - 웹팩으로 빌드한 후 빌드한 파일을 서버로 띄웁니다.
  - 실제로 파일로 빌드되진 않고 메모리에 저장됩니다. 따라서, 폴더에서는 보이지 않습니다.

다음으로 필요한 loader를 깔아줍니다.

```
npm i babel-loader css-loader file-loader -D
```

- babel-loader
  - webpack에서 babel을 적용할때 꼭 필요합니다.
  - js 파일을 번들링 할때 babel을 통해 transpile 후에 번들링 할 수 있습니다.
- css-loader
  - Css 파일을 모듈처럼 import 할 수 있습니다.
  - 자바스크립트 모듈로 변환합니다.
- file-loader
  - 이미지나 파일을 모듈로 변환합니다.
  - 마찬가지로 import 할 수 있습니다.

먼저, 개발 모드든 배포 모드든 필요한 설정인 webpack.common.js 파일을 만듭니다.

**webpack.common.js**

```javascript
// node.js에 기본으로 깔려있는 모듈로 경로 지정을 위해 사용
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')

module.exports = {
  // entry 포인트 지정
  entry: './src/app.js',
  // dist라는 폴더의 bundle.js라는 이름의 파일로 번들링하겠다.
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    // 사용하는 loader를 써줍니다.
    // loader는 순서가 중요합니다. 위에서 아래로 써주세요.
    rules: [
      {
        test: /\.js$/,
        use: 'babel-loader',
        exclude: /node_modules/,
      },
      {
        test: /\.(png|jpe?g|gif)$/i,
        use: 'file-loader',
      },
      {
        test: /\.css$/i,
        // use의 경우 오른쪽에서 왼쪽으로 읽습니다.
        // 즉, css-loader를 적용 후 style-loader 적용
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
  // plugin 설정
  plugins: [
    new HtmlWebpackPlugin({
      template: 'src/app.html',
    }),
    new CleanWebpackPlugin(),
  ],
  // chunk로 나눌때의 옵션
  optimization: {
    splitChunks: {
      chunks: 'async',
      minSize: 30000,
      maxSize: 0,
      minChunks: 1,
      maxAsyncRequests: 6,
      maxInitialRequests: 4,
      automaticNameDelimiter: '~',
      cacheGroups: {
        defaultVendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10,
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true,
        },
      },
    },
  },
  devtool: 'cheap-eval-source-map',
}
```

다음으로 개발 모드에서 사용하는 webpack.dev.js를 적어줍니다.

**webpack.dev.js**

```javascript
const common = require('./webpack.common.js')
const { merge } = require('webpack-merge')

// webpack.common.js에 추가
module.exports = merge(common, {
  // 개발모드
  mode: 'development',
  // debug tool을 정해줍니다.
  devtool: 'inline-source-map',
  // dist의 빌드한 내용을 서버로 띄우겠다.

  devServer: {
    contentBase: './dist',
    port: 9000,
    // 빌드 대상 파일이 변경되면 자동으로 브라우저를 새로고침
    hot: true,
  },
})
```

마지막으로 build 할 때 사용하는 webpack.prod.js를 적어줍니다.

**webpack.prod.js**

```javascript
const { merge } = require('webpack-merge')
const common = require('./webpack.common.js')

module.exports = merge(common, {
  mode: 'production',
})
```

배포 모드의 경우 성능 최적화를 위해 기본적인 파일 압축 등의 빌드 과정이 추가됩니다.

이제 package.json에서 script 부분을 추가해줍니다.

**package.json**

```javascript
...

"scripts": {
    ...
    "dev": "webpack-dev-server --open --config webpack.dev.js",
    "build": "webpack --config webpack.prod.js"
  },

...
```

이제 **npm run dev**를 하면 webpack-dev-server가 돌아가고 **npm run build**를 하면 build한 파일을 diet 폴더 안에 넣어줍니다.

여기까지 babel과 webpack의 기본 설정입니다.

고생하셨습니다~!
