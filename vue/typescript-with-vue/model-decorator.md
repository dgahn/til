# @Model 사용하기

**@Model**은 프로퍼티 데코레이터이다. 컴포넌트를 양방향 바인딩할 때 사용한다.

```Typescript
@Model("change", { type: Boolean }) readonly checked!: boolean
```

첫번째 옵션으로는 해당 속성과 관련된 이벤트를 넣어준다. 그리고 두번째 옵션으로 상위 컴포넌트에서 전달해주는 데이터의 타입을 넣어준다.

<br>

## 1. Home.Vue 파일 작성하기


```html
<template>
    <div class="home">
        <MyCheckBox v-model="homeChecked" @checkChange="homeChange"></MyCheckBox>
        {{text}}
    </div>
</template>

<script lang="ts">
  import {Component, Provide, Vue} from "vue-property-decorator";
  import MyCheckBox from "@/components/MyCheckBox.vue";

  @Component({
    components: {
      MyCheckBox
    }
  })
  export default class Home extends Vue {
    homeChecked: boolean = false;
    text: string = "동의하지 않습니다.";

    homeChange(checked: boolean) {
      this.homeChecked = checked;
      this.text = checked ? '동의합니다.' : '동의하지 않습니다.';
    }

  }
</script>
```

<br>

## 2. MyCheckBox.Vue 작성하기

```html
<template>
    <input type="checkbox"
           :checked="checked"
           @change="checkChange">
    <!-- 이벤트가 발생했을 때 상위 컴포넌트에 데이터를 전달해줄 수 있는거 같음.   -->
</template>

<script lang=ts>
  import {Vue, Component, Model, Emit} from "vue-property-decorator";

  @Component
  export default class MyCheckBox extends Vue {
    @Model("homeChange", {type: Boolean}) readonly checked!: Boolean;

    @Emit()
    checkChange(event: Event) {
      return event.target.checked;
    }

  }
</script>

<style scoped>

</style>

```