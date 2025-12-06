# 堆疊與佇列學習筆記

## 一、前置基礎：陣列與受限存取的概念

陣列（Array）是計算機中最基本的線性資料結構，它佔據一塊連續的記憶體空間，並透過索引（Index）直接存取元素，時間複雜度為 O(1)。我們通常用類似 `[64, 34, 25, 12, 22, 11, 90, 8]` 的整數序列來表示一個陣列。

陣列雖然存取快速，但在靜態實作中大小固定，缺乏靈活性。更重要的是，陣列允許對任何位置的元素進行隨機存取和修改。這種「全能」的特性在某些情境下並非優點，反而可能導致錯誤。因此，我們引入了兩種對陣列操作施加特定限制的抽象資料型別（ADT）：**堆疊（Stack）** 和 **佇列（Queue）**。它們被稱為「受限的線性結構」。

### 核心概念：存取限制決定了資料結構的行為
*   **情境一：單一入口點（Stack 的雛形）**：想像一個只有一個開口的口袋。你只能從這個開口放入（Push）或取出（Pop）物品，最後放進去的東西會最先被拿出來。這種行為模式稱為 **LIFO（後進先出，Last-In-First-Out）**。
*   **情境二：分離的入口與出口（Queue 的雛形）**：想像一個排隊的隊伍。新來的人從隊伍的**尾端（Rear）** 加入（Enqueue），而服務總是從隊伍的**前端（Front）** 離開（Dequeue）。這種行為模式稱為 **FIFO（先進先出，First-In-First-Out）**。

將陣列在視覺上旋轉90度，可以幫助我們理解這些限制：垂直的陣列就像一個垂直的容器，限制存取點（頂部或兩端）就模擬了上述的行為。

#### 延伸理解
*   **動態陣列**：現代程式語言（如 C++ 的 `std::vector`）提供了可動態調整大小的陣列。其原理是當容量不足時，分配一塊更大的新記憶體，將舊資料複製過去。透過「攤銷分析（Amortized Analysis）」，可以證明其平均插入成本仍為 O(1)。
*   **應用場景**：在資源受限的系統（如嵌入式微控制器）中，靜態大小的陣列因其確定性和低開銷而被廣泛使用。

#### 範例程式碼：基礎陣列
```cpp
#include <iostream>
#define MAX_SIZE 8  // 靜態定義大小，如範例所示

int main() {
    // 初始化一個與筆記範例相同的整數陣列
    int arr[MAX_SIZE] = {64, 34, 25, 12, 22, 11, 90, 8};
    
    std::cout << "整數陣列內容: ";
    for (int i = 0; i < MAX_SIZE; ++i) {
        std::cout << arr[i] << " "; // 隨機存取：直接透過索引 i 讀取
    }
    std::cout << std::endl;
    
    // 嘗試「不受限」地修改中間元素
    arr[3] = 100; // 直接將第4個元素（12）改為100
    std::cout << "修改後 arr[3]: " << arr[3] << std::endl;
    
    return 0;
}
// 這個程式展示了陣列的隨機存取能力。堆疊和佇列將封裝此陣列，並限制存取方式。
```

---

## 二、堆疊（Stack）的詳解與實作

### 1. 定義與抽象資料型別（ADT）
堆疊是一種遵循 **LIFO** 原則的線性結構。常見的比喻是疊盤子：你總是將新盤子放在最上面（Push），也總是從最上面拿走盤子（Pop）。

**堆疊的抽象操作（ADT）包括**：
*   `CreateS()`: 建立一個空堆疊。
*   `IsFull(S)`: 檢查堆疊 S 是否已滿。
*   `IsEmpty(S)`: 檢查堆疊 S 是否為空。
*   `Push(S, item)`: 將項目 `item` 放入堆疊 S 的頂部。
*   `Pop(S)`: 從堆疊 S 的頂部移除並返回一個項目。

### 2. 使用陣列實作堆疊
實作時需要一個陣列 `stack[]`、一個標記頂部位置的變數 `top`、以及一個代表容量的 `MAX_SIZE`。

