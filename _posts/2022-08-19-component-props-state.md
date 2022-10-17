---
layout: post
title: 컴포넌트, props and state
tags: [react]
comments: true
---

## 컴포넌트, props and state

## 컴포넌트
컴포넌트는 독립적이고 재사용이 가능한 UI를 만들수 있는 단위이다.

### 함수형 컴포넌트
~~~js
function App(){
    const appName = '리액트';
    return <div>{appName}</div>;
}

export default App;

// 화살표 함수로도 표현이 가능하다.
const MyComponent = ()=>{
    return <div>나의 아름다운 컴포넌트</div>;
}
export default MyComponent;
~~~

함수형 컴포넌트는 state와 라이프사이클 api사용이 불가능 하다는 단점이 있었으나 Hooks라는 기능이 도입된 후 해결되었다.  
이후로는 리액트 공식 매뉴얼에서 컴포넌트를 새로 작성할 때 함수형 컴포넌트와 Hooks를 사용하도록 권장하고 있다.

함수형 컴포넌트는 클래스형 컴포넌트에 비해 다음의 장점이 있다.  
- 선언하기 편하다.  
- 메모리 자원의 사용이 적다.  
- 빌드 배포시 파일 크기가 더 작다.  

### 클래스형 컴포넌트
~~~js
import {Component} from 'react';

class App extends Component {
    render(){
        const appName = '리액트';
        return <div>{appName}</div>;
}

export default App;
~~~
클래스형 컴포넌트에서는 render함수가 꼭 있어야하고, 그 안에서 보여주어야할 JSX를 반환해야한다.  
함수형 컴포넌트와의 차이점은 state기능 및 라이프사이클 기능을 사용할수 있다는 것과  
임의메서드를 정의할 수 있다는것이다.


## Props
- properties를 줄인 표현  
- 컴포넌트끼리 값을 전달하는 수단  
- 해당 컴포넌트를 불러와 사용하는 부모컴포넌트에서 props값을 설정할수 있다.  
따라서 컴포넌트 자신은 해당 props를 읽기전용으로만 사용할 수 있다.

### Props의 사용법
1. props 값은 컴포넌트함수의 파라미터로 받아와서 사용할 수 있다.
~~~js
const MyComponent = props =>{
    return <div>나는야 {props.name}</div>;
}
export default MyComponent;
~~~

2\. 컴포넌트를 사용할 때 props 값을 지정한다.
~~~js
import MyComponent from "./MyComponent";

const App = ()=>{
  return <MyComponent name = 'React'/>;
}
export default App;
~~~

3\. defaultProps를 사용하여 기본값 설정도 가능하다.
~~~js
const MyComponent = props =>{
    return <div>나는야 {props.name}</div>;
}
MyComponent.defaultProps = {
    name : '기본'
};
export default MyComponent;
~~~

4\. `children`은 컴포넌트 태그사이의 내용을 보여준다.
~~~js
import MyComponent from "./MyComponent";

const App = ()=>{
  return <MyComponent>리액트</MyComponent>;
}
export default App;


const MyComponent = props =>{
    return <div>children값: {props.children}</div>;
}
export default MyComponent;
~~~

5\. 구조분해할당 이용하여 props 내부값을 추출하는 방식을 많이 사용한다.
~~~js
// const MyComponent = props =>{
//     const {name,children} = props;
// 하단의 코드와 상단의 코드는 동일한 내용임
const MyComponent = ({name, children}) =>{
    return <div>children값: {children}</div>;
}
export default MyComponent;
~~~

6\. propTypes를 통해 props 검증이 가능하다.  
필수 props를 지정하거나 type을 지정할 때 사용가능하며,  
코드 상단에 import 구문을 사용하여 불러와야한다.
~~~js
import PropTypes from 'prop-types'

const MyComponent = ({name, children}) =>{
...
};

MyComponent.propTypes = {
    name: PropTypes.string // name 값은 무조건 문자열 형태로 전달
    favoriteNumber: PropTypes.number.isRequired // favoriteNumber값은 숫자이몀 필수값임
}
export default MyComponent;
~~~


### 클래스형 컴포넌트에서의 props 사용
render함수에서 this.props를 조회한다.  
defaultProps와 propTypes설정은 함수형 컴포넌트에서 사용할때와 동일하게 작성가능하고,  
클래스 내부에서 지정하는 방법도 가능하다.
~~~js
class MyComponent extends component {
    // 클래스 내부에서 지정하는 방식
    static defaultProps = {...};
    static propTypes = {...};
    render(){
        const {name,favoriteNumber,children} = this.props;
        return (...); // 사용법은 동일
    }
}
~~~

## State
컴포넌트 내부에서 바뀔 수 있는 값을 의미한다.  
두가지 종류의 state가 있는데 하나는 클래스형 컴포넌트가 지니고있는 state이고,  
다른 하나는 함수형 컴포넌트에서 useState함수를 통해 사용하는 state이다.

### 클래스형 컴포넌트
~~~js
import { Component } from "react";

class Counter extends Component{
// constructor 메서드를 이용하여 state의 초기값을 설정하는 방식
    // constructor(props){
    //      // constructor를 작성할 때는 반드시 `super(props)`를 호출한다.
    //     super(props);
    //     // state 초기값 설정
    //     this.state = {
    //         number: 0,
    //         fixedNumber: 0
    //     }
    // }
    state = {
        number: 0,
        fixeedNumber: 0
    }

