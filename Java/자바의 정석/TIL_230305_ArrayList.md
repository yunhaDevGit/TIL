# ArrayList

![image](https://user-images.githubusercontent.com/74949294/223123911-9617da79-ff08-4073-901d-237e8de5e1f3.png)

- ArrayList는 기존 Vector를 개선한 것으로 구현 원리와 기능적으로는 동일
- ArrayList와 달리 Vector는 자체적으로 동기화처리가 되어 있다
- List 인터페이스를 구현하므로, **저장순서가 유지**되고 **중복을 허용**한다
- 데이터의 저장공간으로 배열을 사용한다(배열 기반)

## ArrayList의 메서드

- 생성자
    - ArrayList() : 기본 생성자
    - ArrayList(Collection c) : 컬렉션에 저장되어 있는 거를 저장하는 ArrayList 생성
    - ArrayList(int initialCapacitiy) : 배열의 길이 지정
- 메서드
    - 추가하는 메서드
        - boolean add(Object o)
        - void add(int index, Object element) - 저장 위치 지정
        - boolean addAll(Collection c) - 컬렉션의 모든 요소 저장
        - boolean addAll(int index, Collection c) - 추가될 위치 지정
    - 삭제
        - boolean remove(Object o)
        - Object remove(int index) - 특정 위치 객체 삭제
        - boolean removeAll(Collection c) - 컬렉션에 있는 객체 삭제
        - void clear() - 모든 객체 삭제
    - 검색
        - int indexOf(Object o) - 객체가 몇 번째 저장 되어 있는지(없으면 -1 반환)
        - int lastIndexOf(Object o) - 오른쪽부터 찾음
        - boolean contains(Object o) - 특정 객체가 존재하는지
        - Object get(int index) - 특정 위치의 객체 읽기
        - Object set(int index, Object element) - 특정 위치 객체 변경
        - List subList(int fromIndex, int toIndex) - 특정 위치의 객체를 뽑아 새로운 List 만들기
        - Object[] toArray() - ArrayList의 객체 배열 반환
        - Object[] toArray(Object[] a)
        - boolean isEmpty() - 비어있는지 확인
        - void trimToSize() - 빈공간 제거
        - int size() - 저장된 객체 개수


    ### 예
    
    ```java
    list1 = [-1,0,1,2,3,4,5];
    
    // 값이 1인 걸 지우고 싶을 경우
    list1.remove(1); // index가 1인 값을 지움(결과:[-1,1,2,3,4,5];
    list1.remove(new Integer(1)); // 값이 1인 것을 지움(결과: [-1,0,2,3,4,5];
    ```
    
    ### ArrayList에 저장된 객체의 삭제 과정


![image](https://user-images.githubusercontent.com/74949294/223123966-cab46db0-18f7-487c-8a27-5bd12a9dda56.png)

### ArrayList 삭제 과정 예

- 끝에서부터 지우면 성능상 더 유리하다

![image](https://user-images.githubusercontent.com/74949294/223124044-ec118993-2835-42c7-8932-ad1a0a08bc47.png)
