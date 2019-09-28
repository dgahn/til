# @Watch로 데이터 변화 감지하기

**@Watch**는 뷰 인스턴스의 데이터 변화를 감지하고 변화하가 일어났을 때 필요한 일을 할 수 있는 핸들러를 제공하는 데코레이터이다. 

**@Watch**는 메소드에 쓰여지는데 특정 속성을 감지하고 있다가 변화가 생기면 쓰여진 메소드를 실행시켜서 다른 속성을 변경하는 등의 일을 할 수 있다.

이번 예제에서는 상위 컴포넌트에서 하위 컴포넌트로 전달되는 **prop** 값에 변화가 생겼을 때, 하위 컴포넌트에서 **@Watch**를 통해 해당 **prop**의 변화를 감지하고 화면에 다른 값을 표시할 것이다.

<br>

## 1. @click을 통해서 이벤트 바인딩 하기

**Home.vue** 파일로 이동하여 아래와 같이 코드를 변경하도록 하자.

```html
<template>
    <div class="home">
        <img alt="Vue logo" src="../assets/logo.png">
        <Room :parentMessage="message"></Room>
        <button @click="messageChange">메시지를 바꿉니다.</button>
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
    message: string = "hello world";

    messageChange() {
      this.message = "Hello World!!";
    }
  }
</script>

```

뷰에서 **@click**을 사용하면 클릭 이벤트를 감지할 수 있다. 클릭 이벤트를 감지했을 때, **messageChange** 메소드를 호출하도록 코드를 변경하였다.

그 이후에 프로젝트를 실행하면 화면에서 하위 컴포넌트에게 넘긴 값이 변경되는 것을 확인할 수 있다.

<br>

## 2. @Watch 사용하기

**@Watch**을 이용하여 코드를 아래와 같이 변경해보자.

```html
<template>
    <div>
        {{parentMessage}}
        <p>
            {{alertMessage}}
        </p>
    </div>
</template>

<script lang=ts>
  import {Component, Prop, Vue, Watch} from "vue-property-decorator";

  @Component
  export default class Room extends Vue {
    @Prop() parentMessage?: string;
    alertMessage: string = "";

    @Watch("parentMessage")
    update() {
      this.alertMessage = "메시지를 업데이트했습니다.";
    }

  }
</script>
```

**update** 메소드에 **@Watch** 데코레이터를 통해서 **parentMessage** 필드를 주시하고 있다. 상위 컴포넌트로부터 변경된 **parentMessage**을 받으면 **@Watch** 데코레이터는 이를 감지하고 메소드를 실행시킨다.

<br>

## 4. @Watch 더 알아보기

```Typescript
@Watch('string', {immediate: true, deep: true})
update(value:string, oldValue:string) {

}
```

- **immediate** : 속성의 현재 값으로 핸들러를 즉시 호출하겠다라는 의미이다.
- **deep** : 속성이 객체일 경우, 속성의 내부 값이 변경되는 것까지 감시하겠다는 의미이다.
- **value** : 감시하고 있는 값의 변경된 값이다.
- **oldValue** : 감시하고 있는 값의 예전 값이다.
