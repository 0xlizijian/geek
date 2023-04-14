210. 课程表 II  

思路：  
将每一门课程看成一个节点，对于每一个[A, B]，建立一条从B到A的有项边，表示A的先修课程，遍历所有的数组可以得到一张有向图，先判段该有向图是否有环，若无环，求出该图的拓扑排序，就可以得到一种符合要求的课程学习顺序。  
解法一：广度优先搜索  
考虑拓扑排序中最前面的节点，该节点入度为0，可以直接修，加入答案数组ans中。修完该节点后，可移除该节点的所有出边，同时这些出边的入度-1，当某一出边入度为0，就可以直接修。当所有能修的课程修完后，如果ans的长度小于课程数，则存在环，反之ans即为一个拓扑排序。
```cpp
//c++
class Solution {
public:
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        to = vector<vector<int>>(numCourses, vector<int>());
        inDeg = vector<int>(numCourses, 0);
        
        for (vector<int>& pre : prerequisites) {
            int x = pre[0];
            int y = pre[1];
            //加边，同时入度+1
            to[y].push_back(x);
            inDeg[x]++;
        }
        queue<int> q;
        //将所有入度为0的点放入队列中，这些课程无前置课程，可以直接修
        for (int i = 0; i < numCourses; i++) 
            if (inDeg[i] == 0) q.push(i);
        
        vector<int> lessons;
        while (!q.empty()) {
            int x = q.front();//队头是目前准备修的课程
            q.pop();
            lessons.push_back(x);
            for (int y : to[x]) {
                inDeg[y]--;//y的前置课程-1
                if (inDeg[y] == 0) {//y可以修了
                    q.push(y);
                }
            }
        }
        
        if (lessons.size() == numCourses) return lessons;//所有课程都修完了
        return {};//有环
    }
private:
    vector<vector<int>> to;//出边数组，储存有向图
    vector<int> inDeg;//储存每个节点的入度
};
```
复杂度分析：  
时间复杂度 O(n + m)：其中n为课程数，m为先修课程要求数。  
空间复杂度 O(n + m)：邻接表存图需要O(n + m)的空间  
解法二：深度优先搜索
```cpp
//c++
class Solution {
public:
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        to = vector<vector<int>>(numCourses, vector<int>());
        visited = vector<int>(numCourses, 0);
        for (vector<int>& pre : prerequisites) {
            int x = pre[0];
            int y = pre[1];
            to[y].push_back(x);//加边
        }
        //每次挑选一个未搜索状态的节点，开始dfs
        for (int i = 0; i < numCourses; i++) {
            if (!visited[i]) {
                dfs(i);
            }
        }
        if (!valid) return {};
        reverse(ans.begin(), ans.end());
        return ans;
    }
private:
    vector<vector<int>> to;//出边数组，储存有向图
    vector<int> visited;//标记每个节点的状态：0 = 未搜索，1 = 搜索中，2 = 已完成
    vector<int> ans;//用数组模拟栈
    bool valid = true;//标记图中是否有环

    void dfs(int x) {
        visited[x] = 1;//将节点标记为搜索中
        //搜索其相邻节点，若发现环直接退出
        for (int y : to[x]) {
            if (visited[y] == 0) {//未搜索状态，搜索其相邻节点
                dfs(y);
                if (!valid) {
                    return;
                }
            }
            else if (visited[y] == 1) {//搜索中状态，表示有环
                valid = false;
                return;
            }
        }
        visited[x] = 2;//该节点搜索完成
        ans.push_back(x);
    }
};
```
复杂度分析：  
时间复杂度 O(n + m)：其中n为课程数，m为先修课程要求数。  
空间复杂度 O(n + m)：邻接表存图需要O(n + m)的空间。  
  
  

538. 把二叉搜索树转换为累加树  

思路：  
二叉搜索树的中序遍历是一个单调递增的有序序列，累加树中的每个节点的值即为有序序列中所有比该节点大的数和它本身之和，但中序遍历是从最小的数开始遍历，因此我们可以反序中序遍历，同时用sum记录过程中节点的值，并不断更新到当前节点的值。数的中序遍历有三种方法，因此本题的反序中序遍历也有三种。
```cpp
//c++解法1
class Solution {
public:
    TreeNode* convertBST(TreeNode* root) {
        if (root != nullptr) {
            convertBST(root->right);
            sum += root->val;
            root->val = sum;
            convertBST(root->left);
        }
        return root;
    }
private:
    int sum = 0;
};
```
复杂度分析：  
时间复杂度 O(n)：其中，n是二叉树节点数，每个节点只遍历一次。  
空间复杂度 O(n)：最坏情况下二叉树退化为链表，空间复杂度为O(n)。  
```cpp
//c++解法2
class Solution {
public:
    TreeNode* convertBST(TreeNode* root) {
        int sum = 0;
        TreeNode* curr = root;
        stack<TreeNode*> s;
        while (curr != nullptr || !s.empty()) {
            while (curr != nullptr) {
                s.push(curr);
                curr = curr->right;
            }
            curr = s.top();
            s.pop();
            sum += curr->val;
            curr->val = sum;
            curr = curr->left;
        }
        return root;
    }
};
```
复杂度分析：  
时间复杂度 O(n)：其中，n是二叉树节点数，每个节点只遍历一次。  
空间复杂度 O(n)：最坏情况下二叉树退化为链表，空间复杂度为O(n)。  
```cpp
//c++解法3
class Solution {
public:
    TreeNode* convertBST(TreeNode* root) {
        int sum = 0;
        TreeNode* curr = root;
        while (curr != nullptr) {
            if (curr->right == nullptr) {
                sum += curr->val;
                curr->val = sum;
                curr = curr->left;
            } else {
                TreeNode* pre = curr->right;
                while (pre->left != nullptr && pre->left != curr) 
                    pre = pre->left;
                if (pre->left == nullptr) {
                    pre->left = curr;
                    curr = curr->right;
                } else {
                    pre->left = nullptr;
                    sum += curr->val;
                    curr->val = sum;
                    curr = curr->left;
                }
            }
        }
        return root;
    }
};
```
复杂度分析：  
时间复杂度 O(n)：其中，n是二叉树节点数，没有左子树的节点只被访问一次，其他节点被访问两次。  
空间复杂度 O(1)：只操作已存在的指针，因此只需要常数的额外空间。 
