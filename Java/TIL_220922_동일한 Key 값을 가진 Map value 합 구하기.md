# 동일한 Key 값을 가진 Map value 합 구하기

```java
for (Map<String, Map<String, Integer>> object : objectList) {
	Set<String> keys = object.keySet();
	Iterator<String> itr = keys.iterator();

	while(itr.hasNext()) {
		String key = itr.next();
		if (!result.containsKey(key)) {
			result.put(key, object.get(key));
		} else {
			**object.get(key).forEach((k,v) -> result.get(key).merge(k,v, Integer::sum));**
		}
	}
}
```

```java
// map2.forEach((key, value) -> map1.merge(key, value, 연산));
**object.get(key).forEach((k,v) -> result.get(key).merge(k,v, Integer::sum));**
```

hashMap.merge() 메소드를 사용할 경우 **map1**에 **map2**를 병합합니다.
