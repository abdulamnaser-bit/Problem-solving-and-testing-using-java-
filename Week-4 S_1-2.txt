t = int(input())

for _ in range(t):
    s = input().strip()
    n = len(s)

    freq = [0] * 26

    # Left half
    for i in range(n // 2):
        freq[ord(s[i]) - ord('a')] += 1

    # Right half
    # (n + 1) // 2 skips the middle character for odd length
    for i in range((n + 1) // 2, n):
        freq[ord(s[i]) - ord('a')] -= 1

    if all(x == 0 for x in freq):
        print("YES")
    else:
        print("NO")
