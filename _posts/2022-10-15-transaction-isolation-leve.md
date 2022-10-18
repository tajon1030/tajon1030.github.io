---
layout: post
title: 트랜잭션 isolation 레벨
tags: [database]
comments: true
---

#### 트랜잭션 isolation level?  
한국어로 풀어쓰면 트랜잭션 격리수준  
동시에 여러 트랜잭션이 처리되어야한다면 특정 트랜잭션이 다른 트랜잭션에서 변경 또는 조회하는 데이트를 볼수있도록 허용할지 말지를 결정하는것을 말한다.  

#### isolation level 종류
- Read Uncommitted  
- Read Committed  
- Repeatable Read  
- Serializable  
  
프로젝트중에는 주로 Read Committed와 Repeatable Read를 사용한다.  

#### Read Uncommitted
Commit이나 Rollback 여부에 상관없이 다른 트랜잭션에서 값을 읽을 수 있다.  
따라서 어떤 트랜잭션에서 아직 작업이 완료되지 않은 다른 트랜잭션에 의한 변경사항을 보게되는 **DirtyRead** 현상이 발생  

#### Read Committed
RDB 대부분이 기본적으로 사용하는 격리수준으로,  
Commit이 이루어진 트랜잭션만 조회할 수 있다.  
Oracle의 경우 쿼리시작 시점의 Undo 데이터를 제공하는 방식으로 구현한다.  
만약 update 쿼리가 날아온다면 row를 업데이트하고 이전 값을 undo 영역에 넣어두어 다른 트랜잭션에서 조회시 이용하고, 혹은 롤백이 필요할경우 undo에 있는 값으로 롤백을 해주는 방식으로 동작한다.  
한 트랜잭션에서 같은 쿼리를 두번 수행할때, 두 쿼리의 결과가 상이하게 나타나는 비 일관성 현상인 **Non-Repeatable Read** 현상 발생(한 트랜잭션이 수행중일때 다른 트랜잭션이 값을 수정/삭제함으로써 발생)

#### Repeatable Read
각 트랜잭션 마다 번호를 매겨서 UNDO 영역에 모든 변경을 저장하고, DB 엔진이 불필요하다고 판단하는 시점에 UNDO 영역의 스냅샷을 주기적으로 삭제해준다.(**MVCC**)  
Repeatable Read와 Read committed의 차이는 undo 영역에 백업된 여러개의 레코드 가운데 몇 번째 이전버전까지 찾아 들어가야 하는지에 있음

트랜잭션을 열어 해당 트랜잭션에서 모든 테이블의 데이터를 select한 후 그대로 놔두면 InnoDB undo영역이 계속 커져서 시스템 테이블스페이스 I/O가 유발되어 성능을 떨어뜨릴수있다.  
Binary log가 활성화된 MySQL 서버에서는 Read Committed 격리 수준을 사용할 수 없음. (최소 Repeatable read 격리 수준 이상을 사용해야함)

다른 트랜잭션에서 수행한 변경 작업에 의해 레코드가 보였다가 안 보였다가 하는 현상인 **PhantomRead** 현상이 발생할수있다.(한 트랜잭션이 수행중일때 다른 트랜잭션이 새로운 레코드를 삽입하면 발생) -> PhantomRead를 방지하기위해서는 쓰기 잠금을 걸어야 한다.

#### Serializable
가장 엄격한 격리수준으로 읽기/쓰기같은 다른 트랜잭션이 진행중일 때 해당 레코드에 접근할 수 없다.
동시 처리 성능이 가장 낮다.


## 참고
[nesoy Blog](https://nesoy.github.io/articles/2019-05/Database-Transaction-isolation)