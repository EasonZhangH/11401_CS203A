// 優點：
// - O(1) 兩端插入刪除（類似鏈結串列）
// - 近似 O(1) 隨機存取（類似陣列）
// - 比 vector 更好的記憶體增長策略

template<typename T>
class SimplifiedDeque {
private:
    static const int CHUNK_SIZE = 16;
    struct Chunk {
        T data[CHUNK_SIZE];
    };
    std::vector<Chunk*> chunks;
    int frontIndex, backIndex;
    
public:
    void push_back(const T& value) {
        if (backIndex == CHUNK_SIZE) {
            chunks.push_back(new Chunk());
            backIndex = 0;
        }
        chunks.back()->data[backIndex++] = value;
    }
    
    T& operator[](int index) {
        int chunkIndex = index / CHUNK_SIZE;
        int localIndex = index % CHUNK_SIZE;
        return chunks[chunkIndex]->data[localIndex];
    }
};
```

**2. 記憶體佔用的實際比較**

```cpp
#include <iostream>

void memoryComparison() {
    // 陣列記憶體佔用
    int arr[1000];
    std::cout << "陣列 (1000 個 int): " 
              << sizeof(arr) << " bytes\n";
    // 輸出: 4000 bytes (1000 * 4)
    
    // 單向鏈結串列記憶體佔用
    struct SNode {
        int data;     // 4 bytes
        SNode* next;  // 8 bytes (64-bit 系統)
        // 實際可能因記憶體對齊而更大
    };
    std::cout << "單向鏈結串列每節點: " 
              << sizeof(SNode) << " bytes\n";
    // 輸出: 16 bytes (可能因對齊)
    std::cout << "1000 個節點總計: " 
              << sizeof(SNode) * 1000 << " bytes\n";
    // 輸出: 16000 bytes
    
    // 雙向鏈結串列記憶體佔用
    struct DNode {
        int data;     // 4 bytes
        DNode* prev;  // 8 bytes
        DNode* next;  // 8 bytes
    };
    std::cout << "雙向鏈結串列每節點: " 
              << sizeof(DNode) << " bytes\n";
    // 輸出: 24 bytes
    std::cout << "1000 個節點總計: " 
              << sizeof(DNode) * 1000 << " bytes\n";
    // 輸出: 24000 bytes
    
    // 結論：
    // 陣列: 4KB
    // 單向鏈結串列: 16KB (4 倍)
    // 雙向鏈結串列: 24KB (6 倍)
}
```

**3. 不同操作的實際效能測試**

```cpp
#include <chrono>
#include <vector>
#include <list>
#include <iostream>

class PerformanceTester {
public:
    static void testRandomAccess(int size = 100000, int accesses = 10000) {
        std::vector<int> vec(size);
        std::list<int> lst(size);
        
        // 測試陣列隨機存取
        auto start = std::chrono::high_resolution_clock::now();
        for (int i = 0; i < accesses; i++) {
            int index = rand() % size;
            volatile int val = vec[index];  // volatile 防止編譯器優化
        }
        auto end = std::chrono::high_resolution_clock::now();
        auto vecTime = std::chrono::duration_cast<std::chrono::microseconds>(end - start).count();
        
        // 測試鏈結串列隨機存取
        start = std::chrono::high_resolution_clock::now();
        for (int i = 0; i < accesses; i++) {
            int index = rand() % size;
            auto it = lst.begin();
            std::advance(it, index);  // O(n) 操作
            volatile int val = *it;
        }
        end = std::chrono::high_resolution_clock::now();
        auto lstTime = std::chrono::duration_cast<std::chrono::microseconds>(end - start).count();
        
        std::cout << "隨機存取效能比較 (" << accesses << " 次):\n";
        std::cout << "  陣列: " << vecTime << " μs\n";
        std::cout << "  鏈結串列: " << lstTime << " μs\n";
        std::cout << "  比例: " << (double)lstTime / vecTime << ":1\n";
    }
    
    static void testFrontInsertion(int operations = 10000) {
        // 測試陣列開頭插入
        auto start = std::chrono::high_resolution_clock::now();
        std::vector<int> vec;
        for (int i = 0; i < operations; i++) {
            vec.insert(vec.begin(), i);  // O(n) 操作
        }
        auto end = std::chrono::high_resolution_clock::now();
        auto vecTime = std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count();
        
        // 測試鏈結串列開頭插入
        start = std::chrono::high_resolution_clock::now();
        std::list<int> lst;
        for (int i = 0; i < operations; i++) {
            lst.push_front(i);  // O(1) 操作
        }
        end = std::chrono::high_resolution_clock::now();
        auto lstTime = std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count();
        
        std::cout << "\n開頭插入效能比較 (" << operations << " 次):\n";
        std::cout << "  陣列: " << vecTime << " ms\n";
        std::cout << "  鏈結串列: " << lstTime << " ms\n";
        std::cout << "  比例: " << (double)vecTime / lstTime << ":1\n";
    }
    
    static void testSequentialTraversal(int size = 1000000) {
        std::vector<int> vec(size, 1);
        std::list<int> lst(size, 1);
        
        // 測試陣列順序遍歷
        auto start = std::chrono::high_resolution_clock::now();
        long long sum = 0;
        for (int val : vec) {
            sum += val;
        }
        auto end = std::chrono::high_resolution_clock::now();
        auto vecTime = std::chrono::duration_cast<std::chrono::microseconds>(end - start).count();
        
        // 測試鏈結串列順序遍歷
        start = std::chrono::high_resolution_clock::now();
        sum = 0;
        for (int val : lst) {
            sum += val;
        }
        end = std::chrono::high_resolution_clock::now();
        auto lstTime = std::chrono::duration_cast<std::chrono::microseconds>(end - start).count();
        
        std::cout << "\n順序遍歷效能比較 (大小 " << size << "):\n";
        std::cout << "  陣列: " << vecTime << " μs\n";
        std::cout << "  鏈結串列: " << lstTime << " μs\n";
        std::cout << "  比例: " << (double)lstTime / vecTime << ":1\n";
    }
};

int main() {
    PerformanceTester::testRandomAccess();
    PerformanceTester::testFrontInsertion();
    PerformanceTester::testSequentialTraversal();
    return 0;
}

// 典型輸出（實際結果依硬體而異）：
// 隨機存取效能比較 (10000 次):
//   陣列: 150 μs
//   鏈結串列: 450000 μs
//   比例: 3000:1 (鏈結串列慢 3000 倍！)
//
// 開頭插入效能比較 (10000 次):
//   陣列: 1250 ms
//   鏈結串列: 2 ms
//   比例: 625:1 (陣列慢 625 倍！)
//
// 順序遍歷效能比較 (大小 1000000):
//   陣列: 800 μs
//   鏈結串列: 3500 μs
//   比例: 4.4:1 (鏈結串列慢 4.4 倍)
```

**4. 實務建議總結**

```cpp
// 決策樹實作
class DataStructureSelector {
public:
    static std::string selectBestStructure(
        bool frequentRandomAccess,
        bool frequentInsertion,
        bool frequentDeletion,
        bool sizeKnownInAdvance,
        bool needsBidirectional
    ) {
        if (frequentRandomAccess) {
            return "使用 std::vector（陣列）";
        }
        
        if (frequentInsertion || frequentDeletion) {
            if (!sizeKnownInAdvance) {
                if (needsBidirectional) {
                    return "使用 std::list（雙向鏈結串列）";
                } else {
                    return "使用 std::forward_list（單向鏈結串列）";
                }
            }
        }
        
        if (sizeKnownInAdvance && !frequentInsertion) {
            return "使用 std::vector（陣列）+ reserve()";
        }
        
        return "使用 std::deque（折衷方案）";
    }
};

