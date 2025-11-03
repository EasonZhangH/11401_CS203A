# 11401_CS203A

## 📚 課程資訊
- 課程名稱：資料結構 (CS203A)
- 學期：114 學年度第一學期
- 授課教師：黃鈺峰 教授
- 學校：元智大學

## 👤 學生資訊
- 姓名：張嘉祥
- 學號：s1121432
- GitHub 帳號：[EasonZhangH](https://github.com/EasonZhangH)

## 🗂 倉庫用途
本倉庫用於記錄 CS203A 課程的學習歷程與作業成果，包含：
- 課堂筆記與範例程式
- 作業提交與版本控制
- 專案開發紀錄

## 🛠 技術與工具
- 語言：Python / C++ / JavaScript（依教授課程內容調整）
- 工具：Git, GitHub, VS Code, Visual Studio
- 架構：模組化設計、版本管理、Markdown 文件撰寫


## Expanding content structure  
- The user wants to combine all notes into one cohesive document, moving away from separate thematic sections.
</think># 資料結構與演算法筆記整理

## 1. 資料結構與演算法概述

### 資料結構的定義與重要性
- **定義：** 資料結構是一種組織和管理資料的方式，讓資料能夠被有效存取和操作。根據PDF（05_introduction.pdf），資料結構是將資料組織起來，以便有效使用。它不僅是儲存，還涉及操作如插入、刪除、搜尋。
- **重要性：** 在日常生活中，我們使用電腦時（如編輯文件、搜尋資訊、使用LLM模型、線上購物），資料結構無處不在。它們確保資料處理高效，避免浪費資源。例如，Google搜尋引擎用樹狀結構索引網頁，確保快速查詢。
- **<加深內容>：** 資料結構分為線性和非線性。線性如陣列、鏈表；非線性如樹、圖。選擇時考慮時、空間複雜度 / 操作頻率：若多讀少寫，用陣列；若多寫，用鏈表。
- **<現實中可能遇到的問題>：** 資料結構與演算法（DSA）是software engineer 的核心。根據05_data_structures_algorithms這個教授提供的PDF，DSA將輸入轉換為輸出，資料結構負責組織資料，演算法負責處理。現實中，例如「癌症發生統計分析」涉及 1. 檔案I/O 2. 資料解析和統計計算，可以使用陣列或列表儲存資料，然後用特定的演算法搭配去做預測分析。或是更簡單的case 「電影院訂票系統」，因為考量現實很多操作需求，從最簡單的利用queue有順序得去存取買票資料 -> 利用stack來保存跟時間有關的購票歷史紀錄/資訊 -> 透過hash table來綁定使用者跟他的消費紀錄...等等等。

### 演算法的特性
- **定義：** 演算法是一組有限的、明確的指令，用來解決問題或執行計算（Oxford Language & Wikipedia）。
- **關鍵特性（05_data_structures_algorithms.pdf）：**
  - 輸入：零或多個外部供應的數量。
  - 輸出：至少一個數量。
  - 明確性：每個指令清晰無歧義。
  - 有限性：執行後終止。
  - 有效性：指令基本到能用紙筆執行。
- **<加深的內容>：** 演算法的效率用時間複雜度和空間複雜度衡量。使用漸進符號（O, Ω, θ）來分析最壞、平均、最好情況。最壞情況保證效能，平均情況反映常態。
- **<額外加深加廣的內容>：** 在現實應用中，如癌症發生統計分析（PDF示例），演算法從CSV檔案載入資料，使用資料結構處理，產生趨勢報告。這涉及檔案I/O、資料解析和統計計算。演算法設計時，考慮可擴展性，如處理大資料集時用分治法。

### 資料結構的分類
- **靜態 vs. 動態（05_introduction.pdf）：**
  - 靜態：記憶體大小固定，存取快（如陣列）。優點：簡單、無額外開銷；缺點：不靈活。
  - 動態：記憶體大小可變，適合頻繁更新（如鏈表）。優點：靈活；缺點：額外記憶體管理。
- **原始 vs. 非原始：**
  - 原始：int, char, float 等，直接映射硬體。
  - 非原始：線性（列表、堆疊、隊列）和非線性（樹、圖）。線性適合順序處理，非線性適合階層或網路。
- **<加深的內容>：** 抽象資料型別（ADT）是資料結構的理論基礎，定義操作而不指定實作。ADT如Stack（推入、彈出）、Queue（入隊、出隊），實作可用陣列或鏈表。ADT提供抽象層，隱藏實作細節。
- **<額外加深加廣的內容>：** 在不同程式語言中，資料型別實現不同。C語言靜態、需手動管理記憶體（用malloc/free）；Java動態、垃圾回收（自動管理）；Python動態、強型別（用list、dict）。例如，Python的list是動態陣列，自動擴展。

### 效能分析
- **時間複雜度：** 演算法執行時間隨輸入大小變化。常見：O(1)常數、O(log n)對數、O(n)線性、O(n log n)線性對數、O(n²)平方、O(2^n)指數。
- **空間複雜度：** 記憶體使用量。陣列O(n)，鏈表O(n)，但鏈表有指標開銷。
- **漸進符號：** O(大O)：上界；Ω(歐米加)：下界；θ(西塔)：緊界。
- **<加深的內容>：** 分析時考慮最壞情況，因為它保證效能。實務中，用工具如gprof分析瓶頸。記憶體佈局影響效能：陣列連續儲存，提升快取命中率。
- **<額外加深加廣的內容>：** 比較不同結構：陣列O(1)存取但O(n)插入；鏈表O(n)存取但O(1)插入。選擇依應用：資料庫用B樹平衡讀寫。

## 2. 使用者情境與應用
### 常見情境（04_user_scenarios.pdf）
- **情境I：小學生按身高排隊** - 升序/降序排序，類似陣列排序。使用比較排序演算法。
- **情境II：台北動物園售票窗口** - 多窗口模擬隊列。隊列先進先出，適合排隊系統。
- **情境III：Google地圖** - 圖結構，頂點和邊。頂點是地點，邊是路徑，用圖演算法找最短路。
- **情境IV：巴黎博物館通行證** - 圖結構，博物館間路徑。邊權重表示距離或時間。
- **情境V：檔案系統** - 樹結構，目錄和檔案。根目錄是樹根，子目錄是子節點。
- **情境VI：矩陣計算** - 二維陣列運算。矩陣乘法用巢狀迴圈。
- **<加深的內容>：** 這些情境展示資料結構的實用性。例如，排序可用於排行榜；圖可用於導航。情境II可用多個隊列陣列模擬。
- **<額外加深加廣的內容>：** 在現實中，Google地圖用圖演算法（如Dijkstra）計算最短路徑；檔案系統用樹管理檔案階層。情境VI在機器學習中常見，如神經網路矩陣運算。

### 延伸學習：問答與Prompt
- **PDF問答延伸：** 在動態陣列中，realloc後記憶體地址是否改變？根據08_arrays.pdf，realloc可能移動資料到新位置以擴展空間。這教我們記憶體管理的不可預測性，需檢查返回值。若空間不足，realloc返回NULL，原指標仍有效。
- **AI Prompt回答（08_arrays.pdf）：** 對於陣列學習，建議從靜態/動態區分開始，理解記憶體佈局（連續儲存）。進階操作如搜尋（線性vs.二分）、排序（氣泡、選擇、插入）。實務中，用陣列實作堆疊/隊列。動態管理：用malloc分配，realloc擴展，free釋放。防止洩漏：總是free。效能：陣列高效於隨機存取，鏈表於插入。

## 3. 陣列（Arrays）深入探討
### 陣列定義與特性（08_arrays.pdf）
- **定義：** 元素在連續記憶體中儲存，可用索引直接存取。大小固定，提供O(1)存取。
- **優點：** 隨機存取快，適合已知大小資料。記憶體連續，提升快取效率。支援多維，易於矩陣運算。
- **缺點：** 插入/刪除需移動元素（O(n)），大小固定（靜態）。動態陣列需手動管理，易洩漏記憶體。
- **<加深的內容>：** 陣列是其他資料結構的基礎，如堆疊用陣列實作。記憶體佈局：地址計算為base + index * size。多維陣列用行優先（C）或列優先（Fortran）。邊界檢查防止溢出。
- **<額外加深加廣的內容>：** 在多維陣列中，插入/刪除複雜。字串陣列：C中char array以'\0'結尾。數值陣列支援浮點數。動態陣列如C++ vector自動擴展，但C需手動。效能比較：陣列vs鏈表——陣列快讀慢寫，鏈表慢讀快寫。

### ADT：陣列操作
- **操作：** Create, Retrieve, Store, Insert, Delete, Traverse, Search, Resize。
- **複雜度：**
  - 存取/更新：O(1)
  - 插入/刪除：O(n)
  - 搜尋：O(n)（未排序），O(log n)（排序，二分搜尋）
- **<加深的內容>：** 插入/刪除需移位，效率低。動態陣列用realloc擴展，但可能複製資料。

### 如何實作出陣列
- **靜態陣列：** 在C中用`int arr[size];`，大小編譯時決定。
- **動態陣列：** 用malloc分配，realloc調整大小，free釋放。
- **類別實作（C++）：** 用template類封裝，提供push_back等方法。
- **<加深的內容>：** 在C++ STL中，`std::array`靜態，`std::vector`動態。vector自動管理記憶體，效率高。

### 實作與程式碼示例（擴展）
#### 靜態一維陣列與基本操作
```c
#include <stdio.h>

int main() {
    // 宣告並初始化靜態陣列
    int arr[5] = {10, 20, 30, 40, 50};
    
    // 存取元素
    printf("Element at index 2: %d\n", arr[2]);  // 輸出30
    
    // 更新元素
    arr[2] = 35;
    printf("After update: %d\n", arr[2]);  // 輸出35
    
    // 想法：靜態陣列大小固定，適合小資料。索引從0開始，直接計算地址。優點：無額外開銷；缺點：無法動態調整。更新O(1)，但無邊界檢查。
    return 0;
}
```

#### 動態陣列（使用malloc/realloc）與錯誤處理
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int n = 10;
    int *arr = (int *)malloc(n * sizeof(int));  // 動態分配
    if (arr == NULL) {
        printf("Memory allocation failed\n");
        return 1;
    }
    
    // 初始化
    for (int i = 0; i < n; i++) {
        arr[i] = i + 1;
    }
    
    // 擴展大小
    n *= 2;
    int *newArr = (int *)realloc(arr, n * sizeof(int));  // realloc可能改變地址
    // 原arr仍有效，可繼續使用，不會發生realloc失敗產生原arr消失的問題
    if (newArr == NULL) {
        printf("Reallocation failed, original array preserved\n");
        free(arr);
        return 1;
    }
    arr = newArr;  // 更新指標
    
    // 初始化新元素
    for (int i = n/2; i < n; i++) {
        arr[i] = i + 1;
    }
    
    // 輸出
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");
    
    free(arr);  // 釋放記憶體，避免洩漏
    
    // 想法：動態陣列靈活，但需手動管理記憶體。realloc失敗時原指標仍有效。邊界檢查防止溢出。擴展策略：通常雙倍容量以減少realloc次數。
    return 0;
}
```

#### 二維陣列與矩陣運算（擴展：動態二維陣列）
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    // 靜態二維陣列
    int arr2d[3][4] = {
        {1, 2, 3, 4},
        {5, 6, 7, 8},
        {9, 10, 11, 12}
    };
    
    printf("Element at [1][2]: %d\n", arr2d[1][2]);  // 輸出7
    
    // 動態二維陣列（指標陣列）
    int rows = 3, cols = 4;
    int **dynamic2d = (int **)malloc(rows * sizeof(int *));
    if (dynamic2d == NULL) {
        printf("Allocation failed\n");
        return 1;
    }
    for (int i = 0; i < rows; i++) {
        dynamic2d[i] = (int *)malloc(cols * sizeof(int));
        if (dynamic2d[i] == NULL) {
            printf("Allocation failed for row %d\n", i);
            // 釋放已分配的
            for (int j = 0; j < i; j++) free(dynamic2d[j]);
            free(dynamic2d);
            return 1;
        }
        for (int j = 0; j < cols; j++) {
            dynamic2d[i][j] = i * cols + j + 1;
        }
    }
    
    // 矩陣加法示例
    int **result = (int **)malloc(rows * sizeof(int *));
    for (int i = 0; i < rows; i++) {
        result[i] = (int *)malloc(cols * sizeof(int));
        for (int j = 0; j < cols; j++) {
            result[i][j] = dynamic2d[i][j] + 1;  // 簡單加法
        }
    }
    
    printf("Dynamic 2D after adding 1:\n");
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            printf("%d ", result[i][j]);
        }
        printf("\n");
    }
    
    // 釋放記憶體
    for (int i = 0; i < rows; i++) {
        free(dynamic2d[i]);
        free(result[i]);
    }
    free(dynamic2d);
    free(result);
    
    // 想法：二維陣列用於矩陣運算，索引為[row][col]。動態版本用指標陣列，靈活但複雜。巢狀迴圈處理元素。適合情境VI的矩陣計算。釋放時小心順序，避免洩漏。
    return 0;
}
```

