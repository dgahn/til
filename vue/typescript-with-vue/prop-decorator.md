# @Prop으로 데이터 전달하기

## 1. 상위 - 하위 컴포넌트

**Vue**에서 컴포넌트는 하나의 인스턴스라고 볼 수 있다. 내가 표현하려는 컴포넌트는 상위 컴포넌트인데 그 안에 또 하나의 컴포넌트를 보여주자고 하자. 그러면 영역으로 표시할 때, 아래의 그림처럼 나올 것이다.

<img src="https://imgur.com/aFILQZP.png" width="700" >

그러면 상위 컴포넌트가 호출될 때, 하위 컴포넌트가 같이 호출이 되어 화면에 렌더링 될 것이다. 이런 상황에서 상위 컴포넌트에서 표시되는 값과 하위 컴포넌트에서 표시되는 값이 동일해야하는 경우가 있을 수가 있다.

그럴 때는 상위 컴포넌트에서 사용하는 값과 하위 컴포넌트에서 사용하는 값이 같으면 되는데 **Vue**에서는 **@Prop**을 통해서 **상위 컴포넌트의 데이터를 하위 컴포넌트**에게 전달해줄 수 있다.

<br>

## 2. 데이터 전달하기

**Home** 이라는 상위 컴포넌트에서 **Room**이라는 하위 컴포넌트로 데이터를 전달해보도록 하겠다.

### 2.1 정적인 값 전달하기

**components** 디렉터리에 아래와 같이 **Room.vue** 파일을 추가한다.

그리고 **@Prop** 데코레이터를 통해서 필드에 필요한 값을 전달 받는다.

```html
<template>
    <div>
        {{parentMessage}}
    </div>
</template>

<script lang=ts>
  import {Component, Prop, Vue} from "vue-property-decorator";

  @Component
  export default class Room extends Vue {
    @Prop() parentMessage?: string;
  }
</script>
```

<br>

상위 컴포넌트에서 하위 컴포넌트를 호출하면서 메시지를 추가해주면 된다.

```html
<template>
    <div class="home">
        <img alt="Vue logo" src="../assets/logo.png">
        <Room parent-message="Hello"></Room>
    </div>
</template>

<script lang="ts">
  import {Component, Vue} from "vue-property-decorator";
  import Room from "@/components/Room.vue";

  @Component({
    components: {
      Room
    }
  })
  export default class Home extends Vue {
  }
</script>

```

주의할 점은 상위 컴포넌트에서 **하위 컴포넌트 태그** 안의 속성 값과 **하위 컴포넌트**에서 **@Prop()**이 선언된 변수명과 동일해야한다.

### 2.2 동적인 값 전달하기

동적인 값을 전달하고 싶은 경우에는 **상위 컴포넌트**에서 아래와 같이 작성하면 된다.

```html
<template>
    <div class="home">
        <img alt="Vue logo" src="../assets/logo.png">
        <Room :parent-message="message"></Room>
    </div>
</template>

<script lang="ts">
  import {Component, Vue} from "vue-property-decorator";
  import Room from "@/components/Room.vue";

  @Component({
    components: {
      Room
    }
  })
  export default class Home extends Vue {
      message: string = "Hello";
  }
</script>

```