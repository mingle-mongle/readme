# 트러블슈팅

## DB Buffer Memory 문제

### **1. 문제 정의**

```sql
{
    "error": "Error: target: chatting.-.primary: vttablet: rpc error: 
							code = ResourceExhausted desc = Out of sort memory, 
							consider increasing server sort buffer size (errno 1038) (sqlstate HY001):,
							BindVars: {REDACTED}"
}
```

```sql
SELECT BIN_TO_UUID AS msg_id
		 , content
		 , type
		 , time
		 , image
		 , created
		 , updated
		 , user
		 , version 
  FROM data 
 ORDER 
    BY created DESC
 LIMIT 20
OFFSET 0;
```

- 문제
    - ORDER BY 사용시 sort_buffer_size 부족으로 데이터를 가져올 수 없음.
- 문제 발생 원인
    - 쿼리 실행에 필요한 버퍼 크기가 MySQL 서버에 구성된 sort_buffer_size 값보다 큼.
    - 기본 sort_buffer_size = 262144byte (262KB)
    - image 컬럼의 데이터타입은 TEXT로 1개 당 최대 50KB이다.
    - 25개 이상부터 데이터를 sorting 하려하면 문제가 발생했다.
    

### **2. 문제 해결 과정**

---

**2.1. Buffer Size 늘리기**

```sql
SET sort_buffer_size=2097152;
```

- sort_buffer_size를 늘리더라도 메모리 부족 현상을 겪을 수 있다.
- 메모리 부족 현상이 일어나면 MySQL의 프로세스가 강제로 종료될 수 있다.
- 버퍼 사이즈를 늘리는 방법은 근본적인 해결 방법이 아니라고 생각했다.

---

**2.2. 서브쿼리 사용**

```sql
SELECT BIN_TO_UUID AS msg_id
		 , content
		 , type
		 , time
		 , image
		 , created
		 , updated
		 , user
		 , version 
  FROM (
        SELECT *
				  FROM data
         ORDER
            BY msg_id DESC
       ) AS Data  
 LIMIT 20
OFFSET 0;
```

- 서브쿼리에서 “ORDER BY”를 사용하여 임시 테이블에서 sorting 작업
- 현재 상황에서 buffer 메모리 부족은 일어나지 않지만 “ORDER BY”를 사용하기 때문에 추후에 메모리 부족 문제가 발생할 수 있음.
- Backward Index Scan은 Forward Index Scan 보다 느림.

---

**2.3. 쿼리 2번 요청**

```sql
SELECT COUNT(*) FROM data;
```

```sql
SELECT BIN_TO_UUID AS msg_id
		 , content
		 , type
		 , time
		 , image
		 , created
		 , updated
		 , user
		 , version 
  FROM data
 LIMIT 20
OFFSET (총 row - 20);
```

- 총 row수를 먼저 가져온 후, OFFSET에 반영.
- 평균 처리 속도 : 1,019ms

---

**2.4. 인덱스 설정 후 커서 페이지네이션 (최종 해결 방법)**

```sql
SELECT BIN_TO_UUID AS msg_id
		 , content
		 , type
		 , time
		 , image
		 , created
		 , updated
		 , user
		 , version 
  FROM data
 WHERE time >= (
								SELECT
											 time
								  FROM data
                 LIMIT 1
                OFFSET 20
               )
 LIMIT 20
OFFSET 0;
```

- time 컬럼에 Descending Index를 추가하여, ORDER BY 없이도 DESC 정렬이 되게 함.
- time의 20번째 값을 기준으로 가져와 커서값으로 설정한 후 데이터를 가져옴.
- Forward Index Scan으로 진행되기 때문에 속도가 더 빠르다.
- 평균 처리 속도 : 573ms

### **3. 문제 해결 결과**

- Primary Key(msg_id)는 기본값으로 ASC 정렬로 생성 되기 때문에 인덱스 정렬에 사용하지 않았다.
- 총 row 수를 가져와 다시 요청을 보내는 쿼리와 인덱스 설정하는 방법은 ORDER BY를 사용하지 않기 때문에 Sorting Error가 나지 않는다.
- DESC 인덱스 사용하는 쿼리문으로 바꾼 후 약 43.8% 빨라졌다.
- 최종적으로 DESC 인덱스 쿼리를 사용했다.

![스크린샷 2023-09-07 오후 2.27.04.png](%E1%84%90%E1%85%B3%E1%84%85%E1%85%A5%E1%84%87%E1%85%B3%E1%86%AF%E1%84%89%E1%85%B2%E1%84%90%E1%85%B5%E1%86%BC%208dfe7d13151c41998ddaae135fbe0c3a/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-09-07_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_2.27.04.png)

| 방법 | 인덱스 사용 | 총 요청 수 | 초당 요청 수 | 평균 응답 시간 | 최대 응답 시간 | Error % |
| --- | --- | --- | --- | --- | --- | --- |
| DESC 인덱스 사용 | O | 11,669 | 19.28 | 180~220 ms | 3,212 | 0 |
| 쿼리 2번 요청 | X | 11,652 | 19.25 | 80~100 ms | 4,809 | 0 |

### 4**. 참고 자료 및 링크**

- [https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html)
- [https://dev.mysql.com/doc/refman/8.0/en/descending-indexes.html](https://dev.mysql.com/doc/refman/8.0/en/descending-indexes.html)
- [https://tech.kakao.com/2018/06/19/mysql-ascending-index-vs-descending-index/](https://tech.kakao.com/2018/06/19/mysql-ascending-index-vs-descending-index/)