// 使用範例
void demonstrateSelection() {
    // 場景 1：遊戲中的玩家列表（固定最大數量，頻繁存取）
    auto choice1 = DataStructureSelector::selectBestStructure(
        true,   // 頻繁隨機存取
        false,  // 不頻繁插入
        false,  // 不頻繁刪除
        true,   // 大小已知
        false   // 不需雙向
    );
    std::cout << "遊戲玩家列表: " << choice1 << "\n";
    
    // 場景 2：文字編輯器的 Undo 歷史
    auto choice2 = DataStructureSelector::selectBestStructure(
        false,  // 不需隨機存取
        true,   // 頻繁插入（新操作）
        true,   // 頻繁刪除（Redo 後清除）
        false,  // 大小未知
        true    // 需要雙向（Undo/Redo）
    );
    std::cout << "Undo 歷史: " << choice2 << "\n";
    
    // 場景 3：任務佇列（FIFO）
    auto choice3 = DataStructureSelector::selectBestStructure(
        false,  // 不需隨機存取
        true,   // 頻繁插入（新任務）
        true,   // 頻繁刪除（完成任務）
        false,  // 大小未知
        false   // 單向即可
    );
    std::cout << "任務佇列: " << choice3 << "\n";
}
```

**5. 混合策略：根據資料量動態選擇**

```cpp
template<typename T>
class AdaptiveList {
private:
    std::vector<T> smallData;  // 小資料量用陣列
    std::list<T> largeData;    // 大資料量用鏈結串列
    bool usingVector = true;
    static const int THRESHOLD = 1000;
    
    void switchToList() {
        largeData.assign(smallData.begin(), smallData.end());
        smallData.clear();
        smallData.shrink_to_fit();
        usingVector = false;
    }
    
public:
    void push_back(const T& value) {
        if (usingVector) {
            smallData.push_back(value);
            if (smallData.size() > THRESHOLD) {
                switchToList();
            }
        } else {
            largeData.push_back(value);
        }
    }
    
    size_t size() const {
        return usingVector ? smallData.size() : largeData.size();
    }
    
    // 其他操作類似...
};
```

**6. 記憶體碎片化問題**

```cpp
// 鏈結串列的記憶體碎片化
void demonstrateFragmentation() {
    std::list<int> lst;
    
    // 插入大量元素
    for (int i = 0; i < 10000; i++) {
        lst.push_back(i);
    }
    
    // 刪除奇數位置的元素
    auto it = lst.begin();
    while (it != lst.end()) {
        ++it;
        if (it != lst.end()) {
            it = lst.erase(it);  // 刪除，但記憶體可能未歸還系統
        }
    }
    
    // 問題：已刪除的節點記憶體可能仍被程式保留
    // 造成記憶體碎片化
    
    // 解決方案：使用記憶體池或定期重建
}

// 記憶體池實作
template<typename T>
class MemoryPool {
private:
    union Node {
        T data;
        Node* next;
    };
    
    Node* freeList;
    std::vector<Node*> blocks;
    static const int BLOCK_SIZE = 1000;
    
public:
    T* allocate() {
        if (!freeList) {
            Node* newBlock = new Node[BLOCK_SIZE];
            blocks.push_back(newBlock);
            
            for (int i = 0; i < BLOCK_SIZE - 1; i++) {
                newBlock[i].next = &newBlock[i + 1];
            }
            newBlock[BLOCK_SIZE - 1].next = nullptr;
            freeList = newBlock;
        }
        
        Node* node = freeList;
        freeList = freeList->next;
        return reinterpret_cast<T*>(node);
    }
    
    void deallocate(T* ptr) {
        Node* node = reinterpret_cast<Node*>(ptr);
        node->next = freeList;
        freeList = node;
    }
    
    ~MemoryPool() {
        for (Node* block : blocks) {
            delete[] block;
        }
    }
};
```

### 參考來源
[1] Yuan Ze University, "CS203A Data Structures - Linked Lists (Chapter 4)," Lecture Slides, pp. 47-49.  
[2] U. Drepper, "What Every Programmer Should Know About Memory," Red Hat, Inc., 2007. [Online]. Available: https://people.freebsd.org/~lstewart/articles/cpumemory.pdf  
[3] B. Stroustrup, "Why you should avoid Linked Lists," Going Native 2012 Conference Talk, Microsoft, 2012.  
[4] C++ Reference, "std::deque," https://en.cppreference.com/w/cpp/container/deque

### 理解檢核

1. **問題**：為什麼在某些情況下，即使鏈結串列的插入刪除是 O(1)，實際效能仍可能不如陣列的 O(n) 操作？

   **答案**：因為：(1) **快取效能**：陣列的連續記憶體配置使得 CPU 快取命中率高，即使理論上 O(n)，實際執行速度很快；鏈結串列的節點分散導致大量快取未命中，即使理論上 O(1)，實際可能很慢。(2) **常數因子**：鏈結串列需要動態記憶體分配、指標解參考等操作，常數因子大；陣列的操作更接近硬體，常數因子小。(3) **記憶體頻寬**：現代 CPU 的記憶體頻寬有限，鏈結串列的隨機存取模式會浪費頻寬。實務中，當 n 不大時（如 n < 10000），陣列常常更快。

2. **問題**：在以下場景中，應該選擇陣列還是鏈結串列？(a) 實作瀏覽器的「上一頁/下一頁」功能；(b) 儲存遊戲中所有敵人的位置並頻繁更新；(c) 實作 LRU Cache。

   **答案**：
   - **(a) 瀏覽器歷史**：雙向鏈結串列。理由：需要雙向遍歷（上一頁/下一頁），插入新頁面時刪除未來的頁面（分支歷史），這些操作在鏈結串列中都是 O(1)。
   - **(b) 遊戲敵人位置**：陣列（std::vector）。理由：需要頻繁遍歷所有敵人進行更新和渲染，陣列的快取友善特性使得遍歷非常快；敵人數量通常不會劇烈變化。
   - **(c) LRU Cache**：雙向鏈結串列 + 雜湊表。理由：需要 O(1) 時間將存取的項目移到鏈結串列開頭（最近使用），並在滿時 O(1) 刪除尾端（最少使用），鏈結串列最適合。

3. **問題**：為什麼現代 C++ 專家（如 Bjarne Stroustrup）常建議「預設使用 std::vector，除非有明確理由才用其他容器」？

   **答案**：因為：(1) **硬體現實**：現代 CPU 的快取架構使得連續記憶體存取遠快於隨機存取，vector 的效能優勢在實務中非常明顯。(2) **簡單性**：vector 的介面簡單，bug 少，易於理解和維護。(3) **普遍性**：大多數應用的效能瓶頸不在資料結構的理論複雜度，而在快取和記憶體頻寬；vector 在這方面表現最好。(4) **插入刪除優化**：若需頻繁插入刪除，可考慮「標記刪除」而非真正移除元素，或使用 swap-and-pop 技巧。只有在「確實需要鏈結串列特性」（如雙向遍歷、O(1) 任意位置插入已知節點後）時才選擇 list。

---

## Page 31-46 — 鏈結串列的變體

### 摘要
本部分介紹鏈結串列的三種主要變體：單向鏈結串列（Singly Linked List）、雙向鏈結串列（Doubly Linked List）、循環鏈結串列（Circular Linked List）。每種變體都有其特定的結構定義和適用場景。重點在於理解不同變體的權衡取捨（trade-offs）：遍歷方向、插入刪除靈活性、記憶體開銷等。同時提供了 C 和 C++ 的結構定義，以及實際遍歷範例程式碼。

### 重點整理

**三種鏈結串列變體**：

1. **單向鏈結串列（Singly Linked List）**
   - 每個節點只有一個 next 指標
   - 只能單向遍歷（從頭到尾）
   - 記憶體開銷最小
   - 刪除節點需要知道前驅節點

2. **雙向鏈結串列（Doubly Linked List）**
   - 每個節點有 prev 和 next 兩個指標
   - 可雙向遍歷（向前向後）
   - 刪除節點更容易（可直接存取前驅）
   - 記憶體開銷較大（多一個指標）

3. **循環鏈結串列（Circular Linked List）**
   - 最後一個節點的 next 指向第一個節點
   - 沒有 NULL 終止符
   - 適合循環處理的應用（如輪詢）
   - 可以是單向或雙向

### PDF 原文結構定義

```c
// 單向鏈結串列
struct SNode {
    int data;
    struct SNode *next;
};

