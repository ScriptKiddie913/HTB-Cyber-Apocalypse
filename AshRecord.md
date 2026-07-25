import sys

def main():
    data = sys.stdin.read().strip().split()
    if not data:
        return
    it = iter(data)
    
    N = int(next(it))
    P = int(next(it))
    min_gap = int(next(it))
    
    seq = [next(it) for _ in range(P)]
    
    residues = []
    for _ in range(N):
        t = int(next(it))
        mat = next(it)
        residues.append((t, mat))
    
    # Sort by timestamp
    residues.sort(key=lambda x: x[0])
    
    idx = 0
    last_timestamp = -10**9  # effectively -inf
    matched = 0
    
    for i in range(P):
        required = seq[i]
        threshold = 0 if i == 0 else last_timestamp + min_gap
        
        found = False
        while idx < N:
            t, mat = residues[idx]
            if t >= threshold and mat == required:
                # use this residue
                last_timestamp = t
                idx += 1
                found = True
                matched += 1
                break
            idx += 1
        
        if not found:
            break
    
    print(matched)

if __name__ == "__main__":
    main()
