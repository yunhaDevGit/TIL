HTTP 메소드 중 PUT과 PATCH 모두 리소스의 업데이트를 의미한다. 

두 메소드 모두 리소스를 업데이트 한다는 점에서 비슷해 보이지만 약간의 차이가 있다. 

**PUT : 리소스의 모든 것을 업데이트**

**PATCH : 리소스의 일부를 업데이트**

Spring에서는 각각 @PutMapping과 @PatchMapping으로 제공한다. 
