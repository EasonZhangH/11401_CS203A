# 堆疊與佇列筆記

## 第 1 部分：陣列介紹與輸入/輸出限制
### PDF 原始內容總結
- 第 1 頁：標題「Data Structures - STACKS & QUEUES (CHAPTER 3)」。
- 第 2 頁：介紹陣列，以整數陣列 [64, 34, 25, 12, 22, 11, 90, 8] 為例。
- 第 3 頁：討論輸入/輸出的限制。情境 1：僅一個入口用於輸入和輸出（類似堆疊）。情境 2：一個輸入入口、一個輸出出口，且不同（類似佇列）。顯示陣列的 90° 旋轉視覺化。

**<加深的內容>**：陣列是連續記憶體區塊，適合隨機存取（O(1) 時間），但靜態實作中大小固定。這些限制突顯堆疊和佇列是如何從陣列或鏈結串列衍生的「受限」線性結構，強制操作紀律以模擬特定行為（堆疊的 LIFO，佇列的 FIFO）。範例陣列用於整個 PDF 模擬插入，顯示方向如何影響資料流。

**<額外加深加廣的內容>**：在現代計算中，陣列可動態調整（例如 C++ 的 std::vector），允許調整大小。這涉及攤銷分析—調整大小時容量加倍，使平均插入為 O(1)。擴展到多維陣列用於矩陣，但對堆疊/佇列而言，1D 足夠。現實應用：嵌入式系統（如微控制器）中的陣列因記憶體限制而有嚴格大小。

### 程式碼範例：C++ 中的基本陣列（說明第 2 頁）
```cpp
#include <iostream>
#define MAX_SIZE 8  // 如 PDF 中的固定大小

int main() {
    int arr[MAX_SIZE] = {64, 34, 25, 12, 22, 11, 90, 8};  // PDF 中的整數陣列
    
    // 列印陣列以視覺化
    std::cout << "整數陣列: ";
    for (int i = 0; i < MAX_SIZE; ++i) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    return 0;
}
// 想法：這設定基本結構。我們將在堆疊/佇列中限制存取。
```

### 從 PDF 提示延伸學習想法（第 3 頁：情境）
PDF 旋轉陣列以視覺化垂直堆疊/佇列。問題：為什麼限制方向？答案：為了強制順序—不受限陣列允許隨機存取，但堆疊/佇列防止它以確保安全（例如，並行程式設計中避免無效狀態）。延伸：在作業系統中，堆疊管理函式呼叫（無隨機 pop）；佇列處理程序排程（公平 FIFO）。

## 第 2 部分：情境 1 - 單一入口（堆疊概念與陣列實作）（第 4-7 頁）
### PDF 原始內容總結
- 第 4-6 頁：針對整數 [64,34,25,12,22,11,90,8]，選擇底部/頂部。「a」（索引 0）作為底部較好。顯示陣列填充，top 從 0 或 max_size-1 開始。
- 第 7 頁：為什麼「a」作為底部較好？top==0 表示空，top==max_size-1 表示滿。使用 realloc() 擴展陣列儲存時，只需修改 max_size，無需移位。

**<加深的內容>**：選擇底部在索引 0 符合陣列從低到高位址增長。Push：arr[top++] = value；Pop：return arr[--top]。檢查：空（top == -1 或 0，取決於初始化），滿（top == max_size）。PDF 的選擇避免移位元素，這會是 O(n) 成本。

**<額外加深加廣的內容>**：在動態堆疊中，使用 vector 自動調整大小。擴展到執行緒安全：多執行緒中的堆疊需要鎖定以防止 top 的競爭條件。現實應用：瀏覽器歷史記錄（後退按鈕 pop 最後頁面）。變體：雙端堆疊，在一個陣列中兩個堆疊。

### 程式碼範例：C++ 中的陣列基礎堆疊（基於 PDF 範例）
```cpp
#include <iostream>
#define MAX_SIZE 8

class Stack {
private:
    int arr[MAX_SIZE];
    int top;  // 頂部元素的索引
    
public:
    Stack() : top(-1) {}  // 初始化為空（top == -1 用於空檢查）
    
    bool isEmpty() { return top == -1; }
    bool isFull() { return top == MAX_SIZE - 1; }
    
    void push(int value) {
        if (isFull()) {
            std::cout << "堆疊已滿！" << std::endl;
            return;
        }
        arr[++top] = value;  // 先遞增 top 再插入（底部在 0）
    }
    
    int pop() {
        if (isEmpty()) {
            std::cout << "堆疊已空！" << std::endl;
            return -1;  // 錯誤值
        }
        return arr[top--];  // 先返回再遞減
    }
};

int main() {
    Stack s;
    int inputs[] = {64, 34, 25, 12, 22, 11, 90, 8};
    for (int val : inputs) s.push(val);  // 模擬 PDF 插入
    std::cout << "Pop: " << s.pop() << std::endl;  // 應為 8 (LIFO)
    return 0;
}
// 想法：底部在索引 0 簡化擴展。如果動態，使用 std::vector<int> arr; arr.resize(2*size);
```

