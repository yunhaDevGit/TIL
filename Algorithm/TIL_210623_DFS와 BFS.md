# DFS와 BFS

| 시간 제한 | 메모리 제한 | 제출   | 정답  | 맞은 사람 | 정답 비율 |
| :-------- | :---------- | :----- | :---- | :-------- | :-------- |
| 2 초      | 128 MB      | 135356 | 48541 | 27945     | 33.988%   |

## 문제

그래프를 DFS로 탐색한 결과와 BFS로 탐색한 결과를 출력하는 프로그램을 작성하시오. 단, 방문할 수 있는 정점이 여러 개인 경우에는 정점 번호가 작은 것을 먼저 방문하고, 더 이상 방문할 수 있는 점이 없는 경우 종료한다. 정점 번호는 1번부터 N번까지이다.

## 입력

첫째 줄에 정점의 개수 N(1 ≤ N ≤ 1,000), 간선의 개수 M(1 ≤ M ≤ 10,000), 탐색을 시작할 정점의 번호 V가 주어진다. 다음 M개의 줄에는 간선이 연결하는 두 정점의 번호가 주어진다. 어떤 두 정점 사이에 여러 개의 간선이 있을 수 있다. 입력으로 주어지는 간선은 양방향이다.

## 출력

첫째 줄에 DFS를 수행한 결과를, 그 다음 줄에는 BFS를 수행한 결과를 출력한다. V부터 방문된 점을 순서대로 출력하면 된다.

## 예제 입력 1 복사

```
4 5 1
1 2
1 3
1 4
2 4
3 4
```

## 예제 출력 1 복사

```
1 2 4 3
1 2 3 4
```

## 예제 입력 2 복사

```
5 5 3
5 4
5 2
1 2
3 4
3 1
```

## 예제 출력 2 복사

```
3 1 2 5 4
3 1 4 2 5
```

## 예제 입력 3 복사

```
1000 1 1000
999 1000
```

## 예제 출력 3 복사

```
1000 999
1000 999
```

```java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Iterator;
import java.util.LinkedList;
import java.util.StringTokenizer;

public class BFSAndDFS {

    static int N,M,V;
    static LinkedList<Integer> nodes[];

    BFSAndDFS(int n){
        nodes = new LinkedList[n];
        for(int i=0;i<n;i++){
            nodes[i] = new LinkedList<>();
        }
    }

    void addEdge(int startNode, int endNode){
        nodes[startNode].add(endNode);
    }

    static void BFS(int v){
        boolean visited[] = new boolean[N];
        LinkedList<Integer> queue = new LinkedList<>();

        visited[v] = true;
        queue.add(v);

        while(!queue.isEmpty()){
            v = queue.poll();
            System.out.print(v + " ");

            Iterator<Integer> nearNode = nodes[v].listIterator();
            while (nearNode.hasNext()){
                int n = nearNode.next();
                if(!visited[n]){
                    visited[n] = true;
                    queue.add(n);
                }
            }
        }

    }

    static void DFS(int v){

    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken()); // 정점 개수
        M = Integer.parseInt(st.nextToken()); // 간선 개수
        V = Integer.parseInt(st.nextToken()); // 시작할 정점

        BFSAndDFS bfsAndDFS = new BFSAndDFS(N);
        int startNode, endNode;

        for(int i=0;i<M;i++){
            st = new StringTokenizer(br.readLine());
            startNode = Integer.parseInt(st.nextToken());
            endNode = Integer.parseInt(st.nextToken());
            bfsAndDFS.addEdge(startNode,endNode);
        }
        BFS(V);
        DFS(V);
    }
}
```
