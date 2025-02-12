# 1. JPA 소개

## JPA의 강점

1. 반복적인 CRUD sql을 알아서 처리해준다.
2. 객체 모델링과 관계형 데이터베이스 간 차이도 해결해준다.

## 1.1 SQL을 직접 다룰 때 발생하는 문제점

### CRUD 작업 시 작성해야 하는 파일

- 객체
- DAO
- CRUD용 SQL문
- JDBC API 사용문
- 매핑 로직

**수정 시 위 모든 과정을 전부 수정해야 함**

### 즉, 직접 다룰 때 발생하는 문제점은

- 진정한 의미의 계층 분할이 어려움
- 낮은 엔티티 신뢰성
- SQL 의존적 개발

### JPA를 사용한다면?

- JPA의 CRUD
    
    ```java
    // 저장
    jpa.persist(member);
    
    // 조회
    String memberId = "helloId";
    Member member = jpa.find(Member.class, memberId);
    
    // 수정
    Member member = jpa.find(Member.class, memberId);
    member.setName("변경된 이름");
    
    // 연관 객체 조회
    Member member = jpa.find(Member.class, memberId);
    Team team = member.getTeam();
    ```
    

## 1.2 패러다임의 불일치

: 객체와 관계형 데이터베이스는 지향하는 목적이 달라 기능과 표현 방법이 다르다

### 1.2.1 상속

객체지향 언어에서 상속 시

```java
abstract class Item {
	Long id;
	String name;
	int price;
}

class Album extends Item {
	String artist;
}
```

관계형 데이터베이스에 저장 시

```sql
INSERT INTO ITEM ...
INSERT INTO ALBUM...
```

**이렇게 여러 과정을 거쳐 저쟝해야 한다**

JPA와 상속

```java
jpa.persist(album);
// 이러면 알아서 관계형 데이터베이스에 INSERT 두 개로 나눠서 저장해준다
```

### 1.2.2 연관관계

- RDB의 연관관계
    
    : 외래키를 이용하여 외부 테이블과 관계 형성
    
- 객체의 연관관계
    
    : 참조(객체 내부에 관계를 형성할 객체 선언)를 통해 관계 형성
    
- JPA의 연관관계 CR
    
    ```java
    class Member {
    	String id;
    	Team team;
    	String username;
    	
    	Team getTeam() {
    		return team;
    	}
    }
    ```
    
    ```java
    class Team {
    	Long id;
    	String name;
    }
    ```
    
    ```java
    // 저장
    member.setTeam(team);
    jpa.persist(member);
    
    // 조회
    Member member = jpa.find(Member.class, memberId);
    Team team = member.getTeam();
    ```
    

### 1.2.3 객체 그래프 탐색

![image.png](attachment:0697adc7-19ee-41cb-a1d5-bdcfdf38c83d:image.png)

위와 같이 객체 연관관계가 설계되어 있을 시

- SQL은 실행하는 SQL에 따라 객체 그래프의 탐색 범위가 정해짐(각 지점별 SQL문이 필요하다는 뜻)
- JPA 사용 시 체이닝(?) 가능
    
    ```java
    member.getOrder().getOrderItem()...
    ```
    
    **JPA는 연관된 객체를 사용하는 시점에 SQL문을 실행하기 때문에(JPA가 알아서 쿼리문 작성 및 사용) 유연성이 높다 ⇒ 데이터베이스 조회를 실제 객체 사용 시점까지 미루는 것이므로 **지연로딩**이라고 한다.
    

## 1.3 JPA란?

- JAVA 진영의 ORM 기술 표준(MyBatis와 같은 지점에서 사용)

**JPA를 사용해야 하는 이유**

1. 생산성
    - 반복적인 쿼리문 작성이 불필요
    - DDL문 자동 생성 기능도 존재
2. 유지보수성
    - SQL을 직접 다루면 변경이 있을 시 유지보수가 복잡하지만 JPA는 데이터베이스 쪽 변경사항을 알아서 처리해주기 때문에 편리
3. 패러다임의 불일치 해결
4. 성능
    - 같은 transcation 안에 같은 데이터를 두 번 조회하는 경우 등에서는 db와 두 번 통신하지 않고 한 번 통신 가능