### 從 PDF 問題延伸學習想法（第 4/7 頁：為什麼「a」作為底部？）
PDF 問：a 或 b？為什麼？答案：「a」（低索引）作為底部簡化檢查和擴展—無需移位元素時調整大小（O(n) vs. O(1) 附加）。延伸：在 C++ 中，realloc() 保留低到高順序。學習：用程式碼模擬—嘗試 top 在 max_size-1；插入需移位，證明低效。

## 第 3 部分：情境 2 - 分離入口/出口（佇列概念與陣列實作）（第 8-13 頁）
### PDF 原始內容總結
- 第 8-9 頁：選擇入口（a 或 b）。「a」作為 front（出口），「b」作為 rear（入口）。
- 第 10-12 頁：陣列模擬，front/rear 初始為 -1。Enqueue：rear++，插入；Dequeue：front++。
- 第 13 頁：全部 enqueue 後，dequeue 64,34,25—顯示 front 的浪費空間（非循環）。

**<加深的內容>**：非循環佇列在 dequeue 後浪費空間（front 前移，但陣列不移位）。滿：rear == max_size-1；空：front == rear。PDF 顯示線性佇列問題—dequeue 後無法 enqueue 而不移位（O(n)）。

**<額外加深加廣的內容>**：引入循環佇列修復浪費：rear = (rear + 1) % max_size。擴展到優先佇列（堆積）用於非 FIFO。現實應用：網路中的訊息佇列（例如 RabbitMQ 使用 FIFO 確保順序）。

### 程式碼範例：C++ 中的線性陣列基礎佇列（PDF 風格，非循環）
```cpp
#include <iostream>
#define MAX_SIZE 8

class Queue {
private:
    int arr[MAX_SIZE];
    int front, rear;
    
public:
    Queue() : front(-1), rear(-1) {}  // 如 PDF
    
    bool isEmpty() { return front == -1; }
    bool isFull() { return rear == MAX_SIZE - 1; }
    
    void enqueue(int value) {
        if (isFull()) {
            std::cout << "佇列已滿！" << std::endl;
            return;
        }
        if (front == -1) front = 0;  // 首次 enqueue 設定 front
        arr[++rear] = value;
    }
    
    int dequeue() {
        if (isEmpty()) {
            std::cout << "佇列已空！" << std::endl;
            return -1;
        }
        int val = arr[front++];
        if (front > rear) { front = rear = -1; }  // 若空則重設
        return val;
    }
};

int main() {
    Queue q;
    int inputs[] = {64, 34, 25, 12, 22, 11, 90, 8};
    for (int val : inputs) q.enqueue(val);
    std::cout << "Dequeue: " << q.dequeue() << " (64)" << std::endl;  // FIFO
    return 0;
}
// 想法：匹配 PDF，但 dequeue 後空間浪費。循環版：使用 % MAX_SIZE。
```

### 從 PDF 問題延伸學習想法（第 13 頁：dequeue 64,34,25 後）
PDF 顯示陣列 front 在 3，rear 在 7。問題：dequeue 後陣列有洞。答案：左移元素（O(n)），或使用循環。延伸：程式碼模擬—dequeue 後新增 enqueue；線性失敗，循環成功。學習：討論時間複雜度—線性 enqueue O(1)，但移位使 dequeue 平均 O(n)。

## 第 4 部分：堆疊與佇列定義、ADT 與範例（第 14-20 頁）
### PDF 原始內容總結
- 第 14 頁：堆疊定義（LIFO，push/pop 從 top，盤子比喻）。
- 第 15 頁：河內塔範例（圖片）。
- 第 16 頁：ADT 堆疊（抽象函式：CreateS, IsFull, IsEmpty, Push, Pop）。
- 第 17 頁：佇列定義（FIFO，enqueue rear, dequeue front，排隊比喻）。
- 第 18 頁：結帳佇列範例（圖片）。
- 第 19 頁：ADT 佇列（類似堆疊：CreateQ, IsFullQ, IsEmptyQ, AddQ, DeleteQ）。
- 第 20 頁：思考：堆疊 vs. 佇列（圖片提示）。

**<加深的內容>**：ADT 抽象實作—聚焦「什麼」（操作）而非「如何」（陣列/鏈結）。堆疊：LIFO 用於遞迴（例如，河內塔透過遞迴 push/pop 解決）。佇列：FIFO 用於公平（例如，結帳先到先服務）。

**<額外加深加廣的內容>**：河內塔複雜度：2^n - 1 步，使用堆疊處理遞迴深度。擴展到雙端佇列（deque）用於兩端。現實應用：堆疊在 undo/redo（例如 Photoshop）；佇列在 BFS 演算法。

### 程式碼範例：C++ 中的 ADT 式堆疊（抽象介面）
```cpp
// 抽象堆疊 ADT（透過類別介面）
class AbstractStack {
public:
    virtual bool isFull() = 0;
    virtual bool isEmpty() = 0;
    virtual void push(int item) = 0;
    virtual int pop() = 0;
};

// 具體實作（陣列基礎，如上節）
class ArrayStack : public AbstractStack { /* 如第 2 節實作 */ };

// 想法：ADT 允許交換實作（陣列 vs. 鏈結）而不變更客戶端程式碼。
```

