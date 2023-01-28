### 230116_How_To_Check_Enum_Union_Types

* 두 가지 type 에 대해서 하나의 컴포넌트 혹은 함수 내에서 처리를 하고 싶을 때 어떻게 해야할까?
* 예시) 아래와 같이 정의 되어있을 때,
* ```javascript
  export declare enum Type1 {
    A1 = "APPLE",
    B1 = "BANANA",
    C1 = "CUCUMBER",
    D1 = "DINOSAUR",
    E1 = "ELEPHANT",
    F1 = "FLOWER",
  }
  export declare enum Type2 {
    A2 = "ANT",
    B2 = "BROWNIE",
    C2 = "CAT",
    D2 = "DONKEY",
    E2 = "EGG",
    F2 = "FLOWER",
  }
  
  const status: Type1 | Type2;
  ```  
  * 함수 안에 들어온 `status`의 값이 `Type1` 인지 `Type2`인지에 따라 호출해야 하는 함수, 컴포넌트가 다르다면?
  * ```javascript
    if (condition) {
        //status => Type1
        return functionType1(status);
    }
    else return functionType2(status);
    ```
  * 이때, condition 에는 무엇이 들어가야할까?
  
**시도 1) `typeOf(status)` 를 한다면? A: string이 나온다!**
 * `typeOf` 는 enum 안에 있는 값을 기준으로 하기 때문에 enum의 value 인 string 이 반환된다.  

**시도 2) `status in TypeA` 를 한다면? A: undefined가 나온다!**
 * `in` 연산자는 key를 기준으로 찾는데, `enum` 자체는 Object 같이 생겼지만, 정확하게 object은 아니길래 in 이 작동하지 않는다..?
   * ChatGPT 상의 이유는 이것인데 정확하게 이해는 가지 않는다... 
 * 혹은, status 자체가 `TypeA | TypeB` 인거라 적용이 안되는 것인가?

**시도 3) `Object.keys(TypeA).includes(status)` 를 한다면? A: TypeA, TypeB 양쪽에서 false를 반환한다!**
 * keys는 A2, B2, C2, 이런 값인데 status sms "ANT" 이거나, "APPLE" 이기 때문에 양쪽에서 false 반환 

**시도 4) `Object.values(TypeA).includes(status)` 를 한다면? A: 원하는대로 나오는데, TypeScript 에서 status의 type을 알 수 없다고 한다!**
 * values 에는 적합하다. 근데, 여기서는 status가 union이기 때문에 정확한 type matching이 안되기 때문에 TSError 발생 

**시도 5) `Object.values(TypeA).includes(status as TypeA)` 를 한다면? A: 성공!**
 * Type Assertion을 통해 일단 status를 TypeA로 선언해서 TS가 확인할 수 있게 하고, 해당 value는 "ANT", "BROWNIE" 일 것이기 때문에 알맞게 false 가 발생한다.  

**주의사항**
 * 주의사항은, 위 예시처럼 "FLOWER"의 경우는 모든 타입에 해당되기 때문에, 양쪽에서 모두 true 가 나온다. 이 경우가 edge case 가 될 수 있다.
 * 그래서 겹치지 않을 때sms 다음과 같이 Type 구분할 수 있고, 아니라면 다른 방법을 강구해야 한다. 

