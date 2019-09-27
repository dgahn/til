# vue-cli로 Typescript 프로젝트 생성하기

>이 문서는 MacOS를 기준으로 작성합니다.

## 1. nodejs 설치

brew를 통해서 node를 설치한다.

```bash
$ brew install node
$ node -v
v12.10.0
```

<br>

## 2. vue-cli 설치

[Vue-CLI](https://cli.vuejs.org/) 공식 블로그에 들어가면 설치 방법에 대해서 나와있다.

```bash 
$ npm install -g @vue/cli
# OR
$ yarn global add @vue/cli
```


<br>

## 3. vue-cli로 프로젝트 생성하기

**vue-cli**를 로컬에 생성하였으면 해당 폴더에서 cli 명령어를 통해서 **vue** 프로젝트를 만들어보자. 

```bash
$ vue create vue-started
```

<br>

**preset** 매뉴얼이 나오는데 다른 설정을 적용할 예정이기 때문에 **Manually select features**를 선택하자.

```bash
Vue CLI v3.11.0
? Please pick a preset:
  default (babel, eslint)
❯ Manually select features
```

<br>

**preset** 설정이 나오면 **space bar**를 통해서 아래와 같이 옵션을 선택한다. 옵션을 모두 선택하면 엔터를 누른다.

```bash
Vue CLI v3.11.0
? Please pick a preset: Manually select features
? Check the features needed for your project:
 ◯ Babel
 ◉ TypeScript
 ◯ Progressive Web App (PWA) Support
 ◉ Router
 ◉ Vuex
 ◯ CSS Pre-processors
❯◉ Linter / Formatter
 ◯ Unit Testing
 ◯ E2E Testing
```

<br>

그 다음에는 클래스 타입으로 컴포넌트를 작성한건지에 대한 질문이 나오는데 디폴트 값이 **yes**이기 때문에 **Enter**를 누른다.

```bash
Vue CLI v3.11.0
? Please pick a preset: Manually select features
? Check the features needed for your project: TS, Router, Vuex, Linter
? Use class-style component syntax? (Y/n)
```

<br>

그 다음에는 바벨과 함께 스캐폴딩하겠냐라는 질문이 있는데 디폴트가 **No**이다. **Enter**를 누른다.

```bash
Vue CLI v3.11.0
? Please pick a preset: Manually select features
? Check the features needed for your project: TS, Router, Vuex, Linter
? Use class-style component syntax? Yes
? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? (y/N)
```

<br>

그 다음에는 라우터에서 히스토리 모드를 사용할 것이냐에 대한 설정이다. URL에 **#**이 포함되지 않도록 나오기 때문에 사용하도록 하겠다.

```bash
Vue CLI v3.11.0
? Please pick a preset: Manually select features
? Check the features needed for your project: TS, Router, Vuex, Linter
? Use class-style component syntax? Yes
? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? No
? Use history mode for router? (Requires proper server setup for index fallback in production) (Y/n) Y
```

<br>

그 다음에는 어떤 **Lint**를 사용할 것이냐에 대한 설정이다. Typescript를 사용하니 **TSLint**를 사용하자.

```bash
Vue CLI v3.11.0
? Please pick a preset: Manually select features
? Check the features needed for your project: TS, Router, Vuex, Linter
? Use class-style component syntax? Yes
? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? No
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a linter / formatter config: (Use arrow keys)
❯ TSLint
  ESLint with error prevention only
  ESLint + Airbnb config
  ESLint + Standard config
  ESLint + Prettier
```

<br>

그 다음에는 **Lint**에 대한 추가설정이 나오는데 디폴트 설정을 이용하도록 하겠다.

```bash
Vue CLI v3.11.0
? Please pick a preset: Manually select features
? Check the features needed for your project: TS, Router, Vuex, Linter
? Use class-style component syntax? Yes
? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? No
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a linter / formatter config: TSLint
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection)
❯◉ Lint on save
 ◯ Lint and fix on commit
```

<br>

그 다음에는 **Lint**에 대한 설정을 **packge.json**에 둘 것이냐? 아니면 별도의 파일로 관리할 것이냐에 대한 물음이다. 별도의 파일로 관리하겠다.

```bash
Vue CLI v3.11.0
? Please pick a preset: Manually select features
? Check the features needed for your project: TS, Router, Vuex, Linter
? Use class-style component syntax? Yes
? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? No
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a linter / formatter config: TSLint
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection)Lint o
n save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? (Use arrow keys)
❯ In dedicated config files
  In package.json
```

<br>

그 다음에는 다음 프로젝트를 위해서 프리셋을 저장해둘 것이냐에 대해서 물어본다. 저장하지 않겠다.

```bash
Vue CLI v3.11.0
? Please pick a preset: Manually select features
? Check the features needed for your project: TS, Router, Vuex, Linter
? Use class-style component syntax? Yes
? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? No
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a linter / formatter config: TSLint
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection)Lint o
n save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedicated config files
? Save this as a preset for future projects? (y/N)
```

<br>

다음으로는 패키지 매니저를 무엇으로 둘 것이냐에 대한 질문이 나온다. NPM이 익숙하기 때문에 NPM을 사용하도록 하겠다.

```bash
Vue CLI v3.11.0
? Please pick a preset: Manually select features
? Check the features needed for your project: TS, Router, Vuex, Linter
? Use class-style component syntax? Yes
? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? No
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a linter / formatter config: TSLint
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection)Lint o
n save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedicated config files
? Save this as a preset for future projects? No
? Pick the package manager to use when installing dependencies: (Use arrow keys)
❯ Use Yarn
  Use NPM
```

그리고 나서 각자의 에디터로 프로젝트를 열어보자.