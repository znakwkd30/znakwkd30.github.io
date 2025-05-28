---
layout: post
title: "AUTO_INCREMENT KEY는 ROLLBACK 가능할까?"
---

## AUTO_INCREMENT KEY는 ROLLBACK 가능할까?

예를 들어 아래의 쿼리를 실행하고

```SQL
START TRANSACTION;
INSERT INTO users (name) VALUES ('Alice');
ROLLBACK;
```

그 다음 아래와 같은 쿼리를 실행하면 어떤 결과가 나올까요?
```SQL
INSERT INTO users (name) VALUES ('Bob');
```

ID가 1부터 시작한다고 했을때 1은 SKIP 되고 ID 2로 insert 되는걸 알 수 있습니다.
왜냐하면 AUTO_INCREMENT는 충돌 방지 및 성능을 위해 일단 증가 시키고 트랜잭션 여부는 따로 관리하기 때문인데요.

이렇게 처리하는데는 크게 3가지 이유가 있습니다.

1. 성능 최적화
* AUTO_INCREMENT 값을 할당할 때마다 트랜잭션 커밋 여부를 기다리면 성능이 상당히 떨어지게 됩니다. 특히 동시성이 높은 시스템에서는 해당 처리가 병목이 될 수 있습니다.
그래서 MYSQL은 insert 시점에 바로 ID를 할당하고 ROLLBACK 되더라도 AUTO_INCREMENT 값은 소모된 것으로 간주되어 다시 사용되지 않습니다.

2. 동시성 문제
* 여러 트랜잭션이 동시에 insert를 시도하고 있을때 ROLLBACK 시마다 AUTO_INCREMENT 값을 되돌리면 Race Condition이나 충돌이 자주 발생하게 됩니다.

3. 간단한 구현 구조
* AUTO_INCREMENT는 내부적으로는 숫자 카운터를 관리하는 거라서, 트랜잭션마다 일일이 카운터를 롤백하려면 구현이 복잡해지게 됩니다.

## AUTO_INCREMENT KEY를 FK + PK 로 사용하게 되는 테이블에서는 문제가 있을까?

만약 이 AUTO_INCREMENT KEY를 FK 이면서 PK로 사용하는 다른 테이블에서는 의도하지 않은 값 건너뛰기나 고아 레코드가 생길 수 있게 됩니다.

```SQL
-- 부모 테이블
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(255)
);

-- 자식 테이블: id가 FK이자 PK
CREATE TABLE user_profiles (
  id INT PRIMARY KEY,
  bio TEXT,
  FOREIGN KEY (id) REFERENCES users(id)
);
```

해당 테이블 구조에서는 users.id 와 user_profiles.id 는 동일한 값을 가져야 합니다.

아래의 SQL을 실행했을때는 어떻게 될까요?

```SQL
START TRANSACTION;
INSERT INTO users (name) VALUES ('Charlie'); -- id = 3
INSERT INTO user_profiles (id, bio) VALUES (3, 'Hi');
-- ERROR
ROLLBACK;
```

users insert 에 실패하거나 롤백되면
users_profiles.id = 3 만 남게되는데 잠깐 동안 고아 레코드가 생성될 수 있습니다.
(이는 트랜잭션 롤백으로 정리되지만 설계상 주의가 필요합니다.)

이와 같은 문제를 해결 하기 위해서는 아래와 같은 방법이 있습니다.

1.	항상 parent → child 순서로 insert 하고, 하나라도 실패하면 전체 롤백
2.	가능하면 ID를 공유하는 구조보단, 별도 ID + FK 설계 하는것을 권장
