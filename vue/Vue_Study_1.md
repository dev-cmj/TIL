# Vue.js


## Option API vs Composition API

### Option API

- Vue 2.x에서 사용되던 방식
- data, methods, computed, watch 등을 객체로 정의
- 컴포넌트가 커질수록 관리가 어려워짐

```javascript
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  }
}
```

### Composition API

- Vue 3.x에서 도입된 방식
- setup 함수 내에서 data, methods, computed, watch 등을 정의
- 컴포넌트의 기능별로 묶어서 관리 가능
- 코드의 재사용성이 높아짐 (setup 함수 내에서 훅과 같은 기능을 정의하여 다른 컴포넌트에서 사용 가능)
- 타입스크립트와 호환성이 더 좋고 명시적 타입 정의가 용이
- 
```javascript
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    const increment = () => {
      count.value++
    }

    return {
      count,
      increment
    }
  }
}
```

| Feature       | Option API            | Composition API      |
|---------------|-----------------------|----------------------|
| 가독성           | 초보자에게 친숙하고 쉽게 이해 가능   | 대규모 컴포넌트에서는 복잡할 수 있음 |
| 유연성           | 제한적, 기능별로 나누어짐        | 훨씬 더 유연하고 재사용 가능     |
| TypeScript 통합 | 제한적                   | 더 나은 TypeScript 지원   |
| 코드 재사용성       | mixin을 사용하나 충돌 가능성 있음 | 훅을 사용하여 쉽게 재사용 가능    |
| 코드 구조화        | 기능별로 나누어짐             | 관련 코드들을 그룹화 가능       |

