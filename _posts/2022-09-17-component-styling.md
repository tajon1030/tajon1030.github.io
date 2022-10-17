---
layout: post
title: 컴포넌트 스타일링
tags: [react]
comments: true
---

## 컴포넌트 스타일링
- 일반 CSS : 가장 기본적인 방식  
- Sass : CSS문법을 사용하여 CSS 코드를 더욱 쉽게 작성하게 해줌  
- CSS Module : 다른 CSS클래스의 이름과 절대 충돌하지않도록, 파일마다 고유한 이름을 자동으로 생성해주는 옵션  
- styled-components : 스타일을 js파일에 내장시키는 방식으로, 스타일을 작성함과 동시에 해당 스타일이 적용된 컴포넌트를 만들수 있게 해줌

다양한 리액트 컴포넌트 스타일링 방식이 있고,  
상황에 따라 선택해서 사용한다.

### 1. 일반 CSS
CSS를 작성할때 가장 중요한점은 CSS 클래스를 중복되지 않게 만드는것으로  
이를 방지하는 여러 방식이 있는데 그중 하나는 이름 짓는 규칙을 짓는것이고,  
또 다른 하나는 CSS Selector를 활용하는 것이다.

#### 이름 짓는 규칙  
클래스 이름을 컴포넌트이름-클래스형태(ex: App-header) 형태로 클래스 이름에 컴포넌트 이름을 포함 시켜 다른 컴포넌트에서 중복되는 클래스를 만들어 사용하는것을 방지할 수 있다.  
비슷한 방식으로 BEM 네이밍이라는 CSS방법론을 활용하여 해당 클래스가 어디에서 어떤 용도로 사용하는지 명확하게 작성하는 방식을 사용할 수도 있다. (ex: .card__title-primary)

#### CSS Selector  
CSS 클래스가 특정 클래스 내부에 있는 경우에만 적용할 수 있다.  

.App 안에 들어있는 .logo와 header에 스타일 적용하는 예제  
~~~css
// 이름 짓는 규칙 활용한 방식
.App-logo { ... }
.App-header { ... }

    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />


// CSS Selector방식
.App .logo { ... }
/* header는 header클래스가 아닌 header태그 자체에 스타일을 적용하기 때문에 .이 생략 */
.App header { ... }

    <div className="App">
      <header>
        <img src={logo} className="logo" alt="logo" />
~~~


### 2. Sass (Syntactically Awesome Style Sheets)
CSS 전처리기로 복잡한 작업을 쉽게 할 수 있도록 해주고,  
스타일 코드의 재활용성을 높여 줄 뿐만 아니라  
코드의 가독성을 높여 유지보수를 쉽게 해준다.

.sass 문법  
~~~scss
$font-stack: Helvetica, sans-serif
$primary-color: #333

body
  font: 100% $font-stack
  color: $primary-color
~~~

.scss 문법  
~~~scss
$font-stack: Helvetica, sans-serif
$primary-color: #333

body {
  font: 100% $font-stack
  color: $primary-color
}
~~~

sass확장자는 중괄호와 세미콜론을 사용하지 않는다.  
보통 .scss문법이 더 자주 사용된다.

~~~scss
// 변수 사용하기
$red: #fa5252;
// 믹스인 만들기(재사용되는 스타일 블록을 함수처럼 사용가능)
@mixin square($size){
    $calculated: 32px*$size;
    width: $calculated;
}

.SassComponent{
    display: flex;
    .box{
        &.red {
            // .red클래스가 .box와 함께 사용되었을때
            background: $red;
            @include square(1);
        }
    }
}
~~~

#### utils 함수 분리
Sass변수 및 믹스인은 다른 파일로 따로 분리하여 작성한뒤 필요한곳에서 불러와 사용할 수 있다.  
다른 scss파일을 불러올 때는 @import 구문을 사용한다.

src/styles/utils.scss  
~~~scss
// 변수 사용하기
$red: #fa5252;
// 믹스인 만들기(재사용되는 스타일 블록을 함수처럼 사용가능)
@mixin square($size){
    $calculated: 32px*$size;
    width: $calculated;
}
~~~

SassComponent.scss  
~~~scss
@import './styles/utils';
.SassComponent{
    display: flex;
    .box{
        &.red {
            // .red클래스가 .box와 함께 사용되었을때
            background: $red;
            @include square(1);
        }
    }
}
~~~
#### sass-loader 설정 커스터마이징
`@import './styles/utils';` 형태로 다른 scss파일을 불러오는 방식은  
프로젝트 디렉터리가 많아져서 구조가 깊어질경우 상위폴더로 한참 거슬러 올라가야한다는 단점이 있다.  
이러한 문제를 웹팩에서 Sass를 처리하는 sass-loader설정을 커스터마이징하여 해결할 수 있다.  

