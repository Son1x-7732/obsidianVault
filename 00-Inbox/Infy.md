# Quantum Vault Heist

given is a array sec (size N) decryption time of N linear data vaults. select contiguous block of exactly k vaults to breach. base decryption time for this block is sum of decryption time of k vaults inside it. However you possess one Quantum swapper. before decryption process begins you must use it to swap exactly one vault currently inside your choosen contiguous block with exactly one vault strictly outside your block. the vault swapped out is removed from your total decryption time and the vault swapped is added to your total decryption time, and the vault swapped is added to your total decryption time. find minimum possible total decryption time required to breach a block of k vaults after performing exactly one swap??

test cases
5 3 10 20 30 40 5 -> 35
4 2 100 20 50 200 -> 70
6 4 1 2 3 4 5 6 -> 10

# Endpoint Minimum

given array 'a' of n integers and an integer M. You may replace any a[i] by any integer x between 0 and M paying |a[i] - x} find minimum total cost such that in every non empty contiguous array, at least one end point is equal to the minimum value of the subarray. 

test cases
4 3 0 3 0 3 -> 3
3 3 1 3 2 -> 0
3 3 3 1 3 -> 2

# Digit Sum coprimality

Given decimal integer n and m.
count integer x such that 1 <= x <= n, x has exactly m non zero decimal digits, and gcd(x, digitSum(x)) = 1
find count modulo 10 ^ 9 + 7

test cases
20, 1 -> 2
30, 2 -> 9


# Nodes stuff

array with acceleration given n nodes.

velocity output = vel * (i + 1)

node with max base velocity among all other nodes (exactly node i) is shorted out, reducing output to 0. if there is tie for max base velocity among other nodes exactly one of them is shorted out and output becomes zero
find max total velocity the array can output to slingshot exactly one node.

test case
3 10 20 30 -> 100
4 5 5 5 5 -> 30
2 150 1 -> 150

