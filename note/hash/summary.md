# Hash Table（雜湊表）筆記

## 目錄

1. [Hash Table 基礎概念](#hash-table-基礎概念)
2. [Hash Function 雜湊函數](#hash-function-雜湊函數)
3. [Collision Resolution 碰撞處理](#collision-resolution-碰撞處理)
4. [Hash Table 實作](#hash-table-實作)
5. [Big O 時間複雜度分析](#big-o-時間複雜度分析)
6. [Load Factor 與 Rehashing](#load-factor-與-rehashing)
7. [Hash Table vs 其他資料結構](#hash-table-vs-其他資料結構)
8. [實際應用場景](#實際應用場景)
9. [各程式語言內建實現](#各程式語言內建實現)
10. [學習要點總結](#學習要點總結)

---

## Hash Table 基礎概念

### 什麼是 Hash Table？

**Hash Table**（雜湊表）是一種採用**雜湊函數**對鍵進行對映的資料結構，它支援很快速的 **查詢（Lookup）、插入（Insert）、刪除（Delete）** 等操作。

Hash Table 的核心特性：

- **儲存格式**：以 **鍵值對（Key-Value）** 的形式儲存資料
- **快速存取**：平均情況下，查詢、插入、刪除的時間複雜度都是 **O(1)**
- **無序性**：不同於 Array，Hash Table 中的資料是無序的，位置由雜湊函數決定
- **動態調整**：當負載因子過高時，Hash Table 會自動擴展（rehashing）

### Hash Table 的基本結構

```
Hash Table Structure:
┌─────────────────────────────────────┐
│  Index │ Value (Bucket)              │
├─────────────────────────────────────┤
│   0    │ NULL                        │
│   1    │ (key: "Alice", value: 25)  │
│   2    │ (key: "Bob", value: 30)    │
│   3    │ NULL                        │
│   4    │ (key: "Charlie", value: 28)│
│   5    │ NULL                        │
└─────────────────────────────────────┘
```

### Hash Table 中的重要術語

| 術語 | 說明 |
|------|------|
| **Bucket（桶）** | Hash Table 中用來儲存 Key-Value 對的位置 |
| **Hash Function** | 將任意 Key 轉換為陣列索引的函數 |
| **Collision（碰撞）** | 兩個不同的 Key 經過雜湊函數後得到相同的索引 |
| **Load Factor（負載因子）** | 已使用位置數 / 總位置數，用來評估 Hash Table 是否過滿 |
| **Rehashing** | 當負載因子過高時，擴大 Hash Table 大小並重新計算所有 Key 的位置 |

---

## Hash Function 雜湊函數

### 什麼是 Hash Function？

雜湊函數是一種將輸入（通常是任意大小）轉換為固定大小輸出的函數。在 Hash Table 中，它將 Key 轉換為陣列的索引。

### Hash Function 的特性

一個好的雜湊函數應該具備以下特性：

#### 1. **快速計算（Efficiency）**
   - 雜湊函數的執行時間應該是常數時間 O(1)
   - 如果對很長的字串進行字符遍歷，時間會變成 O(n)，不符合要求
   
#### 2. **確定性（Determinism）**
   - 相同的 Input 每次都應該產生相同的 Output
   - 這是必不可少的特性，否則無法找到已儲存的資料
   
#### 3. **均勻分佈（Uniformity）**
   - 產生的雜湊值應該平均分佈在整個陣列範圍內
   - 避免資料集中在某些位置（clustering），造成過多碰撞
   
#### 4. **不可逆性（Non-Reversibility）**
   - 無法從雜湊值反推出原始的 Key
   - 這在密碼學和資料完整性檢查中很重要

### 基礎 Hash Function 實作

以下是一個簡單的字串雜湊函數範例（JavaScript）：

```javascript
// 基礎版本 - 有效率問題
function hashBasic(key, arrayLen) {
  let total = 0;
  for (let char of key) {
    let value = char.charCodeAt(0) - 96;  // 將字符轉為數字
    total = (total + value) % arrayLen;
  }
  return total;
}

// 測試
console.log(hashBasic("ab", 10));   // 輸出: 3
console.log(hashBasic("ab", 10));   // 輸出: 3（確定性）
```

**問題**：
- 只適用於字串 Key
- 如果 Key 很長，需要 O(n) 時間複雜度
- 沒有使用質數優化，容易產生碰撞

### 改進的 Hash Function

```javascript
// 改進版本 - 使用質數優化
function hashOptimized(key, arrayLen) {
  let total = 0;
  const WEIRD_PRIME = 31;  // 質數，幫助減少碰撞
  
  // 限制迭代次數，確保時間複雜度為 O(1)
  for (let i = 0; i < Math.min(key.length, 100); i++) {
    let char = key[i];
    let value = char.charCodeAt(0) - 96;
    total = (total * WEIRD_PRIME + value) % arrayLen;
  }
  return total;
}

// 測試
console.log(hashOptimized("hello", 53));   // 輸出: 某個 0-52 之間的數字
console.log(hashOptimized("hello", 53));   // 輸出: 相同的數字
```

**改進之處**：
- ✅ 限制迴圈次數 Math.min(key.length, 100)，確保 O(1) 時間複雜度
- ✅ 使用質數 31，減少碰撞發生率
- ✅ 透過模運算保證結果在有效範圍內

### 為什麼使用質數？

使用質數（如 31）作為乘數可以：
- **減少碰撞**：質數與陣列大小互質的機率更高，使得碰撞更少
- **更好的分佈**：質數的數學性質使得雜湊值更均勻分佈
- **提高效能**：實驗證明使用質數能大幅降低碰撞次數

注意：陣列大小通常也選擇質數（如 53），這樣搭配效果最佳。

---

## Collision Resolution 碰撞處理

### 什麼是 Collision（碰撞）？

當兩個或多個不同的 Key 經過雜湊函數後得到相同的索引時，就發生了碰撞。

```
例子：
Key "ab" → Hash Function → Index 2
Key "ba" → Hash Function → Index 2  ← 碰撞發生！
```

### 碰撞解決方案

#### 1. **Separate Chaining（分離鏈接法）**

在每個 Bucket 中使用 **Linked List** 或其他資料結構儲存多個 Key-Value 對。

**工作原理**：

```
Original Key-Value Pairs:
(name, "Alice"), (age, 25), (city, "NYC")

After Hashing to size 3:
Index 0: (name, "Alice") → (city, "NYC")  // 都雜湊到 Index 0
Index 1: NULL
Index 2: (age, 25)

使用 Linked List 表示：
┌──────────────────────────────┐
│ Index │ Bucket (Linked List) │
├──────────────────────────────┤
│   0   │ (name,"Alice")       │
│       │    ↓ next            │
│       │ (city,"NYC")         │
├──────────────────────────────┤
│   1   │ NULL                 │
├──────────────────────────────┤
│   2   │ (age, 25)            │
└──────────────────────────────┘
```

**優點**：
- ✅ 實作簡單直觀
- ✅ Hash Table 不會滿載
- ✅ 刪除操作容易
- ✅ 對負載因子變化不敏感

**缺點**：
- ❌ 需要額外記憶體儲存指標
- ❌ 快取效能較差（Linked List 的節點分散在記憶體各處）
- ❌ 最壞情況下退化為 O(n)

**程式碼範例**（JavaScript）：

```javascript
class HashTable {
  constructor(size = 53) {
    this.keyMap = new Array(size);
  }

  _hash(key) {
    let total = 0;
    const WEIRD_PRIME = 31;
    for (let i = 0; i < Math.min(key.length, 100); i++) {
      const char = key[i];
      const value = char.charCodeAt(0) - 96;
      total = (total * WEIRD_PRIME + value) % this.keyMap.length;
    }
    return total;
  }

  set(key, value) {
    const index = this._hash(key);
    // 如果 Bucket 為空，建立新 Array
    if (!this.keyMap[index]) {
      this.keyMap[index] = [];
    }
    // 將 [key, value] 加入到 Linked List（用 Array 表示）
    this.keyMap[index].push([key, value]);
  }

  get(key) {
    const index = this._hash(key);
    if (this.keyMap[index]) {
      // 遍歷鏈結串列尋找匹配的 Key
      for (let i = 0; i < this.keyMap[index].length; i++) {
        if (this.keyMap[index][i][0] === key) {
          return this.keyMap[index][i][1];
        }
      }
    }
    return undefined;
  }
}
```

#### 2. **Open Addressing（開放定址法）**

當發現目標位置已被佔用時，不斷探測後續位置直到找到空位。

**工作原理**：

```
插入 Key "ab"（雜湊到 Index 2）→ Index 2 為空 → 插入
插入 Key "ba"（雜湊到 Index 2）→ Index 2 已佔用 → 探測 Index 3
插入 Key "cd"（雜湊到 Index 2）→ Index 2 已佔用，Index 3 已佔用 → 探測 Index 4

┌────────────────────┐
│ Index │ Value      │
├────────────────────┤
│   0   │ NULL       │
│   1   │ NULL       │
│   2   │ (ab)       │
│   3   │ (ba)       │← 探測得到的位置
│   4   │ (cd)       │← 探測得到的位置
└────────────────────┘
```

**探測方法**：

| 方法 | 公式 | 說明 |
|------|------|------|
| **Linear Probing** | `h(x) + i` | 線性探測：依次檢查下一個位置 |
| **Quadratic Probing** | `h(x) + i²` | 二次探測：按 1², 2², 3'... 跳躍 |
| **Double Hashing** | `h(x) + i * h₂(x)` | 雙重雜湊：使用第二個雜湊函數 |

**線性探測範例**：

```javascript
class OpenAddressingHashTable {
  constructor(size = 53) {
    this.table = new Array(size);
    this.size = 0;
  }

  _hash(key) {
    let total = 0;
    const WEIRD_PRIME = 31;
    for (let i = 0; i < Math.min(key.length, 100); i++) {
      const value = key.charCodeAt(i) - 96;
      total = (total * WEIRD_PRIME + value) % this.table.length;
    }
    return total;
  }

  set(key, value) {
    let index = this._hash(key);
    // 線性探測：找到空位或相同 Key
    while (this.table[index] !== undefined && this.table[index][0] !== key) {
      index = (index + 1) % this.table.length;
    }
    this.table[index] = [key, value];
    this.size++;
  }

  get(key) {
    let index = this._hash(key);
    // 線性探測：尋找匹配的 Key
    while (this.table[index] !== undefined) {
      if (this.table[index][0] === key) {
        return this.table[index][1];
      }
      index = (index + 1) % this.table.length;
    }
    return undefined;
  }
}
```

**優點**：
- ✅ 記憶體效率高（不需額外指標）
- ✅ 快取效能好（所有資料在連續陣列中）

**缺點**：
- ❌ 容易形成「clustering（群集）」，連續的佔用位置
- ❌ Hash Table 會滿載
- ❌ 刪除操作複雜

### Chaining vs Open Addressing 比較

| 特性 | Separate Chaining | Open Addressing |
|------|-------------------|-----------------|
| **實作複雜度** | 簡單 | 中等到複雜 |
| **表滿的可能** | 否，可無限增長 | 是，限制在陣列大小 |
| **平均查詢時間** | O(1 + λ)* | O(1/(1-λ))** |
| **記憶體使用** | 需要額外指標 | 只需陣列空間 |
| **快取效能** | 較差（指標跳躍） | 較好（連續記憶體） |
| **適用場景** | 資料量不確定 | 資料量已知、記憶體寶貴 |

**註**: λ = Load Factor（負載因子）

---

## Hash Table 實作

### 完整的 Separate Chaining 實作

以下是一個完整的 Hash Table 類別實現，包含所有基本操作：

```javascript
class HashTable {
  constructor(size = 53) {
    this.keyMap = new Array(size);
  }

  // 雜湊函數
  _hash(key) {
    let total = 0;
    const WEIRD_PRIME = 31;
    for (let i = 0; i < Math.min(key.length, 100); i++) {
      const char = key[i];
      const value = char.charCodeAt(0) - 96;
      total = (total * WEIRD_PRIME + value) % this.keyMap.length;
    }
    return total;
  }

  // 設定 Key-Value 對
  set(key, value) {
    const index = this._hash(key);
    if (!this.keyMap[index]) {
      this.keyMap[index] = [];
    }
    this.keyMap[index].push([key, value]);
  }

  // 取得 Key 對應的 Value
  get(key) {
    const index = this._hash(key);
    if (this.keyMap[index]) {
      for (let i = 0; i < this.keyMap[index].length; i++) {
        if (this.keyMap[index][i][0] === key) {
          return this.keyMap[index][i][1];
        }
      }
    }
    return undefined;
  }

  // 取得所有 Keys
  keys() {
    const keysArr = [];
    for (let i = 0; i < this.keyMap.length; i++) {
      if (this.keyMap[i]) {
        for (let j = 0; j < this.keyMap[i].length; j++) {
          if (!keysArr.includes(this.keyMap[i][j][0])) {
            keysArr.push(this.keyMap[i][j][0]);
          }
        }
      }
    }
    return keysArr;
  }

  // 取得所有 Values
  values() {
    const valuesArr = [];
    for (let i = 0; i < this.keyMap.length; i++) {
      if (this.keyMap[i]) {
        for (let j = 0; j < this.keyMap[i].length; j++) {
          if (!valuesArr.includes(this.keyMap[i][j][1])) {
            valuesArr.push(this.keyMap[i][j][1]);
          }
        }
      }
    }
    return valuesArr;
  }

  // 刪除 Key-Value 對
  delete(key) {
    const index = this._hash(key);
    if (this.keyMap[index]) {
      for (let i = 0; i < this.keyMap[index].length; i++) {
        if (this.keyMap[index][i][0] === key) {
          this.keyMap[index].splice(i, 1);
          return true;
        }
      }
    }
    return false;
  }

  // 檢查 Key 是否存在
  has(key) {
    return this.get(key) !== undefined;
  }

  // 取得 Hash Table 的大小
  size() {
    let count = 0;
    for (let i = 0; i < this.keyMap.length; i++) {
      if (this.keyMap[i]) {
        count += this.keyMap[i].length;
      }
    }
    return count;
  }
}

// 使用範例
const myHashTable = new HashTable();
myHashTable.set("grapes", 10000);
myHashTable.set("apples", 54);
myHashTable.set("oranges", 2);

console.log(myHashTable.get("grapes"));     // 10000
console.log(myHashTable.get("apples"));     // 54
console.log(myHashTable.keys());            // ["grapes", "apples", "oranges"]
console.log(myHashTable.values());          // [10000, 54, 2]
console.log(myHashTable.has("bananas"));    // false
myHashTable.delete("apples");
console.log(myHashTable.size());            // 2
```

### Java 實作範例

```java
public class HashTable<K, V> {
    private static final int DEFAULT_CAPACITY = 16;
    private static final double LOAD_FACTOR = 0.75;
    
    private Node<K, V>[] buckets;
    private int size;
    
    @SuppressWarnings("unchecked")
    public HashTable() {
        this.buckets = new Node[DEFAULT_CAPACITY];
        this.size = 0;
    }
    
    // 內部類別：表示 Key-Value 對
    private static class Node<K, V> {
        K key;
        V value;
        Node<K, V> next;
        
        Node(K key, V value) {
            this.key = key;
            this.value = value;
        }
    }
    
    // 雜湊函數
    private int hash(K key) {
        if (key == null) return 0;
        return Math.abs(key.hashCode()) % buckets.length;
    }
    
    // 放置 Key-Value 對
    public void put(K key, V value) {
        if (size >= buckets.length * LOAD_FACTOR) {
            resize();
        }
        
        int index = hash(key);
        Node<K, V> node = buckets[index];
        
        // 檢查是否已存在相同 Key（更新）
        while (node != null) {
            if ((key == null && node.key == null) || 
                (key != null && key.equals(node.key))) {
                node.value = value;
                return;
            }
            node = node.next;
        }
        
        // 新增節點
        Node<K, V> newNode = new Node<>(key, value);
        newNode.next = buckets[index];
        buckets[index] = newNode;
        size++;
    }
    
    // 取得 Value
    public V get(K key) {
        int index = hash(key);
        Node<K, V> node = buckets[index];
        
        while (node != null) {
            if ((key == null && node.key == null) || 
                (key != null && key.equals(node.key))) {
                return node.value;
            }
            node = node.next;
        }
        return null;
    }
    
    // 調整大小（Rehashing）
    @SuppressWarnings("unchecked")
    private void resize() {
        Node<K, V>[] oldBuckets = buckets;
        buckets = new Node[oldBuckets.length * 2];
        size = 0;
        
        for (Node<K, V> node : oldBuckets) {
            while (node != null) {
                put(node.key, node.value);
                node = node.next;
            }
        }
    }
    
    public int size() {
        return size;
    }
}
```

---

## Big O 時間複雜度分析

### 平均情況（Average Case）

在負載因子合理、碰撞較少的情況下：

| 操作 | 時間複雜度 | 說明 |
|------|-----------|------|
| **Search（查詢）** | O(1) | 直接計算索引後查詢 |
| **Insert（插入）** | O(1) | 計算索引後直接插入 |
| **Delete（刪除）** | O(1) | 計算索引後直接刪除 |

### 最壞情況（Worst Case）

當所有 Key 都碰撞到同一個 Bucket 時（極糟的雜湊函數）：

| 操作 | 時間複雜度 | 說明 |
|------|-----------|------|
| **Search** | O(n) | 需要遍歷整個鏈結串列 |
| **Insert** | O(n) | 需要檢查所有已存在的元素 |
| **Delete** | O(n) | 需要遍歷整個鏈結串列 |

### 實際複雜度分析

使用 Separate Chaining 時，平均情況下的複雜度為：

- **O(1 + α)**，其中 α = n/m（n 為元素數，m 為 Bucket 數）

這表示如果維持良好的負載因子（通常 < 0.75），時間複雜度保持為 O(1)。

### 為什麼平均情況是 O(1)？

1. **雜湊函數高效**：計算索引是 O(1)
2. **碰撞稀少**：良好的雜湊函數使碰撞較少
3. **負載因子控制**：透過 Rehashing 維持負載因子 < 0.75
4. **每個鏈較短**：平均每個 Bucket 只有 α 個元素

---

## Load Factor 與 Rehashing

### 什麼是 Load Factor（負載因子）？

**負載因子（λ）** 是衡量 Hash Table 滿度的指標：

```
λ = n / m

其中：
- n = Hash Table 中的元素個數
- m = Hash Table 的大小（Bucket 數）
```

**例子**：
- Hash Table 大小為 100，已放入 50 個元素 → λ = 0.5
- Hash Table 大小為 100，已放入 75 個元素 → λ = 0.75
- Hash Table 大小為 100，已放入 100 個元素 → λ = 1.0

### 負載因子的影響

| 負載因子 | 狀態 | 性能 | 建議 |
|---------|------|------|------|
| λ < 0.5 | 稀疏 | ✅ 快速，記憶體浪費 | 可考慮縮小 |
| 0.5 ≤ λ < 0.75 | 理想 | ✅ 快速，記憶體效率好 | 保持此狀態 |
| 0.75 ≤ λ < 1.0 | 開始擁擠 | ⚠️ 開始變慢 | 應該 Rehash |
| λ ≥ 1.0 | 超滿 | ❌ 非常慢 | 必須 Rehash |

### Rehashing（重新雜湊）

當負載因子超過閾值（通常 0.75）時，進行 Rehashing 來擴大 Hash Table。

#### Rehashing 的步驟

1. **建立新表**：建立一個更大的新 Hash Table（通常是原來的 2 倍）
2. **重新計算**：對所有元素重新計算雜湊值（因為 Hash 函數中有模運算 `% size`）
3. **重新插入**：將所有元素插入新的 Hash Table
4. **替換舊表**：丟棄舊 Hash Table

#### 程式碼範例

```javascript
class HashTableWithRehashing {
  constructor(size = 53) {
    this.keyMap = new Array(size);
    this.size = 0;
    this.loadFactorThreshold = 0.75;
  }

  _hash(key) {
    let total = 0;
    const WEIRD_PRIME = 31;
    for (let i = 0; i < Math.min(key.length, 100); i++) {
      const value = key[i].charCodeAt(0) - 96;
      total = (total * WEIRD_PRIME + value) % this.keyMap.length;
    }
    return total;
  }

  _getLoadFactor() {
    return this.size / this.keyMap.length;
  }

  _shouldRehash() {
    return this._getLoadFactor() >= this.loadFactorThreshold;
  }

  _rehash() {
    const oldKeyMap = this.keyMap;
    const oldSize = this.size;
    
    // 建立新的大表
    this.keyMap = new Array(oldKeyMap.length * 2);
    this.size = 0;
    
    // 重新插入所有元素
    for (let i = 0; i < oldKeyMap.length; i++) {
      if (oldKeyMap[i]) {
        for (let j = 0; j < oldKeyMap[i].length; j++) {
          const [key, value] = oldKeyMap[i][j];
          this.set(key, value);  // 使用新的雜湊函數
        }
      }
    }
    
    console.log(`Rehashing: 從 ${oldKeyMap.length} 擴展到 ${this.keyMap.length}`);
  }

  set(key, value) {
    const index = this._hash(key);
    if (!this.keyMap[index]) {
      this.keyMap[index] = [];
    }
    this.keyMap[index].push([key, value]);
    this.size++;
    
    // 檢查是否需要 Rehashing
    if (this._shouldRehash()) {
      this._rehash();
    }
  }

  get(key) {
    const index = this._hash(key);
    if (this.keyMap[index]) {
      for (let i = 0; i < this.keyMap[index].length; i++) {
        if (this.keyMap[index][i][0] === key) {
          return this.keyMap[index][i][1];
        }
      }
    }
    return undefined;
  }
}

// 使用範例
const table = new HashTableWithRehashing(5);  // 初始大小為 5
console.log(`初始大小: ${table.keyMap.length}`);

// 持續插入，觀察何時觸發 Rehashing
table.set("a", 1);   // size: 1, loadFactor: 0.2
table.set("b", 2);   // size: 2, loadFactor: 0.4
table.set("c", 3);   // size: 3, loadFactor: 0.6
table.set("d", 4);   // size: 4, loadFactor: 0.8 → 觸發 Rehashing！
// 輸出：Rehashing: 從 5 擴展到 10
table.set("e", 5);   // 在新表中繼續插入
```

#### Rehashing 的時間複雜度

- **平均情況**：O(n + m)
  - n = 元素個數（需要重新雜湊）
  - m = 新表大小
- **攤銷時間**：O(1)
  - 雖然單次 Rehashing 很昂貴，但由於 Rehashing 不頻繁發生，平均每次插入的成本仍為 O(1)

---

## Hash Table vs 其他資料結構

### Hash Table vs Array

| 特性 | Hash Table | Array |
|------|-----------|-------|
| **查詢（按值）** | O(1)* | O(n) |
| **查詢（按索引）** | N/A | O(1) |
| **插入** | O(1)* | O(n) |
| **刪除** | O(1)* | O(n) |
| **有序性** | 無序 | 有序 |
| **記憶體** | 可能浪費 | 緊湊 |
| **快取效能** | 差 | 優 |

**註**: * 平均情況下

**何時使用**：
- **Hash Table**：頻繁查詢、插入、刪除；Key-Value 對應
- **Array**：需要有序；按索引存取

### Hash Table vs Linked List

| 特性 | Hash Table | Linked List |
|------|-----------|-------------|
| **查詢** | O(1)* | O(n) |
| **插入** | O(1)* | O(n) |
| **刪除** | O(1)* | O(n) |
| **有序性** | 無序 | 保持插入順序 |
| **記憶體** | 可能浪費 | 每個節點需指標 |
| **快取效能** | 差 | 差（分散記憶體） |

**何時使用**：
- **Hash Table**：需要快速查詢、插入、刪除
- **Linked List**：需要保持順序；頻繁從頭尾操作

### Hash Table vs Binary Search Tree

| 特性 | Hash Table | Binary Search Tree |
|------|-----------|-------------------|
| **查詢** | O(1)* | O(log n) |
| **插入** | O(1)* | O(log n) |
| **刪除** | O(1)* | O(log n) |
| **順序遍歷** | 困難 | 容易（中序遍歷） |
| **範圍查詢** | 不支援 | 支援 |
| **最小/最大** | 需要掃描全表 | O(1) |
| **記憶體** | 可能浪費 | 沒有浪費 |

**何時使用**：
- **Hash Table**：只需精確查詢
- **Binary Search Tree**：需要排序、範圍查詢、有序遍歷

### 選擇標準

```
需要快速查詢、插入、刪除 → Hash Table
需要保持有序或範圍查詢 → Binary Search Tree
需要頻繁從頭尾操作 → Linked List
需要按索引存取、記憶體緊湊 → Array
```

---

## 實際應用場景

### 1. 資料庫索引（Database Indexing）

**場景**：使用者資料庫，需要快速找到特定使用者

```javascript
class UserDatabase {
  constructor() {
    this.userTable = new HashTable();  // 使用 Hash Table 儲存
  }

  addUser(userId, userData) {
    this.userTable.set(userId, userData);
  }

  findUser(userId) {
    return this.userTable.get(userId);  // O(1) 快速查詢
  }
}

// 使用
const db = new UserDatabase();
db.addUser("user123", { name: "Alice", age: 25 });
db.addUser("user456", { name: "Bob", age: 30 });

const user = db.findUser("user123");  // 立即找到，無需掃描整個表
console.log(user);  // { name: "Alice", age: 25 }
```

### 2. 快取（Caching）

**場景**：網頁瀏覽器快取，快速查詢已下載的資源

```javascript
class CacheManager {
  constructor(maxSize = 100) {
    this.cache = new HashTable();
    this.maxSize = maxSize;
  }

  get(url) {
    return this.cache.get(url);  // O(1) 查詢快取
  }

  set(url, data) {
    if (this.cache.size() >= this.maxSize) {
      // 簡化的快取清除策略
      const keys = this.cache.keys();
      this.cache.delete(keys[0]);
    }
    this.cache.set(url, data);
  }
}

// 使用
const cache = new CacheManager();
cache.set("https://example.com/image.jpg", imageData);
const cachedData = cache.get("https://example.com/image.jpg");  // 快速檢索
```

### 3. 去重（Deduplication）

**場景**：給定一個陣列，找出第一個未重複出現的字符

```javascript
function firstUniqueChar(s) {
  const charCount = new HashTable();
  
  // 統計每個字符出現的次數
  for (let char of s) {
    const count = charCount.get(char) || 0;
    charCount.set(char, count + 1);
  }
  
  // 找第一個出現次數為 1 的字符
  for (let char of s) {
    if (charCount.get(char) === 1) {
      return char;
    }
  }
  
  return null;
}

console.log(firstUniqueChar("leetcode"));      // 'l'
console.log(firstUniqueChar("loveleetcode"));  // 'v'
```

### 4. 兩數之和（Two Sum Problem）

**場景**：給定一個陣列和目標值，找出兩個元素相加等於目標值

```javascript
function twoSum(nums, target) {
  const seen = new HashTable();
  
  for (let num of nums) {
    const complement = target - num;
    if (seen.has(complement)) {
      return [complement, num];
    }
    seen.set(num, true);  // 標記已見過此數
  }
  
  return null;
}

console.log(twoSum([2, 7, 11, 15], 9));   // [2, 7]
console.log(twoSum([3, 2, 4], 6));        // [2, 4]
```

### 5. 字集合（Set 實現）

**場景**：管理一群不重複的元素

```javascript
class SimpleSet {
  constructor() {
    this.data = new HashTable();
  }

  add(element) {
    this.data.set(element, true);
  }

  has(element) {
    return this.data.has(element);
  }

  remove(element) {
    this.data.delete(element);
  }

  size() {
    return this.data.size();
  }
}

// 使用
const set = new SimpleSet();
set.add("apple");
set.add("banana");
set.add("apple");  // 重複，不會改變集合

console.log(set.has("apple"));    // true
console.log(set.has("orange"));   // false
console.log(set.size());          // 2
```

### 6. 字群組（Anagrams 群組）

**場景**：將所有相同字母組成的字分為一組

```javascript
function groupAnagrams(words) {
  const groups = new HashTable();
  
  for (let word of words) {
    // 將單字的字母排序後作為 Key
    const sortedWord = word.split('').sort().join('');
    
    if (!groups.has(sortedWord)) {
      groups.set(sortedWord, []);
    }
    
    const group = groups.get(sortedWord);
    group.push(word);
  }
  
  return groups.values();
}

const result = groupAnagrams(["eat", "tea", "ate", "tan", "ant", "cat"]);
console.log(result);
// [["eat", "tea", "ate"], ["tan", "ant"], ["cat"]]
```

### 7. 編譯器中的符號表（Symbol Table）

**場景**：程式編譯時，記錄變數名稱、函數名稱等的資訊

```javascript
class SymbolTable {
  constructor() {
    this.symbols = new HashTable();
  }

  defineVariable(name, type, value) {
    this.symbols.set(name, { type, value, defined: true });
  }

  isDefined(name) {
    return this.symbols.has(name);
  }

  getType(name) {
    if (this.isDefined(name)) {
      return this.symbols.get(name).type;
    }
    return null;
  }
}

// 使用
const symbolTable = new SymbolTable();
symbolTable.defineVariable("x", "int", 10);
symbolTable.defineVariable("name", "string", "Alice");

console.log(symbolTable.isDefined("x"));        // true
console.log(symbolTable.getType("name"));       // "string"
```

---

## 各程式語言內建實現

### Python 中的 Hash Table

Python 使用 **Dictionary（字典）** 來實現 Hash Table，非常方便易用。

```python
# 建立字典
person = {}

# 新增 Key-Value 對
person["name"] = "Alice"
person["age"] = 25
person["city"] = "New York"

# 查詢
print(person["name"])           # Alice

# 檢查 Key 是否存在
if "age" in person:
    print(person["age"])        # 25

# 取得所有 Keys
print(person.keys())            # dict_keys(['name', 'age', 'city'])

# 取得所有 Values
print(person.values())          # dict_values(['Alice', 25, 'New York'])

# 取得所有 Key-Value 對
print(person.items())           # dict_items([('name', 'Alice'), ('age', 25), ...])

# 刪除
del person["city"]

# 使用 get 方法（安全查詢）
print(person.get("name"))       # Alice
print(person.get("phone", "N/A"))  # N/A（預設值）

# 更新多個值
person.update({"email": "alice@example.com", "age": 26})

# 大小
print(len(person))              # 3
```

**Python Set**（基於 Hash Table）：

```python
# 建立集合
fruits = {"apple", "banana", "orange"}

# 新增
fruits.add("grape")

# 移除
fruits.remove("apple")

# 檢查成員
if "banana" in fruits:
    print("Found banana")

# 交集、並集、差集
set1 = {1, 2, 3}
set2 = {2, 3, 4}
print(set1 & set2)              # {2, 3}  交集
print(set1 | set2)              # {1, 2, 3, 4}  並集
print(set1 - set2)              # {1}  差集
```

### JavaScript 中的 Hash Table

#### 1. **使用 Object（傳統方式）**

```javascript
const person = {
  name: "Alice",
  age: 25,
  city: "New York"
};

// 查詢
console.log(person.name);          // Alice
console.log(person["age"]);        // 25

// 新增/修改
person.email = "alice@example.com";
person["phone"] = "555-1234";

// 檢查 Key
console.log("name" in person);     // true

// 取得所有 Keys
console.log(Object.keys(person));  // ["name", "age", "city", "email", "phone"]

// 刪除
delete person.city;

// 遍歷
for (const key in person) {
  console.log(`${key}: ${person[key]}`);
}
```

#### 2. **使用 Map（ES6，推薦）**

```javascript
// 建立 Map
const userMap = new Map();

// 新增
userMap.set("user1", { name: "Alice", age: 25 });
userMap.set("user2", { name: "Bob", age: 30 });

// 查詢
console.log(userMap.get("user1"));     // { name: "Alice", age: 25 }

// 檢查 Key
console.log(userMap.has("user2"));     // true

// 取得大小
console.log(userMap.size);             // 2

// 刪除
userMap.delete("user1");

// 遍歷
for (const [key, value] of userMap) {
  console.log(`${key}: ${JSON.stringify(value)}`);
}

// 清空
userMap.clear();
```

**Map vs Object**：
- Map 的 Key 可以是任何類型，Object 只能是字串或符號
- Map 維持插入順序
- Map 提供更好的效能（特別是大量插入/刪除時）

#### 3. **使用 Set（去重）**

```javascript
// 建立 Set
const uniqueNumbers = new Set([1, 2, 2, 3, 3, 3, 4]);
console.log(uniqueNumbers);         // Set { 1, 2, 3, 4 }

// 新增
uniqueNumbers.add(5);

// 檢查成員
console.log(uniqueNumbers.has(3));  // true

// 刪除
uniqueNumbers.delete(2);

// 大小
console.log(uniqueNumbers.size);    // 4

// 遍歷
for (const num of uniqueNumbers) {
  console.log(num);
}
```

### Java 中的 Hash Table

#### 1. **HashMap（推薦）**

```java
import java.util.HashMap;
import java.util.Map;

public class HashMapExample {
    public static void main(String[] args) {
        // 建立 HashMap
        Map<String, Integer> scores = new HashMap<>();
        
        // 新增
        scores.put("Alice", 95);
        scores.put("Bob", 87);
        scores.put("Charlie", 92);
        
        // 查詢
        System.out.println(scores.get("Alice"));      // 95
        
        // 檢查 Key
        if (scores.containsKey("Bob")) {
            System.out.println("Found Bob");
        }
        
        // 取得所有 Keys
        System.out.println(scores.keySet());          // [Alice, Bob, Charlie]
        
        // 取得所有 Values
        System.out.println(scores.values());          // [95, 87, 92]
        
        // 大小
        System.out.println(scores.size());            // 3
        
        // 刪除
        scores.remove("Bob");
        
        // 遍歷
        for (Map.Entry<String, Integer> entry : scores.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
        
        // 取得或預設值
        int score = scores.getOrDefault("David", 0);  // 0
    }
}
```

#### 2. **HashSet（去重）**

```java
import java.util.HashSet;
import java.util.Set;

public class HashSetExample {
    public static void main(String[] args) {
        // 建立 HashSet
        Set<String> fruits = new HashSet<>();
        
        // 新增
        fruits.add("apple");
        fruits.add("banana");
        fruits.add("orange");
        fruits.add("apple");   // 重複，不會新增
        
        // 大小
        System.out.println(fruits.size());   // 3
        
        // 檢查成員
        System.out.println(fruits.contains("apple"));  // true
        
        // 刪除
        fruits.remove("banana");
        
        // 遍歷
        for (String fruit : fruits) {
            System.out.println(fruit);
        }
    }
}
```

### C++ 中的 Hash Table

```cpp
#include <unordered_map>
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    // 使用 unordered_map（Hash Table）
    unordered_map<string, int> scores;
    
    // 新增
    scores["Alice"] = 95;
    scores["Bob"] = 87;
    
    // 查詢
    cout << scores["Alice"] << endl;        // 95
    
    // 檢查 Key
    if (scores.find("Bob") != scores.end()) {
        cout << "Found Bob" << endl;
    }
    
    // 大小
    cout << scores.size() << endl;          // 2
    
    // 刪除
    scores.erase("Bob");
    
    // 遍歷
    for (auto& pair : scores) {
        cout << pair.first << ": " << pair.second << endl;
    }
    
    // 使用 unordered_set（去重）
    unordered_set<int> uniqueNumbers = {1, 2, 2, 3, 3, 3};
    cout << uniqueNumbers.size() << endl;   // 3
    
    return 0;
}
```

---

## 學習要點總結

### 核心概念

✅ **Hash Table 是什麼**
- 一種透過雜湊函數將 Key 對應到陣列索引的資料結構
- 支援 O(1) 的查詢、插入、刪除（平均情況）

✅ **雜湊函數的特性**
- 快速計算（O(1)）
- 確定性（相同 Input 產生相同 Output）
- 均勻分佈（避免碰撞）
- 使用質數和模運算可以改進效能

✅ **碰撞解決方案**
- **Separate Chaining**：在每個 Bucket 中使用 Linked List（簡單、易實作）
- **Open Addressing**：線性探測、二次探測、雙重雜湊（記憶體高效）

✅ **負載因子和 Rehashing**
- 負載因子 = 元素數 / 表大小
- 當負載因子 > 0.75 時，應進行 Rehashing
- Rehashing 會建立新表並重新計算所有元素的位置

### 時間複雜度

| 操作 | 平均 | 最壞 |
|------|------|------|
| 查詢 | O(1) | O(n) |
| 插入 | O(1) | O(n) |
| 刪除 | O(1) | O(n) |

### 選擇標準

- **Hash Table**：需要快速查詢、插入、刪除；允許無序
- **Binary Search Tree**：需要排序、範圍查詢、有序遍歷
- **Array**：需要按索引存取、隨機訪問
- **Linked List**：需要頻繁在頭尾操作

### 實際應用

- 資料庫索引和查詢優化
- 快取（瀏覽器、CPU）
- 去重和統計
- Set 和 Map 實現
- 編譯器符號表
- 路由表

### 最佳實踐

1. **選擇好的雜湊函數**：減少碰撞，提高效能
2. **監控負載因子**：及時進行 Rehashing
3. **考慮空間時間權衡**：Hash Table 可能浪費記憶體但速度快
4. **使用內建實現**：Python dict、Java HashMap、JavaScript Map
5. **測試邊界情況**：空表、單元素、滿表等

---

## 附加資源

### 複習順序

1. 理解基本概念和雜湊函數
2. 學習碰撞解決方案（Chaining 優先）
3. 實作簡單的 Hash Table
4. 理解負載因子和 Rehashing
5. 分析時間複雜度
6. 學習各語言的內建實現
7. 練習實際應用問題

### 常見面試問題

- 解釋 Hash Table 的工作原理
- 如何設計一個好的雜湊函數？
- Chaining 和 Open Addressing 的優缺點？
- 為什麼要使用質數作為表大小？
- 什麼是負載因子？何時進行 Rehashing？
- Hash Table vs HashMap 有什麼區別？
- 如何解決碰撞？
- 實作一個簡單的 Hash Table

### 練習題目

1. **Two Sum**：找兩個數字相加等於目標值
2. **Contains Duplicate**：檢查是否有重複元素
3. **Valid Anagram**：檢查兩個字是否是變位詞
4. **Group Anagrams**：將變位詞分組
5. **Majority Element**：找出現次數最多的元素
6. **First Unique Character**：找第一個未重複的字符
7. **LRU Cache**：實作最近最少使用快取
8. **Design HashMap**：設計自己的 HashMap

---

## 參考文獻

本筆記整合自以下資源：

- Alright Chiu 的演算法與資料結構教學
- Medium - Amber Fragments 的 Hash Table 學習筆記
- 部落格教學：Hash Table 基礎與實作
- Udemy 資料結構課程
- 官方文件：Python dict、Java HashMap、JavaScript Map

---

*最後更新：2025 年 12 月*

*本筆記為學習用途，持續更新中。*
