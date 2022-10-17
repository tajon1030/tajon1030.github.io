---
layout: post
title: 리액트로 TodoList 만들기2
tags: [react]
comments: true
---
## 리액트로 TodoList 만들기2
기존에 만들었던 일정관리애플리케이션의 성능을 최적화해보았다.

우선 리액트 컴포넌트의 렌더링은 기본적으로 빠르기 때문에 컴포넌트를 개발할 때 모든 컴포넌트에 일일히 React.memo를 작성하거나 큰 스트레스를 받을 필요는 없다.  
단, 리스트와 관련된 컴포넌트를 만들때 보여줄 항목이 100개 이상이고 업데이트가 자주 발생한다면 최적화가 필요하다.

### TodoList가 느려지는 원인
컴포넌트는 다음과 같은 상황에서 리렌더링이 발생한다.  
1. 자신이 전달받은 props가 변경될 때  
2. 자신의 state가 바뀔 때  
3. 부모 컴포넌트가 리렌더링될 때  
4. forceUpdate 함수가 실행될 때

기존 코드를 수정하여 할일목록이 2000개가 되는 TodoList를 만들기위해 코드를 다음과 같이 수정했다.  
~~~js
function createBulkTodos(){
  const array = [];
  for(let i=1; i<=2500; i++){
    array.push({
      id: i,
      title: `할일 ${i}`,
      checked: false
    });
  }
  return array;
}

