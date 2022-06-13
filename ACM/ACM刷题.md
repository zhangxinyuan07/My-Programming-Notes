# [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

```C++
#include <stack>
#include <iostream>
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;

struct TreeNode
{
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution
{
private:
    unordered_map<int, int> index;

public:
    // 左闭右闭区间
    TreeNode *helper(vector<int> &preorder, vector<int> &inorder, int preorderLeft, int preorderRight, int inorderLeft, int inorderRight)
    {
        if (preorderRight < preorderLeft)
            return NULL;

        // 前序遍历的第一个数就是根节点
        int val = preorder[preorderLeft];
        TreeNode *root = new TreeNode(val);

        // 中序遍历数组中root左侧就是左子树, 右侧就是右子树, 这里用mid来定位root在中序数组中的位置
        int mid = index[val];

        // 确定左子树的长度, 方便下面确定前序数组的左右边界
        int leftTreeLength = mid - inorderLeft;

        // 先序遍历中 [从 左边界 + 1 开始的 leftTreeLength] 个元素就对应了中序遍历中 [从 左边界 开始到根节点定位 - 1] 的元素
        root->left = helper(preorder, inorder, preorderLeft + 1, preorderLeft + leftTreeLength, inorderLeft, mid - 1);
        // 先序遍历中 [从 左边界+1+左子树节点数目 开始到 右边界] 的元素就对应了中序遍历中 [从 根节点定位+1 到 右边界] 的元素
        root->right = helper(preorder, inorder, preorderLeft + 1 + leftTreeLength, preorderRight, mid + 1, inorderRight);

        return root;
    }

    TreeNode *buildTree(vector<int> &preorder, vector<int> &inorder)
    {
        int n = preorder.size();
        for (int i = 0; i < n; i++)
        {
            index[inorder[i]] = i;
        }

        return helper(preorder, inorder, 0, n - 1, 0, n - 1); // 左闭右闭区间
    }
};

// 前序遍历
void PreorderTraversal(TreeNode *root, vector<int> &res)
{
    if (root == nullptr)
        return;

    res.push_back(root->val);
    PreorderTraversal(root->left, res);
    PreorderTraversal(root->right, res);
}

// 中序遍历
vector<int> InorderTraversal(TreeNode *root)
{
    stack<TreeNode *> stk;
    if (root)
        stk.push(root);
    vector<int> res;

    while (!stk.empty())
    {
        TreeNode *cur = stk.top();
        stk.pop();
        if (cur)
        {
            if (cur->right)
                stk.push(cur->right); // 右

            stk.push(cur);
            stk.push(nullptr); // 中

            if (cur->left)
                stk.push(cur->left); // 左
        }
        else
        {
            cur = stk.top();
            stk.pop();

            res.push_back(cur->val);
        }
    }
    return res;
}

// 用数组构造二叉树
TreeNode *ConstructBinaryTree(const vector<int> &vec)
{
    vector<TreeNode *> vecTree(vec.size(), nullptr);
    for (int i = 0; i < vec.size(); i++)
    {
        TreeNode *node = nullptr;
        if (vec[i] != -1)
            node = new TreeNode(vec[i]);

        vecTree[i] = node;
    }

    for (int i = 0; i * 2 + 2 < vec.size(); i++)
    {
        if (vecTree[i] != nullptr)
        {
            vecTree[i]->left = vecTree[i * 2 + 1];
            vecTree[i]->right = vecTree[i * 2 + 2];
        }
    }

    return vecTree[0];
}

// 判断两棵二叉树是否相等
bool IsSameTree(TreeNode *root1, TreeNode *root2)
{
    TreeNode *p1 = root1, *p2 = root2;

    if (p1 == nullptr && p2 == nullptr)
        return true;
    else if (p1 == nullptr || p2 == nullptr)
        return false;
    else if (p1->val != p2->val)
        return false;
    else
        return IsSameTree(p1->left, p2->left) && IsSameTree(p1->right, p2->right);
}

//遍历打印一维数组
void printVector(vector<int> &v)
{

    for (auto it = v.begin(); it != v.end(); it++)
    {
        cout << *it << " ";
    }
    cout << endl;
}

// 层序打印打印二叉树
void PrintBinaryTree(TreeNode *root)
{
    queue<TreeNode *> que;
    if (root != NULL)
        que.push(root);
    while (!que.empty())
    {
        int size = que.size();
        for (int i = 0; i < size; i++)
        {
            TreeNode *node = que.front();
            que.pop();
            if (node != NULL)
            {
                cout << node->val << " ";
                que.push(node->left);
                que.push(node->right);
            }
            // 这里的处理逻辑是为了把null节点打印出来，用-1 表示null
            else
                cout << "-1 ";
        }
        cout << endl;
    }
}

int main()
{
    // 先构造测试用例
    vector<int> vec = {3, 9, 20, -1, -1, 15, 7};
    TreeNode *root = ConstructBinaryTree(vec);
    cout << "构造的二叉树: " << endl;
    PrintBinaryTree(root);

    // 根据构造的二叉树生成前序和中序数组
    vector<int> preorder;
    PreorderTraversal(root, preorder);
    vector<int> inorder = InorderTraversal(root);

    cout << "前序数组: " << endl;
    printVector(preorder);
    cout << "中序数组: " << endl;
    printVector(inorder);

    // 根据前序和中序数组构造二叉树
    Solution solve;
    TreeNode *res = solve.buildTree(preorder, inorder);

    if (IsSameTree(root, res))
        cout << "做对了" << endl;
    else
        cout << "做错了" << endl;


    system("pause");
    return 0;
}

输出: 

构造的二叉树: 
3
9 20
-1 -1 15 7
-1 -1 -1 -1
前序数组:
3 9 20 15 7
中序数组:
9 3 15 20 7
做对了
Press any key to continue . . . 
```

