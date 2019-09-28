# @Provide/@Inject로 데이터 전달하기

**@Prop** 데코레이터와 비슷한 역할을 하는 데코레이터이다. **@Prop** 데코레이터는 상위 - 하위 단계가 단 하나여만 하는데 **@Provide/@Inject** 같은 경우 몇단계를 거친 상위 컴포넌트의 속성을 가져올 수 있다.

하지만, **@Prop**와 다르게 코드를 보고 어디서 어떤 속성을 가져오는지에 대한 명시가 비교적 떨어지기 때문에 공식 문서에도 아래와 같은 내용을 보여준다.

>`provide`와 `inject`는 주로 고급 플러그인/컴포넌트 라이브러리를 위해 제공됩니다. 일반 애플리케이션 코드에서는 사용하지 않는 것이 좋습니다.

남용하지 않도록 주의하자.

<br>

## 1. 코드 작성하기

상위 컴포넌트가 될 **Home.vue** 파일을 아래와 같이 수정하자.

```html
<template>
    <div class="home">
        <img alt="Vue logo" src="../assets/logo.png">
        <Room></Room>
    </div>
</template>

<script lang="ts">
  import {Component, Provide, Vue} from "vue-property-decorator";
  import Room from "@/components/Room.vue";

  @Component({
    components: {
      Room
    }
  })
  export default class Home extends Vue {
    @Provide("message") msg = "Hello World";
  }
</script>
```

- **@Provide** : 해당 데코레이터에 해당하는 속성을 하위 컴포넌트에게 전달한다. 이 때, 하위 컴포넌트가 인식하는 속성의 이름을 옵션으로 지정할 수 있다.

<br>

하위 컴포넌트가 될 **Room.vue** 파일을 아래와 같이 수정하자.

```html
<template>
    <div>
        {{message}}
    </div>
</template>

<script lang=ts>
  import {Component, Prop, Vue, Watch, Emit, Inject} from "vue-property-decorator";

  @Component
export default class Room extends Vue {
    @Inject() message!: string;
  }
</script>
```

- **@Inject()** : 상위 컴포넌트가 전달해준 데이터를 하위 컴포넌트의 속성에 주입시킨다. 주의할 점은 상위 컴포넌트의 값을 넣은 속성은 초기화해서는 안된다. 그렇기 때문에 변수명 뒤에 **!** 나 **?** 를 넣어준다.