    render(){
        const {number, fixedNumber} = this.state;
        return (
            <>
                <h1>{number}</h1>
                <h2>바뀌지 않는 값: {fixedNumber}</h2>
                <button 
                onClick={()=>{
//                    this.setState({number: number+1});
                    // 객체 대신 함수 인자 전달하기
                    this.setState(prevState => {
                        number: prevState.number+1;
                    },
                    // 두번째인자로 콜백함수를 등록하여 업데이트이후 작업 설정
                    ()=>{
                        console.log('setState함수가 호출되었습니다');
                    });
                    }}>
                +1
                </button>
            </>
        )
    }
}

export default Counter;
~~~
- 컴포넌트의 state는 객체형식이어야한다.
- 이벤트로 설정할 함수를 넣어줄 때는 화살표함수를 사용하여 넣어주어야 한다.
- this.setState 함수는 state값을 바꾼다.
- setState함수에서 인자로 객체대신 함수를 넣어주는것도 가능하며 다음과 같은 형식으로 작성한다.
~~~
this.setState((prevState, props) => {
    return {...} // 업데이트 하고 싶은 내용
})
~~~
- setState의 두번째 파라미터로 콜백함수를 등록하면 setState가 끝난 후 특정 작업을 실행할 수 있다.


### 함수형 컴포넌트
- useState를 사용하여 state를 사용한다.
- 한 컴포넌트에서 여러번 사용해도 상관 없다.
- useState 함수 인자에 상태의 초기값을 넣어준다. (클래스형 컴포넌트와는 다르게 반드시 객체가 아니어도 상관없다.)
함수를 호출하면 배열이 반환되는데 첫번째 원소는 현재 상태이며 두번째 원소는 상태를 바꾸어 주는 setter함수이다.

~~~js
import { useState } from "react";

const Say = () => {
    const [message, setMessage] = useState('');
    const onClickEnter = () => setMessage('안녕하슈');
    const onClickLeave = () => setMessage('잘가슈');
    const [color, setColor] = useState('blue');
    
    return (
        <>
            <button onClick={onClickEnter}>입장</button>
            <button onClick={onClickLeave}>퇴장</button>
            <h1>{message}</h1>
        </>
    );

}
export default Say;
~~~


### state를 사용할 경우 주의사항
state 값을 바꿀때에는 반드시 setState 혹은 setter함수를 사용해야한다.  
배열이나 객체를 업데이트해야한다면 사본을 만들어 사본값을 업데이트 한 후 사본의 상태를 setState 혹은 setter 함수를 통해 업데이트한다.


## 참고
[proptypes](https://github.com/facebook/prop-types)
[리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791160508796)