# @Emit으로 이벤트 전달하기

뷰에서는 상위 컴포넌트와 하위 컴포넌트간의 데이터 이동이 단방향으로 이루어진다. 그래서 상위 컴포넌트에서 하위 컴포넌트로 데이터를 넘겨주기만 한다. 대신 뷰는 하위 컴포넌트에서 생긴 이벤트에 대해서 상위 컴포넌트로 **@Emit**을 통해서 알려줄 수 있다.

<br>

## 1. 상위 컴포넌트 코드 수정하기

상위 컴포넌트인 **Home.vue** 파일의 코드를 아래와 같이 수정한다.

```html
<template>
    <div class="home">
        <img alt="Vue logo" src="../assets/logo.png">
        <Room @counter="counter"></Room>
        <p>
            상위 컴포넌트에서 숫자를 보여줍니다. : {{count}}
        </p>
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
    count: number = 0;

    counter () {
      this.count++;
    };
  }
</script>

```

먼저 하위 컴포넌트 태그인 **Room** 태그를 보자

- **@counter** : 하위 컴포넌트에서 counter() 메소드가 호출 됬을 때를 의미한다. 즉, counter 이벤트가 발생했을 때라고 생각하면 된다.
- **"counter"** : 상위 컴포넌트의 counter() 메소드를 호출한다.

위 코드에서는 하위 컴포넌트에서 **@counter** 라는 이벤트가 발생했을 때, 상위 컴포넌트의 counter() 메소드가 호출되도록 작성하였다. 다시 말하면, 이벤트를 전달 받은 것이다.

<br>

## 2. 하위 컴포넌트 코드 수정하기

하위 컴포넌트인 **Room.vue**의 코드를 아래와 같이 수정한다.

```html
<template>
    <div>
        <button @click="counter">하위 컴포넌트에서 카운트를 증가시팁니다.</button>
    </div>
</template>

<script lang=ts>
  import {Component, Prop, Vue, Watch, Emit} from "vue-property-decorator";

  @Component
  export default class Room extends Vue {

    @Emit()
    counter(): void {
      console.log("counter called!");
    };

  }
</script>
```

버튼을 클릭할 때마다 하위 컴포넌트의 **counter()** 메소드를 호출한다. 이 때, **@Emit()**을 통해서 상위 컴포넌트로 이벤트가 전달된다. 

그러면 다시 상위 컴포넌트의 **@counter** 이벤트를 통해서 상위 컴포넌트의 **counter()** 메소드를 통해서 실제 **counter** 변수의 값이 증가한다.

<br>

## 3. Prop()와 혼용해서 사용해보기

코드를 아래와 같이 수정하면 **@Prop**와 **@Emit**의 작용에 대해서 더 이해할 수 있을거 같다.

- **Home.vue**

```html
<template>
    <div class="home">
        <img alt="Vue logo" src="../assets/logo.png">
        <Room @counter="counter"
              :count="count"></Room>
        <p>
            상위 컴포넌트에서 숫자를 보여줍니다. : {{count}}
        </p>
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
    count: number = 0;

    counter () {
      this.count++;
    };
  }
</script>
```

<br>

- **Room.vue**

```html
<template>
    <div>
        <button @click="counter">하위 컴포넌트에서 카운트를 증가시팁니다.</button>
        <p>하위 컴포넌트에서 숫자를 보여줍니다. : {{count}} </p>
    </div>
</template>

<script lang=ts>
  import {Component, Prop, Vue, Watch, Emit} from "vue-property-decorator";

  @Component
  export default class Room extends Vue {
    @Prop() count?:number;

    @Emit()
    counter(): void {
      console.log("counter called!");
    };

  }
</script>
```

<br>

## 5. @Watch와 혼용해서 사용하기

**Room.vue**의 코드를 아래와 같이 수정해보자.

```html
<template>
    <div>
        <button @click="counter">하위 컴포넌트에서 카운트를 증가시팁니다.</button>
        <p>하위 컴포넌트에서 숫자의 합을 보여줍니다. : {{sum}} </p>
    </div>
</template>

<script lang=ts>
  import {Component, Prop, Vue, Watch, Emit} from "vue-property-decorator";

  @Component
  export default class Room extends Vue {
    @Prop() count?:number;
    sum: number = 0;

    @Emit()
    counter(): void {
      console.log("counter called!");
    };

    @Watch("count")
    addCount() {
      if(this.count !== undefined) {
        this.sum += this.count;
      }
    }

  }
</script>
```