// 雙向鏈結串列
struct DNode {
    int data;
    struct DNode *prev;
    struct DNode *next;
};

// 循環鏈結串列
struct CNode {
    int data;
    struct CNode *next;  // 最後節點指回頭
};
```

```cpp
// C++ 版本
class SNode {
public:
    int data;
    SNode* next;
    SNode(int val) : data(val), next(nullptr) {}
};

class DNode {
public:
    int data;
    DNode* prev;
    DNode* next;
    DNode(int val) : data(val), prev(nullptr), next(nullptr) {}
};

class CNode {
public:
    int data;
    CNode* next;
    CNode(int val) : data(val), next(nullptr) {}
};
```

### PDF 原文：遍歷與觀察記憶體位址

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node* next;
} Node;

Node* createNode(int value) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Memory allocation failed.\n");
        exit(1);
    }
    newNode->data = value;
    newNode->next = NULL;
    return newNode;
}

void traverseList(Node* head) {
    Node* current = head;
    int index = 0;
    while (current != NULL) {
        printf("Node %d: Value = %d, Address = %p, Next = %p\n",
               index, current->data, (void*)current, (void*)current->next);
        current = current->next;
        index++;
    }
}

int main() {
    Node* head = createNode(10);
    head->next = createNode(20);
    head->next->next = createNode(30);
    
    printf("Traversing the linked list:\n");
    traverseList(head);
    
    // Free memory
    Node* current = head;
    while (current != NULL) {
        Node* temp = current;
        current = current->next;
        free(temp);
    }
    return 0;
}
```

### <加深的內容>

**雙向鏈結串列的優勢與實作**

雙向鏈結串列相比單向鏈結串列，主要優勢在於：

```cpp
class DoublyLinkedList {
private:
    struct Node {
        int data;
        Node* prev;
        Node* next;
        Node(int val) : data(val), prev(nullptr), next(nullptr) {}
    };
    
    Node* head;
    Node* tail;
    
public:
    DoublyLinkedList() : head(nullptr), tail(nullptr) {}
    
    // 優勢 1：O(1) 在已知節點前插入
    void insertBefore(Node* target, int value) {
        if (!target) return;
        
        Node* newNode = new Node(value);
        newNode->next = target;
        newNode->prev = target->prev;
        
        if (target->prev) {
            target->prev->next = newNode;
        } else {
            head = newNode;  // target 是頭節點
        }
        
        target->prev = newNode;
    }
    
    // 優勢 2：O(1) 刪除已知節點（不需前驅）
    void deleteNode(Node* target) {
        if (!target) return;
        
        // 更新前驅的 next
        if (target->prev) {
            target->prev->next = target->next;
        } else {
            head = target->next;  // 刪除頭節點
        }
        
        // 更新後繼的 prev
        if (target->next) {
            target->next->prev = target->prev;
        } else {
            tail = target->prev;  // 刪除尾節點
        }
        
        delete target;
    }
    
    // 優勢 3：雙向遍歷
    void printForward() {
        Node* curr = head;
        while (curr) {
            std::cout << curr->data << " <-> ";
            curr = curr->next;
        }
        std::cout << "NULL\n";
    }
    
    void printBackward() {
        Node* curr = tail;
        while (curr) {
            std::cout << curr->data << " <-> ";
            curr = curr->prev;
        }
        std::cout << "NULL\n";
    }
    
    // 優勢 4：從兩端選擇較近端開始搜尋
    Node* findOptimized(int position, int totalLength) {
        if (position < totalLength / 2) {
            // 從頭開始
            Node* curr = head;
            for (int i = 0; i < position; i++) {
                curr = curr->next;
            }
            return curr;
        } else {
            // 從尾開始（更快）
            Node* curr = tail;
            for (int i = totalLength - 1; i > position; i--) {
                curr = curr->prev;
            }
            return curr;
        }
    }
};
```

**循環鏈結串列的應用場景**

循環鏈結串列特別適合需要「循環」處理的場景：

```cpp
// 應用 1：輪詢排程（Round-Robin Scheduling）
class RoundRobinScheduler {
private:
    struct Process {
        int id;
        int timeSlice;
        Process* next;
        Process(int i, int t) : id(i), timeSlice(t), next(nullptr) {}
    };
    
    Process* current;
    
public:
    void addProcess(int id, int timeSlice) {
        Process* newProc = new Process(id, timeSlice);
        
        if (!current) {
            current = newProc;
            current->next = current;  // 指向自己
        } else {
            newProc->next = current->next;
            current->next = newProc;
        }
    }
    
    void executeNext() {
        if (!current) return;
        
        std::cout << "執行行程 " << current->id 
                  << " (時間片: " << current->timeSlice << "ms)\n";
        
        current = current->next;  // 移到下一個，自動循環
    }
};

// 應用 2：約瑟夫問題（Josephus Problem）
int josephus(int n, int k) {
    // n 個人圍成圈，每次數到第 k 個人出列
    struct Node {
        int data;
        Node* next;
    };
    
    // 建立循環鏈結串列
    Node* head = new Node{1, nullptr};
    Node* prev = head;
    
    for (int i = 2; i <= n; i++) {
        prev->next = new Node{i, nullptr};
        prev = prev->next;
    }
    prev->next = head;  // 形成循環
    
    // 模擬出列過程
    Node* curr = head;
    while (curr->next != curr) {
        // 數 k-1 個人
        for (int i = 1; i < k; i++) {
            prev = curr;
            curr = curr->next;
        }
        
        // 出列
        std::cout << curr->data << " 出列\n";
        prev->next = curr->next;
        delete curr;
        curr = prev->next;
    }
    
    int survivor = curr->data;
    delete curr;
    return survivor;
}

// 應用 3：音樂播放器（循環播放）
class MusicPlayer {
private:
    struct Song {
        std::string title;
        Song* next;
    };
    
    Song* current;
    
public:
    void addSong(const std::string& title) {
        Song* newSong = new Song{title, nullptr};
        
        if (!current) {
            current = newSong;
            current->next = current;
        } else {
            newSong->next = current->next;
            current->next = newSong;
        }
    }
    
    void playNext() {
        if (!current) return;
        current = current->next;
        std::cout << "正在播放: " << current->title << "\n";
    }
    
    void playPrevious() {
        // 循環鏈結串列需要遍歷找前一個
        // 這是單向循環鏈結串列的缺點
        Song* prev = current;
        while (prev->next != current) {
            prev = prev->next;
        }
        current = prev;
        std::cout << "正在播放: " << current->title << "\n";
    }
};
```

**變體的權衡取捨**

| 變體 | 遍歷方向 | 插入/刪除靈活性 | 記憶體開銷 | 適用場景 |
|------|----------|-----------------|------------|----------|
| 單向 | 單向 (從頭到尾) | 中等 (刪除需前驅) | 低 (1 個指標) | 簡單列表、頻繁尾端插入 |
| 雙向 | 雙向 | 高 (O(1) 任意位置) | 中 (2 個指標) | 瀏覽器歷史、Undo/Redo |
| 循環 | 循環 (無盡尾) | 高 (循環處理) | 低/中 (視單/雙向) | 輪詢排程、遊戲循環 |