**設計關鍵：底部與頂部的選擇**
如果將陣列索引 `0` 的位置作為堆疊**底部（Bottom）**，`top` 指向最後一個有效元素（初始化為 `-1` 表示空堆疊），其優點在於：
1.  **直觀**：符合記憶體位址增長方向。
2.  **擴展高效**：當需要動態擴容時（例如使用 `realloc`），只需要在陣列後方增加空間，無需移動現有元素。
3.  **判斷簡單**：`top == -1` 表示空；`top == MAX_SIZE - 1` 表示滿。

**基本操作邏輯**：
*   **Push**: 先檢查是否已滿。若未滿，則將 `top` 加 1，然後將值存入 `stack[top]`。
*   **Pop**: 先檢查是否為空。若不為空，則先讀取 `stack[top]` 的值，然後將 `top` 減 1。

#### 範例程式碼：陣列實作堆疊
```cpp
#include <iostream>
#define MAX_SIZE 8

class ArrayStack {
private:
    int arr[MAX_SIZE];
    int top; // 指向頂部元素的索引
    
public:
    // 建構子：初始化空堆疊，top = -1
    ArrayStack() : top(-1) {}
    
    bool isEmpty() { return top == -1; }
    bool isFull()  { return top == MAX_SIZE - 1; }
    
    void push(int value) {
        if (isFull()) {
            std::cout << "堆疊已滿！無法加入 " << value << std::endl;
            return;
        }
        arr[++top] = value; // top先加1，再存入值
        std::cout << "已推入: " << value << std::endl;
    }
    
    int pop() {
        if (isEmpty()) {
            std::cout << "堆疊已空！" << std::endl;
            return -1; // 返回一個錯誤碼
        }
        int value = arr[top--]; // 先取值，再將top減1
        std::cout << "已彈出: " << value << std::endl;
        return value;
    }
    
    void display() {
        if (isEmpty()) {
            std::cout << "堆疊為空。" << std::endl;
            return;
        }
        std::cout << "堆疊內容 (頂->底): ";
        for (int i = top; i >= 0; --i) {
            std::cout << arr[i] << " ";
        }
        std::cout << std::endl;
    }
};

int main() {
    ArrayStack s;
    int inputs[] = {64, 34, 25, 12};
    
    for (int val : inputs) s.push(val);
    s.display(); // 顯示當前堆疊
    
    std::cout << "\n執行一次 pop():" << std::endl;
    s.pop(); // 應彈出 12
    s.display();
    
    return 0;
}
// 此實作將陣列索引0作為底部，是最高效的方式。
// 動態擴展思路：可以使用 std::vector<int> arr 替代陣列，在 push 時若空間不足，則執行 arr.resize(arr.size() * 2)。
```

#### 延伸討論：為什麼選擇索引0作為底部？
若選擇陣列末端（索引 `MAX_SIZE-1`）作為底部，`top` 初始化為 `MAX_SIZE`。在 `push` 時需要執行 `arr[--top] = value`。雖然邏輯可行，但當需要動態擴容時，必須將所有元素向後（向更高索引）移動，以騰出前面的空間，這會導致 O(n) 的時間成本，效率低下。因此，選擇索引0作為底部是更優的設計。

---

## 三、佇列（Queue）的詳解與實作

### 1. 定義與抽象資料型別（ADT）
佇列是一種遵循 **FIFO** 原則的線性結構。就像在超市結帳排隊，先到的人先接受服務。

**佇列的抽象操作（ADT）包括**：
*   `CreateQ()`: 建立一個空佇列。
*   `IsFullQ(Q)`: 檢查佇列 Q 是否已滿。
*   `IsEmptyQ(Q)`: 檢查佇列 Q 是否為空。
*   `AddQ(Q, item)` (或 `Enqueue`): 將項目 `item` 加入佇列 Q 的尾端。
*   `DeleteQ(Q)` (或 `Dequeue`): 從佇列 Q 的前端移除並返回一個項目。

### 2. 使用陣列實作佇列及其挑戰
實作佇列需要兩個指標/索引：`front`（指向隊首元素）和 `rear`（指向隊尾元素的下一個空位）。還需要一個陣列 `queue[]` 和 `MAX_SIZE`。

**「線性佇列」的問題**：
如果簡單地讓 `front` 和 `rear` 只增不減，在執行多次 `dequeue` 後，`front` 會不斷後移。即使陣列前端空出許多位置，當 `rear` 到達陣列末端時，程式仍會判斷佇列已滿。這種現象稱為「**假性溢位（False Overflow）**」，因為陣列實際上並未真正佔滿。

