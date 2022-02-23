# 완전 탐색(Brute-force Search)

## 완전 탐색이란?
가능한 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>경우의 수</span>를 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>모두 탐색</span>해 보는 것

### 💡 배열에서 서로 다른 수 2개를 더해서 나올 수 있는 가장 큰 값은?

#### 1.
```java
int n = arr.length;
int result = 0;

for(int i = 0; i < n; i++){
	for(int j = 0; j < n; j++){
        if(i != j){
        	result = max(result, arr[i]+arr[j]);
        }
    }
}   

return result;
```
위의 코드는 이중 for문으로 모든 경우의 수를 다 시도하고 있다. 
👉 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>시간복잡도는 O(N^2)</span>가 된다.

#### 2.
```java
int n = arr.length;
Arrays.sort(arr)

return arr[n-1] + arr[n-2];    
```
위의 코드는 정렬을 한 다음에 가장 큰 값과 그 다음으로 큰 값을 더했다.
👉 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>시간복잡도는 O(NlogN)</span>이 된다.

### 참고
	
**[완전 탐색(Brute-force Search)](https://blog.naver.com/PostView.naver?blogId=kks227&logNo=220769870195&categoryNo=299&parentCategoryNo=0&viewDate=&currentPage=13&postListTopCurrentPage=1&from=postList&userTopListOpen=true&userTopListCount=5&userTopListManageOpen=false&userTopListCurrentPage=13)**

**[완전 탐색 (Brute-Force Search / Exhaustive Search) 알고리즘](https://rebro.kr/59)**
