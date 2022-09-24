---
layout: post
title: 리액트로 TodoList 만들기
sitemap: false
hide_last_modified: true
---
## 리액트로 TodoList 만들기

![800x400](../../assets/img/blog/todo-react.png)

todoList를 만들어보았다.

스타일 적용은 거의 하지 않았으며 모든 코드는 함수형 컴포넌트 방식으로 사용하였다.

TodoList의 모습을 상상해본뒤 구획별로 나눠본다면 다음과 같은 부분들을 떠올릴수있을것이다.  
1. 모든것을 취합하는 가장 최상단이 되는 부분  
2. 해야할 일을 입력하는 부분  
3. 해야할 일 목록들을 전체적으로 보여주는 부분  
4. 해야할 일 목록의 객체 각각의 부분(여기에서 해야할일을 삭제하거나 체크할수있을것이다.)

따라서 소스는 네부분으로 나누어 작성하였다.  
1. App.js  
2. TodoInsert.js  
3. TodoList.js  
4. TodoListItem.js


### App.js
App.js는 가장 최상단에 있는 리액트 컴포넌트이고  
이곳에서 TodoList의 입력을 담당하는 TodoInsert컴포넌트와  
TodoList의 목록을 보여주는 TodoList컴포넌트를 호출하고있다.

~~~js
import './App.css';
import TodoInsert from './TodoInsert';
import TodoList from './TodoList';
import React, { useState , useRef, useCallback} from 'react';
function App() {

  // id는 렌더링 되는 정보가 아니고 새로운 항목을 만들때 참조되는 값일뿐->useRef
  const nextId = useRef(1);
  
  const [todos,setTodos] = useState([]);
  
  // props를 전달해야할 함수를 만들때에는 useCallback을 사용하여 함수 감싸기를 습관화한다.
  const onInsert = useCallback(title =>{
    setTodos(todos.concat({id: nextId.current,
    title,
    checked: false}));
    nextId.current +=1;
  },[todos]);

  const onRemove = useCallback(id=>{
    setTodos(todos.filter(todo=>todo.id!==id));
  },[todos]);

  const onCheck = useCallback(id=>{
    setTodos(todos.map(todo=> todo.id === id ? {...todo, checked: !todo.checked}: todo));
  },[todos]);

  return (
    <div className="App">
      <TodoInsert onInsert={onInsert}></TodoInsert>
      <br></br>
      <TodoList todos={todos} onRemove={onRemove} onCheck={onCheck}></TodoList>
    </div>
  );
}

export default App;
~~~

### TodoInsert.js
onSubmit의 경우 form을 사용하지 않고 그냥 button onClick으로도 작성할수있다.  
나도 처음에는 이렇게 생각해서 구현했는데, 책을 보니 엔터를 눌렀을때에도 실행이 될 수 있도록 onSubmit으로 처리하는것이 좋다고 하여 수정하였다.  
~~~js
import React, { useState, useCallback } from "react";

const TodoInsert=({onInsert}) => {
    const [job,setJob] = useState('');
    // 리렌더링 될 때마다 함수를 새로 만드는 것이 아니라, 한번 함수를 만들고 재사용->useCallback
    const addJob = useCallback(e =>{
        setJob(e.target.value);
    },[]);

    // enter로도 실행되도록 onSubmit으로 처리하였음
    const onSubmit = useCallback(e=>{
        onInsert(job);
        setJob(''); // 초기화
        //submit의 브라우저 새로고침 발생 방지
        e.preventDefault();
    },[onInsert,job]);

    return (
        <>
            <p>할일 관리</p>
            <form onSubmit={onSubmit}>
            <input value={job} onChange={addJob}></input>
            <button type="submit">추가</button>
            </form>
        </>
    );
}
export default TodoInsert;
~~~

### TodoList.js
~~~js
import React from "react";
import TodoListItem from "./TodoListItem";

const TodoList = ({todos, onRemove, onCheck}) => {
    return (
        <>
        {todos.map(todo=>
        <TodoListItem todo={todo} key={todo.id} onRemove={onRemove} onCheck={onCheck}>
        </TodoListItem>)}
        </>
    );
}

export default TodoList;
~~~

### TodoListItem.js
checked 상태에 따라 버튼의 내용이 완료와 취소로 번갈아 보이도록 구현하였다.
버튼이 아니라 체크박스를 이용할수도 있을것이고 다른 다양한 방식을 적용해볼수도 있을것이다.

~~~js
import './TodoListItem.scss';
import React from "react";
import cn from 'classnames';

const TodoListItem = ({todo, onRemove, onCheck}) => {
    const {id,title,checked} = todo;
    return(
        <>
        <div className={cn('title',{checked})}>{title}</div>
        <button onClick={()=>onCheck(id)}>{!checked ? '완료' : '취소'}</button>
        <button onClick={()=>onRemove(id)}>삭제</button>
        </>
    );
}
export default TodoListItem;
~~~


#### TodoListItem.scss
~~~scss
.title {
  &.checked{
    text-decoration: line-through;
  }
}
~~~


## 참고
[리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791160508796)