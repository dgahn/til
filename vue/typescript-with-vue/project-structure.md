# 프로젝트 구조 및 설명

## 1. package.json

package.json을 보면 아래와 같이 **scripts** 부분이 있다. 여기에는 **npm**을 통해서 실행시킬 수 있는 명령어들이 작성되어 있다.

예를 들어, **npm run serve**라는 명령어를 작성하면 **npm**은 **packge.json** 파일에서 **scripts**의 **serve**를 보고 **vue-cli-service serve**를 실행하는 것이다.

```json
{
  // ... 생략
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  // ... 생략
}
```

- **serve** : 현재 수정하고 있는 코드에 대해서 데모 화면을 보여준다.
- **build** : 완성된 vue-project에 대해서 배포하기 위한 빌드 파일을 생성한다.
- **lint** : **tslint.json** 파일에 있는 설정을 바탕으로 코드를 점검해준다. 간단한 띄어쓰기 같은 경우는 직접 수정해준다.

<br>

## 2. 프로젝트의 구조

프로젝트는 크게 3개의 디럭토리로 구성되어 있다. 

- **node_modules** : node와 관련된 패키지 모듈이 담겨져 있다.
- **public** : 정적인 파일들이 위치하는 곳이다.
- **src** : 주로 수정할 코드들이 들어 있다.
  - **asset** :  이미지나 css와 같은 리소스들이 들어간다.
  - **component** : 모든 페이지에서 사용하는 공통 모듈이 들어간다.
  - **view** : 각 화면에 대한 컴포넌트들이 들어간다.
  - **main.ts** : 뷰 인스턴스를 생성한다. 프로젝트의 엔트리 포인트가 된다.

## 3. main.ts 좀 더 살펴보기

**vuejs**를 배운 적이 없어 강의에서는 쑥 지나갔지만 자세히 살펴보도록 하겠다. 먼저 기본적으로 생성되는 **main.ts** 파일을 아래와 같다.

```typescript
import Vue from 'vue';
import App from './App.vue';
import router from './router';
import store from './store';

Vue.config.productionTip = false;

new Vue({
  router,
  store,
  render: (h) => h(App),
}).$mount('#app');
```

아래의 **new Vue**를 통해서 뷰 인스턴스가 생성이 되는데 이때 생성자에 각 컴포넌트, 라우터, 스토어 등등을 임포트 할 수 있다. 마지막에 **.$mount('#app')** 을 통해서 해당 인스턴스를 **id**가 **app**인 곳에 렌더링을 한다.

이 **id**는 **public/index.html**에 존재한다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title>vue-started</title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but vue-started doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

이는 빌드를 할 때, **vue**가 **index.html**의 해당 **id**에 렌더링을 알아서 해준다.

<br>

다시 **main.ts** 파일을 보자. 

```typescript
import Vue from 'vue';
import App from './App.vue';
import router from './router';
import store from './store';

Vue.config.productionTip = false;

new Vue({
  router,
  store,
  render: (h) => h(App),
}).$mount('#app');
```

**main.ts**에서는 **App.vue**를 import하고 있고 그것을 뷰 인스턴스에 렌더링하고 있다. 그렇기 때문에 프로젝트를 실행하게 되면 **App.vue**가 렌더링 되어 화면에 나오는 것이다.


<br>

아래는 **App.vue** 코드이다.

```html
<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <router-view/>
  </div>
</template>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
```

이게 렌더링 되면 아래와 같은 화면이 되는 것이다.

![](https://imgur.com/lu7YpZu.png)

<br>

**App.vue**의 코드를 자세히 보자.

```html
<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <router-view/>
  </div>
</template>
// ... 생략
```

네비게인션바를 제외하면 실질적으로 존재하는 부분은 단 하나 **router-view** 태그이다. 이 부분에 라우트(url)에 해당하는 뷰가 렌더링 되는 것이다.

<br>

라우트와 관련된 부분은 **router.ts**에서 관리가 된다.

```typescript
import Vue from 'vue';
import Router from 'vue-router';
import Home from './views/Home.vue';

Vue.use(Router);

export default new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/',
      name: 'home',
      component: Home,
    },
    {
      path: '/about',
      name: 'about',
      // route level code-splitting
      // this generates a separate chunk (about.[hash].js) for this route
      // which is lazy-loaded when the route is visited.
      component: () => import(/* webpackChunkName: "about" */ './views/About.vue'),
    },
  ],
});
```

- **routes**의 **path** : 실제 url에 어떻게 사용되냐를 넣어준다.
- **routes**의 **component** : 해당 url에 어떤 컴포넌트가 들어가냐를 적어준다. **Home** 같은 경우는 위에 import를 해줬기 때문에 간단하게 **Home**으로 적어줄 수 있다.

## 4. tsconfig.json

**Typescript**로 짜여진 코드를 어떻게 **Javascript**로 빌드시킬지에 대한 설정이 들어있다.

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "esnext",
    "strict": true,
    "jsx": "preserve",
    "importHelpers": true,
    "moduleResolution": "node",
    "experimentalDecorators": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "sourceMap": true,
    "baseUrl": ".",
    "types": [
      "webpack-env"
    ],
    "paths": {
      "@/*": [
        "src/*"
      ]
    },
    "lib": [
      "esnext",
      "dom",
      "dom.iterable",
      "scripthost"
    ]
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.tsx",
    "src/**/*.vue",
    "tests/**/*.ts",
    "tests/**/*.tsx"
  ],
  "exclude": [
    "node_modules"
  ]
}
```

- **compilerOptions**의 **target** : Javascript 버전을 의미한다.
- **compilerOptions**의 **moduleResolution** : 모듈의 해석 방식이다.
- **include** : 어떤 파일을 빌드할지에 대한 범위를 지정한다.

<br>

## 5. tslint.json

Typescript 관련 **Lint** 설정을 할 수 있는 파일이다.

```json
{
  "defaultSeverity": "warning",
  "extends": [
    "tslint:recommended"
  ],
  "linterOptions": {
    "exclude": [
      "node_modules/**"
    ]
  },
  "rules": {
    "indent": [true, "spaces", 2],
    "interface-name": false,
    "no-consecutive-blank-lines": false,
    "object-literal-sort-keys": false,
    "ordered-imports": false,
    "quotemark": [true, "single"]
  }
}
```

**rule**에 필요한 **Lint**를 추가할 수 있다.