import sys

input = sys.stdin.readline

n, m, q = map(int, input().split())

g = [[] for _ in range(n)]

for eid in range(m):
    u, v, w = map(int, input().split())

    g[u].append((v, w, eid))
    g[v].append((u, w, eid))

comp = [-1] * n
xr = [0] * n
bases = []

def insert_basis(b, value):
    for bit in range(5, -1, -1):
        if value & (1 << bit):
            if b[bit]:
                value ^= b[bit]
            else:
                b[bit] = value
                return

def can_make(b, value):
    for bit in range(5, -1, -1):
        if value & (1 << bit):
            if b[bit]:
                value ^= b[bit]
            else:
                return False

    return True

component_count = 0

for start in range(n):
    if comp[start] != -1:
        continue

    basis = [0] * 6
    bases.append(basis)

    comp[start] = component_count
    xr[start] = 0

    stack = [(start, -1)]

    while stack:
        u, parent_edge = stack.pop()

        for v, w, eid in g[u]:
            if eid == parent_edge:
                continue

            if comp[v] == -1:
                comp[v] = component_count
                xr[v] = xr[u] ^ w
                stack.append((v, eid))
            else:
                cycle_value = xr[u] ^ w ^ xr[v]
                insert_basis(basis, cycle_value)

    component_count += 1

for _ in range(q):
    u, v, target = map(int, input().split())

    if comp[u] != comp[v]:
        print("NO")
        continue

    required = xr[u] ^ xr[v] ^ target
    basis = bases[comp[u]]

    if can_make(basis, required):
        print("YES")
    else:
        print("NO")