#### 三維陣列與應用（擴展：3D陣列實作）
```c
#include <stdio.h>

int main() {
    // 靜態三維陣列：2個平面，每個3x4
    int arr3d[2][3][4] = {
        {
            {1, 2, 3, 4},
            {5, 6, 7, 8},
            {9, 10, 11, 12}
        },
        {
            {13, 14, 15, 16},
            {17, 18, 19, 20},
            {21, 22, 23, 24}
        }
    };
    
    printf("Element at [1][2][3]: %d\n", arr3d[1][2][3]);  // 輸出24
    
    // 應用：模擬3D空間，如遊戲地圖
    // 想法：三維陣列索引為[plane][row][col]。適合儲存立體資料，如影像處理中的RGB立方體。存取O(1)，但記憶體密集。
    return 0;
}
```

#### 字串陣列與操作（擴展：字串處理）
```c
#include <stdio.h>
#include <string.h>

int main() {
    // 字串陣列（單一字串）
    char str[6] = "Hello";  // 以'\0'結尾
    printf("String: %s\n", str);
    
    // 字串陣列（多字串）
    char *words[3] = {"cat", "dog", "fish"};
    for (int i = 0; i < 3; i++) {
        printf("Word %d: %s\n", i, words[i]);
    }
    
    // 搜尋字串
    char target[] = "dog";
    int found = -1;
    for (int i = 0; i < 3; i++) {
        if (strcmp(words[i], target) == 0) {
            found = i;
            break;
        }
    }
    printf("Found '%s' at index %d\n", target, found);
    
    // 想法：字串陣列以'\0'結尾。指標陣列儲存多字串，靈活。搜尋用strcmp比較。適合文字處理，如檔案系統路徑。
    return 0;
}
```

