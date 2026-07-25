import sys
from collections import deque

class Dinic:
    def __init__(self, n):
        self.n = n
        self.adj = [[] for _ in range(n)]
    
    def add_edge(self, u, v, cap):
        self.adj[u].append([v, cap, len(self.adj[v])])
        self.adj[v].append([u, 0, len(self.adj[u]) - 1])
    
    def bfs(self, s, t, level):
        for i in range(self.n):
            level[i] = -1
        q = deque()
        level[s] = 0
        q.append(s)
        while q:
            u = q.popleft()
            for v, cap, rev in self.adj[u]:
                if cap > 0 and level[v] == -1:
                    level[v] = level[u] + 1
                    q.append(v)
        return level[t] != -1
    
    def dfs(self, u, t, f, level, it):
        if u == t:
            return f
        while it[u] < len(self.adj[u]):
            v, cap, rev = self.adj[u][it[u]]
            if cap > 0 and level[v] == level[u] + 1:
                ret = self.dfs(v, t, min(f, cap), level, it)
                if ret > 0:
                    # update capacities
                    self.adj[u][it[u]][1] -= ret
                    self.adj[v][rev][1] += ret
                    return ret
            it[u] += 1
        return 0
    
    def max_flow(self, s, t):
        flow = 0
        INF = 10**9
        level = [-1] * self.n
        while self.bfs(s, t, level):
            it = [0] * self.n
            while True:
                f = self.dfs(s, t, INF, level, it)
                if f == 0:
                    break
                flow += f
        return flow


def main():
    data = sys.stdin.read().strip().split()
    if not data:
        return
    it = iter(data)
    N = int(next(it))
    E = int(next(it))
    S = int(next(it))
    T = int(next(it))
    
    # node splitting: for each original node i, in = i, out = i + N
    total_nodes = 2 * N
    dinic = Dinic(total_nodes)
    INF = 10**9
    
    # internal edges: i_in -> i_out
    for i in range(N):
        cap = INF if (i == S or i == T) else 1
        dinic.add_edge(i, i + N, cap)
    
    # original edges
    for _ in range(E):
        u = int(next(it))
        v = int(next(it))
        dinic.add_edge(u + N, v, INF)   # u_out -> v_in
    
    source = S          # S_in
    sink = T + N        # T_out
    ans = dinic.max_flow(source, sink)
    print(ans)

if __name__ == "__main__":
    main()