**常見錯誤與陷阱**

```cpp
// 錯誤 1：循環鏈結串列無限迴圈
void badTraversal(CNode* head) {
    CNode* curr = head;
    while (curr) {  // 錯誤：循環中無 NULL，會無限迴圈
        std::cout << curr->data << " ";
        curr = curr->next;
    }
}

// 正確：使用 do-while 或標記
void goodTraversal(CNode* head) {
    if (!head) return;
    CNode* curr = head;
    do {
        std::cout << curr->data << " ";
        curr = curr->next;
    } while (curr != head);
}

// 錯誤 2：雙向鏈結串列忘記更新 prev
void badInsert(DNode* target, int value) {
    DNode* newNode = new DNode(value);
    newNode->next = target->next;
    target->next = newNode;
    // 錯誤：忘記更新 newNode->prev 和 target->next->prev
}

// 正確
void goodInsert(DNode* target, int value) {
    DNode* newNode = new DNode(value);
    newNode->next = target->next;
    newNode->prev = target;
    if (target->next) target->next->prev = newNode;
    target->next = newNode;
}
```

**效能優化技巧**

```cpp
// 優化 1：使用 sentinel node 簡化邊界處理
class SentinelLinkedList {
private:
    DNode sentinel;  // 虛擬節點
    DNode* head;
    
public:
    SentinelLinkedList() : sentinel(0), head(&sentinel) {
        sentinel.next = &sentinel;
        sentinel.prev = &sentinel;
    }
    
    void insertAfter(DNode* pos, int value) {
        DNode* newNode = new DNode(value);
        newNode->next = pos->next;
        newNode->prev = pos;
        pos->next->prev = newNode;
        pos->next = newNode;
    }
};

// 優化 2：雙向鏈結串列尾端指標
class TailOptimizedList {
private:
    DNode* head = nullptr;
    DNode* tail = nullptr;
    
public:
    void pushBack(int value) {
        DNode* newNode = new DNode(value);
        if (!head) {
            head = tail = newNode;
        } else {
            tail->next = newNode;
            newNode->prev = tail;
            tail = newNode;
        }
    }
    
    void popBack() {
        if (!tail) return;
        DNode* toDelete = tail;
        tail = tail->prev;
        if (tail) tail->next = nullptr;
        else head = nullptr;
        delete toDelete;
    }
};
```

### 參考來源
[1] Yuan Ze University, "CS203A Data Structures - Linked Lists (Chapter 4)," Lecture Slides, pp. 31-46.  
[2] T. H. Cormen, C. E. Leiserson, R. L. Rivest, and C. Stein, *Introduction to Algorithms*, 3rd ed. MIT Press, 2009, Ch. 10 "Elementary Data Structures."  
[3] GeeksforGeeks, "Circular Linked List," https://www.geeksforgeeks.org/circular-linked-list/  
[4] C++ Reference, "std::list," https://en.cppreference.com/w/cpp/container/list

### 理解檢核

1. **問題**：為什麼雙向鏈結串列在刪除尾節點時比單向鏈結串列更有效率？

   **答案**：單向鏈結串列刪除尾節點需從頭遍歷到倒數第二節點（O(n)），因為無法從尾回溯。雙向鏈結串列有 prev 指標，可直接從尾節點更新 prev->next = nullptr（O(1)）。如果維護 tail 指標，單向也可優化，但雙向更靈活。

2. **問題**：循環鏈結串列在什麼情況下會導致無限迴圈？如何避免？

   **答案**：在遍歷時若使用 `while (curr != nullptr)` 會無限迴圈，因為無 NULL 終止。避免方法：使用 do-while 循環並檢查是否回到頭節點（`do { ... } while (curr != head)`），或使用標記變數追蹤循環。

3. **問題**：比較單向、雙向、循環鏈結串列的記憶體開銷，並說明選擇時的考量。

   **答案**：單向：1 個指標，低開銷，適合記憶體受限環境。雙向：2 個指標，中開銷，適合需要雙向遍歷。循環：與單/雙向相同，但無 NULL，適合循環應用。考量：若需頻繁雙向操作選雙向；若記憶體緊迫選單向；若需循環如輪詢選循環。

---

## Page 20-30 — 插入與刪除操作

### 摘要
本部分詳細說明鏈結串列的插入與刪除操作，包括在特定目標後插入、在開頭插入、在尾端插入、刪除特定目標、刪除開頭與尾端。強調遍歷列表找目標的必要性，以及處理邊界情況（如空列表、單節點）。PDF 提供步驟圖示，顯示指標變更順序。

### 重點整理

**插入操作**：
- 一般插入：找目標後，更新指標 (O(n) 找目標 + O(1) 插入)
- 開頭插入：直接更新 head (O(1))
- 尾端插入：需遍歷到尾 (O(n))，或用 tail 指標優化為 O(1)

**刪除操作**：
- 一般刪除：找前驅，更新前驅->next (O(n))
- 開頭刪除：更新 head (O(1))
- 尾端刪除：找倒數第二，更新為 NULL (O(n))

### PDF 原文：插入範例

```c
// 插入到目標後
void insertAfter(Node* target, int value) {
    if (target == NULL) return;
    Node* newNode = createNode(value);
    newNode->next = target->next;
    target->next = newNode;
}
```

### <加深的內容>

**完整鏈結串列類別實作**

```cpp
class CompleteLinkedList {
private:
    struct Node {
        int data;
        Node* next;
        Node(int val) : data(val), next(nullptr) {}
    };
    
    Node* head = nullptr;
    Node* tail = nullptr;
    int count = 0;
    
public:
    bool isEmpty() { return count == 0; }
    int size() { return count; }
    
    // 插入開頭
    void insertFront(int value) {
        Node* newNode = new Node(value);
        newNode->next = head;
        head = newNode;
        if (!tail) tail = newNode;
        count++;
    }
    
    // 插入尾端 (用 tail 優化)
    void insertEnd(int value) {
        Node* newNode = new Node(value);
        if (!head) {
            head = tail = newNode;
        } else {
            tail->next = newNode;
            tail = newNode;
        }
        count++;
    }
    
    // 插入位置
    void insertAt(int position, int value) {
        if (position < 0 || position > count) throw std::out_of_range("Position out of range");
        if (position == 0) return insertFront(value);
        if (position == count) return insertEnd(value);
        
        Node* prev = head;
        for (int i = 0; i < position - 1; i++) {
            prev = prev->next;
        }
        Node* newNode = new Node(value);
        newNode->next = prev->next;
        prev->next = newNode;
        count++;
    }
    
    // 刪除開頭
    bool deleteFront() {
        if (isEmpty()) return false;
        Node* toDelete = head;
        head = head->next;
        delete toDelete;
        count--;
        if (count == 0) tail = nullptr;
        return true;
    }
    
    // 刪除尾端 (用 tail 優化)
    bool deleteEnd() {
        if (isEmpty()) return false;
        if (count == 1) {
            delete head;
            head = tail = nullptr;
            count = 0;
            return true;
        }
        Node* prev = head;
        while (prev->next != tail) prev = prev->next;
        delete tail;
        prev->next = nullptr;
        tail = prev;
        count--;
        return true;
    }
    
    // 刪除值
    bool deleteValue(int value) {
        if (isEmpty()) return false;
        if (head->data == value) return deleteFront();
        
        Node* prev = head;
        while (prev->next && prev->next->data != value) prev = prev->next;
        if (!prev->next) return false;
        
        Node* toDelete = prev->next;
        prev->next = toDelete->next;
        if (toDelete == tail) tail = prev;
        delete toDelete;
        count--;
        return true;
    }
    
    // 刪除位置
    void deleteAt(int position) {
        if (position < 0 || position >= count) throw std::out_of_range("Position out of range");
        if (position == 0) {
            deleteFront();
            return;
        }
        if (position == count - 1) {
            deleteEnd();
            return;
        }
        
        Node* prev = head;
        for (int i = 0; i < position - 1; i++) {
            prev = prev->next;
        }
        Node* toDelete = prev->next;
        prev->next = toDelete->next;
        delete toDelete;
        count--;
    }
    
    ~CompleteLinkedList() {
        while (!isEmpty()) deleteFront();
    }
};
```