function App() {
  const nextId = useRef(2501);
  const [todos,setTodos] = useState(createBulkTodos);
  ...
~~~  
이때 할일 1의 완료버튼을 눌렀을 경우에 App컴포넌트의 state가 변경되면서 App컴포넌트가 리렌더링된다.
부모컴포넌트가 리렌더링 되었으니 자식컴포넌트들도 리렌더링된다.

하지만 이는 비효율적인 상황으로 할일 2부터 할일 2500까지는 리렌더링을 할 필요가 없다는것을 모두 알수있을것이다.


### React.memo 를 사용한 컴포넌트 성능 최적화
컴포넌트의 리렌더링을 방지할 때에는 shouldComponentUpdate라는 라이프사이클을 사용하면되지만 함수형 컴포넌트에서는 사용할 수 없다.  
대신 React.memo 함수를 사용하여 컴포넌트의 props가 바뀌지 않았다면 리렌더링하지 않도록 설정하여 성능을 최적화해줄 수 있다.

React.memo는 컴포넌트를 만들고 나서 감싸주기만 하면 사용할 수 있다.  
~~~js
const TodoListItem = ({todo, onRemove, onCheck}) => {
  ...
};

export default React.memo(TodoListItem);
~~~  
이렇게 하면 TodoListItem컴포넌트는 todo, onRemove, onToggle이 바뀌지 않으면 리렌더링하지 않는다.


### onToggle, onRemove 함수가 바뀌지 않도록 하기
뿐만아니라 현재 프로젝트에서는 onRemove와 onToggle 함수는 todos배열상태가 업데이트 되는 과정에서 최신상태의 todos를 참조하기때문에 todos배열이 바뀔때마다 함수가 새롭게 만들어진다.  
이를 방지하는 방법에는 두가지방식이 있는데 하나는 useState의 함수형 업데이트를 사용하는 기능이고  
하나는 useReducer를 사용하는 방식이다.

#### useState의 함수형 업데이트
기존 setTodos 함수를 사용할 때에는 새로운 상태를 파라미터로 넣어줬다.  
이러한 방식 대신 상테 업데이트를 어떻게 할지 정의해주는 업데이트 함수를 넣을수도 있는데 이를 함수형 업데이트 라고 부른다.

~~~js  
// 예시
const [number,setNumber] = useState(0);
// prevNumbers는 현재 number 값을 가르킨다.
const onIncrease = useCallback(
  () => setNumber(prevNumber => prevNumber+1),
  []
);
~~~  
setNumber(number+1)을 하는것이 아니라, 업데이트함수를 넣어주면 useCallback을 사용할 때 두번째 파라미터로 넣는 배열에 number를 넣지 않아도 된다.

기존 코드를 함수형업데이트를 사용하여 변경하면 다음과 같다.  
setTodos를 사용할 때 그 안에 todos=>만 앞에 넣어주면 된다.  
~~~js  
  // const onInsert = useCallback(title =>{
  //   setTodos(todos.concat({id: nextId.current,
  //   title,
  //   checked: false}));
  //   nextId.current +=1;
  // },[todos]);
  const onInsert = useCallback(title =>{
    setTodos(todos=>todos.concat({id: nextId.current,
    title,
    checked: false}));
    nextId.current +=1;
  },[]);
  // const onRemove = useCallback(id=>{
  //   setTodos(todos.filter(todo=>todo.id!==id));
  // },[todos]);
  const onRemove = useCallback(id=>{
    setTodos(todos=>todos.filter(todo=>todo.id!==id));
  },[]);
  // const onCheck = useCallback(id=>{
  //   setTodos(todos.map(todo=> todo.id === id ? {...todo, checked: !todo.checked}: todo));
  // },[todos]);
  const onCheck = useCallback(id=>{
    setTodos(todos=>todos.map(todo=> todo.id === id ? {...todo, checked: !todo.checked}: todo));
  },[]);
~~~

#### useReducer 사용하기
useState의 함수형 업데이트를 사용하는 대신, useReducer를 사용해도 onToggle과 onRemove가 계속 새로워지는 문제를 해결할수있다.  
기존 코드를 많이 고쳐야 한다는 단점이 있지만, 상태를 업데이트하는 로직을 모아 컴포넌트 바깥에 둘 수 있다는 장점이 있다.  
~~~js  
function todoReducer(todos,action){
  switch(action.type){
    case 'INSERT':
      return todos.concat(action.todo);
    case 'REMOVE':
      return todos.filter(todo=>todo.id !== action.id);
    case 'TOGGLE':
      return todos.map(todo=>
        todo.id === action.id ? {...todo, checked: !todo.checked} : todo);
    default :
        return todos;
  }
}

function App() {
  const[todos, dispatch] = useReducer(todoReducer, undefined, createBulkTodos);
  const nextId = useRef(2501);
  
  const onInsert = useCallback(title =>{
    const todo = {id: nextId.current,
      title,
      checked: false};
    dispatch({type:'INSERT', todo});
    nextId.current +=1;
  }, []);

  const onRemove = useCallback(id=>{
    dispatch({type:'REMOVE', id});
  },[]);

  const onCheck = useCallback(id=>{
    dispatch({type: 'TOGGLE',id});
  },[]);
  ...
~~~  
원래 두번째 파라미터에 초기상태를 넣어주어야하는데 지금은 undefined를 두번째 파라미터에 넣고 세번째 파라미터에 초기상태를 만들어주는 createBulkTodos 함수를 넣어주었다. 이렇게 하면 컴포넌트가 맨 처음 렌더링 될 때에만 createBulkTodos함수가 호출된다.

### 불변성의 중요성
상태를 업데이트할 때 불변성을 지키는것은 매우 중요하다.  
이전에 작성한 onCheck 코드를 보면 기존 데이터를 수정할때 직접 수정하지않고 새로운 배열을 만든 다음 새로운 객체를 만들어 필요한 부분을 교체해 주는 방식으로 구현했다.  
이렇게 기존값을 직접 수정하지않으면서 새로운 값을 만들어내는것을 불변성을 지킨다라고하는데  
불변성이 지켜지지 않으면 객체 내부값이 새로워져도 바뀐것을 감지하지 못한다. 그렇게되면 React.memo에서 서로 비교하여 최적화하는것또한 불가능하다.

추가로 전개연산자(...문법)을 사용하여 객체나 배열 내부값을 복사할때에는 얕은 복사를 하게되어 내부값이 완전히 새로 복사되는것이 아니라 가장 바깥쪽에 있는 값만 복사된다. 따라서 내부값이 객체나 배열이라면 내부값또한 따로 복사해주어야한다.  
그러나 배열 혹은 객체의 구조가 복잡해질 경우 불변성을 유지하면서 업데이트하는것도 까다로워지기때문에 immer라는 라이브러리의 도움을 받아 작업을 한다. (다음 포스팅예정)


### TodoList 컴포넌트 최적화하기
리스트에 관련된 컴포넌트를 최적화할 때는 리스트 내부에서 사용하는 컴포넌트도 최적화해야하고, 리스트로 사용되는 컴포넌트 자체도 최적화해주는것이 좋다.  
~~~js
//TodoList.js
...
export default React.memo(TodoList);
~~~  
위 코드는 현재 프로젝트에 전혀 영향을 주지않는데, TodoList컴포넌트의 부모컴포넌트인 App컴포넌트가 리렌더링되는 유일한 이유는 todos배열이 업데이트 될때이기 때문이다. 즉, 현재로서는 불필요한 리렌더링이 발생하지않는다.  
그러나 App컴포넌트에 다른 state가 추가되어 해당값들이 업데이트 될때에는 불필요한 리렌더링을 할 수 있기때문에 미리 최적화 해놓는다.

결국, 리스트 관련 컴포넌트를 작성할 때는 리스트 아이템과 리스트, 이 두가지 컴포넌트를 최적화해주어야하지만 내부데이터가 10개를 넘지않거나 업데이트가 자주 발생하지 않는다면 반드시 해줄 필요는 없다.


### react-virtualized
react-virtualized를 사용하면 리스트 컴포넌트에서 스크롤되기 전에 보이지 않는 컴포넌트는 렌더링하지 않고 크기만 차지하게끔 할수있다.  
그리고 만약 스크롤되면 해당 스크롤 위치에서 보여주어야 할 컴포넌트를 자연스럽게 렌더링시킨다.  
~~~  
yarn add react-virtualized
~~~  

## 참고
[리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791160508796)