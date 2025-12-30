# 資料結構完整筆記：樹與堆
---
## 目錄

### Part I: 樹 (Trees)
1. [樹的基本概念](#樹的基本概念)
2. [樹的術語與定義](#樹的術語與定義)
3. [樹的類型](#樹的類型)
4. [樹的遍歷](#樹的遍歷)
5. [樹的表示法](#樹的表示法)
6. [二元樹](#二元樹)
7. [二元搜尋樹 (BST)](#二元搜尋樹-bst)
8. [AVL 樹](#avl-樹)

### Part II: 堆 (Heaps)
9. [堆的定義與性質](#堆的定義與性質)
10. [堆的操作](#堆的操作)
11. [優先權隊列](#優先權隊列)

### Part III: 實作與應用
12. [完整C++實作](#完整c實作)
13. [練習題與解答](#練習題與解答)
14. [GitHub專案結構](#github專案結構)

---

# Part I: 樹 (Trees)

## 樹的基本概念

### 摘要
樹是**非線性階層式資料結構**,由節點與邊組成,具有一個根節點和零或多個子樹。根據PDF Page 1-11,樹是資料結構中表達階層關係的基礎,與自然樹不同,資料結構中的樹是倒置的(根在上,葉在下)。

### 重點整理

| 概念 | 說明 |
|------|------|
| **定義** | 非線性階層式資料結構,無環且連通 |
| **特性** | 每個節點最多一個父節點,任意兩節點間唯一路徑 |
| **倒置** | 根在上,葉在下,便於演算法執行和視覺理解 |
| **應用** | 檔案系統、編譯器AST、資料庫索引、DOM樹 |

### PDF原文定義 (Page 10)

> A tree is a non-linear hierarchical data structure consisting of nodes connected by edges. Each node can have zero or more child nodes, forming parent-child relationships. Trees have no cycles and are commonly used for representing hierarchical relationships.

### 加深的內容

**樹 vs 圖:**
樹是圖的特例,是一個連通且無環的圖。任意兩節點間有且僅有一條路徑。這個限制使得許多圖演算法在樹上可以簡化(例如最短路徑問題在樹中變為路徑查詢)。

**樹 vs 鏈結串列:**
鏈結串列是每個節點最多一個後繼的線性結構,樹則允許一個節點有多個子節點。轉換的關鍵是增加多個指標(如left, right for binary tree)。

### 額外內容

**變體與應用:**
- **森林(Forest):** 多棵不相交樹的集合,用於Disjoint Set Union
- **有序樹 vs 無序樹:** 子節點有/無特定順序
- **m-ary樹:** 每個節點最多m個子節點
- **實務:** Git提交圖(DAG)、組織結構、家族樹

### C++ 基礎實作

```cpp
// 樹節點基礎結構
template <typename T>
struct TreeNode {
    T data;
    std::vector<std::shared_ptr<TreeNode>> children;
    
    TreeNode(const T& val) : data(val) {}
    
    void addChild(std::shared_ptr<TreeNode> child) {
        children.push_back(child);
    }
};

// 簡單的前序遍歷
template <typename T>
void preorderTraversal(std::shared_ptr<TreeNode<T>> node) {
    if (!node) return;
    std::cout << node->data << " ";  // 訪問根
    for (auto& child : node->children) {
        preorderTraversal(child);      // 遞迴訪問子樹
    }
}
```

**複雜度分析:** \(O(n)\) 時間, \(O(h)\) 空間(遞迴堆疊)

### ✅ 理解檢核

Q1: 樹為什麼不能有環？  
A: 若有環,就無法從任意節點唯一確定到根的路徑,違反階層結構定義。

Q2: 鏈結串列能否視為特殊的樹？  
A: 可以,每個節點最多一個子節點的樹就是鏈結串列。

Q3: 二元樹何時退化為鏈結串列？  
A: 當每個節點只有一個子節點時(例如只有左子或只有右子)。

---

## 樹的術語與定義

### 摘要
PDF Pages 12-15定義了樹的核心術語:根、葉、父子、兄弟、深度、高度、度數等。理解這些術語對後續學習至關重要。

### 術語表

| 術語 | 定義 | 例子 |
|------|------|------|
| **根(Root)** | 最頂端節點,無父節點 | 樹中只有一個根 |
| **葉(Leaf)** | 無子節點的節點 | 樹的最底層 |
| **父/子(Parent/Child)** | 直接連接的節點間關係 | A是B的父,B是A的子 |
| **兄弟(Sibling)** | 共同父節點的節點 | B和C若父同則為兄弟 |
| **邊(Edge)** | 連接兩節點的連線 | 代表父子關係 |
| **深度(Depth)** | 節點到根的距離 | 根的深度為0 |
| **高度(Height)** | 節點到最遠葉的距離 | 葉的高度為0 |
| **度數(Degree)** | 節點的子節點數 | 二元樹度數≤2 |
| **子樹(Subtree)** | 以某節點為根的樹 | 左子樹、右子樹 |

### PDF原文定義 (Page 14-15)

根據Page 14圖示,一棵樹的:
- **高度 = 3** (根到最深葉的距離)
- **深度** 對每個節點不同
- **度數** 表示分支數

### 加深的內容

**深度 vs 高度的區別:**
- 深度是從上往下計算(根到該節點)
- 高度是從下往上計算(該節點到最遠葉)
- 樹的高度 = 根的高度,樹的深度通常定義為樹高

**平衡性與高度:**
對於n個節點的樹:
- 最差情況:樹退化為鏈結,高度 = n-1
- 最優情況:完全平衡,高度 = ⌈log₂(n+1)⌉ - 1

### 額外內容

**時間複雜度與高度的關係:**
在BST中,搜尋時間 = O(h),其中h為高度。保持低高度對性能至關重要。這也是為什麼需要AVL樹、紅黑樹等自平衡結構。

### ✅ 理解檢核

Q1: 高度為3的樹最多有多少個節點?  
A: 最多 2⁴ - 1 = 15 個節點(完全二元樹)。

Q2: 度數為什麼重要?  
A: 度數決定了分支因子,影響樹的寬度和空間效率。

Q3: 深度和高度何時相等?  
A: 只有單個節點時(深度=高度=0)。

---

## 樹的類型

### 摘要
PDF Pages 16-17列舉了樹的多種類型,從基礎的二元樹到進階的B樹、線段樹等。選擇合適的樹類型對應用效能至關重要。

### 樹型快速參考

**基礎類型:**
| 類型 | 性質 | 應用 |
|------|------|------|
| **二元樹** | 每節點≤2個子 | 基礎結構 |
| **完全二元樹** | 最後層從左到右連續 | 堆 |
| **BST** | 左<根<右 | 搜尋、排序 |
| **AVL樹** | 自平衡BST | C++ STL set |
| **紅黑樹** | 顏色平衡 | Linux核心、Java TreeMap |

**進階類型:**
| 類型 | 應用 |
|------|------|
| **B樹/B+樹** | 資料庫索引、檔案系統 |
| **線段樹** | 範圍查詢(min/max/sum) |
| **Trie樹** | 字典、自動完成 |
| **KD樹** | 空間索引、最近鄰查詢 |

### 加深的內容

**為什麼需要多種樹?**
- **B樹:** 考慮磁碟I/O成本,減少樹高
- **AVL樹:** 嚴格平衡,保證O(log n)
- **紅黑樹:** 寬鬆平衡,插入刪除快
- **線段樹:** 支援範圍查詢和更新

### ✅ 理解檢核

Q1: BST、AVL樹、紅黑樹都能O(log n)搜尋,為何需要不同的類型?  
A: 主要在於插入/刪除效率和平衡維護成本。AVL更嚴格,旋轉更多;紅黑樹寬鬆,操作快。

Q2: 為什麼堆不用AVL樹或紅黑樹?  
A: 堆只需要O(log n)插入/刪除,陣列表示足夠簡單。AVL/紅黑樹的平衡維護對堆而言是浪費。

---

## 樹的遍歷

### 摘要
PDF Pages 21-26介紹了深度優先搜尋(DFT)和廣度優先搜尋(BFT)。DFT有前序、中序、後序三種,每種適用於不同場景。

### 遍歷方式對比

| 遍歷 | 順序 | 使用場景 |
|------|------|--------|
| **前序(Pre)** | 根-左-右 | 複製樹、表達式求值 |
| **中序(In)** | 左-根-右 | BST排序輸出 |
| **後序(Post)** | 左-右-根 | 刪除樹、計算子樹信息 |
| **層序(Level)** | 逐層訪問 | BFS、破裂檢測 |

### C++ 實作

```cpp
// 前序遍歷
void preorder(TreeNode* root) {
    if (!root) return;
    cout << root->val << " ";        // 1. 訪問根
    preorder(root->left);              // 2. 遍歷左子樹
    preorder(root->right);             // 3. 遍歷右子樹
}

// 中序遍歷(二元樹專用,結果有序)
void inorder(TreeNode* root) {
    if (!root) return;
    inorder(root->left);               // 1. 遍歷左子樹
    cout << root->val << " ";        // 2. 訪問根
    inorder(root->right);              // 3. 遍歷右子樹
}

// 後序遍歷
void postorder(TreeNode* root) {
    if (!root) return;
    postorder(root->left);             // 1. 遍歷左子樹
    postorder(root->right);            // 2. 遍歷右子樹
    cout << root->val << " ";        // 3. 訪問根
}

// 層序遍歷(BFS)
void levelOrder(TreeNode* root) {
    if (!root) return;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        TreeNode* node = q.front();
        q.pop();
        cout << node->val << " ";
        if (node->left) q.push(node->left);
        if (node->right) q.push(node->right);
    }
}
```

**複雜度:** DFT \(O(n)\) 遞迴深度 \(O(h)\);BFS \(O(n)\) 隊列 \(O(w)\)

### ✅ 理解檢核

Q1: 中序遍歷為什麼能輸出BST的排序結果?  
A: BST定義保證左<根<右,中序遍歷正好是由小到大的順序。

Q2: 何時使用後序遍歷?  
A: 當需要先處理子樹再處理根時,如計算子樹大小、刪除樹、表達式求值。

Q3: DFS vs BFS在空間複雜度上的差異?  
A: DFS \(O(h)\)(遞迴堆疊),BFS \(O(w)\)(隊列寬度)。寬樹用DFS,深樹用BFS。

---

## 樹的表示法

### 摘要
PDF Pages 27-37介紹了三種樹的表示法:(1)列表表示、(2)左孩子-右兄弟表示、(3)度二樹表示。不同表示法適合不同應用。

### 三種表示法

**1. 列表表示法(List Representation)**
```
根(子節點列表)
(A (B (E (K L) F) C (G) D (H (M) I J)))
```
特點:直觀,簡單,但序列化和搜尋較慢。

**2. 左孩子-右兄弟表示(Left Child-Right Sibling)**
- 每個節點有兩個指標:left_child, right_sibling
- 任意樹可轉換為二元樹
- 優點:空間效率高(每節點恆為2個指標)
- 缺點:結構複雜,導航困難

**3. 度二樹(Binary Tree)**
- 將左孩子-右兄弟表示旋轉45°
- 結果就是標準二元樹
- 優點:可使用二元樹演算法

### 加深的內容

**變換過程(Page 37):**
```
原始樹(A有3個子B,C,D)
      A
    / | \
   B  C  D
   
↓ 變換為
      A
     /
    B ← C ← D (右兄弟連)
    
↓ 旋轉45°
      A
     / \
    B   C
         \
          D
```

此變換使得任意多元樹都能用二元樹表示,統一資料結構。

### ✅ 理解檢核

Q1: 為什麼要將n元樹轉換為二元樹?  
A: 統一資料結構,複用二元樹演算法(遍歷、平衡、搜尋),簡化實作。

Q2: 左孩子-右兄弟表示的優勢是什麼?  
A: 任意節點恆為2個指標,記憶體預測性好;空間高效(不需陣列存子節點)。

---

## 二元樹

### 摘要
PDF Pages 43-44定義了二元樹:每節點最多2個子節點,稱為左子和右子。無序序限制,基於形狀定義。

### 定義與特性

```
二元樹節點結構:
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
};
```

**關鍵特性:**
- 每節點最多2個子節點
- 無順序限制(值大小無要求)
- 基於形狀定義(Shape constraint)
- 可為空樹

### 二元樹類型

| 類型 | 定義 | 節點數(n,h) |
|------|------|-----------|
| **滿二元樹(Full)** | 每節點度=0或2 | 2^(h+1)-1 |
| **完全二元樹(Complete)** | 除最後層外滿,最後層左對齐 | n ≤ 2^(h+1)-1 |
| **完美二元樹(Perfect)** | 所有葉在同層 | 2^(h+1)-1 |
| **平衡二元樹** | 高度差≤常數 | O(log n) |

### C++ 實作

```cpp
#include <queue>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int v) : val(v), left(nullptr), right(nullptr) {}
};

// 建立二元樹
TreeNode* createBinaryTree() {
    TreeNode* root = new TreeNode(1);
    root->left = new TreeNode(2);
    root->right = new TreeNode(3);
    root->left->left = new TreeNode(4);
    root->left->right = new TreeNode(5);
    return root;
}

// 計算樹高
int height(TreeNode* root) {
    if (!root) return 0;
    return 1 + max(height(root->left), height(root->right));
}

// 計算節點數
int countNodes(TreeNode* root) {
    if (!root) return 0;
    return 1 + countNodes(root->left) + countNodes(root->right);
}

// 檢查是否完全二元樹
bool isCompleteBinaryTree(TreeNode* root) {
    if (!root) return true;
    queue<TreeNode*> q;
    q.push(root);
    bool foundNonFull = false;
    
    while (!q.empty()) {
        TreeNode* node = q.front();
        q.pop();
        
        if (foundNonFull && (node->left || node->right)) {
            return false;  // 非完全樹
        }
        
        if (!node->left || !node->right) {
            foundNonFull = true;
        }
        
        if (node->left) q.push(node->left);
        if (node->right) q.push(node->right);
    }
    return true;
}
```

### ✅ 理解檢核

Q1: 完全二元樹有何特殊性?  
A: 可用陣列表示,無浪費空間,層序編號簡單(父在i,子在2i+1和2i+2)。

Q2: 完美二元樹的節點數公式?  
A: 2^(h+1) - 1,其中h為高度。

---

## 二元搜尋樹 (BST)

### 摘要
PDF Pages 44-59介紹BST,即在二元樹基礎上加入**排序不變量**(左<根<右)。這使得搜尋、插入、刪除都能高效進行。

### BST 定義與性質

**不變量(Invariant):**
- 左子樹中所有值 < 根值
- 右子樹中所有值 > 根值
- 遞迴地對每個子樹成立

**優勢:**
- 搜尋 O(log n) 平均, O(n) 最壞
- 中序遍歷得到排序序列
- 插入、刪除也是 O(h)

### 插入操作

```cpp
TreeNode* insert(TreeNode* root, int val) {
    if (!root) return new TreeNode(val);
    
    if (val < root->val) {
        root->left = insert(root->left, val);
    } else {
        root->right = insert(root->right, val);
    }
    return root;
}
```

### 刪除操作(PDF Pages 52-59)

**三種情況:**

1. **葉節點:** 直接刪除
```
    52           52
   /  \    →    /  \
  18   82      18   82
  /            /
 7  ...       7  ...
 (刪除31)
```

2. **一個子節點:** 用子節點替換
```
只有左子或右子的節點,直接跳過
```

3. **兩個子節點:** 用中序前驅或後繼替換
```
// 選項A:中序前驅(左子樹最大值)
// 選項B:中序後繼(右子樹最小值)

TreeNode* findMin(TreeNode* node) {
    while (node->left) node = node->left;
    return node;
}

TreeNode* findMax(TreeNode* node) {
    while (node->right) node = node->right;
    return node;
}

TreeNode* deleteNode(TreeNode* root, int val) {
    if (!root) return nullptr;
    
    if (val < root->val) {
        root->left = deleteNode(root->left, val);
    } else if (val > root->val) {
        root->right = deleteNode(root->right, val);
    } else {
        // 找到要刪除的節點
        if (!root->left) return root->right;
        if (!root->right) return root->left;
        
        // 兩個子節點:用中序後繼
        TreeNode* minRight = findMin(root->right);
        root->val = minRight->val;
        root->right = deleteNode(root->right, minRight->val);
    }
    return root;
}
```

### 複雜度分析

| 操作 | 平衡BST | 傾斜BST |
|------|--------|--------|
| 搜尋 | O(log n) | O(n) |
| 插入 | O(log n) | O(n) |
| 刪除 | O(log n) | O(n) |

**問題:** 輸入序列決定樹形狀。若輸入已排序(1,2,3,4,5),BST退化為鏈結→O(n)。

### ✅ 理解檢核

Q1: BST中序遍歷為何得到排序序列?  
A: 左<根<右,中序正好輸出這個順序。

Q2: 刪除兩子節點時,為何用中序前驅或後繼?  
A: 它們是最接近被刪節點的元素,替換後仍保持BST性質。

Q3: 如何避免BST退化?  
A: 使用自平衡樹(AVL、紅黑樹)。

---

## AVL 樹

### 摘要
PDF Pages 60-63介紹AVL樹,即在BST基礎上加入**高度平衡約束**(|左高-右高|≤1)。確保O(log n)操作。

### AVL 性質

**平衡定義:**
- 每節點的左右子樹高度差≤1
- 遞迴地對每個子樹成立
- 自動維護平衡(插入/刪除後旋轉)

**保證:**
- n個節點的AVL樹高 ≤ 1.44*log₂(n+1)
- 所有操作 O(log n)

### 旋轉操作

AVL樹通過旋轉維護平衡:

```cpp
// 左旋
//     z                y
//    / \              / \
//   T1  y     -->    z   T3
//      / \          / \
//     T2  T3       T1  T2

TreeNode* rotateLeft(TreeNode* z) {
    TreeNode* y = z->right;
    TreeNode* T2 = y->left;
    
    y->left = z;
    z->right = T2;
    return y;
}

// 右旋(對稱)
TreeNode* rotateRight(TreeNode* z) {
    TreeNode* y = z->left;
    TreeNode* T2 = y->right;
    
    y->right = z;
    z->left = T2;
    return y;
}

// 計算平衡因子
int getBalance(TreeNode* node) {
    if (!node) return 0;
    return height(node->left) - height(node->right);
}
```

### AVL 插入

```cpp
TreeNode* insertAVL(TreeNode* root, int val) {
    // 1. 正常BST插入
    if (!root) return new TreeNode(val);
    if (val < root->val) {
        root->left = insertAVL(root->left, val);
    } else {
        root->right = insertAVL(root->right, val);
    }
    
    // 2. 更新高度
    root->height = 1 + max(height(root->left), height(root->right));
    
    // 3. 計算平衡因子
    int balance = getBalance(root);
    
    // 4. 根據情況旋轉
    // 左左
    if (balance > 1 && getBalance(root->left) >= 0)
        return rotateRight(root);
    // 左右
    if (balance > 1 && getBalance(root->left) < 0) {
        root->left = rotateLeft(root->left);
        return rotateRight(root);
    }
    // 右右
    if (balance < -1 && getBalance(root->right) <= 0)
        return rotateLeft(root);
    // 右左
    if (balance < -1 && getBalance(root->right) > 0) {
        root->right = rotateRight(root->right);
        return rotateLeft(root);
    }
    
    return root;
}
```

### ✅ 理解檢核

Q1: 為什麼AVL樹需要4種旋轉(LL,LR,RR,RL)?  
A: 不平衡可能來自4個方向。每個方向需要不同旋轉組合才能復原平衡。

Q2: AVL樹插入後最多旋轉多少次?  
A: 最多1次雙旋(例如左右旋)。刪除時最多O(log n)次單旋。

---

# Part II: 堆 (Heaps)

## 堆的定義與性質

### 摘要
PDF Pages 1-3介紹堆,是**完全二元樹**且滿足**堆序性質**(父≥子 or 父≤子)。常用於優先權隊列。

### 堆的兩大性質

**1. 形狀性質(Shape):**
- 完全二元樹
- 最後層從左到右連續
- 無浪費空間,可用陣列表示

**2. 堆序性質(Heap-Order):**

| 堆型 | 性質 | 特點 |
|------|------|------|
| **最大堆** | 父≥所有子 | 根=最大值 |
| **最小堆** | 父≤所有子 | 根=最小值 |

### 陣列表示

```
完全二元樹在陣列中:
  索引      0    1    2    3    4    5
  值      [50] [30] [40] [10] [20]

樹形:
        50
       /  \
      30   40
     / \
    10  20

計算規則(0-indexed):
- 左子: 2*i + 1
- 右子: 2*i + 2
- 父:   (i-1)/2
```

### ✅ 理解檢核

Q1: 為什麼堆必須是完全二元樹?  
A: 確保高度最小(O(log n)),且能高效地用陣列表示無浪費空間。

Q2: 最大堆和最小堆何時選用?  
A: 最大堆用於取最大優先順序;最小堆用於取最小優先順序(如Dijkstra)。

---

## 堆的操作

### 摘要
PDF Pages 4-5介紹堆的核心操作:插入、提取根、構造堆。都通過**Sift-Up**(上沉)和**Sift-Down**(下沉)維護堆序。

### 核心操作表

| 操作 | 複雜度 | 說明 |
|------|--------|------|
| **peek_root** | O(1) | 直接取根(index 0) |
| **insert** | O(log n) | 末尾插入+上沉 |
| **extract_root** | O(log n) | 根與末尾交換+下沉 |
| **build_heap** | O(n) | Floyd演算法 |

### 插入 (Sift-Up)

```cpp
void insert(int val) {
    heap.push_back(val);
    int idx = heap.size() - 1;
    
    // 與父比較,若違反堆序則交換
    while (idx > 0) {
        int parent = (idx - 1) / 2;
        if (heap[idx] > heap[parent]) {  // 最大堆
            swap(heap[idx], heap[parent]);
            idx = parent;
        } else {
            break;
        }
    }
}
```

**範例(最大堆):**
```
插入55到[80,70,60,30,20,50]:
[80,70,60,30,20,50,55]
           55
交換:
[80,70,60,55,20,50,30]  (55>50)
       55
交換:
[80,70,60,70,20,50,30] -> [80,70,60,70,20,50,30] 再試

實際步驟:
初始:     80
        /  \
       70   60
      / \   /
     30 20 50  (新增55)
         55

交換50↔55:
         80
        /  \
       70   60
      / \   /
     30 20 55
        30

交換70↔55:
         80
        /  \
       55   60
      / \  /
     30 20 70
        
結果: 80仍在根,因80>55
```

### 提取根 (Sift-Down)

```cpp
int extractMax() {
    int max_val = heap[0];
    heap[0] = heap.back();
    heap.pop_back();
    
    int idx = 0;
    while (2*idx + 1 < heap.size()) {
        int left = 2*idx + 1;
        int right = 2*idx + 2;
        int largest = idx;
        
        if (left < heap.size() && heap[left] > heap[largest])
            largest = left;
        if (right < heap.size() && heap[right] > heap[largest])
            largest = right;
            
        if (largest != idx) {
            swap(heap[idx], heap[largest]);
            idx = largest;
        } else {
            break;
        }
    }
    return max_val;
}
```

### 構造堆 (Floyd算法)

```cpp
// O(n)構造,比逐個插入的O(n log n)快
void buildHeap(vector<int>& arr) {
    heap = arr;
    // 從最後一個非葉節點開始下沉
    for (int i = heap.size() / 2 - 1; i >= 0; i--) {
        siftDown(i);
    }
}
```

### ✅ 理解檢核

Q1: 為何堆的插入/刪除是O(log n)?  
A: 上沉/下沉最多進行樹高次比較,高度=O(log n)。

Q2: buildHeap為何比n次insert快?  
A: buildHeap O(n),逐個插入O(n log n)。因大多數操作在樹底層,工作量少。

Q3: Floyd算法為何從size/2-1開始?  
A: 因為最後一個節點之後都是葉,無需下沉。

---

## 優先權隊列

### 摘要
PDF Pages 6-9解釋優先權隊列的概念:不同元素有不同優先級,高優先級先出。堆是實現優先權隊列的理想資料結構。

### 優先權 = 鍵值

```
優先權解釋:
- 最大堆: 值大=優先級高
- 最小堆: 值小=優先級高

實例:
1. 醫院掛號: 緊急度5(高優先) vs 普通(低優先) -> 最大堆
2. Dijkstra: 最短距離小=優先級高 -> 最小堆
3. 進程排程: 剩餘時間短=優先級高 -> 最小堆
```

### 優先權隊列 C++ 實作

```cpp
class PriorityQueue {
private:
    vector<int> heap;
    
    void siftUp(int idx) {
        while (idx > 0) {
            int parent = (idx - 1) / 2;
            if (heap[idx] > heap[parent]) {
                swap(heap[idx], heap[parent]);
                idx = parent;
            } else break;
        }
    }
    
    void siftDown(int idx) {
        while (2*idx + 1 < heap.size()) {
            int left = 2*idx + 1;
            int right = 2*idx + 2;
            int largest = idx;
            if (heap[left] > heap[largest]) largest = left;
            if (right < heap.size() && heap[right] > heap[largest])
                largest = right;
            if (largest != idx) {
                swap(heap[idx], heap[largest]);
                idx = largest;
            } else break;
        }
    }
    
public:
    void push(int val) {
        heap.push_back(val);
        siftUp(heap.size() - 1);
    }
    
    int top() { return heap[0]; }
    
    void pop() {
        heap[0] = heap.back();
        heap.pop_back();
        if (!heap.empty()) siftDown(0);
    }
    
    bool empty() { return heap.empty(); }
};
```

---

# Part III: 實作與應用

## 完整C++實作

### 二元樹完整類別

```cpp
#include <iostream>
#include <queue>
#include <algorithm>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    int height;  // 用於AVL
    
    TreeNode(int v) : val(v), left(nullptr), right(nullptr), height(1) {}
};

// 二元搜尋樹類別
class BST {
private:
    TreeNode* root;
    
    TreeNode* insert(TreeNode* node, int val) {
        if (!node) return new TreeNode(val);
        if (val < node->val) {
            node->left = insert(node->left, val);
        } else {
            node->right = insert(node->right, val);
        }
        return node;
    }
    
    TreeNode* deleteNode(TreeNode* node, int val) {
        if (!node) return nullptr;
        if (val < node->val) {
            node->left = deleteNode(node->left, val);
        } else if (val > node->val) {
            node->right = deleteNode(node->right, val);
        } else {
            if (!node->left) return node->right;
            if (!node->right) return node->left;
            TreeNode* minRight = findMin(node->right);
            node->val = minRight->val;
            node->right = deleteNode(node->right, minRight->val);
        }
        return node;
    }
    
    TreeNode* findMin(TreeNode* node) {
        while (node->left) node = node->left;
        return node;
    }
    
    bool search(TreeNode* node, int val) {
        if (!node) return false;
        if (val == node->val) return true;
        if (val < node->val) return search(node->left, val);
        return search(node->right, val);
    }
    
    void inorder(TreeNode* node) {
        if (!node) return;
        inorder(node->left);
        cout << node->val << " ";
        inorder(node->right);
    }
    
public:
    BST() : root(nullptr) {}
    
    void insert(int val) { root = insert(root, val); }
    void deleteVal(int val) { root = deleteNode(root, val); }
    bool search(int val) { return search(root, val); }
    void inorder() { inorder(root); cout << "\n"; }
};

// AVL樹類別(簡化版)
class AVLTree {
private:
    TreeNode* root;
    
    int height(TreeNode* node) {
        return node ? node->height : 0;
    }
    
    int getBalance(TreeNode* node) {
        return node ? height(node->left) - height(node->right) : 0;
    }
    
    TreeNode* rotateRight(TreeNode* y) {
        TreeNode* x = y->left;
        TreeNode* T2 = x->right;
        x->right = y;
        y->left = T2;
        y->height = 1 + max(height(y->left), height(y->right));
        x->height = 1 + max(height(x->left), height(x->right));
        return x;
    }
    
    TreeNode* rotateLeft(TreeNode* x) {
        TreeNode* y = x->right;
        TreeNode* T2 = y->left;
        y->left = x;
        x->right = T2;
        x->height = 1 + max(height(x->left), height(x->right));
        y->height = 1 + max(height(y->left), height(y->right));
        return y;
    }
    
    TreeNode* insert(TreeNode* node, int val) {
        if (!node) return new TreeNode(val);
        if (val < node->val) {
            node->left = insert(node->left, val);
        } else {
            node->right = insert(node->right, val);
        }
        
        node->height = 1 + max(height(node->left), height(node->right));
        int balance = getBalance(node);
        
        if (balance > 1 && val < node->left->val)
            return rotateRight(node);
        if (balance < -1 && val > node->right->val)
            return rotateLeft(node);
        if (balance > 1 && val > node->left->val) {
            node->left = rotateLeft(node->left);
            return rotateRight(node);
        }
        if (balance < -1 && val < node->right->val) {
            node->right = rotateRight(node->right);
            return rotateLeft(node);
        }
        return node;
    }
    
public:
    AVLTree() : root(nullptr) {}
    void insert(int val) { root = insert(root, val); }
};

// 最大堆類別
class MaxHeap {
private:
    vector<int> heap;
    
    void siftUp(int idx) {
        while (idx > 0) {
            int parent = (idx - 1) / 2;
            if (heap[idx] > heap[parent]) {
                swap(heap[idx], heap[parent]);
                idx = parent;
            } else break;
        }
    }
    
    void siftDown(int idx) {
        while (2*idx + 1 < heap.size()) {
            int left = 2*idx + 1;
            int right = 2*idx + 2;
            int largest = idx;
            if (heap[left] > heap[largest]) largest = left;
            if (right < heap.size() && heap[right] > heap[largest])
                largest = right;
            if (largest != idx) {
                swap(heap[idx], heap[largest]);
                idx = largest;
            } else break;
        }
    }
    
public:
    void push(int val) {
        heap.push_back(val);
        siftUp(heap.size() - 1);
    }
    
    int top() { return heap[0]; }
    
    void pop() {
        heap[0] = heap.back();
        heap.pop_back();
        if (!heap.empty()) siftDown(0);
    }
    
    bool empty() { return heap.empty(); }
    
    void print() {
        for (int x : heap) cout << x << " ";
        cout << "\n";
    }
};

// 測試
int main() {
    // BST測試
    BST bst;
    bst.insert(50);
    bst.insert(30);
    bst.insert(70);
    bst.insert(20);
    bst.insert(40);
    cout << "BST Inorder: ";
    bst.inorder();  // 20 30 40 50 70
    
    // 堆測試
    MaxHeap heap;
    heap.push(80);
    heap.push(70);
    heap.push(60);
    heap.push(30);
    heap.push(20);
    heap.push(50);
    cout << "Heap: ";
    heap.print();  // 80 70 60 30 20 50
    cout << "Max: " << heap.top() << "\n";  // 80
    
    return 0;
}
```

---

## 練習題與解答

### 題目1: 最低公共祖先 (Lowest Common Ancestor, LCA)
**難度:** ★★☆

給定二元搜尋樹和兩個節點,求其最低公共祖先。

```cpp
TreeNode* findLCA(TreeNode* root, int p, int q) {
    if (!root) return nullptr;
    if (p < root->val && q < root->val)
        return findLCA(root->left, p, q);
    if (p > root->val && q > root->val)
        return findLCA(root->right, p, q);
    return root;  // p和q在根兩側或等於根
}
```

**複雜度:** O(log n) 平衡, O(n) 最壞

---

### 題目2: 樹的鏡像
**難度:** ★★☆

將二元樹翻轉(左右子互換)。

```cpp
TreeNode* mirrorTree(TreeNode* root) {
    if (!root) return nullptr;
    swap(root->left, root->right);
    mirrorTree(root->left);
    mirrorTree(root->right);
    return root;
}
```

**複雜度:** O(n) 時間, O(h) 空間

---

### 題目3: Kth 最小元素
**難度:** ★★★

在BST中找第k小的元素。

```cpp
int count = 0;
int result = -1;

void inorder(TreeNode* root, int k) {
    if (!root || count >= k) return;
    inorder(root->left, k);
    count++;
    if (count == k) {
        result = root->val;
        return;
    }
    inorder(root->right, k);
}

// 或用堆
int findKthSmallest(vector<int>& arr, int k) {
    MaxHeap maxHeap;
    for (int x : arr) {
        maxHeap.push(x);
        if (maxHeap.size() > k) maxHeap.pop();
    }
    return maxHeap.top();  // O(n log k)
}
```

---

### 題目4: 驗證BST
**難度:** ★★☆

檢查樹是否為有效的BST。

```cpp
bool isValidBST(TreeNode* root, long long minVal = LLONG_MIN, 
                long long maxVal = LLONG_MAX) {
    if (!root) return true;
    if (root->val <= minVal || root->val >= maxVal) return false;
    return isValidBST(root->left, minVal, root->val) &&
           isValidBST(root->right, root->val, maxVal);
}
```

**複雜度:** O(n) 時間, O(h) 空間

---

### 題目5: 堆排序
**難度:** ★★☆

使用堆實現排序。

```cpp
void heapSort(vector<int>& arr) {
    // 構造堆
    MaxHeap heap;
    for (int x : arr) heap.push(x);
    
    // 依序提取
    for (int i = arr.size() - 1; i >= 0; i--) {
        arr[i] = heap.top();
        heap.pop();
    }
}
```

**複雜度:** O(n log n) 時間, O(n) 空間

---

### 題目6: 路徑和為目標
**難度:** ★★★

在樹中找到所有根到葉的路徑和等於目標的路徑。

```cpp
void findPaths(TreeNode* root, int target, int sum, 
               vector<int>& path, vector<vector<int>>& result) {
    if (!root) return;
    
    path.push_back(root->val);
    sum -= root->val;
    
    // 到達葉且和為0
    if (!root->left && !root->right && sum == 0) {
        result.push_back(path);
    }
    
    findPaths(root->left, target, sum, path, result);
    findPaths(root->right, target, sum, path, result);
    path.pop_back();
}
```

**複雜度:** O(n) 時間(遍歷所有節點)

---

### 題目7: 完全二元樹的節點數
**難度:** ★★☆

計算完全二元樹的節點數,比O(n)更快。

```cpp
int countNodes(TreeNode* root) {
    if (!root) return 0;
    
    int leftH = 0, rightH = 0;
    TreeNode* left = root, *right = root;
    
    while (left) { leftH++; left = left->left; }
    while (right) { rightH++; right = right->right; }
    
    // 若左高=右高,是完美樹
    if (leftH == rightH) {
        return (1 << leftH) - 1;
    }
    
    // 否則遞迴計算
    return 1 + countNodes(root->left) + countNodes(root->right);
}
```

**複雜度:** O(log²n) vs 樸素O(n)

---

### 題目8: 拓撲排序(DAG)
**難度:** ★★★

對有向無環圖進行拓撲排序,用於編譯器檢測循環依賴。

```cpp
bool topologicalSort(vector<vector<int>>& graph, vector<int>& order) {
    int n = graph.size();
    vector<int> inDegree(n, 0);
    queue<int> q;
    
    for (int i = 0; i < n; i++) {
        for (int j : graph[i]) {
            inDegree[j]++;
        }
    }
    
    for (int i = 0; i < n; i++) {
        if (inDegree[i] == 0) q.push(i);
    }
    
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        order.push_back(u);
        
        for (int v : graph[u]) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.push(v);
        }
    }
    
    return order.size() == n;  // 若有環,返回false
}
```

---

### 題目9: Dijkstra最短路徑
**難度:** ★★★

使用最小堆實現Dijkstra算法。

```cpp
vector<int> dijkstra(vector<vector<pair<int,int>>>& graph, int start) {
    int n = graph.size();
    vector<int> dist(n, INT_MAX);
    priority_queue<pair<int,int>, vector<pair<int,int>>, 
                   greater<pair<int,int>>> pq;  // 最小堆
    
    dist[start] = 0;
    pq.push({0, start});
    
    while (!pq.empty()) {
        auto [d, u] = pq.top();
        pq.pop();
        
        if (d > dist[u]) continue;
        
        for (auto [v, w] : graph[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

**複雜度:** O((V+E)log V)

---

### 題目10: Huffman編碼樹
**難度:** ★★★

構造Huffman樹進行無損資料壓縮。

```cpp
struct HuffNode {
    char ch;
    int freq;
    HuffNode* left;
    HuffNode* right;
    
    HuffNode(char c, int f) : ch(c), freq(f), left(nullptr), right(nullptr) {}
};

HuffNode* buildHuffmanTree(map<char,int>& freq) {
    priority_queue<HuffNode*, vector<HuffNode*>, 
                   greater<HuffNode*>> pq;  // 最小堆
    
    for (auto [c, f] : freq) {
        pq.push(new HuffNode(c, f));
    }
    
    while (pq.size() > 1) {
        HuffNode* left = pq.top(); pq.pop();
        HuffNode* right = pq.top(); pq.pop();
        
        HuffNode* parent = new HuffNode('\0', left->freq + right->freq);
        parent->left = left;
        parent->right = right;
        pq.push(parent);
    }
    
    return pq.top();
}
```

---

## GitHub專案結構

```
trees-heaps-course/
├── README.md                 # 專案說明
├── docs/
│   ├── 00_introduction.md    # 課程介紹
│   ├── 01_trees_basics.md    # 樹基礎
│   ├── 02_bst.md             # 二元搜尋樹
│   ├── 03_avl.md             # AVL樹
│   ├── 04_heaps.md           # 堆與優先權隊列
│   └── images/               # 流程圖、結構圖
├── src/
│   ├── tree.h                # 樹基礎類別
│   ├── bst.h                 # BST實作
│   ├── avl.h                 # AVL樹實作
│   ├── heap.h                # 堆實作
│   └── main.cpp              # 範例程式
├── test/
│   ├── test_tree.cpp         # 單元測試
│   ├── test_bst.cpp
│   ├── test_heap.cpp
│   └── CMakeLists.txt
├── problems/
│   ├── lca.cpp               # 最低公共祖先
│   ├── kth_smallest.cpp      # 第k小元素
│   ├── huffman.cpp           # Huffman編碼
│   └── solutions.md          # 解答
├── CMakeLists.txt            # 編譯配置
└── LICENSE
```

### README 範例

```markdown
# 樹與堆 - 資料結構完整教學

涵蓋樹、二元樹、BST、AVL樹、堆等資料結構的深入教學和實作。

## 快速開始

### 編譯
\`\`\`bash
mkdir build
cd build
cmake ..
make
\`\`\`

### 執行範例
\`\`\`bash
./main
\`\`\`

### 執行測試
\`\`\`bash
./test_tree
./test_bst
./test_heap
\`\`\`

## 核心概念

- **樹的基礎:** 階層結構、遍歷、表示法
- **二元搜尋樹:** 排序不變量、插入/刪除
- **AVL樹:** 自平衡、旋轉操作
- **堆:** 完全二元樹、優先權隊列、Heapsort

## 練習題

超過10題研究所等級的問題,涵蓋:
- LCA、路徑和、完全樹計算
- Dijkstra、Huffman編碼
- 拓撲排序、驗證BST

## 參考資源

- [3Blue1Brown - 樹的可視化](https://www.youtube.com/watch?v=...)
- [Algorithm Visualizer](https://algorithm-visualizer.org/)
- [CP-Algorithms 樹章節](https://cp-algorithms.com/)
```

---

## 常見錯誤與除錯

### 樹常見錯誤

| 錯誤 | 原因 | 解決 |
|------|------|------|
| 堆疊溢出(SO) | 無窮遞迴 | 檢查終止條件 |
| 空指標(NPE) | 未檢查null | 加入null檢查 |
| 記憶體洩漏 | 未delete | 用smart_ptr |
| 無限迴圈 | 錯誤更新指標 | 確保指標改變 |

### 堆常見錯誤

| 錯誤 | 原因 | 解決 |
|------|------|------|
| 堆序破壞 | Sift邏輯錯誤 | 仔細檢查比較條件 |
| 索引越界 | 子節點計算錯誤 | 確認 `2i+1`, `2i+2` |
| 容量不足 | 未動態擴展 | 使用vector自動擴展 |

### 除錯技巧

```cpp
// 1. 列印樹結構(前序遍歷)
void debug_print(TreeNode* root, int indent = 0) {
    if (!root) return;
    for (int i = 0; i < indent; i++) cout << "  ";
    cout << root->val << "\n";
    debug_print(root->left, indent + 1);
    debug_print(root->right, indent + 1);
}

// 2. 驗證堆序
bool isValidHeap(vector<int>& heap) {
    for (int i = 0; i < heap.size() / 2; i++) {
        int left = 2*i + 1;
        int right = 2*i + 2;
        if (heap[i] < heap[left]) return false;  // 最大堆
        if (right < heap.size() && heap[i] < heap[right]) return false;
    }
    return true;
}

// 3. 單元測試範本
void testInsertDelete() {
    BST bst;
    bst.insert(50);
    bst.insert(30);
    bst.insert(70);
    assert(bst.search(30) == true);
    bst.deleteVal(30);
    assert(bst.search(30) == false);
    cout << "測試通過!\n";
}
```

---

## 進階主題

### 樹的變種

**1. Segment Tree (線段樹)**
- 支援範圍查詢(最小、最大、和)和區間更新
- 時間複雜度:O(log n)
- 應用:競賽程式設計、計算幾何

**2. Fenwick Tree (樹狀陣列)**
- 高效的前綴和查詢和單點更新
- 時間複雜度:O(log n)
- 應用:排序陣列、逆序對計算

**3. KD樹**
- 用於k維空間分割
- 時間複雜度:O(log n) 平均搜尋
- 應用:最近鄰查詢、機器學習

### 樹的應用案例

**案例1: 資料庫索引 (B樹)**
- 磁碟I/O優化:每個節點存多個鍵
- 減少樹高度,降低磁碟存取次數
- 例:MySQL InnoDB使用B+樹

**案例2: 作業系統排程 (堆)**
- 優先權隊列管理待執行進程
- 每次O(log n)時間取得最高優先級
- 降低排程開銷

**案例3: 編譯器 (AST)**
- 抽象語法樹表示程式碼結構
- 進行語意分析和最佳化
- 最終生成目標程式碼

---

## 參考文獻

- Cormen, T. H., Leiserson, C. E., Rivest, R. L., & Stein, C. (2009). *Introduction to algorithms* (3rd ed.). MIT Press.
- Knuth, D. E. (1998). *The art of computer programming* (Vol. 1). Addison-Wesley.
- 演算法可視化: https://www.cs.usfca.edu/~galles/visualization/
- CP-Algorithms: https://cp-algorithms.com/

---

## 引用格式

**APA:**
Yuan Ze University. (2024). *Data Structures: Trees and Heaps* [Course slides]. Computer Science & Engineering Department.

**IEEE:**
Yuan Ze University, "Data structures: Trees and heaps," Computer Science & Engineering, 11402 CS203A, 2024.

---

## 更新日誌

- **2024-12-26:** 初版發布
  - 完成樹基礎至AVL樹
  - 完成堆和優先權隊列
  - 新增10題練習題
  - 提供完整C++實作

---

**Last Updated:** 2024-12-26  