**2. 尋找節點的各種策略**

```cpp
// 策略 1：按位置尋找 - O(n)
Node* findByPosition(int position) {
    if (position < 0 || position >= count) return nullptr;
    
    Node* current = head;
    for (int i = 0; i < position; i++) {
        current = current->next;
    }
    return current;
}

// 策略 2：按值尋找 - O(n)
Node* findByValue(int value) {
    Node* current = head;
    while (current) {
        if (current->data == value) {
            return current;
        }
        current = current->next;
    }
    return nullptr;
}

// 策略 3：同時回傳前驅節點（用於刪除）- O(n)
std::pair<Node*, Node*> findWithPrev(int value) {
    Node* prev = nullptr;
    Node* current = head;
    
    while (current) {
        if (current->data == value) {
            return {prev, current};
        }
        prev = current;
        current = current->next;
    }
    return {nullptr, nullptr};
}

// 策略 4：使用 dummy node 簡化邏輯
Node* findWithDummy(int value) {
    Node dummy(0);
    dummy.next = head;
    Node* prev = &dummy;
    
    while (prev->next) {
        if (prev->next->data == value) {
            return prev;  // 回傳前驅
        }
        prev = prev->next;
    }
    return nullptr;
}
```

**3. 插入刪除的時間複雜度分析總結**

| 操作 | 無 tail 指標 | 有 tail 指標 | 雙向鏈結串列 | 說明 |
|-----|------------|------------|------------|------|
| 插入開頭 | O(1) | O(1) | O(1) | 直接操作 head |
| 插入尾端 | O(n) | O(1) | O(1) | 需遍歷或直接用 tail |
| 插入中間 | O(n) | O(n) | O(n) | 需先遍歷到位置 |
| 刪除開頭 | O(1) | O(1) | O(1) | 直接操作 head |
| 刪除尾端 | O(n) | O(n) | O(1) | 單向需找前驅，雙向直接操作 |
| 刪除中間 | O(n) | O(n) | O(n) | 需遍歷找到節點 |
| 搜尋 | O(n) | O(n) | O(n) | 都需要遍歷 |

**4. 常見錯誤與陷阱**

```cpp
// 錯誤 1：記憶體洩漏
void badDelete(Node*& head, int value) {
    Node* current = head;
    while (current && current->data != value) {
        current = current->next;
    }
    if (current) {
        head = current->next;  // 錯誤：沒有 delete
    }
}

// 錯誤 2：野指標（Dangling Pointer）
void createDanglingPointer() {
    Node* node = new Node(10);
    Node* ptr = node;
    delete node;
    // ptr 現在是野指標
    cout << ptr->data;  // 未定義行為！
}

// 錯誤 3：重複刪除
void doubleDelete(Node* node) {
    delete node;
    delete node;  // 錯誤：重複刪除
}

// 錯誤 4：忘記更新 tail
void forgotToUpdateTail() {
    // 刪除尾節點後忘記更新 tail
    Node* prev = getPrevOfTail();
    delete tail;
    prev->next = nullptr;
    // tail 現在指向已刪除的記憶體！
}

// 錯誤 5：環形連結
void createCycle() {
    Node* node1 = new Node(1);
    Node* node2 = new Node(2);
    node1->next = node2;
    node2->next = node1;  // 形成環，遍歷會無限迴圈！
}
```

**5. 單元測試範例**

```cpp
#include <cassert>
#include <iostream>

void testLinkedList() {
    CompleteLinkedList list;
    
    // 測試空串列
    assert(list.isEmpty() == true);
    assert(list.size() == 0);
    
    // 測試插入
    list.insertFront(10);
    assert(list.size() == 1);
    assert(list.isEmpty() == false);
    
    list.insertEnd(20);
    list.insertEnd(30);
    assert(list.size() == 3);
    
    // 測試插入位置
    list.insertAt(1, 15);  // 10, 15, 20, 30
    assert(list.size() == 4);
    
    // 測試刪除
    assert(list.deleteFront() == true);  // 15, 20, 30
    assert(list.size() == 3);
    
    assert(list.deleteEnd() == true);    // 15, 20
    assert(list.size() == 2);
    
    assert(list.deleteValue(15) == true); // 20
    assert(list.size() == 1);
    
    // 測試刪除不存在的值
    assert(list.deleteValue(999) == false);
    assert(list.size() == 1);
    
    // 測試清空
    list.deleteFront();
    assert(list.isEmpty() == true);
    assert(list.size() == 0);
    
    // 測試邊界情況
    try {
        list.deleteAt(0);  // 空串列
        assert(false);  // 不應到達這裡
    } catch (const std::exception& e) {
        // 預期的例外
    }
    
    std::cout << "所有測試通過！" << std::endl;
}
```

**6. 實務應用：LRU Cache**

鏈結串列的一個經典應用是實作 LRU（Least Recently Used）快取：

```cpp
#include <unordered_map>

class LRUCache {
private:
    struct Node {
        int key, value;
        Node *prev, *next;
        Node(int k, int v) : key(k), value(v), prev(nullptr), next(nullptr) {}
    };
    
    int capacity;
    std::unordered_map<int, Node*> cache;  // key -> node
    Node *head, *tail;  // dummy nodes
    
    void addToHead(Node* node) {
        node->next = head->next;
        node->prev = head;
        head->next->prev = node;
        head->next = node;
    }
    
    void removeNode(Node* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }
    
    void moveToHead(Node* node) {
        removeNode(node);
        addToHead(node);
    }
    
    Node* removeTail() {
        Node* node = tail->prev;
        removeNode(node);
        return node;
    }
    
public:
    LRUCache(int capacity) : capacity(capacity) {
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head->next = tail;
        tail->prev = head;
    }
    
    int get(int key) {
        if (cache.find(key) == cache.end()) {
            return -1;
        }
        Node* node = cache[key];
        moveToHead(node);  // 最近使用，移到開頭
        return node->value;
    }
    
    void put(int key, int value) {
        if (cache.find(key) != cache.end()) {
            // 已存在，更新值並移到開頭
            Node* node = cache[key];
            node->value = value;
            moveToHead(node);
        } else {
            // 新增節點
            Node* node = new Node(key, value);
            cache[key] = node;
            addToHead(node);
            
            if (cache.size() > capacity) {
                // 超過容量，移除最久未使用的
                Node* removed = removeTail();
                cache.erase(removed->key);
                delete removed;
            }
        }
    }
    
    ~LRUCache() {
        Node* current = head;
        while (current) {
            Node* next = current->next;
            delete current;
            current = next;
        }
    }
};
```

**7. 效能優化技巧**

