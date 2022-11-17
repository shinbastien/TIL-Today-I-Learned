### 221117 ~ 
# React Rendering 에 대한 이해
- [(번역) 블로그 답변: React 렌더링 동작에 대한 (거의) 완벽한 가이드](https://velog.io/@superlipbalm/blogged-answers-a-mostly-complete-guide-to-react-rendering-behavior?utm_source=substack&utm_medium=email)
###렌더링이란? 
- props 와 state 를 기반으로 리액트가 컴포넌트에게 UI 명세? 를 요청하는 프로세스
### 렌더링 프로세스
  - jsx 구문의 함수 실행 ⇒ 컴파일 ⇒ React.createElement 로 변환 ⇒ html 을 wrapping 하는 react 요소
  - 렌더 출력을 수집하고 기존 트리와 가상 트리를 비교하여 모든 변경사항을 수집 ⇒ 재조정 (reconciliation)

    [Reconciliation - React](https://reactjs.org/docs/reconciliation.html)

    - 리액트에선 O(n) 알고리즘으로 이 비교 분석을 빠르게 끝낼 수 있다고 함
        - 타입이 다를 땐 기존 트리를 지우고 새 트리를 생성
        - 타입이 같은 DOM 요소에 대해선 props 값만 변경
        - Children 항목들에 대해 위에서 부터 하나씩 살피지만, 단순히 위치가 다르다고 다 없앴다가 지우는 것이 아님
            - 각 아이템의 key 값을 확인하고 key 값이 같으면 굳이 지웠다가 다시 만들지 않음
### 렌더 및 커밋 단계
- 렌더 단계 ⇒ 컴포넌트 렌더링 후 변경사항 계산
- 커밋 단계 ⇒ 변경사항 DOM에 반영
    - 요청된 DOM 요소의 참조를 업데이트
    - useEffect 실행 (passive Effects) 라고도 지칭
- **렌더 ≠ DOM 업데이트:** 가시적 변경 없이 컴포넌트가 렌더될 수 있다.
    - 컴포넌트가 동일한 렌더 출력이 발생할 수 있음
    - 동시 렌더링 시 ⇒ 업데이트로 인해 기존 작업이 무효화될 수 있음
- mount ⇒ render ⇒ update dom ⇒ layoutEffect ⇒ effect
### 렌더링 방식은?
- 렌더링을 호출하는 방식
  - 함수형: useState setter ⇒ setState, useReducer dispatch
    - 클래스형: this.setState, this.forceUpdate
    - 일반적인 렌더링 동작
      - 상위 컴포넌트가 렌더링되면, 내부 모든 하위 컴포넌트를 돌며 렌더링한다.
          - 부모가 렌더되면 모든 자식은 무조건 렌더된다. (props 변경과 무관)
          - 렌더링 규칙 => 순수성
              - 기존 변수 및 객체 변경 X
              - 임의 값 생성 X (`Math.random`, `Date.now` 등)
### Fiber란?
