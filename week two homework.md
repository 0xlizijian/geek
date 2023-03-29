811. 子域名访问计数
思路：
遍历数组，切割域名及访问次数，使用哈希表建立域名及其子域名到访问次数的映射。遍历哈希表，将哈希表中的键值对转化为访问次数+域名的数组，并添加到ans中。
```cpp
class Solution {
public:
    vector<string> subdomainVisits(vector<string>& cpdomains) {
        for (auto& cpdomain : cpdomains) {
            int space = cpdomain.find(' ');
            int num = stoi(cpdomain.substr(0, space));//将数字切割出来, 并转换为int类型
            for (int i = space; i < cpdomain.size(); i++) {
                if (cpdomain[i] == ' ' || cpdomain[i] == '.')//判断域名及子域名
                    h[cpdomain.substr(i+1)] += num;
            }
            
        }
        vector<string> ans;
        for (auto& [cpdomain, num] : h) {//遍历域名及其访问次数
            ans.push_back(to_string(num) + ' ' + cpdomain);
        }
        return ans;
    }
private:
    unordered_map<string, int> h;
};
```
复杂度分析
时间复杂度：O(L), 其中L是数组cpdomains中所有字符串长度之和。遍历数组中的域名并分割子域名需要O(L)的时间。
空间复杂度：O(L), 其中L是数组cpdomains中所有字符串长度之和。哈希表需要O(L)的空间。



697. 数组的度
思路：
使用哈希表建立数组中不同元素到频次、首次出现的下标、最后出现的下标之间的映射。然后遍历哈希表，找到频次最多、前后位置最短的元素，并计算长度。
```cpp
class Solution {
public:
    int findShortestSubArray(vector<int>& nums) {
        for (int i = 0; i < nums.size(); i++) {
            if (h.count(nums[i])) {
                h[nums[i]][0]++;
                if (h[nums[i]][0] == 1) {//看nums[i]是否为第一次出现，若第一次出现则记录i
                    h[nums[i]][1] = i;
                }
                h[nums[i]][2] = i;//记录nums[i]最后一次出现的i
            }
            else {
                h[nums[i]] = {1, i, i};//nums[1]只出现一次直接赋值
            }
        }
        int maxNum = 0, minlen = 0;
        //频次最多的数maxNum，若有多个nums[i]频次相同，记录长度最短的minlen
        for (auto& [unordered_map, vec] : h) {
            if (vec[0] > maxNum) {
                maxNum = vec[0];
                minlen = vec[2] - vec[1] + 1;
            }
            else if (vec[0] == maxNum) {
                if (vec[2] - vec[1] + 1 < minlen) {
                    minlen = vec[2] - vec[1] + 1;
                }
            }
        }
        return minlen;
    }
private:
    unordered_map<int, vector<int>> h;
};
```
复杂度分析：
时间复杂度 O(n)，其中，n是原数组长度。遍历数组及哈希表需要O(n)的时间。
空间复杂度 O(n)，其中，n是原数组长度。最坏情况原数组中每个元素都不相同，哈希表需要O(n)的空间。