```cpp
// 優化 1：批次插入
void batchInsert(const std::vector<int>& values) {
    if (values.empty()) return;
    
    // 一次建立所有節點並連結
    Node* first = new Node(values[0]);
    Node* current = first;
    
    for (size_t i = 1; i < values.size(); i++) {
        current->next = new Node(values[i]);
        current = current->next;
    }
    
    // 一次性連接到串列
    if (tail) {
        tail->next = first;
        tail = current;
    } else {
        head = first;
        tail = current;
    }
    
    count += values.size();
}

// 優化 2：避免重複遍歷
void insertAndDelete(int insertPos, int insertVal, int deletePos) {
    // 錯誤：兩次遍歷
    // insertAt(insertPos, insertVal);  // 遍歷一次
    // deleteAt(deletePos);              // 又遍歷一次
    
    // 優化：一次遍歷完成兩個操作
    // （如果 insertPos 和 deletePos 接近）
    // ...實作細節
}

// 優化 3：使用記憶體池
class PooledLinkedList {
private:
    static const int POOL_SIZE = 1000;
    Node nodePool[POOL_SIZE];
    int poolIndex = 0;
    
    Node* allocateNode(int value) {
        if (poolIndex < POOL_SIZE) {
            Node* node = &nodePool[poolIndex++];
            node->data = value;
            node->next = nullptr;
            return node;
        }
        return new Node(value);  // fallback
    }
};
```

### 參考來源
[1] Yuan Ze University, "CS203A Data Structures - Linked Lists (Chapter 4)," Lecture Slides, pp. 20-30.  
[2] T. H. Cormen, C. E. Leiserson, R. L. Rivest, and C. Stein, *Introduction to Algorithms*, 3rd ed. MIT Press, 2009, Ch. 10 "Elementary Data Structures."  
[3] LeetCode, "LRU Cache Problem," https://leetcode.com/problems/lru-cache/

### 理解檢核

1. **問題**：為什麼在插入操作中，必須先設定 `newNode->next = target->next`，然後才設定 `target->next = newNode`？如果順序反過來會發生什麼？

   **答案**：如果先執行 `target->next = newNode`，原本 `target->next` 指向的節點連結就丟失了。當執行 `newNode->next = target->next` 時，會讓 newNode 指向自己（因為此時 target->next 已經是 newNode），形成循環或者原有節點鏈斷裂。正確順序是：先保存原有連結（newNode->next = target->next），再修改前一個節點的指標（target->next = newNode）。

2. **問題**：為什麼單向鏈結串列刪除尾節點需要 O(n) 時間，而刪除頭節點只需要 O(1) 時間？

   **答案**：刪除頭節點只需要更新 head 指標（`head = head->next`）並釋放原頭節點，不需要遍歷。但刪除尾節點需要更新倒數第二個節點的 next 指標為 nullptr，而在單向鏈結串列中，從尾節點無法回溯到前一個節點，必須從頭開始遍歷找到倒數第二個節點，因此需要 O(n) 時間。雙向鏈結串列因為有 prev 指標，可以直接回溯，所以刪除尾節點也是 O(1)。

3. **問題**：在什麼情況下，維護 tail 指標的額外開銷是值得的？

   **答案**：當應用場景需要頻繁在尾端插入元素時，維護 tail 指標可以將插入尾端操作從 O(n) 優化到 O(1)。典型場景包括：佇列（Queue）實作、日誌記錄系統、任務排程系統等。代價是每次插入刪除時需要額外維護 tail 指標，並多佔用一個指標變數的記憶體。如果主要操作是隨機插入刪除或很少操作尾端，則不值得維護 tail。

---

## Page 54-72 — 節點重排與指標操作

### 摘要
本部分通過具體範例深入探討鏈結串列中的節點重排操作（MoveTo），強調指標操作的順序至關重要。展示了錯誤順序如何導致「懸空節點」（dangling node）的產生，以及正確的操作步驟。這是理解鏈結串列進階操作的關鍵，為後續的排序演算法奠定基礎。

### 重點整理

**節點重排範例：交換 Prev 和 Target**

給定串列：`Head → [2] → [3] → [1] → NULL`

目標：交換節點 2 和 3 的位置

**錯誤順序**（會產生懸空節點）：
```
步驟：
1. Head = Target  (Head 指向 3)
2. Target->next = Prev  (3 指向 2)
3. Prev->next = Target->next  (2 指向 3，形成循環！)

結果：Head → [3] → [2] → [3] → ...  (循環)
      [1] 變成懸空節點
```

**正確順序**（避免懸空節點）：
```
步驟：
1. Head = Target  (Head 指向 3)
2. Prev->next = Target->next  (2 指向 1，先保存後續連結)
3. Target->next = Prev  (3 指向 2)

結果：Head → [3] → [2] → [1] → NULL  (正確！)
```

**關鍵原則**：
- 先處理「保存連結」的操作
- 再處理「修改指標」的操作
- 避免過早覆蓋需要的指標資訊

### <加深的內容>

**為什麼指標順序如此重要？**

指標操作的本質是修改記憶體中儲存的位址值。一旦覆蓋，原有資訊就永久丟失：

```cpp
// 視覺化指標操作
struct Node {
    int data;
    Node* next;
};

void demonstratePointerOrder() {
    // 初始狀態
    Node* node1 = new Node{2, nullptr};
    Node* node2 = new Node{3, nullptr};
    Node* node3 = new Node{1, nullptr};
    
    node1->next = node2;  // 2 → 3
    node2->next = node3;  // 3 → 1
    
    Node* head = node1;
    Node* prev = node1;
    Node* target = node2;
    
    cout << "初始:\n";
    cout << "head = " << head << " (指向節點 2)\n";
    cout << "prev = " << prev << " (指向節點 2)\n";
    cout << "target = " << target << " (指向節點 3)\n";
    cout << "prev->next = " << prev->next << " (應該是 target)\n";
    cout << "target->next = " << target->next << " (指向節點 1)\n\n";
    
    // 錯誤順序演示
    cout << "=== 錯誤順序 ===\n";
    Node* savedTargetNext = target->next;  // 保存以便觀察
    
    // 步驟 1
    head = target;
    cout << "步驟 1 後: head = " << head << "\n";
    
    // 步驟 2
    target->next = prev;
    cout << "步驟 2 後: target->next = " << target->next 
         << " (現在指向 prev)\n";
    cout << "問題：原本的 target->next (指向節點 1) 已經丟失!\n";
    
    // 步驟 3
    prev->next = target->next;  // 這會讓 prev->next 指向 prev 自己！
    cout << "步驟 3 後: prev->next = " << prev->next << "\n";
    cout << "嚴重問題：形成循環！prev->next 指向 target，\n";
    cout << "           target->next 指向 prev\n";
}
```

**正確操作的記憶體狀態變化**

```cpp
void correctReorderingSteps() {
    // 設定初始串列：2 → 3 → 1 → NULL
    Node* node1 = new Node{2, nullptr};
    Node* node2 = new Node{3, nullptr};
    Node* node3 = new Node{1, nullptr};
    node1->next = node2;
    node2->next = node3;
    
    Node* head = node1;
    Node* prev = node1;
    Node* target = node2;
    
    // 正確順序
    cout << "初始狀態：\n";
    printList(head);  // 2 → 3 → 1 → NULL
    
    // 步驟 1：更新 head
    head = target;
    cout << "\n步驟 1 (head = target)：\n";
    cout << "head 現在指向節點 3\n";
    cout << "但串列結構未變：";
    printList(node1);  // 從原 head 看仍是 2 → 3 → 1 → NULL
    
    // 步驟 2：斷開 prev 與 target 的連結，連接到後續節點
    Node* afterTarget = target->next;  // 保存節點 1 的位址
    prev->next = afterTarget;
    cout << "\n步驟 2 (prev->next = target->next)：\n";
    cout << "節點 2 現在跳過節點 3，直接指向節點 1\n";
    printList(node1);  // 從節點 2 看：2 → 1 → NULL (節點 3 已脫離)
    printList(target);  // 從節點 3 看：3 → 1 → NULL (仍指向原位置)
    
    // 步驟 3：讓 target 指向 prev
    target->next = prev;
    cout << "\n步驟 3 (target->next = prev)：\n";
    cout << "節點 3 現在指向節點 2\n";
    printList(head);  // 從新 head 看：3 → 2 → 1 → NULL (完成！)
    
    // 清理
    delete node1;
    delete node2;
    delete node3;
}
```