**解決方案：循環佇列（Circular Queue）**
將陣列在邏輯上視為一個環。當指標到達陣列末端時，再前進一步就回到開頭。
*   **初始化**：`front = 0`, `rear = 0`。
*   **Enqueue**: `queue[rear] = item; rear = (rear + 1) % MAX_SIZE;`
*   **Dequeue**: `item = queue[front]; front = (front + 1) % MAX_SIZE;`
*   **判斷滿**：為了區分「滿」和「空」的狀態（因為 `front == rear` 可能表示空），通常有兩種策略：
    1.  使用一個 `count` 變數記錄元素數量。`count == 0` 為空，`count == MAX_SIZE` 為滿。
    2.  犧牲一個陣列單元：當 `(rear + 1) % MAX_SIZE == front` 時，即認為佇列已滿。

#### 範例程式碼：循環佇列實作
```cpp
#include <iostream>
#define MAX_SIZE 8

class CircularQueue {
private:
    int arr[MAX_SIZE];
    int front, rear, count; // 使用 count 輔助判斷
    
public:
    CircularQueue() : front(0), rear(0), count(0) {}
    
    bool isEmpty() { return count == 0; }
    bool isFull()  { return count == MAX_SIZE; }
    
    void enqueue(int value) {
        if (isFull()) {
            std::cout << "佇列已滿！無法加入 " << value << std::endl;
            return;
        }
        arr[rear] = value;
        rear = (rear + 1) % MAX_SIZE; // 循環移動
        count++;
        std::cout << "已入隊: " << value << std::endl;
    }
    
    int dequeue() {
        if (isEmpty()) {
            std::cout << "佇列已空！" << std::endl;
            return -1;
        }
        int value = arr[front];
        front = (front + 1) % MAX_SIZE; // 循環移動
        count--;
        std::cout << "已出隊: " << value << std::endl;
        return value;
    }
    
    void display() {
        if (isEmpty()) {
            std::cout << "佇列為空。" << std::endl;
            return;
        }
        std::cout << "佇列內容 (前->後): ";
        int i = front;
        for (int c = 0; c < count; ++c) {
            std::cout << arr[i] << " ";
            i = (i + 1) % MAX_SIZE;
        }
        std::cout << std::endl;
    }
};

int main() {
    CircularQueue q;
    // 模擬筆記中的情境
    for (int i = 1; i <= 6; ++i) q.enqueue(i*10); // 加入 10,20,30,40,50,60
    q.display();
    
    std::cout << "\n執行三次 dequeue():" << std::endl;
    q.dequeue(); // 10
    q.dequeue(); // 20
    q.dequeue(); // 30
    q.display();
    
    std::cout << "\n再嘗試加入兩個元素:" << std::endl;
    q.enqueue(70);
    q.enqueue(80);
    q.display(); // 應顯示 40,50,60,70,80，前端空間被成功復用
    return 0;
}
```

---

## 四、鏈結串列實作與綜合比較

### 1. 使用鏈結串列實作
鏈結串列（Linked List）提供了另一種實作堆疊和佇列的方式，其核心優點是**動態大小**。

*   **鏈結堆疊**：只需要一個指向串列**頭部（Head）** 的指標 `top`。
    *   `Push`: 建立新節點，將其 `next` 指向當前的 `top`，然後將 `top` 更新為新節點。（在頭部插入）
    *   `Pop`: 檢查 `top` 是否為空。若不為空，記錄 `top` 節點的值，將 `top` 更新為 `top->next`，然後刪除原節點。

*   **鏈結佇列**：需要兩個指標，`front` 指向隊首節點，`rear` 指向隊尾節點。
    *   `Enqueue`: 建立新節點，將當前 `rear->next` 指向新節點，然後更新 `rear` 為新節點。（在尾部插入）
    *   `Dequeue`: 檢查 `front` 是否為空。若不為空，記錄 `front` 節點的值，將 `front` 更新為 `front->next`。如果更新後 `front` 為空，別忘了將 `rear` 也設為空。

### 2. 實作方式比較

