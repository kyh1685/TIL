## 분할 정복이란?
전체 문제를 여러 개의 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>부분 문제들로 분할</span> <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>각 부분 문제들을 해결(정복)</span>함을 통해 전체 문제의 해답을 도출하는 방식이다. 이는 Top-down approach로 볼 수 있다.

- 분할 (Divide) : 해결할 문제를 여러 개의 작은 부분으로 나눔
- 정복 (Conquer) : 나눈 부분 문제를 각각 해결
- 통합 (Combine) : 각 부분 문제의 해답을 모아서 전체 문제의 해답 도출

### 🤔 오히려 할 일이 많아지지 않을까?
분할 정복이 적용되는 경우는 그냥 풀었을 때보다 위의 방식처럼 이미 풀린 부분 문제들을 합치는 게 더 빠른 경우이다.

대표적인 예 : <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>병합 정렬(merge sort), 이분 탐색(binary search), 거듭제곱 연산</span> 등

### 문제를 2개로 분할하는 경우를 생각해보자
- 문제의 크기(보통 값의 개수)를 N이라 할 때 한 단계 분할 후 N은 N/2이다.
- 기저 사례가 N = 1인 문제라고 할 때, N은 log₂N 단계까지 분할된다.
  👉 기저 사례는 더 이상 쪼갤 수 없는 조건. 
  
>**🤔 log₂N 단계?**
>
>n을 노드의 개수, k를 깊이라고 한다면
>
k가 1인 경우 n = 1
k가 2인 경우 n = 1 + 2 = 3
k가 3인 경우 n = 1 + 2 + 4 = 7
>
깊이가 k인 경우 n = 1 + 2 + .... + 2^(k-1) = <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>2^k - 1</span>이 된다.
>
n개의 노드일 때 깊이가 k가 되므로 O(n) = k 로 정의할 수 있다.
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>n = 2^k - 1</span>이므로 log로 표현하면 log₂N = k가 되고, <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>O(n) = log₂N</span>이 된다.

### 병합 정렬
정복 알고리즘의 수행시간은 분할할 때  <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>나누어지는 문제의 개수</span>,  <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>분할 후 문제의 크기</span>, 각 문제마다  <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>병합(정복) 단계에서 걸리는 시간</span>이 결정한다.

병합 정렬의 경우 분할할 문제 크기를 N이라 할 때 각각 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>2</span>, <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>N/2</span>, <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>O(N)</span>이다. 병합 단계에서 걸리는 시간을 통해 최종 시간복잡도를 구할 수 있다.

- 분할해놓은 걸 역순으로 병합하면 k-1단계에서 2^(k-1)번, ... 2단계에서 2^2=4번, 1단계에서 2번, 0단계에서 1번의 병합을 해야한다.
- 각 병합에 걸리는 시간이 해당 문제 크기가 N일 때 O(N)이므로 연산 횟수는 아래처럼 된다.
  - 0단계 : 1 * O(N)
  - 1단계 : 2 * O(N/2)
  - 2단계 : 4 * O(N/4)
  - ...
  - m단계 : 2^m * O(N/(2^m)) = O(N)
- 최종적으로 각 단계에서 드는 총 연산량은 O(N)이며 단계는 총 O(logN)개 있으므로, 이를 곱해서 O(NlogN)이라는 시간복잡도를 구할 수 있다.

### 이분 탐색
>💡 이분 탐색 진행을 위해서는 자료가 **정렬된 상태**여야 한다.

정렬된 배열에서 특정 값 X를 찾고자 할 때, 이분 탐색을 이용하면 훨씬 빠르게 검색을 수행할 수 있다.

- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>분할</span>
  - 배열을 두 개의 부분 배열로 나눈다. 
  - X가 가운데에 있는 값보다 작으면 왼쪽, 크면 오른쪽 배열을 선택한다.
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>정복</span> 
  - 배열의 가운데 값이 X와 같은지 비교한다. 
  - 같지 않다면 분할 과정을 재귀적으로 수행한다.
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>통합</span>
  - 부분 배열에서 찾은 답이 곧 전체 해답이다.
  
_분할은 하지만 그 중 한 문제만 풀면 되기 때문에 시간복잡도는 O(logN)이다.
_


### 참고

**[분할 정복(Divide and Conquer)](https://blog.naver.com/PostView.naver?blogId=kks227&logNo=220776241154&categoryNo=299&parentCategoryNo=0&viewDate=&currentPage=13&postListTopCurrentPage=1&from=postList&userTopListOpen=true&userTopListCount=5&userTopListManageOpen=false&userTopListCurrentPage=13)**

**[Basic Knowledge of Computer Science](https://github.com/Seogeurim/CS-study/blob/main/contents/algorithm/basic.md#%EB%B6%84%ED%95%A0-%EC%A0%95%EB%B3%B5%EB%B2%95-divide-and-conquer)**