**通用的節點重排模式**

```cpp
// 通用模式：在單向鏈結串列中重排節點
class LinkedListReordering {
public:
    // 模式 1：交換相鄰節點
    static void swapAdjacent(Node*& head, Node* first) {
        if (!first || !first->next) return;
        
        Node* second = first->next;
        
        // 關鍵：先保存 second 的 next
        Node* afterSecond = second->next;
        
        // 重新連結
        second->next = first;
        first->next = afterSecond;
        
        // 如果 first 是頭節點，更新 head
        if (head == first) {
            head = second;
        }
    }
    
    // 模式 2：將節點移到開頭
    static void moveToFront(Node*& head, Node* target, Node* prev) {
        if (!target || head == target) return;
        
        // 步驟順序很重要！
        prev->next = target->next;  // 先斷開 target
        target->next = head;         // target 指向原 head
        head = target;               // 更新 head
    }
    
    // 模式 3：將節點移到指定位置之後
    static void moveAfter(Node* position, Node* target, Node* targetPrev) {
        if (!target || !position) return;
        
        // 斷開 target
        targetPrev->next = target->next;
        
        // 插入到 position 後
        target->next = position->next;
        position->next = target;
    }
};
```

**常見錯誤與陷阱**

```cpp
// 錯誤 1：過早覆蓋指標導致循環
void badMove(Node* prev, Node* target) {
    target->next = prev;  // 錯誤：先改 target->next，丟失原連結
    prev->next = target->next;  // 現在 target->next 已改，造成循環
}

// 錯誤 2：未處理頭節點交換
void badSwapFirst(Node*& head, Node* first, Node* second) {
    first->next = second->next;
    second->next = first;
    // 錯誤：忘記更新 head 為 second
}

// 錯誤 3：懸空節點
void badReorder(Node* prev, Node* target) {
    prev->next = target;  // 可能造成原有 target->next 遺失
}
```

**效能優化技巧**

```cpp
// 優化 1：使用 dummy node 簡化重排
Node* reorderWithDummy(Node* head) {
    Node dummy;
    dummy.next = head;
    Node* prev = &dummy;
    
    // 假設重排邏輯...
    while (prev->next) {
        if (prev->next->data > prev->next->next->data) {
            Node* temp = prev->next->next;
            prev->next->next = temp->next;
            temp->next = prev->next;
            prev->next = temp;
        }
        prev = prev->next;
    }
    return dummy.next;
}

// 優化 2：批次重排
void batchReorder(std::vector<Node*>& nodes) {
    // 假設 nodes 是要重排的節點列表
    for (size_t i = 0; i < nodes.size() - 1; i++) {
        if (nodes[i]->data > nodes[i+1]->data) {
            std::swap(nodes[i], nodes[i+1]);
        }
    }
}
```

### 參考來源
[1] Yuan Ze University, "CS203A Data Structures - Linked Lists (Chapter 4)," Lecture Slides, pp. 54-72.  
[2] GeeksforGeeks, "Swap Nodes in Linked List," https://www.geeksforgeeks.org/swap-nodes-in-a-linked-list-without-swapping-data/  
[3] LeetCode, "Sort List," https://leetcode.com/problems/sort-list/

### 理解檢核

1. **問題**：在 MoveTo 操作中，為什麼錯誤順序會產生懸空節點？如何避免？

   **答案**：錯誤順序（如先改 target->next = prev）會覆蓋原有連結資訊，導致後續節點無法存取，形成懸空節點。避免方法：先保存必要連結（如 afterTarget = target->next），再修改指標。原則是先斷開再重新連結。

2. **問題**：在交換兩個相鄰節點時，如果第一個是頭節點，需注意什麼？

   **答案**：需更新 head 指向新第一節點（second）。忽略此步會導致 head 仍指向舊節點，列表頭部錯誤。使用 dummy node 可簡化此邊界情況。

3. **問題**：指標操作的依賴性如何影響重排演算法的設計？

   **答案**：指標依賴意味著修改順序必須考慮資訊丟失（如 target->next 被覆蓋前需保存）。設計時，先分析所有必要連結，引入臨時變數保存，再逐步修改。雙向鏈結串列因有 prev 可減輕依賴問題。

---

## Page 73-96 — 鏈結串列排序

### 摘要
本部分討論鏈結串列的排序演算法，以選擇排序（Selection Sort）為例。比較陣列與鏈結串列的實作差異：陣列交換值，鏈結串列可交換值或指標。強調指標交換的優勢（維持穩定性），並步驟分解過程，使用 dummy node 優化邊界。

### 重點整理

**選擇排序核心**：
- 從未排序部分找最小值
- 與未排序首元素交換
- 分 sorted / unsorted 兩部分

**鏈結串列實作變體**：
- 交換值：簡單但不穩定
- 交換指標：穩定，使用 MoveTo 操作

### PDF 原文：陣列選擇排序

```pseudocode
procedure selectionSort(A[1..n]):
  for i from 1 to n-1:
    minIndex = i
    for j from i+1 to n:
      if A[j] < A[minIndex]:
        minIndex = j
    swap A[i] and A[minIndex]
```

### <加深的內容>

**鏈結串列選擇排序 - 交換值**

```cpp
void selectionSortValue(Node* head) {
    for (Node* i = head; i && i->next; i = i->next) {
        Node* minNode = i;
        for (Node* j = i->next; j; j = j->next) {
            if (j->data < minNode->data) minNode = j;
        }
        if (minNode != i) {
            std::swap(i->data, minNode->data);
        }
    }
}
```

**鏈結串列選擇排序 - 交換指標**

```cpp
Node* selectionSortPointer(Node* head) {
    if (!head) return head;
    Node dummy{0, head};  // Dummy node 簡化頭部處理
    Node* sortedTail = &dummy;
    
    while (sortedTail->next) {
        Node* minPrev = sortedTail;
        for (Node* p = sortedTail; p->next; p = p->next) {
            if (p->next->data < minPrev->next->data) {
                minPrev = p;
            }
        }
        Node* minNode = minPrev->next;
        // Detach minNode
        minPrev->next = minNode->next;
        // Insert after sortedTail
        minNode->next = sortedTail->next;
        sortedTail->next = minNode;
        sortedTail = sortedTail->next;
    }
    return dummy.next;
}
```

**穩定性比較**

- 交換值：不穩定（相同值順序可能變）
- 交換指標：穩定（保留原相對順序）

**效能分析**

| 操作 | 陣列 | 鏈結串列 (交換值) | 鏈結串列 (交換指標) |
|------|------|-----------------|-------------------|
| 時間複雜度 | O(n^2) | O(n^2) | O(n^2) |
| 空間複雜度 | O(1) | O(1) | O(1) |
| 穩定性 | 不穩定 | 不穩定 | 穩定 |

**常見錯誤與陷阱**

```cpp
// 錯誤 1：未處理 dummy node 導致頭部錯誤
Node* badSort(Node* head) {
    // 缺少 dummy，頭部交換失敗
    Node* sortedTail = head;  // 錯誤
    // ...
}

// 錯誤 2：找 min 時未檢查 next
for (Node* p = sortedTail; p; p = p->next) {  // 錯誤：應 p->next
    if (p->data < minPrev->next->data) {  // 錯比 p 而非 p->next
        // ...
    }
}
```

**效能優化技巧**

