### Redux 알아보기

[참고 문헌: 벨로퍼와 함께하는 모던 리액트 6장. 리덕스](https://react.vlpt.us/redux/)
### 목표
* 글로벌 상태관리를 위해 Context API만 종종 사용했었는데, 이와 유사하게 범용적으로 많이 쓰이는 Redux는 무엇인지, 어떻게 활용하는 것인지 알아본다.
  * 더불어, context API 활용법도 복습한다.

## Redux란?
* 리액트 생태계에서 가장 많이 사용되는 상태 관리 라이브러
* 상태 관리 로직을 다른 파일로 분리시켜 효율적으로 관리할 수 있다.
* Context API가 개선되기 이전부터 활용되어 온 라이브러리.

### Redux 를 사용하는 것과 Context API의 차이
* 미들웨어: Context API와 유사하게 Reducer를 사용하나, reducer 에서 처리하기 이전에 필요한 작업 수행 가능
  * 비동기 작업 처리에 자주 쓰인다.
* 유용한 함수와 Hooks
  * `connect` 를 통해, 리덕스 상태 혹은 함수를 Props로 쉽게 불러올 수 있다.
  * 또한, redux 라이브러리 내 내장 Hooks 를 사용하여, 상태 조회, 디스패치 등이 가능하다.
    * 이 함수는 최적화가 되어있기 때문에, 실제 상태가 변경될 때만 컴포넌트가 리랜더된다.
    * 반면, Context 는 context 가 지니는 상태가 변경되면 Provider 내부 컴포넌트가 전부 변경된다. 
* 하나의 커다란 상태
  * context API를 사용하여 관리할 때는, 기능별 Context를 생성한다.
  * 반면, redux는 모든 글로벌 상태 하나의 큰 상태 객체 내에서 관리하는 것이 필수.

### Redux 언제 사용해야 할까?
  * 프로젝트 규모가 큰가? Yes. If no, context API
  * 비동기 작업이 많은가? Yes. If no, context API
  * 리덕스를 배워보니 사용하는게 편한가? Yes
    * 배워보고 불편하다면, context API 를 사용해도 무관. (MobX라는 것도 있나보다)

## 1. Redux 에서 사용되는 키워드 숙지하기

### 액션 (Action)
* 상태에 변경이 필요할 때 우리는 액션을 발생시킨다. 액션은 하나의 객체로 표현된다.
* ```javascript
  {
    type: "ADD_TODO",
    data: {
        id: 0,
        text: "리덕스 배우기"
    }
    text: "hello",
    // ...
  }
  ```
* `type` 필드는 필수적으로 필요, 나머지는 개발자의 필요에 따라 삽입하면 된다.
  * 아마 type을 기준으로 실행해야 할 액션을 분류하는 것일까..?
  
### 액션 생성함수 (Action Creator)
* 액션을 만드는 함수, 파라미터를 불러와서 액션 객체 형태로 생성한다.
* ```javascript
  export function addTodo(data) {
    return {
      type: "ADD_TODO",
      data
    };
  }
  // 화살표 함수로도 만들 수 있습니다.
  export const changeInput = text => ({
   type: "CHANGE_INPUT",
   text
  });
  ```
* 액션 생성함수를 만드는 이유는, 컴포넌트에서 쉽게 액션을 발생시키기 위함
  * 일일히 액션 객체를 그때그때 만들어서 해도 된다.
    * 단 동일한 코드의 반복이 그럼 많아지겠지..??

### 리듀서 (Reducer)
* 변화를 일으키는 함수, `state`, `action` 두 가지 파라미터를 이용
  * 현재의 상태와 전달받은 액션을 기반으로 새로운 상태를 반환
* ```javascript
  function counter(state, action) {
    switch (action.type) {
      case 'INCREASE':
        return state + 1;
      case 'DECREASE':
        return state - 1;
      default:
        return state;
    }
  }
  ```
  * action type을 기준으로, 변경할 state return
  * default 에서는 `useReducer()` 와 다르게, 기존 state를 그대로 return

### 스토어 (Store)
* 리덕스 내에서 한 애플리케이션 당 하나의 스토어를 만든다.
* 스토어 내에는 현재의 앱 상태, 리듀서와 일부 추가 함수 포함

### 디스패치 (Dispatch)
* 스토어 내장함수의 일종, 액션을 발생시키는 함수..?
  * `dispatch(action)` 의 꼴 => 스토어는 리듀서 함수를 실행하고, 해당 액션을 처리하는 로직 확인

### 구독 (Subscribe)
* 스토어 내장함수의 일종. 함수를 파라미터로 받아서, 액션이 디스패치 되었을 때마다 전달해준 함수 호출
* React 에서는 react-redux 에서 제공하는 connect 와 useSelector Hook을 사용하여, 스토어 내 상태를 구독.