### 從 PDF 提示延伸學習想法（第 20 頁：思考堆疊 vs. 佇列）
PDF 提示思考。答案：堆疊反轉順序（LIFO），佇列保留（FIFO）。延伸：使用堆疊反轉佇列？全部 enqueue 到堆疊，pop 到新佇列。學習：程式碼實作—顯示互動（堆疊作為暫存緩衝）。

## 第 5 部分：使用陣列與鏈結串列實作（第 21-26 頁）
### PDF 原始內容總結
- 第 21 頁：使用陣列實作堆疊/佇列。
- 第 22 頁：堆疊陣列變數（stack[], top, MAX_SIZE）；操作：push(++top), pop(top--)。
- 第 23 頁：佇列陣列（循環：%）；變數：front, rear，有時 count。
- 第 24 頁：使用鏈結串列實作堆疊/佇列。
- 第 25 頁：堆疊鏈結（top 指標；push 插入頭部，pop 移除頭部）。
- 第 26 頁：佇列鏈結（front/rear 指標；enqueue 在 rear, dequeue 在 front）。

**<加深的內容>**：陣列：固定大小，O(1) 操作，但溢位。鏈結：動態，O(1) 操作，但每個節點額外指標開銷（額外記憶體）。

**<額外加深加廣的內容>**：鏈結串列避免調整大小但碎片化記憶體。擴展到 STL：std::stack（vector/deque 的適配器），std::queue（在 deque 上）。現實應用：核心中的鏈結佇列用於中斷處理（動態大小）。

### 程式碼範例：C++ 中的鏈結串列基礎佇列
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
    
    bool isEmpty() { return front == nullptr; }
    
    void enqueue(int value) {
        Node* newNode = new Node(value);
        if (rear) rear->next = newNode;
        else front = newNode;  // 第一個節點
        rear = newNode;
    }
    
    int dequeue() {
        if (isEmpty()) return -1;
        int val = front->data;
        Node* temp = front;
        front = front->next;
        if (!front) rear = nullptr;  // 現在空
        delete temp;  // 釋放記憶體
        return val;
    }
};

int main() {
    LinkedQueue q;
    q.enqueue(64); q.enqueue(34);
    std::cout << "Dequeue: " << q.dequeue() << std::endl;  // 64
    return 0;
}
// 想法：指標允許動態增長。使用 delete 處理記憶體洩漏。
```

## 第 6 部分：比較
### PDF 原始內容總結
- 第 27-28 頁：表格比較堆疊/佇列特徵，陣列 vs. 鏈結實作（存取、記憶體、效能、溢位）。

**<加深的內容>**：堆疊：較簡單（一個指標/索引）。佇列：需兩個以 O(1)。陣列：連續，快取友好；鏈結：大小靈活。

**<額外加深加廣的內容>**：時間：全攤銷 O(1)。空間：陣列若未滿則浪費；鏈結 +sizeof(指標) 每元素。擴展：混合（鏈結陣列用於雜湊表）。現實應用：已知大小選陣列（固定緩衝）；未知選鏈結（任務佇列）。

## 挑戰性問題與現實應用
1. **困難問題：使用堆疊的河內塔**  
   使用堆疊模擬柱子實作河內塔遞迴。想法：三個堆疊 (A, B, C)。遞迴：從 A 移 n-1 到 B（經 C），A 頂到 C，B n-1 到 C（經 A）。以 2^n-1 步解決。使用堆疊 LIFO 暫存。C++：定義 Stack 類，遞迴函式 moveDisks(Stack& src, Stack& dest, Stack& aux, int n)。

2. **現實問題：印表機佇列管理**  
   問題：處理帶優先級的列印工作；正常 FIFO 佇列若緊急工作到來則失效。解決：使用優先佇列（佇列的 min-heap 變體）。Enqueue 帶優先級；dequeue 最高者。特徵：同優先 FIFO，但緊急覆蓋。在 OS 中，透過老化防止饑餓。

3. **困難問題：使用兩個堆疊模擬佇列**  
   Enqueue 到 stack1；Dequeue：pop stack1 到 stack2（一次），pop stack2。想法：堆疊反轉，雙反轉 = FIFO。攤銷 O(1)。有用於僅有堆疊的環境（某些語言）。

## 如何實作這些資料結構
- **堆疊 (陣列)**：類別含 int arr[MAX], int top=-1；push: if(!full) arr[++top]=val；pop: if(!empty) return arr[top--]。優點：快速存取。缺點：固定大小—用 vector 動態。
- **堆疊 (鏈結)**：Struct Node{int data; Node* next;}; 類別含 Node* top=nullptr；push: newNode->next=top; top=newNode；pop: temp=top; top=top->next; delete temp。
- **佇列 (循環陣列)**：int arr[MAX], front=0, rear=0, count=0；enqueue: arr[rear]=val; rear=(rear+1)%MAX; count++；dequeue: val=arr[front]; front=(front+1)%MAX; count--。檢查滿: count==MAX；空: count==0。
- **佇列 (鏈結)**：如上程式碼。優點：無溢位（直到堆積滿）。缺點：分配較慢。