#### 搜尋演算法：線性搜尋與二分搜尋（擴展：遞歸二分）
```c
#include <stdio.h>

// 線性搜尋（未排序陣列）
int linearSearch(int arr[], int n, int target) {
    for (int i = 0; i < n; i++) {
        if (arr[i] == target) {
            return i;  // 找到索引
        }
    }
    return -1;  // 未找到
}

// 迭代二分搜尋（排序陣列）
int binarySearch(int arr[], int low, int high, int target) {
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return -1;
}

// 遞歸二分搜尋
int recursiveBinarySearch(int arr[], int low, int high, int target) {
    if (low > high) return -1;
    int mid = low + (high - low) / 2;
    if (arr[mid] == target) return mid;
    else if (arr[mid] < target) return recursiveBinarySearch(arr, mid + 1, high, target);
    else return recursiveBinarySearch(arr, low, mid - 1, target);
}
```

## 核心軟體開發技能
### 常見情境（04_user_scenarios.pdf）
- **版本控制：** Git命令如clone, commit, push。分支策略如main/develop。
- **文件與協作：** 用Markdown寫README，GitHub Issues追蹤任務。
- **建置與依賴：** 用make, cmake管理專案；pip, npm處理依賴。
- **容器化：** Docker打包應用，確保環境一致。
- **CI/CD：** GitHub Actions自動化測試與部署。
- **測試：** 單元測試用JUnit, pytest；TDD驅動開發。
- **編碼標準：** PEP8, Google Style；用linter檢查。
- **SDLC：** 敏捷方法如Scrum。
- **除錯與效能：** gdb, pdb；profiling工具。
- **雲端部署：** AWS, GCP基本部署。
  
