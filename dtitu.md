
- **NetID:** dtitu
- **Major:** CS
- **Question that I chose from the memoization exercise:** Cutting rod
- **Link to the question article:** [Paste the URL to the question article]
- **My solution:**
```c /*
 * CS 211 - Module 07b Lecture Activity, Part 1: Memoization
 * Problem: Cutting a Rod
 *
 * Given a rod of length n and price[i] = price of a piece of length i+1,
 * find the maximum revenue from cutting the rod into pieces.
 *
 * Approach: top-down recursion + memoization.
 *   best(len) = max over cut = 1..len of price[cut-1] + best(len - cut)
 * memo[len] caches best(len) so each subproblem is solved once:
 *   plain recursion O(2^n)  ->  memoized O(n^2)
 *
 *   - memo must be initialised to a sentinel (-1). Initialising to 0 is wrong
 *     because 0 is a valid answer (best(0) = 0), so cached results are confused
 *     with "not computed yet".
 *   - price[] is 0-indexed, so a piece of length `cut` is price[cut - 1].
 *   - Don't forget the base case best(0) = 0, or the recursion never stops.
 */
#include <stdio.h>
#include <stdlib.h>

static int max(int a, int b) { return a > b ? a : b; }

static int best(int len, const int price[], int memo[]) {
    if (len == 0) return 0;               /* base case: no rod, no revenue */
    if (memo[len] != -1) return memo[len];/* already solved, reuse it */

    int result = 0;
    for (int cut = 1; cut <= len; cut++)
        result = max(result, price[cut - 1] + best(len - cut, price, memo));

    memo[len] = result;                   /* store before returning */
    return result;
}

int cutRod(const int price[], int n) {
    int *memo = malloc((n + 1) * sizeof(int));
    if (!memo) return -1;
    for (int i = 0; i <= n; i++) memo[i] = -1;
    int answer = best(n, price, memo);
    free(memo);
    return answer;
}

int main(void) {
    int p1[] = {1, 5, 8, 9, 10, 17, 17, 20};   /* expected 22 (2 + 6) */
    int p2[] = {3, 5, 8, 9, 10, 17, 17, 20};   /* expected 24 (eight 1s) */
    int p3[] = {2, 5, 7, 8};                   /* n=4 -> expected 10 (2 + 2) */

    printf("n=8, prices 1 5 8 9 10 17 17 20 -> %d (expected 22)\n", cutRod(p1, 8));
    printf("n=8, prices 3 5 8 9 10 17 17 20 -> %d (expected 24)\n", cutRod(p2, 8));
    printf("n=4, prices 2 5 7 8             -> %d (expected 10)\n", cutRod(p3, 4));
    printf("n=1, prices 2                   -> %d (expected 2)\n", cutRod(p3, 1));
    return 0;
}
