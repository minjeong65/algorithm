# [프로그래머스] 소수 찾기(Lv.2) 

### 문제

한자리 숫자가 적힌 종이 조각이 흩어져있습니다. 흩어진 종이 조각을 붙여 소수를 몇 개 만들 수 있는지 알아내려 합니다.

각 종이 조각에 적힌 숫자가 적힌 문자열 numbers가 주어졌을 때, 종이 조각으로 만들 수 있는 소수가 몇 개인지 return 하도록 solution 함수를 완성해주세요.

##### 제한사항

- numbers는 길이 1 이상 7 이하인 문자열입니다.
- numbers는 0~9까지 숫자만으로 이루어져 있습니다.
- "013"은 0, 1, 3 숫자가 적힌 종이 조각이 흩어져있다는 의미입니다.

</br>

### 문제해결과정

입력되는 숫자가 str형이므로 이것을 list화하여 permutation 연산을 이용했다. 처음엔 combination으로 했었는데 17과 같은 입력이 들어올 경우 1/7/17만 뽑고 끝난다. 71을 만들기 위해서는 permutation을 사용해야 한다.

1개씩 뽑는 것부터 전체를 뽑는 것까지 해야하므로 for문을 이용해서 뽑기를 진행했다. 그리고 num이라는 리스트에 잠깐 넣었다가 리스트 형으로 new라는 새로운 리스트에 담았다.

이렇게 new에는 각 원소가 리스트에 담겨있는 이차원 리스트의 형태이다. 각각의 원소들은 하나의 값으로 합쳐주어야 한다. 즉, [1,7]의 형태를 17로 바꿔야 한다는 것이다.

두 문자를 이어주는 것은 str형태가 편하므로 우선 str으로 형변환 시킨뒤 to_int라는 리스트에 int형으로 바꾼 숫자들을 넣었다.

이제 이 값을 하나씩 검사하여 소수인지 판단하기만 하면 된다.

하지만 이때 to_int에는 중복되는 값이 있으므로 set로 바꿔준 뒤 반복을 실행한다.

소수인지 판별하는 과정은 우선 1부터 자기자신까지 나눴을 때 나머지가 0이면 약수로 카운트하고, 약수의 개수가 2인 경우 소수로 카운트한다.

하지만 두개의 테스트 케이스를 시간초과 때문에 통과하지 못했다.

아무래도 소수를 확인하는 곳을 개선해야 할 것 같다.

**시간 초과**

```python
from itertools import permutations

def solution(numbers):
    new=[] #원소가 리스트 형태
    to_int =[] #각 원소를 정수로 변환한 리스트
    answer = 0 #만들 수 있는 소수의 개수
    
    #순열
    for i in range(1, len(numbers)+1):
        num=[] #i개씩 뽑고 잠깐 담을 리스트
        num = list(permutations(list(numbers), i))
        for i in num:
            new.append(list(i))
	
    #리스트 -> int
    for i in new:
        to_str = ''
        for j in range(len(i)):
            to_str += i[j]
        to_int.append(int(to_str))

    #소수 확인
    for i in set(to_int):
        count=0
        for j in range(1,i+1):
            if i % j == 0:
                count+=1
        if count == 2:
            answer+=1

    return answer
```

</br>

에라토스테네스의 체를 이용해서 다음과 같이 소수 판별 부분을 수정했다.

```python
#소수 판별
    n = max(to_int)
    prime = [True]*(n+1) #인덱스 : 0~n
    #0과 1은 소수에서 제외
    prime[0] = False 
    prime[1] = False

    m=int(n**0.5)
    for i in range(2,m+1):
        if prime[i] == True: #True면 소수인지 판별
            for j in range(i+i,n+1,i): #i 이후 i의 배수는
                prime[j] = False # 소수에서 제거

    for i in set(to_int):
        if prime[i]:
            answer+=1
```

역시나 소수찾기 부분에서 시간초과가 났던 것이었다.

이전에 다루었던 에라토스테네스의 체를 이용하면 시간을 단축시킬 수 있을 것 같아 사용했다.

지난 소수 찾기 문제는 n까지의 소수를 모두 찾는 것이었지만 이번에는  to_int에 있는 수 중에 소수인 개수를 찾는 것이다.

따라서 to_int에 있는 최댓값을 n으로 하여 n까지의 소수를 prime배열로 찾은 뒤, True값의 인덱스와 to_int의 값이 맞는 것이 있으면 카운트를 하도록 설계했다.

</br>

### 코드개선

문자열로 들어온 숫자를 하나씩 잘라서 순열 연산을 한 뒤 다시 정수형의 숫자들로 만들기까지의 과정이 복잡하다고 느꼈는데, join()과  map()를 이용해서 이부분을 아주 간단하게 구현한 코드가 있어서 참고해서 내 코드를 개선시켜 보았다.

```python
#문자열->순열->정수형 set 생성
for i in range(len(numbers)):#i개씩 뽑고 잠깐 담을 리스트
    num |= set(map(int, map("".join, permutations(list(numbers), i+1 ))))
num -= set(range(0, 2)) #소수가 아닌 0과 1은 제외(집합끼리 연산가능)

```

 우선 permutations부분은 동일하다, 다만 range()에서 범위에 +1을 하는 것보다 i+1이 하는 것이 깔끔해보여서 약간 수정했다. 

그 앞에 join함수가 있는데, 이것은 앞의 구분자로 permutations가 반환하는 원소들을 문자열로 합치는 기능을 한다.

이때  permutations는 반환값이 튜플이므로 map()함수를 이용했다.

이것을 바로 int형으로 변환하기 위해 또 한번  map()으로 감싸주었고, 여기서 중복 숫자를 지우기 위해  set()으로 만들었다. '|='는 비트연산자인데, 집합에서는 합집합 연산을 한다.

애초에 num을 set형으로 생성했기 때문에 이와 같은 연산이 가능하다.

따라서 i가 바뀌면서 생성되는 값을 계속해서 num에 넣을 수 있다.

num에 값을 다 넣고 나서 0과 1은 소수가 될 수 없으므로 차집합 연산을 이용해 빼주었다.

다른 코드를 통해 join()과 집합 연산, 그리고  map()의 사용까지 배우게 되었다.

</br>

### 요약정리

**'구분자'.join(리스트)**

* 문자열 합치기
* ''구분자''로 문자열을 합침
  * 그냥 연결하려면 "".join(리스트) 로 사용

**map()**

* map(function, iterable)
  * int, str : 형변환 함수이므로 function자리에 넣어서 사용가능
  * 각각의 iterable들을 function에 넣어서 함수를 수행함
* map 함수의 반환 값은 map객체이기 때문에 해당 자료형을 list 혹은 tuple로 형 변환시켜주어야 함

**집합 연산**

* & 연산자 : 교집합(intersection)
* | 연산자 : 합집합(union)
* \- 연산자 : 차집합(difference)