### 進階題目與現實應用
#### 難度題目1：動態陣列實作堆疊
- **問題：** 實作一個支援push, pop, top的堆疊，使用動態陣列。當容量不足時自動擴展。
  - 想法： 用陣列模擬堆疊，top指標追蹤頂部。擴展時用realloc雙倍容量。優點：O(1)存取；缺點：擴展時O(n)複製。
      - 解決方案：
```c
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    int *data;
    int top;
    int capacity;
} Stack;

void initStack(Stack *s, int cap) {
    s->data = (int *)malloc(cap * sizeof(int));
    s->top = -1;
    s->capacity = cap;
}

void push(Stack *s, int val) {
    if (s->top == s->capacity - 1) {
        s->capacity *= 2;
        s->data = (int *)realloc(s->data, s->capacity * sizeof(int));
    }
    s->data[++s->top] = val;
}

int pop(Stack *s) {
    if (s->top == -1) return -1;  // 空堆疊
    return s->data[s->top--];
}

// 想法：動態擴展避免溢出，適合不確定大小的堆疊。
```
#### 難度題目2：現實問題：Nginx IP存取控制
- **問題：** 實作IP黑白名單，使用陣列儲存規則，檢查請求IP。
  - 想法： 用字串陣列儲存IP規則。搜尋用線性掃描（小列表）或雜湊（大列表）。陣列連續性適合快取。
      - 解決方案：解析IP為整數，排序後二分搜尋。實作時，用vector儲存規則，binary_search檢查
### 如何實做出一個 data structure
#### 如何實作出資料結構
- **一般步驟：** 定義結構（struct），實作操作函數。測試邊界情況（如空陣列）。用C++類別封裝，提供介面。
  - 最佳實務： 檢查記憶體分配失敗，釋放資源。記錄複雜度，選擇合適結構。
 


