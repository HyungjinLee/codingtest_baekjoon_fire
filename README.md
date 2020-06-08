# codingtest_baekjoon_fire
bfs - 기준이 여러 개 

문제 : https://www.acmicpc.net/problem/4179

### 풀이 법

1. 맵을 스캔하여 사람의 위치, 불의 위치를 각각 큐 q, fire에 담아준다.
2. 시작점으로부터 bfs 시작한다.
3. 불에 대해 bfs를 돌고, 이어서 바로 사람의 위치에 대해 bfs를 실시한다.
4. 결과를 출력한다.
  - 4-1. 탈출하지 못하는 경우 'IMPOSSIBLE'
  - 4-2. 도중에 탈출하는 경우 최소거리를 출력한다.
 
### KEY POINT

* 틀린 코드

 ``` python
from collections import deque

R,C = map(int,input().split())
b = [input() for _ in range (R)]
v = [[0] * C for _ in range (R)]
fire = deque()

def bfs(i,j) :
    q = deque()
    q.append((i,j,0))
    v[i][j] = 1 # 지훈이가 지나간 자리
    
    while q :
        x,y,t = q.popleft()
        flen = len(fire)
        
        # 불 확산
        while flen>0 :
            fx,fy = fire.popleft()
            for n,m in (-1,0), (1,0), (0,-1), (0,1) :
                nfx,nfy = fx+n, fy+m
                if 0 <= nfx < R and 0 <= nfy < C and (v[nfx][nfy] == 0 or v[nfx][nfy] == 2):
                    fire.append((nfx,nfy))
                    v[nfx][nfy] = 1
            flen-=1
                    
        # 지훈이 움직임
        for n,m in (-1,0),(1,0),(0,-1),(0,1) :
            nx,ny = x+n,y+m
            if nx<0 or nx>=R or ny<0 or ny>=C : #탈출
                return t+1
            if b[nx][ny] == '.' and not v[nx][ny]:
                q.append((nx,ny,t+1))
                v[nx][ny] = 1
    return -1
answer = -1

for i in range (R) :
    for j in range (C) :
        if b[i][j] == 'J' :
            sx,sy = i,j
        elif b[i][j] == 'F' :
            fire.append((i,j))
            v[i][j] = 2 # 불이 지나간 자리

answer = bfs(sx,sy)
if answer == -1 :
    print('IMPOSSIBLE')
else :
    print(answer)
```

불 bfs 한 스탭(현재 fire에 있는 모든 좌표에 대해 인접 검사) 돌고 나서 지훈이의 현재 좌표에 대해 bfs를 돌려줌
아니 답은 맞게 나오는데 왜 틀린거? (맞왜틀) ㅎㅎㅎㅎ
3시간 넘게 고민하고... 이유를 몰라서 다른 코드를 참조했다.

* 통과한 코드
``` python
from collections import deque

R,C = map(int,input().split())
b = [input() for _ in range (R)]
v = [[0] * C for _ in range (R)]
fire = deque()

def bfs(i,j) :
    q = deque()
    q.append((i,j,0))
    v[i][j] = 2
    
    while q :
        flen = len(fire)
        qlen = len(q)
        
        # 불 확산
        if fire :
            for i in range (flen) :    
                fx,fy = fire.popleft()
                for n,m in (-1,0), (1,0), (0,-1), (0,1) :
                    nfx,nfy = fx+n, fy+m
                    if 0 <= nfx < R and 0 <= nfy < C and (v[nfx][nfy] == 0 or v[nfx][nfy] == 2):
                        fire.append((nfx,nfy))
                        v[nfx][nfy] = 1
        
        # 지훈이 움직임
        for j in range (qlen) :
            x,y,t = q.popleft()
            
            for n,m in (-1,0),(1,0),(0,-1),(0,1) :
                nx,ny = x+n,y+m
                if nx<0 or nx>=R or ny<0 or ny>=C : #탈출
                    return t+1
                if not v[nx][ny]:
                    q.append((nx,ny,t+1))
                    v[nx][ny] = 2
    return -1

answer = -1
for i in range (R) :
    for j in range (C) :
        if b[i][j] == 'J' :
            sx,sy = i,j
        elif b[i][j] == 'F' :
            fire.append((i,j))
            v[i][j] = 1 # 불이 지나간 자리
        elif b[i][j] == '#' :
            v[i][j] = 1

answer = bfs(sx,sy)
if answer == -1 :
    print('IMPOSSIBLE')
else :
    print(answer)

```

코드의 차이점은 다음과 같다.
통과한 코드는, 불에 대해 bfs 한 스탭을 돌고 나서 지훈의 현재 좌표만이 아닌  **현재 지훈이 큐에 있는 모든 좌표**에 대해 bfs를 수행해준다.


참고 : 백준 온라인 저지