| 特性 | **陣列實作** | **鏈結串列實作** |
| :--- | :--- | :--- |
| **大小** | 固定（靜態陣列）或需動態調整（向量） | 動態，隨需求增長 |
| **記憶體使用** | 連續區塊，**快取命中率高**。可能浪費預分配空間。 | 分散節點，每個節點含資料和指標，有**額外開銷**。記憶體可能碎片化。 |
| **基本操作時間** | Push/Pop, Enqueue/Dequeue 均為 **O(1)**。動態調整大小時有攤銷成本。 | Push/Pop, Enqueue/Dequeue 均為 **O(1)**（需注意佇列Enqueue需找到尾端，若無rear指標則為O(n)）。 |
| **溢位** | 可能發生（需預先定義大小或處理調整）。 | 除非系統記憶體耗盡，否則不會溢位。 |
| **實作複雜度** | 相對簡單，尤其循環佇列需注意邊界。 | 需處理指標操作和動態記憶體分配/釋放。 |

**選擇建議**：
*   當資料量上限已知或可預估，且追求極致效能時，可選擇**陣列（或向量）實作**。
*   當資料量變化很大、難以預估上限，或頻繁在結構中間插入/刪除（此為堆疊/佇列外的需求）時，應選擇**鏈結串列實作**。

#### 範例程式碼：鏈結佇列實作
```cpp
#include <iostream>

struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class LinkedQueue {
private:
    Node* front;
    Node* rear;
    
public:
    LinkedQueue() : front(nullptr), rear(nullptr) {}
    
    ~LinkedQueue() { // 解構子，釋放所有節點記憶體
        while (!isEmpty()) {
            dequeue();
        }
    }
    
    bool isEmpty() { return front == nullptr; }
    
    void enqueue(int value) {
        Node* newNode = new Node(value);
        if (rear == nullptr) { // 佇列為空
            front = rear = newNode;
        } else {
            rear->next = newNode;
            rear = newNode;
        }
        std::cout << "已入隊: " << value << std::endl;
    }
    
    int dequeue() {
        if (isEmpty()) {
            std::cout << "佇列已空！" << std::endl;
            return -1;
        }
        Node* temp = front;
        int value = temp->data;
        front = front->next;
        
        if (front == nullptr) { // 如果取出後佇列變空
            rear = nullptr;
        }
        
        delete temp; // 釋放記憶體
        std::cout << "已出隊: " << value << std::endl;
        return value;
    }
};
```

---

## 五、進階挑戰與應用

1.  **用兩個堆疊模擬一個佇列**
    *   **思路**：維護兩個堆疊 `stackIn` 和 `stackOut`。
    *   **Enqueue**：直接 `push` 到 `stackIn`。
    *   **Dequeue**：
        1.  如果 `stackOut` 為空，則將 `stackIn` 中的所有元素逐一 `pop` 出來並 `push` 到 `stackOut` 中。此步驟將元素順序顛倒了兩次，恢復為 FIFO 順序。
        2.  從 `stackOut` 中 `pop` 出元素並返回。
    *   **攤銷時間複雜度**：每個元素最多被壓入和彈出兩個堆疊各一次，因此攤銷後 `enqueue` 和 `dequeue` 均為 **O(1)**。

2.  **現實應用：印表機工作佇列**
    *   基礎需求是 FIFO。但當有「緊急」文件時，簡單的佇列無法處理。
    *   **解決方案**：使用**優先權佇列（Priority Queue）**，通常以**二元堆積（Binary Heap）** 實作。每個工作帶有優先級。`Dequeue` 時總是取出優先級最高的工作（若優先級相同，則依到達時間 FIFO）。
    *   作業系統中的排程器常使用此類結構，並可能引入「老化（Aging）」機制，逐漸提高等待過久工作的優先級，防止「饑餓（Starvation）」。

3.  **經典問題：河內塔（Towers of Hanoi）**
    *   這是遞迴的經典範例，其執行過程隱含了堆疊的 LIFO 行為。
    *   可以用三個堆疊來具體模擬三根柱子上的圓盤。遞迴的每一步 `move(n, source, target, auxiliary)` 都對應著從一個堆疊 `pop` 並 `push` 到另一個堆疊的操作。
    *   移動次數的數學解為 **2^n - 1**，顯示了問題的指數級複雜度。

**總結**：堆疊與佇列是理解更複雜演算法（如遞迴、圖形遍歷 BFS/DFS）的基石。掌握其核心思想（LIFO/FIFO）、實作細節（尤其是邊界條件）以及適用場景，是學習資料結構的重要一步。
