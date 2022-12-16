### 221207
#word-break, overflow-wrap 내용 정리

### 문제 상황
* 고객 작성 리뷰 노출 시, 고객이 띄어쓰기 없이 "111111111111111111111111...."과 같은 아주 긴 한 단어를 썼을 때 문제 발생
  1. 텍스트가 칸을 초과해서 노출됨
  2. 칸이 텍스트 길이만큼 확장됨

### 문제 원인
* 텍스트가 칸을 초과해서 노출되는 이슈
  * 필요한 상황에서 줄바꿈이 발생하지 않음 => 기존에는 줄바꿈이 단어 즉, 띄어쓰기 혹은 line break 기준이었는데, 문제 상황처럼 하나의 긴 단어일 때는 줄바꿈이 생성되지 않음
  * word-wrap 으로만 대응해왔음
* 칸이 텍스트 길이만큼 확장되는 이슈
  * 칸의 max-width가 고정되어 있지 않았음 => 줄이 바뀌지 않을 때, 필요한 만큼 줄이 보장되는 것이고, 그 줄의 크기만큼 컨테이너의 크기가 확장되는 현상 발생

### 문제 해결 방법
* word-wrap 으로만 대응되어 있던 코드를 => word-break: break-word으로 변경
  * 줄바꿈을 직접적으로 정의

### word-break 이란?
* [공식문서](https://developer.mozilla.org/en-US/docs/Web/CSS/word-break)
* 줄바꿈을 생성하는 기준을 정의. 크게 4가지 구분
  * `word-break: normal;`
    * 기본 line-break 을 적용: 한 단어를 보존하되 줄넘침이 발생하면 다음 줄로 넘긴다.
    * 단, 한 단어 자체가 줄을 넘어가면, 줄 넘침이 발생한대로 놔둔다.
  * `word-break: break-all;`
    * 줄넘침 (overflow)를 방지하기 위해, 줄이 넘치는 어떤 글자 사이에든 word-break 이 발생한다.
  * `word-break: keep-all;`
    * normal과 동일하나 CJK (Chinese, Japanese, Korean)에서는 적용되지 않는다.
  * `word-break: break-word;`
    * normal 과 유사하나, 한 단어가 한 줄을 넘어가면 그때 단어를 끊어준다.
    
### overflow-wrap (과거 word-wrap) 이란?
* [공식문서](https://developer.mozilla.org/en-US/docs/Web/CSS/overflow-wrap)
* In contrast to word-break, overflow-wrap will only create a break **if an entire word cannot be placed on its own line** without overflowing.
  * 즉 한 단어가 한 줄을 넘는 길이일 때만 line-break을 생성
* 세 가지 구분
  * `overflow-wrap: normal;`
    * 단어 사이 공백, 정의된 line-break에서만 line-break을 생성
  * `overflow-wrap: anywhere;`
    * 줄 넘침을 방지하기 위해 어느 곳에서나 줄바꿈이 생성될 수 있다. 이로 인해 생성된 soft-wrap들은 컨테이너의 최소 width를 결정하는데 영향을 준다.
      * 즉, min-width 가 될 수 있는 만큼이 반영되어서 컨테이너 너비가 결정된다.
  * `overflow-wrap: break-word;`
    * anywhere 와 동일하나, soft-wrap이 컨테이너의 크기를 결정하는데 계산되지는 앟는다. 즉, 이미 정의된 line-break에 의해서만 한 줄의 너비가 결정된다.
  * 이해가 정확한지는 모르겠으나, 아마 컨테이너의 min-width, max-width 등이 미리 정의되어 있으면 큰 차이가 없을 것으로 예상.

