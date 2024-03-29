### 220113 TIL React Component Design Pattern
- React Component Design Pattern  (with ChatGPT)
    - Container Pattern
        - Container component 와 Presentational Component 로 컴포넌트 구분
            - 로직, state을 담는 컴포넌트와 레이아웃, 뷰를 담는 컴포넌트

            ```jsx
            // Presentational component
            const UserList = ({users}) => (
              <ul>
                {users.map(user => (
                  <li key={user.id}>{user.name}</li>
                ))}
              </ul>
            );
            
            // Container component
            class UserListContainer extends React.Component {
              state = {
                users: []
              }
            
              componentDidMount() {
                fetch('/api/users')
                  .then(res => res.json())
                  .then(users => this.setState({ users }));
              }
            
              render() {
                return <UserList users={this.state.users} />;
              }
            }
            ```

            - **`UserList`** component 는 뷰를 담당 ⇒ 리스트를 렌더
            - The **`UserListContainer`** component 는 API 로부터 유저 정보를 받아서 state를 관리
            - The **`UserList`** component 는 받은 데이터의 뷰를 담당하는 역할만을 한다.
    - HoC Pattern
        - 컴포넌트 로직을 재사용하기 위한 방법. 상위 컴포넌트에서는 하위 컴포넌트에 대해 새로운 Props나 함수를 적용할 수 있다.

        ```jsx
        const withLoadingIndicator = (WrappedComponent) => {
          return class extends React.Component {
            render() {
              const { isLoading, ...otherProps } = this.props;
              return isLoading ? <LoadingIndicator /> : <WrappedComponent {...otherProps} />;
            }
          }
        }
        
        const UserList = ({users}) => (
          <ul>
            {users.map(user => (
              <li key={user.id}>{user.name}</li>
            ))}
          </ul>
        );
        
        const UserListWithLoadingIndicator = withLoadingIndicator(UserList);
        ```

        - **`withLoadingIndicator`** is a higher-order component (HOC) 로, 컴포넌트를 받아서 새로운 컴포넌트를 호출한다.
            - **`isLoading`** prop 이**`true`** 일때, **`LoadingIndicator`** component 렌더
            - **`isLoading`** is **`false`** 일때.  **`WrappedComponent`** component 렌더 ****
                - 하위 컴포넌트를 함수에 감싸서 만들어진 HOC 컴포넌트 **`UserListWithLoadingIndicator`**
        - 이 예시에서 HOC 패턴은 기존 코드를 수정하지 않고 새로운 함수 (기능)을 추가하기 위해 쓰임
