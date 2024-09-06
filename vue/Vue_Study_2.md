# Setup


### setup 함수

vue3에서 도입된 'setup' 함수는 Composition API의 핵심이며, 이 함수는 컴포넌트가 생성될 때 한번 호출되며,
여기서 작성된 로직은 컴포넌트의 반응형 상태를 설정하고, 컴포넌트의 생명주기 동안 유지된다.

#### 특징
- setup 함수는 Vue 컴포넌트의 초기화 단계에서 가장 먼저 호출된다
- 함수의 반환 값은 템플릿에서 사용될 수 있고, Vue는 자동으로 이를 템플릿에 반영한다.
- setup 함수는 props를 첫번째 인자로 받으머, 컴포넌트의 상태와 메서드를 정의한다.
- ref, reactive, computed, watch 등의 Composition API 함수를 사용하여 반응형 상태를 정의한다.

#### 사용법
```javascript
import { ref } from 'vue'

export default {
    setup(props) {
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