import sys

def main():
    data = sys.stdin.read().strip().split()
    if not data:
        return
    it = iter(data)
    N = int(next(it))
    G = int(next(it))
    
    A = [int(next(it)) for _ in range(N)]
    B = [int(next(it)) for _ in range(G)]
    
    A.sort()
    B.sort()
    
    total = 0
    j = 0
    for a in A:
        # skip clearances that open before this convoy arrives
        while j < G and B[j] < a:
            j += 1
        # guaranteed valid assignment, so j < G
        total += B[j] - a
        j += 1
    
    print(total)

if __name__ == "__main__":
    main()
