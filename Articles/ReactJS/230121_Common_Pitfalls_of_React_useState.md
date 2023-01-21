## 230121 useState를 사용하면서 종종 발생할 수 있는 흔한 실수들

[블로그 링크](https://ktseo41.github.io/blog/log/avoid-these-common-pitfalls-of-react-usestate.html?utm_source=substack&utm_medium=email)

* 블로그에는 참고 예제와 함께 풀어볼 수 있는 자세한 설명이 있다.

### 핵심 요약
* 결국은, 담아야 할 정보가 무엇인지를 확인하고, 변경되어야 하는 정보만 state에 담자라는 것이다.
  * 상호간 의존성이 있는 정보는 의존성이 없는 정보들로 분해해서 state로 저장
* 만약 의존성이 있는 정보를 useEffect를 통해서 표출한다면, 의존성이 발생하는 원인을 파악하자.
* 서로 의존하는 여러 상태 변수는 useReducer를 사용해서, 미리 정의해주고 사용하자.
  * reducer에는 switch case를 이용해서 해당 경우에 일어나야 할 행동을 미리 정의
* 깊게 중첩된 상태
  * 예) 댓글 안에 대댓글 같은 상황
    * 대댓글 자체를 바깥으로 빼고, id로서 대댓글에 접근할 수 있도록 한다.

### 결론
 * state는 최대한 단순화해서, 최소 단위를 기준으로 만들자.
   * 논리 로직의 분리가 필요하다. 기본이 되는 단위를 기준으로 state를 정리
   * 복잡한 state는 (특히 객체로 이뤄진), reducer를 활용해서 발생 가능한 경우를 직관적으로 명시해주자.  
 * useEffect 또한 하나의 side-effect 이자, unreadable code. 최대한 줄여보자.

### 의존성이 있는 useEffect
* 예시)
  ```javascript
  import { useEffect, useState } from "react";

  function ProductView({ name, details }) {
  const [isDetailsVisible, setIsDetailsVisible] = useState(false);
    
  useEffect(() => {
  trackEvent({ event: "Toggle Product Details", value: isDetailsVisible });
  }, [isDetailsVisible]);
    
  const toggleDetails = () => {
  setIsDetailsVisible(!isDetailsVisible);
  };
    
  return (
  <div>
  {name}
  <button onClick={toggleDetails}>Show details</button>
  {isDetailsVisible && <ProductDetails {...details} />}
  </div>
  );
  }
  ```
  위 코드에서 `trackEvent`는 isDetailsVisible에 의존성을 갖는다.
    * 하지만, 보다 정확한 원인은 onClick이 발생할 때 trackEvent를 해줘야하는 것이기 때문에, toggleDetails 안에 넣어주는 것이 마땅하다.
  ```javascript
  function ProductView({ name, details }) {
  const [isDetailsVisible, setIsDetailsVisible] = useState(false);

  const toggleDetails = () => {
  setIsDetailsVisible(!isDetailsVisible);
  trackEvent({ event: "Toggle Product Details", value: !isDetailsVisible });
  };

  return (
    <div>
      {name}
      <button onClick={toggleDetails}>Show details</button>
      {isDetailsVisible && <ProductDetails {...details} />}
    </div>
  );
  }
  ```
  
### useReducer 를 이용하여 실제 state 정리
* 예시)

  * 해결방법)
  * ```javascript
    import { useReducer } from "react";
    function reducer(state, action) {
      switch (action.type) {
        case "remove":
          return {
            rows: state.rows.filter(({ id }) => action.row.id !== id),
            removedRows: state.removedRows.concat(action.row),
          };
        case "undo":
          const lastRemovedRow = state.removedRows[state.removedRows.length - 1];
          const restoredRows = state.rows
            .concat(lastRemovedRow)
            .sort((a, b) => a.id - b.id);
          return {
            rows: restoredRows,
            removedRows: state.removedRows.slice(0, -1),
          };
        default:
         throw new Error();
      }
    }
    export function ListWithUndo() {
      const [state, dispatch] = useReducer(reducer, initialState);
      return (
        <>
         <button onClick={() => dispatch({ type: "undo" })}
         disabled={state.removedRows.length === 0}>
         Undo Last Action
         </button>
         <ul>
           {state.rows.map((row) => (
             <li key={row.id}>
               <button onClick={() => dispatch({ type: "remove", row })}>
                 Delete
               </button>
               {row.name}
             </li>
           ))}
         </ul>
       </>
      );
    }
    ```