#### node_modules에서 라이브러리 불러오기
물결문자(~)사용하면 자동으로 node_modules에서 라이브러리 디렉터리를 감지하여 스타일을 불러올 수 있다.  
yarn명령어를 이용해 설치한 후 scss파일에서 물결표시를 사용하여 라이브러리를 불러온다.  
~~~css
@import '~include0media/dist/include-media';
@import '~open-color/open-color';
~~~

### 3. CSS Module
CSS를 불러와서 사용할때 클래스 이름을 고유한 값인 [파일이름]_[클래스이름]__[해시값] 형태로 자동으로 만들어서 이름이 중첩되는 현상을 방지해주는 기술
.module.css 확장자로 저장하기만 하면 적용된다.  
자동으로 고유한 이름으로 변하기때문에 흔히 사용되는 단어를 클래스 이름으로 마음대로 사용 가능하다.  
특정 클래스가 웹 페이지에서 전역적으로 사용되는 경우라면 :global을 앞에 입력하여 글로벌 CSS임을 명시해 줄 수 있다.

CSSModule.module.css  
~~~css
.wrapper {
    background: black;
}
.invited {
    color: black;
}
/* 글로벌 CSS를 작성하고 싶다면 */
:global .something {
    color: aqua;
}
~~~
CSSModule.js  
~~~js
import React from 'react';
import styles from './CSSModule.module.css';
const CSSModule = () => {
    return (
        <div className={styles.wrapper}>
        안녕하세요 저는 <span className="something">CSS Module!</span>
        </div>
    );
};

export default CSSModule;
~~~

#### calssnames
CSS클래스를 조건부로 설정할때 유용한 라이브러리  
여러 종류의 파라미터를 조합하여 CSS클래스를 설정할수 있기때문에 컴포넌트에서 조건부로 클래스를 설정할 때 매우 편리하다.

해당 라이브러리를 설치한 뒤 사용  
~~~
yarn add classnames
~~~

간략 사용법  
~~~js
import classNames from 'classnames';

classNames('one', 'two'); // = 'one two'

const myClass = 'hello';
classNames('one', myClass, {myCondition: true});
// = 'one hello myCondition'
~~~

props 값에 따라 다른 스타일을 주는 예제  
~~~js
const MyComponent = ({highlighted, theme}) => (
    <div className={classNames('MyComponent', {highlighted}, theme)}>
    Hello
    </div>
);
// highlighted값이 true이면 highlighted클래스가 적용되고, false이면 적용되지 않는다.
~~~  


CSSModule과 함께 사용할 경우 classnames 내장함수인 bind를 사용하면 클래스를 넣어줄때마다 styles.[클래스이름] 형태를 사용할 필요가 없다.  
클래스를 여러 개 설정할때도 편하다.  
사전에 미리 styles에서 받아온 후 사용하게끔 설정해두고, cx('클래스이름','클래스이름2') 형태로 사용가능하다.  
~~~js
import React from 'react';
import classNames from 'classnames/bind';
import styles from './CSSModule.module.css';

const cx = classNames.bind(styles);

const CSSModule = () => {
    return (
        <div className={cx('wrapper','invited')}>
        안녕하세요 저는 <span className="something">CSS Module!</span>
        </div>
    );
};

export default CSSModule;
~~~

#### Sass와 함께 사용하기
파일이름뒤에 .module.scss 확장자를 사용해주면 Sass를 사용할때에도 CSSModule을 사용할 수 있다.

#### CSS Module이 아닌 파일에서 CSS Module 사용
일반 .css/ .scss 파일에서도 :local을 사용하여 CSS Module을 사용가능하다.
~~~css
:local.wrapper{
    /*스타일*/
}
~~~

### 4. styled-components
자바스크립트 파일 안에 스타일을 선언하는 방식을 CSS-in-JS라고 부르는데  
선호되는 라이브러리중 하나가 style-components이다.

라이브러리 설치  
~~~
yarn add styled-components
~~~

styled-components와 일반 classNames를 사용하는 CSS/Sass를 비교했을때,  
가장 큰 장점은 props 값으로 전달해 주는 값을 쉽게 스타일에 적용할 수 있다는 것이다.  

StyledComponent.js  
~~~js
import React from 'react';
import styled, {css} from 'styled-components';

const Box = styled.div`
/* props로 넣어준 값을 직접 전달해 줄 수 있다. */
background: ${props => props.color || 'blue'};
padding: 1rem;
`;

const Button = styled.button`
background: white;

/* &문자를 사용하여 Sass처럼 자기자신 선택 가능 */
&:hover{
    background: rgba(255,255,255,0.9);
}

/* inverted값이 true 일때 특정 스타일 부여 */
${props =>
    props.inverted && css`
    background: none;
    &:hover {
        background: white;
        color:black;
    }`
};
& + button {
    margin-left: 1rem;
}
`;


