1011. 在 D 天内送达包裹的能力  
思路：二分答案  
假设当船的运载能力为x时，运送天数为days，则运载能力大于x时，运送天数小于等于days。  
因此，对于每一次任务，存在一个最小的x，当船的运载能力大于等于minX时，运送天数小于等于days，此时视为合法。当船的运载能力小于minX时，运送天数大于days，此时视为不合法。  
minX即为本题的答案。我们可以用二分法查找minX。对于每一次查找，通过判定当船的运载能力为x时，运送天数day是否小于等于days，我们可找到minX。  
```cpp
//c++
class Solution {
public:
    int shipWithinDays(vector<int>& weights, int days) {
        //二分查找
        int left = 0, right = 0;//左边界为max(weights)，即每天只运送一个包裹, 右边界为sum(weights), 即一天运送所有包裹。
        for (int weight : weights) {
            left = max(left, weight);
            right += weight;
        }
        while (left < right) {
            int mid = (left + right) / 2;
            if (validate(weights, days, mid)) {//判定mid是否合法，若合法，则minX在 [left, mid] 内
                right = mid;
            } else {//若不合法，则minX在 [mid + 1, right] 内
                left = mid + 1;
            }
        }
        return right;
    }
private:
    bool validate(vector<int>& weights, int days, int size) {
        int day = 1;
        int load = 0;
        for (int weight : weights) {
            if (load + weight <= size) {//小于size则可以一天运完
                load += weight;
            } else {//大于size下一天运
                day++;
                load = weight;
            }
        }
        return day <= days;
    }
};
```
复杂度分析：  
时间复杂度 O(nlogw)：其中n是数组weights的长度，w是数组weights中元素的和。二分查找需要查找的次数为O(logw)，每次查找时进行判定需要遍历数组weights一次。  
空间复杂度 O(1)：没有使用新的数组，因此只需要常数的空间。  
  
******  
  
74. 搜索二维矩阵  
方法一：两次二分查找  
思路：  
由于每行元素从左到右升序排列，且每行第一个整数大于前一行的最后一个整数，因此该矩阵第一列元素从上至下升序排列。  
因此我们可以先用一次二分找到该矩阵第一列中小于等于target的最大值并记录其所在行(如果target存在则必在该行)，然后再用一次二分查找target是否在该行。  
```cpp
//c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix[0][0] == target) return true;
        int left = 0, right = matrix.size() - 1;//从第一行到最后一行内查找，左边界为数组下标初始位置0，右边界为二维数组的行数
        while (left < right) {
            int mid = (left + right + 1) / 2;
            if (matrix[mid][0] <= target) {//小于等于target在mid右边界查找，mid自己也可能是答案
                left = mid;
            } else {//大于target在mid左边界查找
                right = mid - 1;
            }
        }
        int row = right;
        left = 0, right = matrix[0].size() - 1;//从第一列到最后一列内查找，左边界为数组下标初始位置0，右边界为二维数组的列数
        while (left <= right) {
            int mid = (left + right) / 2;
            if (matrix[row][mid] == target) return true;//找到target返回true
            if (matrix[row][mid] > target) {//大于target在mid的左侧 [left, mid - 1] 内查找
                right = mid - 1;
            } else {//小于target在mid的右侧 [mid + 1, right] 内查找
                left = mid + 1;
            }
        }
        return false;
    }
};
```
复杂度分析：  
时间复杂度 O(lognm)：其中n是矩阵的行数，m是矩阵的列数。第一次二分查找需要查找的次数为O(logn)，第二次二分查找需要查找的次数为O(logm)。  
空间复杂度 O(1)：没有使用新的数组，因此只需要常数的空间。  
  
方法二：一次二分查找  
思路：  
由于每行元素从左到右升序排列，且每行第一个整数大于前一行的最后一个整数，则该矩阵可视为一个升序的一维数组。一维数组中下标为i的元素即为原矩阵中下标为[i / col][i % col]的元素（其中col为原矩阵的列数）。 
```cpp
//c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix[0][0] == target) return true;
        int row = matrix.size(), col = matrix[0].size();
        int left = 0, right = row*col - 1;//在所有元素中查找，左侧为数组下标初始位置0，右侧为二维数组中元素的个数
        while (left <= right) {
            int mid = (left + right) / 2;
            if (matrix[mid / col][mid % col] == target) return true;//找到target返回true
            if (matrix[mid / col][mid % col] > target) {//大于target在mid的左侧 [left, mid - 1] 内查找
                right = mid - 1;
            } else {//小于target在mid的右侧 [mid + 1, right] 内查找
                left = mid + 1;
            }
        }
        return false;
    }
};
```
复杂度分析：  
时间复杂度 O(lognm)：其中n是矩阵的行数，m是矩阵的列数。二分查找需要查找的次数为O(lognm)。  
空间复杂度 O(1)：没有使用新的数组，因此只需要常数的空间。  