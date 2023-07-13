---
layout: post
title: LOCATE vs INSTR
subtitle:
tags: [db]
comments: true
---

MySQL에서 LOCATE와 INSTR은 문자열 함수로,  
주어진 문자열 내에서 특정 문자열이나 패턴의 위치를 찾는 데 사용된다.  
이 둘은 유사하지만 약간의 차이가 있다.  

## LOCATE
`LOCATE(substring, string, [start])`  
: string에서 substring의 위치를 찾아 반환하며 찾는 문자열이 없을 경우에는 0을 반환한다.  
start 매개변수를 생략하면 문자열의 처음부터 탐색하고, 지정할 경우 해당 위치부터 탐색을 시작한다.  

~~~sql
SELECT LOCATE('world', 'Hello world'); -- 결과: 7
~~~

## INSTR
`INSTR(string, substring)`  
: string에서 substring의 위치를 찾아 반환하며 찾는 문자열이 없을 경우 0을 반환한다.  
하지만 INSTR은 LOCATE와는 달리 start 매개변수를 지원하지 않는다.  
-> 항상 문자열의 처음부터 탐색을 수행.  

~~~sql
SELECT INSTR('Hello world', 'world'); -- 결과: 7
~~~

요약하자면, LOCATE와 INSTR은 문자열 내에서 특정 문자열의 위치를 찾는 함수이지만,  
LOCATE는 start 매개변수를 사용하여 시작 위치를 지정할 수 있고,  
INSTR는 항상 문자열의 처음부터 탐색을 수행한다.


## LIKE를 쓰면 되지 않나?
보통 LIKE는 찾고자 하는 문자열이 서브스트링인 경우에 유용하지만 그 반대의 경우에는 사용할 수가 없다.  
예를 들어 컬럼 데이터가, 주어지는 값의 서브스트링인 row를 찾고 싶을때는 어떻게 해야할까?
즉 'Hello world'의 서브스트링을 테이블에서 찾고 싶다면 어떻게 해야할까?  
이때는 LIKE로 검색을 할 수는 없고 LOCATE를 활용하여 검색할 수 있다.  

| str   |
|-------|
| hihi  |
| world |

~~~sql
SELECT * FROM test WHERE LOCATE(str, 'Hello world') > 0 ;
~~~

위의 쿼리 처럼 substr에 대상 컬럼을 주고, 문자열을 인자로 준 후 LOCATE 함수의 리턴 값이 0보다 클때 값을 리턴해주면 된다.

#### 참고한 사이트
[프로그래머스-특정 옵션이 포함된 자동차 리스트 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/157343)

[림코딩 블로그 - LOCATE 를 통한 문자열 처음 등장 위치 확인](https://devpouch.tistory.com/164)  
