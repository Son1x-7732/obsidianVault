# Prefix Sum

```cpp
int subarraySum(vector<int>& nums, int k) {

    unordered_map<int,int> mp;

    mp[0] = 1;

    int currSum = 0;
    int count = 0;

    for(int num : nums) {

        currSum += num;

        if(mp.find(currSum - k) != mp.end()) {
            count += mp[currSum - k];
        }

        mp[currSum]++;
    }

    return count;
}
```

