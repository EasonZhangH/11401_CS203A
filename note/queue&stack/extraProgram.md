# 堆疊 (Stack) 與 佇列 (Queue) 完整筆記

## 目錄
1. [核心概念與生活比喻](#核心概念與生活比喻)
2. [堆疊 (Stack) 的實作與應用](#堆疊-stack-的實作與應用)
3. [佇列 (Queue) 的實作與應用](#佇列-queue-的實作與應用)
4. [實作方式比較：陣列 vs. 鏈結串列](#實作方式比較陣列-vs-鏈結串列)
5. [進階應用與挑戰](#進階應用與挑戰)

---

## 核心概念與生活比喻

### 堆疊 (Stack) - LIFO (後進先出)
**就像一疊盤子**：你只能從最上面放上新盤子(push)，也只能從最上面拿走盤子(pop)。

**核心操作**：
- `push(element)`：將元素放入堆疊頂端
- `pop()`：從堆疊頂端移除並返回元素
- `peek()`/`top()`：查看頂端元素但不移除
- `isEmpty()`：檢查堆疊是否為空

**現實應用**：
- 瀏覽器的「返回」按鈕（後退紀錄）
- 文字編輯器的 Undo/Redo 功能
- 函式呼叫的執行堆疊（Call Stack）
- 遞迴演算法的實作

### 佇列 (Queue) - FIFO (先進先出)
**就像排隊買票**：新人從隊伍尾端加入(enqueue)，服務從隊伍前端開始(dequeue)。

**核心操作**：
- `enqueue(element)`：將元素加入佇列尾端
- `dequeue()`：從佇列前端移除並返回元素
- `front()`：查看前端元素但不移除
- `isEmpty()`：檢查佇列是否為空

**現實應用**：
- 印表機工作排程
- 客服中心的來電等待
- 網路封包傳輸緩衝
- 廣度優先搜尋(BFS)演算法

---

## 堆疊 (Stack) 的實作與應用

### 使用陣列實作堆疊
陣列實作簡單高效，適合已知最大大小的情況。

```cpp
#include <iostream>
#include <stdexcept>

class ArrayStack {
private:
    int* arr;        // 動態陣列
    int capacity;    // 最大容量
    int topIndex;    // 頂端索引（-1表示空堆疊）
    
public:
    // 建構子
    ArrayStack(int size = 10) {
        capacity = size;
        arr = new int[capacity];
        topIndex = -1;
    }
    
    // 解構子
    ~ArrayStack() {
        delete[] arr;
    }
    
    // 檢查堆疊是否為空
    bool isEmpty() {
        return topIndex == -1;
    }
    
    // 檢查堆疊是否已滿
    bool isFull() {
        return topIndex == capacity - 1;
    }
    
    // 動態擴展容量
    void resize(int newCapacity) {
        int* newArr = new int[newCapacity];
        for (int i = 0; i <= topIndex; i++) {
            newArr[i] = arr[i];
        }
        delete[] arr;
        arr = newArr;
        capacity = newCapacity;
        std::cout << "堆疊容量擴展為: " << capacity << std::endl;
    }
    
    // 推入元素
    void push(int value) {
        if (isFull()) {
            // 自動擴展為兩倍大小
            resize(capacity * 2);
        }
        arr[++topIndex] = value;
        std::cout << "推入: " << value << std::endl;
    }
    
    // 彈出元素
    int pop() {
        if (isEmpty()) {
            throw std::runtime_error("堆疊已空！無法彈出元素");
        }
        
        int value = arr[topIndex--];
        std::cout << "彈出: " << value << std::endl;
        
        // 如果使用率太低，縮小陣列以節省空間
        if (topIndex < capacity / 4 && capacity > 10) {
            resize(capacity / 2);
        }
        
        return value;
    }
    
    // 查看頂端元素
    int peek() {
        if (isEmpty()) {
            throw std::runtime_error("堆疊已空！無法查看元素");
        }
        return arr[topIndex];
    }
    
    // 顯示堆疊內容
    void display() {
        if (isEmpty()) {
            std::cout << "堆疊為空" << std::endl;
            return;
        }
        
        std::cout << "堆疊內容 (頂端→底端): ";
        for (int i = topIndex; i >= 0; i--) {
            std::cout << arr[i] << " ";
        }
        std::cout << std::endl;
    }
    
    // 取得當前大小
    int size() {
        return topIndex + 1;
    }
};

// 使用範例
int main() {
    ArrayStack stack(5);  // 初始容量為5
    
    // 推入元素
    for (int i = 1; i <= 7; i++) {
        stack.push(i * 10);
    }
    
    stack.display();  // 顯示當前堆疊
    
    // 彈出元素
    std::cout << "頂端元素: " << stack.peek() << std::endl;
    stack.pop();
    stack.pop();
    
    stack.display();
    
    return 0;
}
```

**時間複雜度分析**：
| 操作 | 平均情況 | 最壞情況 | 說明 |
|------|----------|----------|------|
| push | O(1) | O(n) | 攤銷分析下平均O(1) |
| pop | O(1) | O(n) | 攤銷分析下平均O(1) |
| peek | O(1) | O(1) | 直接存取頂端元素 |
| isEmpty | O(1) | O(1) | 檢查索引值 |

### 使用鏈結串列實作堆疊
鏈結串列實作無需預先分配空間，可以動態增長。

```cpp
#include <iostream>
#include <stdexcept>

// 節點結構
struct StackNode {
    int data;
    StackNode* next;
    
    StackNode(int value) : data(value), next(nullptr) {}
};

class LinkedListStack {
private:
    StackNode* top;  // 指向堆疊頂端
    
public:
    LinkedListStack() : top(nullptr) {}
    
    ~LinkedListStack() {
        // 釋放所有節點記憶體
        while (!isEmpty()) {
            pop();
        }
    }
    
    bool isEmpty() {
        return top == nullptr;
    }
    
    void push(int value) {
        StackNode* newNode = new StackNode(value);
        newNode->next = top;  // 新節點指向原頂端
        top = newNode;        // 更新頂端指標
        std::cout << "推入: " << value << std::endl;
    }
    
    int pop() {
        if (isEmpty()) {
            throw std::runtime_error("堆疊已空！無法彈出元素");
        }
        
        StackNode* temp = top;
        int value = temp->data;
        top = top->next;      // 移動頂端到下一個節點
        delete temp;          // 釋放原頂端記憶體
        
        std::cout << "彈出: " << value << std::endl;
        return value;
    }
    
    int peek() {
        if (isEmpty()) {
            throw std::runtime_error("堆疊已空！無法查看元素");
        }
        return top->data;
    }
    
    void display() {
        if (isEmpty()) {
            std::cout << "堆疊為空" << std::endl;
            return;
        }
        
        std::cout << "堆疊內容 (頂端→底端): ";
        StackNode* current = top;
        while (current != nullptr) {
            std::cout << current->data << " ";
            current = current->next;
        }
        std::cout << std::endl;
    }
};

// 應用範例：括號匹配檢查
bool isBalancedParentheses(const std::string& expression) {
    LinkedListStack stack;
    
    for (char ch : expression) {
        if (ch == '(' || ch == '[' || ch == '{') {
            // 左括號推入堆疊
            stack.push(ch);
        }
        else if (ch == ')' || ch == ']' || ch == '}') {
            // 右括號檢查匹配
            if (stack.isEmpty()) {
                return false;  // 右括號多餘
            }
            
            char topChar = stack.peek();
            if ((ch == ')' && topChar == '(') ||
                (ch == ']' && topChar == '[') ||
                (ch == '}' && topChar == '{')) {
                stack.pop();
            }
            else {
                return false;  // 括號不匹配
            }
        }
    }
    
    // 最後堆疊應為空
    return stack.isEmpty();
}

int main() {
    // 測試括號匹配
    std::string test1 = "{(a+b)*[c-d]}";
    std::string test2 = "((a+b)*c";
    
    std::cout << test1 << " 是否平衡: " 
              << (isBalancedParentheses(test1) ? "是" : "否") << std::endl;
    std::cout << test2 << " 是否平衡: " 
              << (isBalancedParentheses(test2) ? "是" : "否") << std::endl;
    
    return 0;
}
```

---

## 佇列 (Queue) 的實作與應用

### 使用循環陣列實作佇列
解決線性陣列空間浪費的問題，實現高效的佇列操作。

```cpp
#include <iostream>
#include <stdexcept>

class CircularArrayQueue {
private:
    int* arr;           // 動態陣列
    int capacity;       // 最大容量
    int front;          // 前端索引
    int rear;           // 尾端索引
    int count;          // 當前元素數量（可替代(rear-front+capacity)%capacity的計算）
    
public:
    CircularArrayQueue(int size = 10) {
        capacity = size;
        arr = new int[capacity];
        front = 0;
        rear = -1;
        count = 0;
    }
    
    ~CircularArrayQueue() {
        delete[] arr;
    }
    
    bool isEmpty() {
        return count == 0;
    }
    
    bool isFull() {
        return count == capacity;
    }
    
    void resize(int newCapacity) {
        int* newArr = new int[newCapacity];
        
        // 將元素從front開始複製到新陣列
        for (int i = 0; i < count; i++) {
            newArr[i] = arr[(front + i) % capacity];
        }
        
        delete[] arr;
        arr = newArr;
        capacity = newCapacity;
        front = 0;
        rear = count - 1;
        
        std::cout << "佇列容量擴展為: " << capacity << std::endl;
    }
    
    void enqueue(int value) {
        if (isFull()) {
            resize(capacity * 2);
        }
        
        rear = (rear + 1) % capacity;  // 循環移動
        arr[rear] = value;
        count++;
        
        std::cout << "加入佇列: " << value << std::endl;
    }
    
    int dequeue() {
        if (isEmpty()) {
            throw std::runtime_error("佇列已空！無法取出元素");
        }
        
        int value = arr[front];
        front = (front + 1) % capacity;  // 循環移動
        count--;
        
        std::cout << "移出佇列: " << value << std::endl;
        
        // 如果使用率太低，縮小陣列
        if (count < capacity / 4 && capacity > 10) {
            resize(capacity / 2);
        }
        
        return value;
    }
    
    int getFront() {
        if (isEmpty()) {
            throw std::runtime_error("佇列已空！無法查看前端元素");
        }
        return arr[front];
    }
    
    void display() {
        if (isEmpty()) {
            std::cout << "佇列為空" << std::endl;
            return;
        }
        
        std::cout << "佇列內容 (前端→尾端): ";
        for (int i = 0; i < count; i++) {
            int index = (front + i) % capacity;
            std::cout << arr[index] << " ";
        }
        std::cout << std::endl;
        
        // 視覺化顯示（用於理解循環結構）
        std::cout << "陣列狀態: [";
        for (int i = 0; i < capacity; i++) {
            if (i == front && i == rear) {
                std::cout << "F/R";  // 只有一個元素
            }
            else if (i == front) {
                std::cout << "F";
            }
            else if (i == rear) {
                std::cout << "R";
            }
            else if ((front <= rear && i >= front && i <= rear) ||
                    (front > rear && (i >= front || i <= rear))) {
                std::cout << "*";  // 佇列元素
            }
            else {
                std::cout << "_";  // 空位置
            }
            
            if (i < capacity - 1) std::cout << " ";
        }
        std::cout << "]" << std::endl;
    }
    
    int size() {
        return count;
    }
};

// 應用範例：模擬銀行叫號系統
class BankQueueSystem {
private:
    CircularArrayQueue normalQueue;   // 一般客戶
    CircularArrayQueue vipQueue;      // VIP客戶
    int nextNumber;
    
public:
    BankQueueSystem() : normalQueue(5), vipQueue(3), nextNumber(1) {}
    
    // 取號
    void takeNumber(bool isVip = false) {
        if (isVip) {
            vipQueue.enqueue(nextNumber);
            std::cout << "VIP客戶 " << nextNumber << " 號已取號" << std::endl;
        } else {
            normalQueue.enqueue(nextNumber);
            std::cout << "一般客戶 " << nextNumber << " 號已取號" << std::endl;
        }
        nextNumber++;
    }
    
    // 叫號（優先服務VIP）
    void callNext() {
        if (!vipQueue.isEmpty()) {
            std::cout << "請 VIP " << vipQueue.dequeue() << " 號到櫃檯" << std::endl;
        }
        else if (!normalQueue.isEmpty()) {
            std::cout << "請 " << normalQueue.dequeue() << " 號到櫃檯" << std::endl;
        }
        else {
            std::cout << "目前沒有等待客戶" << std::endl;
        }
    }
    
    void displayStatus() {
        std::cout << "\n=== 當前佇列狀態 ===" << std::endl;
        std::cout << "VIP客戶佇列: ";
        vipQueue.display();
        std::cout << "一般客戶佇列: ";
        normalQueue.display();
        std::cout << "===================\n" << std::endl;
    }
};

int main() {
    // 測試銀行叫號系統
    BankQueueSystem bank;
    
    bank.takeNumber(false);  // 一般客戶1號
    bank.takeNumber(true);   // VIP客戶2號
    bank.takeNumber(false);  // 一般客戶3號
    bank.takeNumber(false);  // 一般客戶4號
    bank.takeNumber(true);   // VIP客戶5號
    
    bank.displayStatus();
    
    // 叫號服務
    for (int i = 0; i < 3; i++) {
        bank.callNext();
        bank.displayStatus();
    }
    
    return 0;
}
```

### 使用鏈結串列實作佇列
```cpp
#include <iostream>
#include <stdexcept>

struct QueueNode {
    int data;
    QueueNode* next;
    
    QueueNode(int value) : data(value), next(nullptr) {}
};

class LinkedListQueue {
private:
    QueueNode* front;
    QueueNode* rear;
    
public:
    LinkedListQueue() : front(nullptr), rear(nullptr) {}
    
    ~LinkedListQueue() {
        while (!isEmpty()) {
            dequeue();
        }
    }
    
    bool isEmpty() {
        return front == nullptr;
    }
    
    void enqueue(int value) {
        QueueNode* newNode = new QueueNode(value);
        
        if (isEmpty()) {
            // 第一個元素，front和rear都指向它
            front = rear = newNode;
        }
        else {
            // 加到尾端
            rear->next = newNode;
            rear = newNode;
        }
        
        std::cout << "加入佇列: " << value << std::endl;
    }
    
    int dequeue() {
        if (isEmpty()) {
            throw std::runtime_error("佇列已空！無法取出元素");
        }
        
        QueueNode* temp = front;
        int value = temp->data;
        front = front->next;
        
        // 如果取出後佇列為空，rear也設為nullptr
        if (front == nullptr) {
            rear = nullptr;
        }
        
        delete temp;
        std::cout << "移出佇列: " << value << std::endl;
        return value;
    }
    
    int getFront() {
        if (isEmpty()) {
            throw std::runtime_error("佇列已空！無法查看前端元素");
        }
        return front->data;
    }
    
    void display() {
        if (isEmpty()) {
            std::cout << "佇列為空" << std::endl;
            return;
        }
        
        std::cout << "佇列內容 (前端→尾端): ";
        QueueNode* current = front;
        while (current != nullptr) {
            std::cout << current->data << " ";
            current = current->next;
        }
        std::cout << std::endl;
    }
};
```

---

## 實作方式比較：陣列 vs. 鏈結串列

### 堆疊實作比較
| 特性 | 陣列實作 | 鏈結串列實作 |
|------|----------|--------------|
| **記憶體使用** | 連續記憶體，可能浪費空間 | 動態分配，無浪費但每個節點有額外指標開銷 |
| **擴展性** | 需重新分配和複製（O(n)） | 可隨時擴展（O(1)） |
| **快取效率** | 高（連續記憶體） | 低（非連續記憶體） |
| **實作複雜度** | 簡單 | 需處理指標和記憶體管理 |
| **最壞情況push** | O(n)（需擴展時） | O(1) |
| **最壞情況pop** | O(n)（需縮小時） | O(1) |
| **適用場景** | 大小已知或變化不大 | 大小未知或變化頻繁 |

### 佇列實作比較
| 特性 | 循環陣列實作 | 鏈結串列實作 |
|------|--------------|--------------|
| **空間浪費** | 可能有未使用的保留空間 | 無浪費，精確分配所需 |
| **擴展操作** | 需重新分配和複製 | 可隨時加入新節點 |
| **記憶體碎片** | 無（連續記憶體） | 可能產生碎片 |
| **實作難度** | 中等（需處理循環索引） | 中等（需處理頭尾指標） |
| **enqueue複雜度** | O(1)攤銷 | O(1) |
| **dequeue複雜度** | O(1)攤銷 | O(1) |
| **執行緒安全** | 較易實現（固定記憶體區塊） | 較複雜（需同步多個指標） |

### 時間複雜度總結表
| 資料結構 | 實作方式 | 插入 | 刪除 | 存取 | 搜尋 |
|----------|----------|------|------|------|------|
| **堆疊** | 陣列（動態） | O(1)* | O(1)* | O(1) | O(n) |
| **堆疊** | 鏈結串列 | O(1) | O(1) | O(1) | O(n) |
| **佇列** | 循環陣列 | O(1)* | O(1)* | O(1) | O(n) |
| **佇列** | 鏈結串列 | O(1) | O(1) | O(1) | O(n) |

*註：攤銷分析(Amortized Analysis)下的平均時間複雜度*

---

## 進階應用與挑戰

### 1. 用兩個堆疊模擬佇列
這是一個經典的面試問題，展示如何用LIFO結構實現FIFO行為。

```cpp
#include <iostream>
#include <stack>

class QueueUsingTwoStacks {
private:
    std::stack<int> stack1;  // 主要用於enqueue
    std::stack<int> stack2;  // 主要用於dequeue
    
    // 將stack1的所有元素轉移到stack2
    void transferStacks() {
        while (!stack1.empty()) {
            stack2.push(stack1.top());
            stack1.pop();
        }
    }
    
public:
    void enqueue(int value) {
        stack1.push(value);
        std::cout << "Enqueued: " << value << std::endl;
    }
    
    int dequeue() {
        if (isEmpty()) {
            throw std::runtime_error("Queue is empty!");
        }
        
        // 如果stack2為空，將stack1的元素轉移過來
        if (stack2.empty()) {
            transferStacks();
        }
        
        int value = stack2.top();
        stack2.pop();
        std::cout << "Dequeued: " << value << std::endl;
        return value;
    }
    
    int front() {
        if (isEmpty()) {
            throw std::runtime_error("Queue is empty!");
        }
        
        if (stack2.empty()) {
            transferStacks();
        }
        
        return stack2.top();
    }
    
    bool isEmpty() {
        return stack1.empty() && stack2.empty();
    }
    
    int size() {
        return stack1.size() + stack2.size();
    }
};

// 分析時間複雜度：
// Enqueue: O(1) - 直接push到stack1
// Dequeue: 
//   - 最好情況: O(1) - stack2不為空
//   - 最壞情況: O(n) - 需要轉移n個元素
//   - 攤銷分析: 每個元素只會被push/pop兩次，所以平均O(1)
```

### 2. 最小堆疊 (Min Stack)
在O(1)時間內支援push、pop、top和getMin操作。

```cpp
#include <iostream>
#include <stack>
#include <algorithm>

class MinStack {
private:
    std::stack<int> mainStack;    // 主堆疊
    std::stack<int> minStack;     // 輔助堆疊，記錄當前最小值
    
public:
    void push(int value) {
        mainStack.push(value);
        
        // 如果minStack為空或新值≤當前最小值，推入minStack
        if (minStack.empty() || value <= minStack.top()) {
            minStack.push(value);
        }
        
        std::cout << "Pushed: " << value << ", Current min: " << getMin() << std::endl;
    }
    
    void pop() {
        if (mainStack.empty()) {
            throw std::runtime_error("Stack is empty!");
        }
        
        // 如果彈出的值是當前最小值，也從minStack彈出
        if (mainStack.top() == minStack.top()) {
            minStack.pop();
        }
        
        std::cout << "Popped: " << mainStack.top() << std::endl;
        mainStack.pop();
    }
    
    int top() {
        if (mainStack.empty()) {
            throw std::runtime_error("Stack is empty!");
        }
        return mainStack.top();
    }
    
    int getMin() {
        if (minStack.empty()) {
            throw std::runtime_error("Stack is empty!");
        }
        return minStack.top();
    }
    
    bool isEmpty() {
        return mainStack.empty();
    }
};
```

### 3. 循環佇列的實際應用：生產者-消費者問題
```cpp
#include <iostream>
#include <thread>
#include <chrono>
#include <mutex>
#include <condition_variable>
#include <random>

template<typename T>
class ThreadSafeCircularQueue {
private:
    T* buffer;
    int capacity;
    int front;
    int rear;
    int count;
    
    std::mutex mtx;
    std::condition_variable not_empty;
    std::condition_variable not_full;
    
public:
    ThreadSafeCircularQueue(int size) : capacity(size), front(0), rear(0), count(0) {
        buffer = new T[capacity];
    }
    
    ~ThreadSafeCircularQueue() {
        delete[] buffer;
    }
    
    void enqueue(const T& item) {
        std::unique_lock<std::mutex> lock(mtx);
        
        // 等待佇列有空間
        not_full.wait(lock, [this]() { return count < capacity; });
        
        buffer[rear] = item;
        rear = (rear + 1) % capacity;
        count++;
        
        std::cout << "Produced: " << item << std::endl;
        
        // 通知消費者
        not_empty.notify_one();
    }
    
    T dequeue() {
        std::unique_lock<std::mutex> lock(mtx);
        
        // 等待佇列有元素
        not_empty.wait(lock, [this]() { return count > 0; });
        
        T item = buffer[front];
        front = (front + 1) % capacity;
        count--;
        
        std::cout << "Consumed: " << item << std::endl;
        
        // 通知生產者
        not_full.notify_one();
        
        return item;
    }
    
    bool isEmpty() {
        std::lock_guard<std::mutex> lock(mtx);
        return count == 0;
    }
    
    bool isFull() {
        std::lock_guard<std::mutex> lock(mtx);
        return count == capacity;
    }
    
    int size() {
        std::lock_guard<std::mutex> lock(mtx);
        return count;
    }
};

// 生產者函數
void producer(ThreadSafeCircularQueue<int>& queue, int id, int items_to_produce) {
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_int_distribution<> dis(1, 100);
    
    for (int i = 0; i < items_to_produce; i++) {
        int item = dis(gen);
        queue.enqueue(item);
        std::this_thread::sleep_for(std::chrono::milliseconds(dis(gen) % 50));
    }
    
    std::cout << "Producer " << id << " finished." << std::endl;
}

// 消費者函數
void consumer(ThreadSafeCircularQueue<int>& queue, int id, int items_to_consume) {
    for (int i = 0; i < items_to_consume; i++) {
        int item = queue.dequeue();
        std::this_thread::sleep_for(std::chrono::milliseconds((item % 50) + 10));
    }
    
    std::cout << "Consumer " << id << " finished." << std::endl;
}

int main() {
    ThreadSafeCircularQueue<int> queue(5);  // 容量為5的執行緒安全佇列
    
    const int PRODUCE_COUNT = 10;
    const int CONSUME_COUNT = 10;
    
    // 建立生產者和消費者執行緒
    std::thread prod1(producer, std::ref(queue), 1, PRODUCE_COUNT);
    std::thread prod2(producer, std::ref(queue), 2, PRODUCE_COUNT);
    std::thread cons1(consumer, std::ref(queue), 1, CONSUME_COUNT);
    std::thread cons2(consumer, std::ref(queue), 2, CONSUME_COUNT);
    
    // 等待所有執行緒完成
    prod1.join();
    prod2.join();
    cons1.join();
    cons2.join();
    
    std::cout << "All threads finished. Final queue size: " << queue.size() << std::endl;
    
    return 0;
}
```

### 4. 經典演算法應用：使用堆疊的河內塔
```cpp
#include <iostream>
#include <stack>
#include <vector>

class TowerOfHanoi {
private:
    std::vector<std::stack<int>> towers;
    int numDisks;
    
public:
    TowerOfHanoi(int disks) : numDisks(disks) {
        // 初始化三個塔
        towers.resize(3);
        
        // 將所有盤子放在第一個塔（從大到小）
        for (int i = numDisks; i >= 1; i--) {
            towers[0].push(i);
        }
    }
    
    void display() {
        std::cout << "\nCurrent state:\n";
        
        // 為每個塔建立副本以顯示內容
        std::vector<std::stack<int>> tempTowers = towers;
        
        for (int i = 0; i < 3; i++) {
            std::cout << "Tower " << i + 1 << ": ";
            
            // 將塔的內容轉移到vector以便顯示
            std::vector<int> disks;
            while (!tempTowers[i].empty()) {
                disks.push_back(tempTowers[i].top());
                tempTowers[i].pop();
            }
            
            // 反向顯示（從底到頂）
            for (int j = disks.size() - 1; j >= 0; j--) {
                std::cout << disks[j] << " ";
            }
            std::cout << std::endl;
        }
        std::cout << std::endl;
    }
    
    void moveDisk(int from, int to) {
        if (towers[from].empty()) {
            std::cout << "Illegal move: Tower " << from + 1 << " is empty!" << std::endl;
            return;
        }
        
        int disk = towers[from].top();
        
        // 檢查移動是否合法（不能將較大的盤子放在較小的盤子上）
        if (!towers[to].empty() && disk > towers[to].top()) {
            std::cout << "Illegal move: Cannot place disk " << disk 
                      << " on top of disk " << towers[to].top() << std::endl;
            return;
        }
        
        towers[from].pop();
        towers[to].push(disk);
        
        std::cout << "Moved disk " << disk << " from Tower " 
                  << from + 1 << " to Tower " << to + 1 << std::endl;
    }
    
    // 遞迴解法
    void solveRecursive(int n, int from, int to, int aux) {
        if (n == 1) {
            moveDisk(from, to);
            display();
            return;
        }
        
        solveRecursive(n - 1, from, aux, to);
        moveDisk(from, to);
        display();
        solveRecursive(n - 1, aux, to, from);
    }
    
    // 迭代解法（使用堆疊模擬遞迴）
    void solveIterative() {
        // 總移動次數 = 2^n - 1
        int totalMoves = (1 << numDisks) - 1;
        
        // 決定移動方向（根據盤子數量的奇偶性）
        int from = 0, to, aux;
        
        if (numDisks % 2 == 0) {
            // 偶數盤子：順時針方向
            to = 2;
            aux = 1;
        } else {
            // 奇數盤子：逆時針方向
            to = 1;
            aux = 2;
        }
        
        for (int move = 1; move <= totalMoves; move++) {
            if (move % 3 == 1) {
                // 移動最小的盤子
                moveSmallestDisk(from, to, aux);
            } else if (move % 3 == 2) {
                // 移動非最小的盤子（合法移動）
                moveNonSmallestDisk(from, to, aux);
            } else {
                // 移動非最小的盤子（合法移動）
                moveNonSmallestDisk(aux, from, to);
            }
            
            display();
        }
    }
    
private:
    void moveSmallestDisk(int& from, int& to, int& aux) {
        // 根據當前方向移動最小盤子
        moveDisk(from, to);
        
        // 更新方向（循環移動）
        int temp = from;
        from = aux;
        aux = to;
        to = temp;
    }
    
    void moveNonSmallestDisk(int a, int b, int c) {
        // 在a和c之間進行合法移動
        if (towers[a].empty() || 
            (!towers[c].empty() && towers[a].top() > towers[c].top())) {
            moveDisk(c, a);
        } else {
            moveDisk(a, c);
        }
    }
};

int main() {
    int numDisks = 3;
    std::cout << "Solving Tower of Hanoi with " << numDisks << " disks:\n";
    
    TowerOfHanoi hanoi(numDisks);
    hanoi.display();
    
    // 使用遞迴解法
    std::cout << "\n=== Recursive Solution ===\n";
    hanoi.solveRecursive(numDisks, 0, 2, 1);
    
    // 重置遊戲
    TowerOfHanoi hanoi2(numDisks);
    
    // 使用迭代解法
    std::cout << "\n=== Iterative Solution ===\n";
    hanoi2.solveIterative();
    
    return 0;
}
```

## 總結與延伸學習

### 關鍵要點回顧
1. **堆疊遵循LIFO原則**，適合需要「回溯」或「撤銷」的場景
2. **佇列遵循FIFO原則**，適合需要「公平」或「順序處理」的場景
3. **實作選擇取決於應用需求**：
   - 已知最大大小 → 陣列實作
   - 大小變化頻繁 → 鏈結串列實作
   - 需要高效快取 → 陣列實作
   - 需要動態擴展 → 鏈結串列實作

### 延伸學習建議
1. **標準模板庫(STL)的使用**：
   ```cpp
   #include <stack>
   #include <queue>
   
   std::stack<int> s;      // 預設基於deque
   std::queue<int> q;      // 預設基於deque
   std::priority_queue<int> pq;  // 優先佇列（堆積）
   ```

2. **進階資料結構**：
   - **雙端佇列(Deque)**：兩端都可插入刪除
   - **優先佇列(Priority Queue)**：元素按優先級排序
   - **單調堆疊(Monotonic Stack)**：用於解決「下一個更大元素」等問題

3. **實際應用深入研究**：
   - 作業系統中的行程排程佇列
   - 編譯器中的語法分析堆疊
   - 網路路由中的封包緩衝佇列
   - 遊戲中的指令緩衝和撤銷系統

4. **演算法應用**：
   - 深度優先搜尋(DFS)使用堆疊
   - 廣度優先搜尋(BFS)使用佇列
   - 使用堆疊實現運算式求值
   - 使用佇列實現緩衝區管理

透過實際編碼練習這些實作，並嘗試解決LeetCode等平台上的相關問題（如第20、225、232、155題），將能深刻掌握堆疊與佇列的應用精髓。