```cpp
// 優化 1：提早終止（已排序檢查）
bool isSorted(Node* head) {
    while (head && head->next) {
        if (head->data > head->next->data) return false;
        head = head->next;
    }
    return true;
}

// 優化 2：合併排序（更好複雜度 O(n log n)）
Node* mergeSort(Node* head) {
    if (!head || !head->next) return head;
    
    // 找中點
    Node* slow = head;
    Node* fast = head->next;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    Node* mid = slow->next;
    slow->next = nullptr;
    
    // 遞迴排序
    Node* left = mergeSort(head);
    Node* right = mergeSort(mid);
    
    // 合併
    Node dummy(0);
    Node* tail = &dummy;
    while (left && right) {
        if (left->data < right->data) {
            tail->next = left;
            left = left->next;
        } else {
            tail->next = right;
            right = right->next;
        }
        tail = tail->next;
    }
    tail->next = left ? left : right;
    return dummy.next;
}
```

### 參考來源
[1] Yuan Ze University, "CS203A Data Structures - Linked Lists (Chapter 4)," Lecture Slides, pp. 73-96.  
[2] T. H. Cormen, C. E. Leiserson, R. L. Rivest, and C. Stein, *Introduction to Algorithms*, 3rd ed. MIT Press, 2009, Ch. 2 "Getting Started."  
[3] LeetCode, "Sort List," https://leetcode.com/problems/sort-list/

### 理解檢核

1. **問題**：鏈結串列選擇排序中，交換指標比交換值有何優勢？

   **答案**：交換指標維持排序穩定性（相同值保留原順序），而交換值不穩定。指標交換也避免資料複製開銷，尤其資料複雜時。但需小心指標順序，避免懸空節點。

2. **問題**：在選擇排序中使用 dummy node 的好處是什麼？

   **答案**：Dummy node 簡化頭部邊界處理（如頭為最小值），讓所有操作統一（無需特殊檢查 head）。它充當虛擬前驅，讓 sortedTail 從 dummy 開始。

3. **問題**：為什麼選擇排序在鏈結串列中仍是 O(n^2)，無法優化？

   **答案**：找最小值需 O(n) 遍歷未排序部分，每次迭代都如此，總計 O(n^2)。鏈結串列無隨機存取，無法如陣列般優化。更好選擇是合併排序 (O(n log n))。

---

## Page 97-104 — 指標交換進階

### 摘要
本部分探討透過指標操縱交換兩個節點，強調前驅節點（predecessor）的重要性。在單向鏈結串列中，交換需知道 a' 和 b'（a 和 b 的前驅）。示範步驟，並警告未處理前驅會導致錯誤連結。

### 重點整理

**交換步驟**：
1. 保存臨時指標：temp_a = a, temp_b = b
2. 更新前驅：a'->next = b, b'->next = a
3. 更新節點：a->next = temp_b->next, b->next = temp_a->next

**關鍵**：單向鏈結串列交換需前驅；雙向更簡單。

### <加深的內容>

**交換兩個節點**

```cpp
void swapNodes(Node*& head, Node* a, Node* b) {
    if (!a || !b || a == b) return;
    
    // 找前驅 (假設已知或遍歷找)
    Node* aPrev = findPrev(head, a);
    Node* bPrev = findPrev(head, b);
    
    if (!aPrev || !bPrev) return;
    
    // 如果 a 是頭
    if (a == head) head = b;
    else if (b == head) head = a;
    
    // 保存 next
    Node* aNext = a->next;
    Node* bNext = b->next;
    
    // 更新前驅
    aPrev->next = b;
    bPrev->next = a;
    
    // 更新節點
    a->next = bNext;
    b->next = aNext;
}

Node* findPrev(Node* head, Node* target) {
    Node* prev = nullptr;
    Node* curr = head;
    while (curr != target) {
        prev = curr;
        curr = curr->next;
    }
    return prev;
}
```

**常見錯誤與陷阱**

```cpp
// 錯誤 1：未處理頭節點交換
void badSwap(Node* a, Node* b) {
    Node* temp = a->next;
    a->next = b->next;
    b->next = temp;
    // 錯誤：若 a/b 是頭，head 未更新
}

// 錯誤 2：相鄰節點交換遺漏
void badAdjacentSwap(Node* a, Node* b) {
    a->next = b->next;
    b->next = a;  // 錯誤：未處理前驅
}
```

### 參考來源
[1] Yuan Ze University, "CS203A Data Structures - Linked Lists (Chapter 4)," Lecture Slides, pp. 97-104.  
[2] GeeksforGeeks, "Swap Nodes in Linked List Without Swapping Data," https://www.geeksforgeeks.org/swap-nodes-in-a-linked-list-without-swapping-data/

### 理解檢核

1. **問題**：為什麼單向鏈結串列交換節點需知道前驅節點？

   **答案**：因為無法從節點回溯前一個，需更新前驅->next 指向新節點。無前驅無法斷開原連結，導致錯誤或遺失節點。

2. **問題**：交換兩個節點時，如果其中一個是頭節點，需額外處理什麼？

   **答案**：更新 head 指向新頭節點（如交換 head 和 b，head = b）。忽略會導致列表頭指向錯誤。

3. **問題**：指標交換與值交換在穩定性上有何差異？

   **答案**：指標交換穩定（保留相對順序），值交換不穩定（相同值可能換位）。指標交換適合需穩定排序的場景。

---

## Page 105-108 — AI 學習提示與總結

### 摘要
本部分提供使用 AI 學習鏈結串列的提示，從進階類型、複雜操作到實際應用。強調時間/空間分析、邊界處理與優化。作為章節總結，鼓勵實作與分析。

### 重點整理

**AI 學習提示**：
1. 進階類型：雙向/循環/比較
2. 複雜操作：插入/刪除/搜尋/反轉
3. 演算法：合併/循環偵測/中點/去重
4. 記憶體與錯誤：分配/邊界/洩漏
5. 效能：複雜度/陣列 vs 鏈結/貿易-off
6. 技巧：sentinel/雙指標/遞迴/模板
7. 應用：堆疊/佇列/Undo/雜湊/播放列表

### <加深的內容>

**實作範例：合併兩個排序鏈結串列**

```cpp
Node* mergeTwoLists(Node* l1, Node* l2) {
    Node dummy(0);
    Node* tail = &dummy;
    
    while (l1 && l2) {
        if (l1->data < l2->data) {
            tail->next = l1;
            l1 = l1->next;
        } else {
            tail->next = l2;
            l2 = l2->next;
        }
        tail = tail->next;
    }
    tail->next = l1 ? l1 : l2;
    return dummy.next;
}
```

**循環偵測（Floyd 演算法）**

```cpp
bool hasCycle(Node* head) {
    if (!head) return false;
    Node* slow = head;
    Node* fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) return true;
    }
    return false;
}
```

### 參考來源
[1] Yuan Ze University, "CS203A Data Structures - Linked Lists (Chapter 4)," Lecture Slides, pp. 105-108.  
[2] LeetCode, "Merge Two Sorted Lists," https://leetcode.com/problems/merge-two-sorted-lists/  
[3] LeetCode, "Linked List Cycle," https://leetcode.com/problems/linked-list-cycle/

### 理解檢核

1. **問題**：何時使用 sentinel node？

   **答案**：簡化邊界如頭/尾操作，避免特殊檢查空列表或頭刪除。常見於排序、合併。

2. **問題**：Floyd 循環偵測如何工作？

   **答案**：慢指標每次一步，快指標兩步。若有循環，快慢終會相遇（因相對速度）。時間 O(n)，空間 O(1)。

3. **問題**：鏈結串列適合哪些應用，而非陣列？

   **答案**：頻繁插入/刪除（如 Undo）、未知大小（如播放列表）、循環（如輪詢）。不適合隨機存取。
