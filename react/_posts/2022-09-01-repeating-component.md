---
layout: post
title: 컴포넌트 반복
sitemap: false
hide_last_modified: true
---

### 컴포넌트 반복

웹어플리케이션을 만들다 보면,  
~~~
<li>...</li>
~~~ 
처럼 반복되는 데이터를 작성할 때가 있다.  
js 배열객체 내장함수인 map함수를 사용하면 이처럼 반복되는 컴포넌트를 렌더링 할 수 있다.

또한, 상태 안에서 배열을 변형할 때는 배열에 직접 접근하여 수정하는 것이 아닌  
`concat`, `filter` 등의 배열 내장함수를 사용해 새로운 배열을 만들어 새로운 상태로 설정해주어야 한다.


#### key

key는 컴포넌트 배열을 렌더링 했을 때 어떤 원소에 변동이 있었는지 알아내려고 사용한다.

예를 들어 유동적인 데이터를 다룰때 원소를 생성,제거,수정할 수 있는데  
key가 없을때는 Virtual DOM을 비교하는 과정에서 리스트를 순차적으로 비교하며 변화를 감지하지만,  
key가 있다면 이를 사용하여 어떤 변화가 일어났는지 더욱 빠르게 알아낼 수 있다.  

key값은 언제나 유일해야하기 때문에 데이터가 가진 고유값을 key로 설정해야한다.

~~~js
const IterationSample = () => {
  const names = ['봄','여름','가을','겨울'];
  const nameList = names.map((name,index) => 
    <li key = {index}>{name}</li>);
  return <ul>{namesList}</ul>;
  };

export default IterationSample;
~~~

참고로, index를 key로 사용하면 배열이 변경될 때 효율적으로 리렌더링하지 못하므로,  
고유한 값이 없을 때에만 index 값을 key로 사용해야한다.

### 불변성 유지
리액트에서 상태를 업데이트 할 때는 기존상태를 그대로 두면서 새로운 값을 상태로 설정해야한다.  
이를 불변성 유지라고 하며,  
불변성 유지를 해주어야 나중에 컴포넌트 성능을 최적화할 수 있다.


## 참고
[리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791160508796)
