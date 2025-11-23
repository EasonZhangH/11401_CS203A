# 資料結構：鏈結串列完整筆記（Linked Lists）

**作者：Manus AI**
**來源：CS203A 資料結構 (Yuan Ze University, Chapter 4) 筆記整理與增補**

---

## 目錄（Table of Contents）

1.  [列表（List）概念與抽象資料型別（ADT）](#1-列表list概念與抽象資料型別adt)
2.  [高效率的插入與刪除操作](#2-高效率的插入與刪除操作)
3.  [陣列的限制](#3-陣列的限制)
4.  [鏈結串列的引入](#4-鏈結串列的引入)
5.  [鏈結串列的變體](#5-鏈結串列的變體)
6.  [陣列與鏈結串列的比較與實務建議](#6-陣列與鏈結串列的比較與實務建議)
7.  [進階議題與優化技巧](#7-進階議題與優化技巧)
8.  [附錄：GitHub 專案結構建議](#8-附錄github-專案結構建議)

---

## 1. 列表（List）概念與抽象資料型別（ADT）

### 摘要
本節介紹了「列表（List）」的基本概念，說明列表是一系列按照特定順序連接的項目。列表在日常生活與電腦科學中都極為常見，例如待辦事項清單、郵寄清單等。在電腦科學領域，列表是實作堆疊、佇列、集合、雜湊表、圖等資料結構的基礎。

### 重點整理
- **列表定義**：一系列依序排列、通常逐項列出的連接項目或名稱
- **日常應用**：檢查清單、郵寄清單、待辦清單、願望清單
- **電腦科學應用**：
  - 作業系統中的記憶體管理
  - 資料結構實作（堆疊、佇列、集合、雜湊表、圖）
- **核心特性**：元素間具有順序性、可包含多個項目

### PDF 原文重要定義

> **List**: A number of connected items or names written or printed consecutively, typically one below the other.

### 加深的內容

**為什麼需要「列表」這個抽象概念？**

在資料結構的學習中，「列表」是一個非常重要的**抽象資料型別（ADT）**。它提供了一個統一的介面來處理有序資料的集合，而不關心底層實作細節。這種抽象化的好處在於：

1.  **關注點分離**：使用者只需知道列表「能做什麼」（操作介面），而不必了解「如何實作」（底層機制）
2.  **靈活性**：同樣的列表介面可以用不同的方式實作（陣列、鏈結串列、跳躍表等），根據應用場景選擇最適合的實作
3.  **可維護性**：當需要更換實作方式時，只要保持介面不變，使用該列表的程式碼無需修改

**列表在作業系統中的實際應用**

作業系統中廣泛使用列表來管理各種資源。例如：
- **行程控制區塊（PCB）鏈**：作業系統維護一個所有行程的列表，每個行程有自己的狀態（執行中、就緒、等待）
- **記憶體空閒區塊鏈**：使用鏈結串列追蹤記憶體中的空閒區塊，便於動態記憶體分配
- **I/O 請求佇列**：裝置驅動程式使用列表管理待處理的 I/O 請求
- **檔案系統 inode 鏈**：檔案系統使用列表結構組織檔案的元資料

這些應用場景的共同特點是：元素數量動態變化、需要頻繁插入刪除、順序訪問為主。這些特性使得鏈結串列成為比陣列更適合的選擇。

### 額外加深加廣的內容

**1. 相關變體或延伸主題**

列表的抽象概念可以延伸到多種變體：
- **雙向列表（Doubly Linked List）**：每個元素同時記錄前後鄰居，支援雙向遍歷
- **跳躍列表（Skip List）**：多層索引結構，提供 O(log n) 的搜尋效能
- **展開列表（Unrolled Linked List）**：每個節點儲存多個元素，結合陣列與鏈結串列優點
- **XOR 鏈結串列**：使用 XOR 運算壓縮儲存空間，每個節點只需一個指標欄位

**2. 實務應用場景分析**

在現代軟體開發中，列表的應用無所不在：

**網頁瀏覽器歷史記錄**：使用雙向鏈結串列，支援前進/後退操作，每次訪問新頁面時插入新節點，並可能刪除過舊的記錄。

**文字編輯器的 Undo/Redo**：
```cpp
// 文字編輯器的命令歷史管理（概念性程式碼）
class CommandHistory {
    struct Command {
        string action;      // 動作類型
        string data;        // 相關資料
        Command* prev;      // 上一個命令
        Command* next;      // 下一個命令
    };
    
    Command* current;  // 目前位置
    
    void execute(Command* cmd) {
        // 執行命令並加入歷史
        // 刪除 current 之後的所有命令（分支歷史）
    }
    
    void undo() { 
        if (current) current = current->prev; 
    }
    
    void redo() { 
        if (current && current->next) current = current->next; 
    }
};
```

**音樂播放器播放清單**：循環鏈結串列特別適合實作「重複播放」功能，最後一首歌的 next 指標指回第一首。

**3. 潛在研究問題與開放議題**

- **記憶體局部性問題**：鏈結串列的節點分散在記憶體中，導致快取命中率低。如何設計更 cache-friendly 的鏈結結構？
- **無鎖並發列表**：在多執行緒環境下，如何設計無鎖（lock-free）的鏈結串列以提高並發效能？
- **持久化資料結構**：如何設計支援版本控制的鏈結串列（每次修改保留舊版本）？

### 理解檢核
1.  **問題**：為什麼作業系統的行程管理更適合使用鏈結串列而非陣列？
    **答案**：因為行程數量動態變化，使用鏈結串列可以在 O(1) 時間內插入或刪除行程，而陣列需要移動元素或預先分配大量空間。
2.  **問題**：列表的「抽象」特性如何幫助程式設計？
    **答案**：抽象使得使用者只需關注列表的操作介面（如 insert、delete、search），而不必了解底層實作。這允許在不改變使用程式碼的前提下，替換不同的實作方式（如從陣列改為鏈結串列）。
3.  **問題**：請舉一個日常生活中「列表」概念的例子，並說明其特性。
    **答案**：例如餐廳的候位名單。特性包括：(1) 有順序性（先到先候位）(2) 可動態增加（新顧客加入）(3) 可刪除（顧客取消或入座）(4) 需要查詢（查看排隊位置）。

---

## 2. 高效率的插入與刪除操作

### 摘要
本節提出資料結構設計的核心問題：如何實現高效率的插入與刪除操作？不同的資料結構有不同的時間複雜度：O(1)、O(n)、O(n log n)。這個問題引導我們思考陣列與鏈結串列在插入刪除操作上的效能差異。

### 重點整理
- **核心問題**：什麼是高效率的插入與刪除方式？
- **時間複雜度等級**：
  - O(1)：常數時間，最理想
  - O(n)：線性時間，與元素數量成正比
  - O(n log n)：對數線性時間，如某些排序演算法
- **設計目標**：盡可能達到 O(1) 的插入刪除效能

### 加深的內容

**時間複雜度的實務意義**

理解不同時間複雜度的實際影響對於選擇資料結構至關重要。假設我們有一個包含 1,000,000 筆資料的系統：

- **O(1) 操作**：無論資料量多大，執行時間固定，約 1 微秒
- **O(n) 操作**：需要遍歷所有元素，約 1 秒
- **O(n²) 操作**：如簡單排序，可能需要 16 分鐘以上

這就是為什麼在處理大量資料時，選擇正確的資料結構如此重要。一個看似簡單的操作（如插入元素），如果使用不當的資料結構，可能導致系統效能瓶頸。

**為什麼陣列的插入刪除成本高？**

陣列在記憶體中是連續儲存的，這帶來了以下問題：

1.  **插入中間元素**：需要將插入位置後的所有元素向後移動一位
2.  **刪除中間元素**：需要將刪除位置後的所有元素向前移動一位
3.  **記憶體限制**：陣列大小固定，插入可能導致需要重新分配更大的記憶體空間並複製所有元素

這些「移動元素」的操作在最壞情況下需要 O(n) 時間。如果應用場景需要頻繁插入刪除，陣列就不是最佳選擇。

### 額外加深加廣的內容

**1. 攤銷分析（Amortized Analysis）**

有些資料結構雖然單次操作可能較慢，但長期平均下來效能很好。例如動態陣列（如 C++ 的 `std::vector`）：
- 插入元素到尾端：通常 O(1)
- 當容量不足時：需要重新分配記憶體並複製所有元素，這次操作是 O(n)
- 但透過「每次擴容加倍容量」的策略，可以證明平均插入時間仍是 O(1)（攤銷複雜度）

**2. 不同應用場景的選擇策略**

| 應用場景 | 推薦資料結構 | 原因 |
|:---------|:-------------|:-----|
| 需要隨機存取 | 陣列 | O(1) 索引存取 |
| 頻繁首尾插入刪除 | 雙向鏈結串列或 deque | O(1) 插入刪除 |
| 需要排序且查詢頻繁 | 平衡二元搜尋樹 | O(log n) 插入刪除查詢 |
| 鍵值對且需快速查詢 | 雜湊表 | 平均 O(1) 查詢 |
| 需要維護優先順序 | 堆積（Heap） | O(log n) 插入刪除最值 |

**3. 實務案例：資料庫索引設計**

資料庫系統需要同時支援快速查詢和高效更新，通常使用 **B+ 樹**（B+ Tree）：
- 查詢：O(log n)
- 插入/刪除：O(log n)
- 範圍查詢：透過葉節點鏈結，可以高效遍歷連續資料

這是在「查詢效能」與「更新效能」之間取得平衡的經典範例。

### 理解檢核
1.  **問題**：為什麼在陣列中間插入元素需要 O(n) 時間？
    **答案**：因為陣列元素在記憶體中連續儲存，插入新元素後，需要將插入位置之後的所有元素向後移動一位以騰出空間，最壞情況需要移動 n 個元素。
2.  **問題**：什麼情況下 O(n) 的複雜度是可接受的？
    **答案**：當資料量小（如 n < 100）、操作不頻繁、或沒有更優演算法可用時，O(n) 複雜度是可接受的。例如線性搜尋在小型陣列中就很實用。
3.  **問題**：攤銷分析與最壞情況分析有何不同？
    **答案**：最壞情況分析關注單次操作的最大成本，而攤銷分析關注一系列操作的平均成本。例如動態陣列的擴容操作單次是 O(n)，但攤銷後平均每次插入仍是 O(1)。

---

## 3. 陣列的限制

### 摘要
本節詳細說明了陣列在插入和刪除操作上的限制。陣列的連續記憶體配置特性導致在插入或刪除元素時，需要移動大量元素（costly shifts），造成效能瓶頸。這些限制促使我們尋找更適合動態操作的資料結構。

### 重點整理
- **陣列結構**：連續記憶體空間儲存，例如整數陣列 `[64, 34, 25, 12, 22, 11, 90, 8]`
- **主要操作**：插入元素（Insert）、刪除元素（Delete）
- **核心問題**：Costly shifts（昂貴的元素移動）
- **改進方向**：尋找不需要移動元素的資料結構

### PDF 原文程式碼與資料

```
Array: [64, 34, 25, 12, 22, 11, 90, 8]
Integer Array
```

### 加深的內容

**陣列插入操作的詳細分析**

讓我們以程式碼來說明陣列插入操作的成本：

```cpp
#include <iostream>
using namespace std;

// 在陣列 position 位置插入元素 value
// arr: 陣列指標, size: 目前元素數量, capacity: 陣列容量
// 回傳: 插入後的新 size，若失敗則回傳 -1
int insertArray(int* arr, int size, int capacity, int position, int value) {
    // 檢查是否超出容量
    if (size >= capacity) {
        cout << "陣列已滿，無法插入" << endl;
        return -1;
    }
    
    // 檢查插入位置是否合法
    if (position < 0 || position > size) {
        cout << "插入位置不合法" << endl;
        return -1;
    }
    
    // 關鍵操作：將 position 之後的所有元素向後移動
    // 時間複雜度：O(n - position)，最壞情況 O(n)
    for (int i = size; i > position; i--) {
        arr[i] = arr[i - 1];  // 每個元素都需要一次記憶體寫入操作
    }
    
    // 在空出的位置插入新元素
    arr[position] = value;
    
    return size + 1;  // 回傳新的 size
}

// 示範程式
int main() {
    const int CAPACITY = 10;
    int arr[CAPACITY] = {64, 34, 25, 12, 22, 11, 90, 8};
    int size = 8;
    
    cout << "原始陣列: ";
    for (int i = 0; i < size; i++) cout << arr[i] << " ";
    cout << endl;
    
    // 在位置 2 插入元素 99
    size = insertArray(arr, size, CAPACITY, 2, 99);
    
    cout << "插入後陣列: ";
    for (int i = 0; i < size; i++) cout << arr[i] << " ";
    cout << endl;
    // 輸出: 64 34 99 25 12 22 11 90 8
    
    return 0;
}
```

**為什麼元素移動如此昂貴？**

從硬體層面來看，每次元素移動涉及：
1.  **讀取原始位置的資料**（記憶體讀取操作）
2.  **寫入新位置**（記憶體寫入操作）
3.  **可能觸發快取失效**（cache miss），導致需要從主記憶體載入資料

當陣列很大時（如百萬個元素），在開頭插入一個元素就需要移動所有後續元素，這在高頻操作場景下會嚴重影響系統效能。

**陣列刪除操作的成本**

刪除操作的成本與插入類似：

```cpp
// 刪除陣列 position 位置的元素
int deleteArray(int* arr, int size, int position) {
    if (position < 0 || position >= size) {
        cout << "刪除位置不合法" << endl;
        return -1;
    }
    
    // 將 position 之後的所有元素向前移動
    // 時間複雜度：O(n - position)，最壞情況 O(n)
    for (int i = position; i < size - 1; i++) {
        arr[i] = arr[i + 1];
    }
    
    return size - 1;  // 回傳新的 size
}
```

### 額外加深加廣的內容

**1. 陣列操作的時間複雜度完整分析**

| 操作 | 最好情況 | 平均情況 | 最壞情況 | 說明 |
|:-----|:---------|:---------|:---------|:-----|
| 存取（Access） | O(1) | O(1) | O(1) | 直接計算記憶體位址 |
| 搜尋（Search） | O(1) | O(n) | O(n) | 需要逐一檢查元素 |
| 插入（Insert）尾端 | O(1) | O(1) | O(1) | 若有空間，直接加在最後 |
| 插入（Insert）開頭 | O(n) | O(n) | O(n) | 需移動所有元素 |
| 插入（Insert）中間 | O(1) | O(n) | O(n) | 平均需移動一半元素 |
| 刪除（Delete） | O(1) | O(n) | O(n) | 同插入分析 |

**2. 動態陣列（Dynamic Array）的改進**

C++ 的 `std::vector` 和 Java 的 `ArrayList` 使用動態陣列技術，改善了陣列大小固定的問題：

```cpp
// std::vector 的簡化實作概念
template <typename T>
class SimpleVector {
private:
    T* data;           // 指向陣列的指標
    size_t size;       // 目前元素數量
    size_t capacity;   // 目前容量
    
    // 當容量不足時，重新分配更大的記憶體
    void resize() {
        size_t newCapacity = capacity * 2;  // 加倍容量
        T* newData = new T[newCapacity];
        
        // 複製舊資料（O(n) 操作，但不常發生）
        for (size_t i = 0; i < size; i++) {
            newData[i] = data[i];
        }
        
        delete[] data;  // 釋放舊記憶體
        data = newData;
        capacity = newCapacity;
    }
    
public:
    void push_back(const T& value) {
        if (size >= capacity) {
            resize();  // 容量不足時擴容
        }
        data[size++] = value;
    }
};
```

**動態陣列的優點**：
- 自動管理容量
- 插入尾端平均 O(1)（攤銷分析）
- 保留陣列的隨機存取優勢

**動態陣列的缺點**：
- 擴容時的單次操作仍是 O(n)
- 可能浪費記憶體（capacity > size）
- 中間插入刪除仍然昂貴

**3. 記憶體對齊與快取效能**

陣列的連續記憶體配置雖然導致插入刪除昂貴，但在「讀取連續資料」時有巨大優勢：

```cpp
// 遍歷陣列（cache-friendly）
int sumArray(int* arr, int n) {
    int sum = 0;
    for (int i = 0; i < n; i++) {
        sum += arr[i];  // 連續存取，cache 命中率高
    }
    return sum;
}
```

現代 CPU 的快取系統會一次載入一個「快取行」（Cache Line，通常 64 bytes）。陣列的連續儲存使得存取一個元素時，相鄰元素也被載入快取，後續存取非常快速。這就是為什麼在某些應用場景下（如數值計算、圖像處理），陣列仍是最佳選擇。

---

## 4. 鏈結串列的引入

### 摘要
本節透過形象化的比喻（火車車廂的連結器）引出鏈結串列的概念。鏈結串列由多個元素（節點）透過「連結器」（指標）串接而成，這種結構使得插入和刪除操作不需要移動其他元素，只需修改指標。

### 重點整理
- **核心概念**：Something should be linked（元素之間需要連結）
- **火車比喻**：火車車廂透過連結器連接，類似節點透過指標連接
- **結構轉變**：
  - 陣列：連續記憶體區塊
  - 鏈結串列：節點 + 連結器（指標）
- **必要元素**：
  - 資料欄位：儲存實際資料
  - 指標欄位：儲存下一個節點的記憶體位址
  - 頭節點（Head）：指向第一個節點的指標

### 鏈結串列的結構與操作

**節點結構**

```c
typedef struct Node {
    int data;
    struct Node* next;
} Node;

Node* createNode(int data) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    if (newNode == NULL) {
        perror("Memory allocation failed");
        exit(EXIT_FAILURE);
    }
    newNode->data = data;
    newNode->next = NULL;
    return newNode;
}
```

**遍歷操作**

遍歷鏈結串列需要從頭節點開始，依序跟隨 `next` 指標直到遇到 `NULL`。

```c
void traverseList(Node* head) {
    Node* current = head;
    int index = 0;
    while (current != NULL) {
        printf("Node %d: Data=%d, Address=%p, Next=%p\n", 
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

---

## 5. 鏈結串列的變體

### 摘要
鏈結串列有多種變體，包括單向、雙向和循環鏈結串列，它們在結構和功能上各有側重，以滿足不同的應用需求。

### 單向鏈結串列（Singly Linked List）
- **結構**：每個節點只有一個 `next` 指標。
- **特性**：只能單向遍歷。若要刪除某節點，必須先找到其**前驅節點**。

### 雙向鏈結串列（Doubly Linked List）

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
            tail = target->prev;  // 刪除頭節點
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

### 循環鏈結串列（Circular Linked List）

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
    
    void nextProcess() {
        if (current) {
            current = current->next;
            std::cout << "執行程序: " << current->id << "\n";
        }
    }
};
```

**應用 2：約瑟夫問題（Josephus Problem）**

```cpp
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
```

**應用 3：音樂播放器（循環播放）**

```cpp
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

---

## 6. 陣列與鏈結串列的比較與實務建議

### 變體的權衡取捨

| 變體 | 遍歷方向 | 插入/刪除靈活性 | 記憶體開銷 | 適用場景 |
|:-----|:---------|:----------------|:-----------|:---------|
| 單向 | 單向 (從頭到尾) | 中等 (刪除需前驅) | 低 (1 個指標) | 簡單列表、頻繁尾端插入 |
| 雙向 | 雙向 | 高 (O(1) 任意位置) | 中 (2 個指標) | 瀏覽器歷史、Undo/Redo |
| 循環 | 循環 (無盡尾) | 高 (循環處理) | 低/中 (視單/雙向) | 輪詢排程、遊戲循環 |

### 記憶體佔用的實際比較

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

### 不同操作的實際效能測試

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

/*
int main() {
    PerformanceTester::testRandomAccess();
    PerformanceTester::testFrontInsertion();
    PerformanceTester::testSequentialTraversal();
    return 0;
}
*/

// 典型輸出（實際結果依硬體而異）：
// 隨機存取效能比較 (10000 次):
//   陣列: 150 μs
//   鏈結串列: 450000 μs
//   比例: 3000:1 (鏈結串列慢 3000 倍！)
//
// 開頭插入效能比較 (10000 次):
//   陣列: 1250 ms
//   鏈結串列: 2 ms
//   比例: 625:1 (陣結慢 625 倍！)
//
// 順序遍歷效能比較 (大小 1000000):
//   陣列: 800 μs
//   鏈結串列: 3500 μs
//   比例: 4.4:1 (鏈結串列慢 4.4 倍)
```

### 實務建議總結（決策樹實作）

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

---

## 7. 進階議題與優化技巧

### 記憶體碎片化問題

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
```

**記憶體池實作**

```cpp
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
    
    // 析構函數釋放所有區塊
    ~MemoryPool() {
        for (Node* block : blocks) {
            delete[] block;
        }
    }
};
```

### 混合策略：根據資料量動態選擇（Adaptive List）

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

### 陣列塊鏈結串列（Deque 概念）

```cpp
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

### 常見錯誤與陷阱

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

---

## 8. 附錄：GitHub 專案結構建議

```
.
├── .gitignore
├── README.md
├── src
│   ├── main.cpp              # 測試程式入口
│   ├── linked_list.h         # 鏈結串列介面定義
│   ├── linked_list.cpp       # 鏈結串列實作
│   ├── doubly_linked_list.h  # 雙向鏈結串列介面定義
│   └── circular_linked_list.h# 循環鏈結串列介面定義
├── tests
│   ├── test_singly.cpp       # 單向鏈結串列單元測試
│   └── test_doubly.cpp       # 雙向鏈結串列單元測試
└── CMakeLists.txt
```

---

## 參考來源

[1] Yuan Ze University, "CS203A Data Structures - Linked Lists (Chapter 4)," Lecture Slides.
[2] T. H. Cormen, C. E. Leiserson, R. L. Rivest, and C. Stein, *Introduction to Algorithms*, 3rd ed. MIT Press, 2009, Ch. 10 "Elementary Data Structures."
[3] T. H. Cormen, C. E. Leiserson, R. L. Rivest, and C. Stein, *Introduction to Algorithms*, 3rd ed. MIT Press, 2009, Ch. 17 "Amortized Analysis."
[4] B. Stroustrup, *The C++ Programming Language*, 4th ed. Addison-Wesley, 2013, Ch. 13 "Exception Handling."
[5] GeeksforGeeks, "Circular Linked List," https://www.geeksforgeeks.org/circular-linked-list/
