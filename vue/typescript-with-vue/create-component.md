# @Component로 컴포넌트 만들기

## 1. @Decorator란 무엇인가?

Typescript에서 **@Decorator**는 함수를 의미한다. 그래서 클래스 위에 **Decorator**를 추가하면 해당 기능이 클래스에 포함된다고 생각을 하면 된다.

## 2. 새로운 컴포넌트 추가하기

기본적으로 생성해야하는 형식은 아래와 같다.

```html
<template>
    <div>
        
    </div>
</template>

<script lang=ts>
  import {Component, Vue} from "vue-property-decorator";

  @Component
  export default class Message extends Vue{
  }
</script>

<style scoped>

</style>
```

- **template** 태그 : 템플릿 태그는 실제로 렌더링될 **html** 코드가 들어간다. 주의할 점은 **template** 태그 안에는 하나의 태그만 존재해야하기 때문에 **div**과 같은 태그로 감싸줘야 한다.
- **script** 태그 : Typescript 코드가 들어가는 부분이다. 클래스 기반으로 코드를 작성하기 때문에 위와 같이 작성해야한다. 주의할 점은 **Typescript** 문법을 사용할 경우에는 **lang=ts**라는 속성을 추가해줘야한다.

<br>

아래와 같이 코드를 추가해보자.

```html
<template>
    <div>
        <input type="text" v-model="message">
        <div>{{message}}</div>
    </div>
</template>

<script lang=ts>
  import {Component, Vue} from "vue-property-decorator";

  @Component
  export default class Message extends Vue{
    message : string = "메세지를 입력해주세요."
  }
</script>
```

먼저, input 태그를 추가하여 **v-model**을 통해서 스크립과 바인딩을 했다. 그리고 **{{message}}**를 통해서 메시지의 내용을 화면에 출력하는 코드를 작성했다. 새로운 컴포넌트를 추가하고 코드도 작성하였으니 뷰에서 우리가 만든 컴포넌트를 보여줄 수 있게 설정해보도록 하자.

<br>

**Vue**에서 컴포넌트를 렌더링하는 절차를 다시 이야기하면 아래와 같다.

1. **main.ts** 파일에서 렌더링할 **Vue** 파일을 설정한다. 이때, 일반적으로 **App.vue**이 설정되어 있다.
2. **App.vue** 파일에는 **router-view** 태그에 의해서 라우터마다 보여지는 화면을 변경한다.
3. **router.ts** 파일에 **url**에 따라 뷰 컴포넌트가 결정된다. 해당 뷰를 보여준다.
4. **뷰 컴포넌트**에 있는 내용들이  **App.vue** 파일의 **router-view** 태그에 보여진다.
5. 인스턴스화 된 **Vue**는 **index.html**에 렌더링 된다.

현재는 **"/"** 에 접속할 때, 화면이 변경되도록 설정할 것이다. 그렇기 때문에 **router.ts**를 확인해보자.

```Typescript
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

**"/"** url은 **'./views/Home.vue'** 컴포넌트와 매칭이 되어 있다. 

<br>

그러므로 **'./views/Home.vue'** 컴포넌트를 수정하러 가보자.

```html
<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import HelloWorld from '@/components/HelloWorld.vue'; // @ is an alias to /src

@Component({
  components: {
    HelloWorld,
  },
})
export default class Home extends Vue {}
</script>
```

<br>

**HelloWorld** 컴포넌트는 필요가 없으므로 삭제시키자.

```html
<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

@Component({
  components: {

  },
})
export default class Home extends Vue {}
</script>
```

<br>

그리고 새로 만든 **Message.vue** 컴포넌트를 추가하자.

```html
<template>
    <div class="home">
        <img alt="Vue logo" src="../assets/logo.png">
        <Message></Message> 
    </div>
</template>

<script lang="ts">
  import {Component, Vue} from "vue-property-decorator";
  import Message from "@/components/Message.vue";

  @Component({
    components: {
      Message
    }
  })
  export default class Home extends Vue {
  }
</script>
```

**script** 태그에 내가 작성한 Message 컴포넌트를 추가하고 **template** 태그에 **Message** 컴포넌트 태그를 추가하면 완성한다.