const StyledComponent = () => {
    <Box color = "black">
        <Button>안녕하세요</Button>
        <Button inverted={true}>테두리만</Button>
    </Box>
}

export default StyledComponent;
~~~

#### Tagged 템플릿 리터럴
앞서 작성한 코드를 보면 스타일을 작성할때 `를 사용하여 만든 문자열에 스타일 정보를 넣어주었다.  
일반 템플릿 리터럴과 다른 점은  
템플릿안에 자바스크립트 객체나 함수를 전달할 때 원본값을 그대로 추출할 수 있다는 점이다.  
~~~js
`hello ${%raw%}{{foo: 'bar'}}{%endraw%} ${()=>'word'}!`
// 결과: "hello [object Object] ()->'word'!"

function tagged(...args){
    console.log(args);
}
tagged`hello ${%raw%}{{foo: 'bar'}}{%endraw%} ${()=>'word'}!`
// 콘솔에 원본값이 그대로 나옴
~~~

#### 스타일링된 엘리먼트 만들기
컴포넌트 파일 상단에서 styled를 불러오고 styled.태그명을 사용하여 구현  
~~~js
import styled from 'styled-components';

const MyComponent = styled.div`
    font-size: 2rem;
`;
~~~  
예제처럼 styled.div 뒤에 Tagged 템플릿 리터럴 문법을 통해 스타일을 넣어주면,  
해당 스타일이 적용된 div로 이루어진 리액트컴포넌트가 생성된다.  
`<MyComponent>Hello</MyComponent>` 와 같은 형태로도 사용할수 있다.  

사용해야할 태그명이 유동적이거나  
특정 컴포넌트 자체에 스타일링해주고 싶은경우 다음과 같이 나타낸다.  
~~~js
// 태그 타입을 styled 함수의 인자로 전달
const MyInput = styled('input')`
    background: gray;
`
// 아예 컴포넌트 형식의 값을 넣어줌
const StyledLink = styled(Link)`
    color: blue;
`
~~~

#### 스타일에서 props 조회하기
styled-components를 사용하면 스타일쪽에서 컴포넌트에게 전달된 props값을 참조할수있다.

StyledComponents.js - Box 컴포넌트  
~~~js
const Box = styled.div`
/* props로 넣어준 값을 직접 전달해 줄 수 있다. */
background: ${props => props.color || 'blue'};
padding: 1rem;
`;
~~~
이렇게 만들어진 코드는 JSX에서 사용할때 다음과 같이 color를 props로 넣어줄 수 있다.
~~~html
<Box color="black">(...)</Box>
~~~

#### props에 따른 조건부 스타일링
조건부 스타일링을 간단하게 props로 처리할수있다.
~~~html
<Button inverted={true}>테두리만</Button>
~~~

#### 반응형 디자인
브라우저 가로크기에 따라 다른 스타일을 적용하기 위해서는  
일반 CSS를 사용할때와 똑같이 media쿼리를 사용한다.  
작업을 여러 컴포넌트에서 반복해야 한다면 작업을 함수화하여 간편하게 사용 가능하다.

~~~js
const Box = styled.div`
  /* props로 넣어 준 값을 직접 전달해 줄 수 있습니다. */
  background: ${props => props.color || 'blue'};
  padding: 1rem;
  display: flex;
  /* 기본적으로는 가로 크기 1024px에 가운데 정렬을 하고
    가로 크기가 작아짐에 따라 크기를 줄이고
    768px 미만이 되면 꽉 채웁니다. */
  width: 1024px;
  margin: 0 auto;
  @media (max-width: 1024px) {
    width: 768px;
  }
  @media (max-width: 768px) {
    width: 100%;
  }
`;
~~~
~~~js
import React from 'react';
import styled, { css } from 'styled-components';
 
const sizes = {
  desktop: 1024,
  tablet: 768
};
 
// 위에 있는 size 객체에 따라 자동으로 media 쿼리 함수를 만들어준다.
// 참고: https://www.styled-components.com/docs/advanced#media-templates
const media = Object.keys(sizes).reduce((acc, label) => {
acc[label] = (...args) => css`
  @media (max-width: ${sizes[label] / 16}em) {
    ${css(...args)};
    }
`;
 
return acc;
}, {});
 
const Box = styled.div`
/* props로 넣어 준 값을 직접 전달해 줄 수 있습니다. */
background: ${props => props.color || 'blue'};
padding: 1rem;
display: flex;
width: 1024px;
margin: 0 auto;
${media.desktop`width: 768px;`}
${media.tablet`width: 100%;`};
`;
~~~

## 참고
[리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791160508796)
