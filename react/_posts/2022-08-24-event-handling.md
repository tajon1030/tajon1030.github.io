---
layout: post
title: 이벤트 핸들링
sitemap: false
hide_last_modified: true
---

## 리액트의 이벤트 핸들링

사용자가 웹 브라우저에서 DOM 요소들과 상호작용 하는것을 이벤트라고 하며,  
리액트에서 이벤트를 다루는 것은 순수 자바스크립트나 jQuery를 사용하는것과 비슷하다.


## 리액트에서 이벤트를 사용할때 주의사항
1. 이벤트 이름은 소문자 대신 카멜 표기법으로 작성 (`onclick` -> `onClick`)
2. 이벤트에 함수형태의 값을 전달한다(실행할 자바스크립트 코드X)
3. DOM 요소에만 이벤트 설정가능


## 클래스형 컴포넌트
### onChange 이벤트 핸들링
컴포넌트에 input 요소를 렌더링 + onChange 이벤트 설정  
state에 input값 담아서 활용(input의 value값을 state값으로 설정)  
button 누를때 comment값 공백으로 설정   

~~~js

const { Component } = require("react");

class EventPractice extends Component{
    state = {message:''}
    render(){
        return (
            <>
                <h1>이벤트 연습</h1>
                <input
                    type="text"
                    name = "message"
                    placeholder = "아무거나 입력하시오"
                    value={this.state.message}
                    onChange = {(e)=> this.setState({message: e.target.value})}
                ></input>
                <button
                    onClick={()=> this.setState({message:''})}
                >확인</button>
            </>
        );
    };
}

 export default EventPractice;
~~~

### 임의 메서드 만들기
onChange, onClick 전달함수를 따로 빼내어 임의 메서드 만들어 활용

메서드 바인딩은 생성자 메서드에서 하는것이 정석이나,  
바벨의 transform-class-properties 문법을 사용하여 화살표함수 형태로 메서드를 정의하여도 된다.  
~~~js
const { Component } = require("react");

class EventPractice extends Component{
    state = {message:''}
    // constructor(props){
    //     super(props);
    //     // 함수가 호출될 때 this는 호출부에 따라 결정되므로 특정 html 요소의 이벤트로 등록되는 과정에서 메서드와 this관계가 끊어져버림
    //     // 따라서 this를 컴포넌트 자신으로 제대로 가리키기 위해 메서드를 this 와 바인딩한다.
    //     this.handleChange = this.handleChange.bind(this); // 바인딩하지않으면 this가 undefined
    //     this.handleChange = this.handleClick.bind(this);
    // }
    // handleChange(e){
    //     this.setState({message:e.target.value});
    // }
    // handleClick(){
    //     this.setState({
    //         message:''
    //     })
    // }

    // 메서드 바인딩은 생성자 메서드에서 하는것이 정석이나,
    // 바벨의 transform-class-properties 문법을 사용하여 화살표함수 형태로 메서드를 정의하여도 된다.
    handleChange = (e) => this.setState({message:e.target.value})
    handleClick = () => this.setState({message:''})
    
    render(){
        return (
            <>
                <h1>이벤트 연습</h1>
                <input
                    type="text"
                    name = "message"
                    placeholder = "아무거나 입력하시오"
                    value={this.state.message}
                    onChange = {this.handleChange}
                ></input>
                <button
                    onClick={this.handleClick}
                >확인</button>
            </>
        );
    };
}

 export default EventPractice;
~~~

### input 여러 개 다루기  
input값이 여러개일 경우에는 event객체를 활용한다. (e.target.name값 사용)  
객체 안에서 key를 []로 감싸면 그 안에 넣은 레퍼런스가 가리키는 실제 값이 key값으로 사용된다.

~~~js  
const { Component } = require("react");

class EventPractice extends Component{
    state = {
        username:'',
        message:''
        }

    handleChange = (e) => this.setState({[e.target.name]:e.target.value})
    handleClick = () => this.setState({
        username: '',
        message:''
        });
    
    render(){
        return (
            <>
                <h1>이벤트 연습</h1>
                <input
                    type="text"
                    name = "username"
                    placeholder = "사용자명"
                    value={this.state.message}
                    onChange = {this.handleChange}
                ></input>
                <input
                    type="text"
                    name = "message"
                    placeholder = "아무거나 입력하시오"
                    value={this.state.message}
                    onChange = {this.handleChange}
                ></input>
                <button
                    onClick={this.handleClick}
                >확인</button>
            </>
        );
    };
}

 export default EventPractice;
~~~

### onKeyDown 이벤트 핸들링
comment인풋에서 enter를 눌렀을 때 handleClick메서드 호출

~~~js  
...생략 
handleKeyPress = (e) => {
    if(e.key === 'Enter'){
        this.handleClick();
    }
}
    
    render(){
        return ( 
            ...
                <input
                    type="text"
                    name = "message"
                    placeholder = "아무거나 입력하시오"
                    value={this.state.message}
                    onChange = {this.handleChange}
                    onKeyDown = {this.handleKeyPress}
                ></input>
            ... 생략
~~~

## 함수형 컴포넌트
클래스형 컴포넌트로 할 수 있는 대부분의 작업은 함수형 컴포넌트로도 구현가능하다.
### 여러개의 input 관리 -> form 객체 이용

~~~js  
import { useState } from "react";

const EventPractice = ()=>{
    // const [username, setUsername] = useState('');
    // const [message, setMessage] = useState('');
    // const onChangeUsername = e => setUsername(e.target.value);
    // const onChangeMessage = e => setMessage(e.target.value);
    // const onClick = () => {
    //     setUsername('');
    //     setMessage('');
    // }
    // const onKeyPress = e => {
    //     if(e.key === 'Enter') onClick();
    // }

    const [form, setForm] = useState({
        username:'',
        message:''
    });
    const {username, message} = form;
    const onChange = e => {
        const nextForm = {
            ...form,
            [e.target.name]: e.target.value
        }
        setForm(nextForm);
    };
    const onClick = e => {
        setForm({
            username:'',
            message:''
        });
    };
    const onKeyPress = e=> {
        if(e.key === 'Enter') onClick();
    }

    return (
        <div>
            <h1>이벤트 연습</h1>
            <input
            type='text'
            name='username'
            placeholder="사용자명"
            value={username}
            // onChange={onChangeUsername}
            onChange={onChange}
            />
            <input
            type="text"
            name="message"
            placeholder="메시지를 입력하시오"
            value={message}
            // onChange={onChangeMessage}
            onChange={onChange}
            onKeyUp={onKeyPress}
            />
            <button onClick={onClick}>확인</button>
        </div>
    );
};
 export default EventPractice;
~~~

여러개의 인풋 상태를 관리하기위해 useState에서 form객체를 사용하였지만  
useReducer와 커스텀 Hooks를 사용하면 더욱 편리하게 관리할 수 있다.  이는 추후 포스팅에서 다룰것이다.

## 참고
[리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791160508796)  
[이벤트 리액트 메뉴얼](https://ko.reactjs.org/docs/events.html)