# 반올림(ROUND()), 버림 (TRUNCATE())

### ROUND()

- 반올림을 위한 함수

- ROUND(숫자, 반올림 할 자리수)

  ```SQL
  SELECT ROUND(1234.5678) FROM DUAL
  -- 1234
  
  SELECT ROUND(1234.56789, 1) FROM DUAL
  -- 1234.5
  
  SELECT ROUND(1234.56789, 4) FROM DUAL
  -- 1234.5679
  
  SELECT ROUND(1234.56789, -1) FROM DUAL
  -- 1230
  ```

  

### TRUNCATE()

- 버림을 위한 함수

- TRUNCATE(숫자, 버림 할 자리수)

  ```SQL
  SELECT TRUNCATE(1234.56789, 1) FROM DUAL;
  -- 1234.5
  SELECT TRUNCATE(1234.56789, 4) FROM DUAL;
  -- 1234.5678
  SELECT TRUNCATE(1234.56789, -1) FROM DUAL;
  -- 1230
  ```

  

참고 : https://soft.plusblog.co.kr/6