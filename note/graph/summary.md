# 資料結構完整筆記 : 圖 (Graph)
---

## 目錄 (Table of Contents)

1. [第一章：Graph 基本概念](#第一章graph-基本概念)
   - 1.1 [Graph 的定義與術語](#11-graph-的定義與術語)
   - 1.2 [Graph 的類型](#12-graph-的類型)
   - 1.3 [基本性質與定理](#13-基本性質與定理)

2. [第二章：Graph 的表示法](#第二章graph-的表示法)
   - 2.1 [鄰接矩陣 (Adjacency Matrix)](#21-鄰接矩陣-adjacency-matrix)
   - 2.2 [鄰接串列 (Adjacency List)](#22-鄰接串列-adjacency-list)
   - 2.3 [表示法比較與選擇](#23-表示法比較與選擇)

3. [第三章：Graph 遍歷演算法](#第三章graph-遍歷演算法)
   - 3.1 [深度優先搜尋 (DFS)](#31-深度優先搜尋-dfs)
   - 3.2 [廣度優先搜尋 (BFS)](#32-廣度優先搜尋-bfs)
   - 3.3 [DFS vs BFS 比較](#33-dfs-vs-bfs-比較)

4. [第四章：最短路徑演算法](#第四章最短路徑演算法)
   - 4.1 [Dijkstra's Algorithm](#41-dijkstras-algorithm)
   - 4.2 [Bellman-Ford Algorithm](#42-bellman-ford-algorithm)
   - 4.3 [Floyd-Warshall Algorithm](#43-floyd-warshall-algorithm)
   - 4.4 [最短路徑演算法比較](#44-最短路徑演算法比較)

5. [第五章：最小生成樹](#第五章最小生成樹)
   - 5.1 [Prim's Algorithm](#51-prims-algorithm)
   - 5.2 [Kruskal's Algorithm](#52-kruskals-algorithm)
   - 5.3 [MST 演算法比較](#53-mst-演算法比較)

6. [第六章：進階主題](#第六章進階主題)
   - 6.1 [拓撲排序 (Topological Sort)](#61-拓撲排序-topological-sort)
   - 6.2 [強連通分量 (Strongly Connected Components)](#62-強連通分量-strongly-connected-components)
   - 6.3 [圖著色問題 (Graph Coloring)](#63-圖著色問題-graph-coloring)
   - 6.4 [網路流 (Network Flow)](#64-網路流-network-flow)

7. [附錄 A：演算法複雜度總表](#附錄-a演算法複雜度總表)
8. [附錄 B：實務應用案例](#附錄-b實務應用案例)
9. [參考文獻](#參考文獻)

---

## 第一章：Graph 基本概念

### 1.1 Graph 的定義與術語

#### 摘要

Graph（圖）是一種非線性資料結構，由頂點（vertices/nodes）和邊（edges）組成，用於表示物件之間的關係。與陣列或鏈結串列不同，Graph 不遵循順序結構，能靈活表示複雜的關係網絡。Graph 在計算機科學中應用廣泛，從社交網路分析到路徑規劃，從編譯器優化到神經網路，都能看到其身影。理解 Graph 的基本概念是掌握進階演算法的關鍵基礎。

#### 重點整理

- **Graph 定義**：一個 Graph \( G = (V, E) \) 由頂點集合 \( V \) 和邊集合 \( E \) 組成
- **頂點 (Vertices/Nodes)**：Graph 中的基本單位，代表實體或狀態
- **邊 (Edges/Arcs)**：連接兩個頂點的線，表示它們之間的關係
- **鄰接 (Adjacency)**：若兩個頂點之間有邊相連，則稱這兩個頂點相鄰
- **度 (Degree)**：與一個頂點相連的邊的數量
  - **入度 (In-degree)**：有向圖中指向該頂點的邊的數量
  - **出度 (Out-degree)**：有向圖中從該頂點出發的邊的數量
- **路徑 (Path)**：從一個頂點到另一個頂點經過的頂點序列
- **環 (Cycle)**：起點和終點相同的路徑

#### 重要定義與公式

**定義 1.1 (Graph)**：一個圖 \( G \) 是一個有序對 \( G = (V, E) \)，其中：
- \( V \) 是有限非空集合，稱為頂點集
- \( E \subseteq V \times V \) 是邊集，每條邊連接兩個頂點

**定義 1.2 (度)**：對於無向圖中的頂點 \( v \)，其度 \( deg(v) \) 定義為與 \( v \) 相連的邊的數量。

**握手定理 (Handshaking Lemma)**：
\[ \sum_{v \in V} deg(v) = 2|E| \]

這個定理表明，所有頂點的度數之和等於邊數的兩倍（因為每條邊貢獻兩個度）。

#### <加深的內容>

**為什麼 Graph 如此重要？**

Graph 作為一種抽象的數學模型，能夠自然地表示現實世界中各種複雜的關係。想像一個城市的地圖：每個路口是一個頂點，每條道路是一條邊。這樣的表示方式讓我們能夠使用演算法來解決實際問題，例如尋找最短路徑、規劃交通流量等。Graph 的威力在於它的普適性——幾乎任何涉及「關係」的問題都可以用 Graph 來建模。

從理論角度看，Graph 理論起源於 1736 年數學家歐拉解決柯尼斯堡七橋問題，這標誌著 Graph 理論的誕生。從那時起，Graph 理論發展成為離散數學的核心分支，並在現代計算機科學中扮演關鍵角色。在演算法設計中，許多看似不相關的問題實際上都可以轉化為 Graph 問題來解決，這種抽象能力是計算機科學家必須掌握的思維方式。

**Graph 與其他資料結構的關係**

值得注意的是，許多我們熟悉的資料結構實際上都是 Graph 的特例。例如，Tree（樹）是一種特殊的 Graph——它是無環連通圖，且任意兩個頂點之間恰有一條路徑。Linked List（鏈結串列）可以視為一種退化的 Tree，每個節點最多只有一個子節點。理解這種階層關係能幫助我們更好地選擇合適的資料結構。

在實作層面，Graph 的靈活性也帶來了挑戰。不同於陣列或鏈結串列有標準的實作方式，Graph 的表示方法需要根據具體問題的特性來選擇。稀疏圖（邊數遠小於 \( |V|^2 \)）和稠密圖（邊數接近 \( |V|^2 \)）可能需要完全不同的表示方法，這將在後續章節中詳細探討。

#### <額外加深加廣的內容>

**1. 相關變體或延伸主題（推論/擴充）**

- **多重圖 (Multigraph)**：允許兩個頂點之間有多條邊，或者允許自環（一條邊的兩端連接同一個頂點）。多重圖在建模實際交通網路時很有用，因為兩個城市之間可能有多條不同的道路或航線。

- **超圖 (Hypergraph)**：將邊的概念推廣，一條超邊可以連接任意數量的頂點（而不只是兩個）。超圖在建模數據庫關係、化學反應網路等場景中很有用。

- **動態圖 (Dynamic Graph)**：圖的結構隨時間變化，頂點和邊可能被添加或刪除。這在社交網路分析、交通流量預測等應用中非常重要。

- **權重圖的擴展**：除了邊有權重，頂點也可以有權重或多個屬性，形成屬性圖（Property Graph），這在知識圖譜和圖資料庫中廣泛應用。

**2. 實務應用**

- **社交網路分析**：使用者是頂點，關注或好友關係是邊。可以分析社群結構、影響力傳播、推薦好友等。Facebook 有超過 20 億使用者，形成的圖有數千億條邊，如何高效存儲和查詢是巨大挑戰。

- **網頁排名 (PageRank)**：將網際網路建模為有向圖，網頁是頂點，超連結是邊。Google 的 PageRank 演算法通過分析這個圖的結構來評估網頁的重要性。

- **生物資訊學**：蛋白質交互網路、基因調控網路、代謝網路都可以用圖來表示。分析這些圖可以發現疾病機制、藥物靶點等。

**3. 潛在研究問題或開放題（作者補充）**

- **圖同構問題 (Graph Isomorphism Problem)**：判斷兩個圖是否在結構上相同（即存在頂點之間的一一對應使得邊的連接關係保持不變）。這個問題的複雜度至今未完全解決，2015 年 László Babai 提出了準多項式時間演算法，但仍不確定是否存在多項式時間演算法。

- **大規模圖處理**：當圖的規模達到數十億頂點和數千億邊時，如何高效存儲、查詢和分析？這涉及分散式圖處理系統（如 Apache Giraph、GraphX）的設計。

- **動態圖演算法**：在圖結構不斷變化的情況下，如何維護某些圖的性質（如最短路徑、連通性）而不需要每次都從頭計算？

#### 理解檢核

**問題 1**：為什麼握手定理中所有頂點的度數之和等於邊數的兩倍？

**答案**：因為每條邊連接兩個頂點，所以在計算所有頂點的度數之和時，每條邊會被計算兩次（一次在它連接的第一個頂點，一次在第二個頂點）。因此，度數之和 = 2 × 邊數。

**問題 2**：在一個有 \( n \) 個頂點的無向簡單圖（無自環、無多重邊）中，最多可能有多少條邊？

**答案**：\( \frac{n(n-1)}{2} \) 條邊。因為每兩個不同的頂點之間最多有一條邊，這是組合數 \( C(n, 2) = \frac{n(n-1)}{2} \)。

**問題 3**：Tree（樹）和 Graph 的主要區別是什麼？能否舉例說明？

**答案**：Tree 是一種特殊的 Graph，滿足以下條件：
1. 連通（任意兩個頂點之間都有路徑）
2. 無環（不存在起點和終點相同的路徑）
3. 有 \( n \) 個頂點的樹恰有 \( n-1 \) 條邊

例如，組織架構圖是一棵樹（有明確的階層關係），而社交網路是一般的圖（可能有環，如 A 認識 B，B 認識 C，C 又認識 A）。

#### 參考來源

[1] GeeksforGeeks, "Introduction to Graph Data Structure," 2025. [Online]. Available: https://www.geeksforgeeks.org/dsa/introduction-to-graphs-data-structure-and-algorithm-tutorials/ [Accessed: 29-Dec-2025].

[2] T. H. Cormen, C. E. Leiserson, R. L. Rivest, and C. Stein, "Introduction to Algorithms," 3rd ed., MIT Press, 2009, Chapter 22: Elementary Graph Algorithms.

---

### 1.2 Graph 的類型

#### 摘要

Graph 可以根據不同的特性進行分類，主要分類依據包括：邊的方向性（有向/無向）、邊的權重（加權/非加權）、連通性（連通/非連通）、是否有環（循環/無環），以及邊的密度（稀疏/稠密）。理解這些分類對於選擇正確的演算法和資料結構至關重要，因為不同類型的圖需要不同的處理策略。掌握圖的類型也有助於將現實世界的問題正確地抽象為圖模型。

#### 重點整理

**依據邊的方向性：**
- **無向圖 (Undirected Graph)**：邊沒有方向，表示對稱關係（如：A 和 B 是朋友）
- **有向圖 (Directed Graph / Digraph)**：邊有方向，表示非對稱關係（如：A 關注 B，但 B 不一定關注 A）

**依據邊的權重：**
- **加權圖 (Weighted Graph)**：每條邊有一個數值（權重），表示距離、成本、時間等
- **非加權圖 (Unweighted Graph)**：所有邊視為等價，沒有額外的數值

**依據連通性：**
- **連通圖 (Connected Graph)**：任意兩個頂點之間都存在路徑
- **非連通圖 (Disconnected Graph)**：存在某些頂點無法從其他頂點到達
- **強連通圖 (Strongly Connected Graph)**：有向圖中，任意兩個頂點之間都存在雙向路徑
- **弱連通圖 (Weakly Connected Graph)**：有向圖中，忽略方向後是連通的

**依據是否有環：**
- **循環圖 (Cyclic Graph)**：存在環（起點和終點相同的路徑）
- **無環圖 (Acyclic Graph)**：不存在環
- **有向無環圖 (DAG - Directed Acyclic Graph)**：有向且無環，常用於表示依賴關係

**特殊類型：**
- **完全圖 (Complete Graph)**：任意兩個頂點之間都有邊
- **二分圖 (Bipartite Graph)**：頂點可以分為兩個集合，同一集合內的頂點之間沒有邊
- **平面圖 (Planar Graph)**：可以在平面上繪制且邊不相交
- **稀疏圖 (Sparse Graph)**：邊數遠小於 \( |V|^2 \)，通常 \( |E| = O(|V|) \)
- **稠密圖 (Dense Graph)**：邊數接近 \( |V|^2 \)，通常 \( |E| = O(|V|^2) \)

#### 重要定義與公式

**定義 1.3 (完全圖)**：一個有 \( n \) 個頂點的無向完全圖記為 \( K_n \)，其邊數為：
\[ |E| = \frac{n(n-1)}{2} = C(n, 2) \]

對於有向完全圖，邊數為 \( n(n-1) \)（每對頂點之間有兩條方向相反的邊）。

**定義 1.4 (二分圖)**：一個圖 \( G = (V, E) \) 是二分圖，當且僅當存在 \( V \) 的一個劃分 \( V = V_1 \cup V_2 \)（\( V_1 \cap V_2 = \emptyset \)），使得所有邊的兩個端點分別在 \( V_1 \) 和 \( V_2 \) 中。

**定理 1.1 (二分圖判定)**：一個圖是二分圖，當且僅當它不包含奇數長度的環。

**定義 1.5 (DAG)**：一個有向圖 \( G = (V, E) \) 是 DAG，當且僅當它不包含有向環。

#### <加深的內容>

**有向圖 vs 無向圖的選擇**

在建模實際問題時，選擇有向圖還是無向圖是一個重要決策。社交網路中，Facebook 的好友關係是對稱的（A 是 B 的好友，則 B 也是 A 的好友），適合用無向圖；而 Twitter 的關注關係是非對稱的（A 關注 B，但 B 不一定關注 A），必須用有向圖。這種差異會直接影響演算法的設計：在無向圖中，如果 A 和 B 相連，則邊 (A, B) 和 (B, A) 是同一條邊；但在有向圖中，它們是兩條不同的邊。

在實作上，無向圖可以用兩種方式表示：一種是將每條無向邊 (u, v) 存儲為兩條有向邊 (u, v) 和 (v, u)；另一種是只存儲一次，但在演算法中需要考慮雙向性。前者簡化了演算法邏輯但增加了空間消耗，後者節省空間但增加了程式複雜度。選擇哪種方式取決於具體應用場景。

**稀疏圖與稠密圖的影響**

圖的稀疏性對演算法選擇有決定性影響。稀疏圖（\( |E| \ll |V|^2 \)）在現實中很常見，例如社交網路中一個人通常只認識幾百或幾千人，而不是認識所有人。對於稀疏圖，鄰接串列表示法更高效，因為它只存儲實際存在的邊。許多圖演算法的時間複雜度表示為 \( O(V + E) \)，對於稀疏圖這接近線性時間。

相反，稠密圖（\( |E| \approx |V|^2 \)）在某些應用中也會出現，例如完全圖或接近完全圖。對於稠密圖，鄰接矩陣可能是更好的選擇，因為它可以在 \( O(1) \) 時間內查詢任意兩個頂點是否相連。一些演算法（如 Floyd-Warshall）在稠密圖上表現更好。

#### <額外加深加廣的內容>

**1. 相關變體或延伸主題（推論/擴充）**

- **歐拉圖 (Eulerian Graph)**：存在歐拉迴路（經過每條邊恰好一次的迴路）的圖。判定條件：連通圖且所有頂點的度數都是偶數。

- **哈密頓圖 (Hamiltonian Graph)**：存在哈密頓迴路（經過每個頂點恰好一次的迴路）的圖。判定哈密頓圖是 NP-Complete 問題。

- **區間圖 (Interval Graph)**：可以用數軸上的區間表示頂點，兩個區間相交則對應頂點之間有邊。許多 NP-Complete 問題在區間圖上可以多項式時間解決。

- **弦圖 (Chordal Graph)**：每個長度大於 3 的環都有弦（連接環上非相鄰頂點的邊）的圖。許多圖問題在弦圖上可以高效解決。

**2. 實務應用**

- **DAG 在任務排程中的應用**：編譯器中的指令排程、專案管理中的任務依賴（PERT/CPM）、Makefile 中的編譯依賴等都可以用 DAG 建模。拓撲排序可以找到滿足所有依賴關係的執行順序。

- **二分圖在配對問題中的應用**：招聘系統中的求職者與職位配對、相親系統中的男女配對、宿舍分配等都可以建模為二分圖的最大匹配問題。匈牙利演算法可以在多項式時間內解決。

- **平面圖在 VLSI 設計中的應用**：集成電路的布線設計需要確保導線不交叉（或交叉次數最少），這涉及平面圖的判定和繪製。

**3. 潛在研究問題或開放題（作者補充）**

- **圖的參數化複雜度**：許多在一般圖上是 NP-Complete 的問題，在某些參數（如樹寬、團數）受限的圖上可以高效解決。如何找到合適的參數並設計固定參數可計算（FPT）演算法是活躍的研究領域。

- **動態圖的維護**：在社交網路等應用中，圖的結構不斷變化。如何高效維護圖的某些性質（如連通分量、最短路徑）而不需要每次都重新計算？

- **近似演算法**：對於 NP-Complete 的圖問題（如圖著色、最大團），如何設計近似演算法在多項式時間內得到接近最優解的解？

#### 理解檢核

**問題 1**：一個無向圖有 5 個頂點和 12 條邊，請問這可能嗎？為什麼？

**答案**：不可能。因為 5 個頂點的無向簡單圖最多有 \( C(5, 2) = 10 \) 條邊（完全圖 \( K_5 \)）。如果有 12 條邊，說明存在多重邊或自環，那麼這是一個多重圖而不是簡單圖。

**問題 2**：如何判斷一個圖是否為二分圖？

**答案**：可以使用圖著色的方法：從任意未訪問的頂點開始進行 BFS 或 DFS，嘗試用兩種顏色給頂點著色，使得相鄰頂點顏色不同。如果能成功著色，則是二分圖；如果發現相鄰頂點必須著相同顏色（矛盾），則不是二分圖。時間複雜度 \( O(V + E) \)。

**問題 3**：DAG 有什麼重要性質？為什麼在實務中常用？

**答案**：DAG 的重要性質：
1. 一定存在至少一個入度為 0 的頂點（起始節點）
2. 一定存在至少一個出度為 0 的頂點（結束節點）
3. 一定存在拓撲排序（頂點的線性順序，使得所有邊都從前指向後）

DAG 在實務中常用於表示依賴關係（如任務排程、編譯依賴），因為有向邊表示依賴方向，無環保證不會出現循環依賴的死鎖情況。

#### 參考來源

[1] GeeksforGeeks, "Introduction to Graph Data Structure," 2025. [Online]. Available: https://www.geeksforgeeks.org/dsa/introduction-to-graphs-data-structure-and-algorithm-tutorials/ [Accessed: 29-Dec-2025].

[2] S. Simplilearn, "Graph in Data Structure: Types & Explanation," 2025. [Online]. Available: https://www.simplilearn.com/tutorials/data-structure-tutorial/graphs-in-data-structure [Accessed: 29-Dec-2025].

[3] D. West, "Introduction to Graph Theory," 2nd ed., Prentice Hall, 2001.

---

### 1.3 基本性質與定理

#### 摘要

Graph 理論中有許多基本但重要的性質和定理，這些理論基礎不僅幫助我們理解圖的結構，還為演算法設計提供理論保證。本節將介紹握手定理、連通性、路徑與環的性質，以及樹的等價定義等核心概念。這些性質看似簡單，但在解決實際問題時經常被用作分析工具或證明依據。深入理解這些基礎定理能夠提升我們對圖演算法正確性和複雜度的洞察能力。

#### 重點整理

- **握手定理**：無向圖中所有頂點的度數之和等於邊數的兩倍
- **度數序列**：圖中所有頂點度數的序列，可用於判斷某個序列是否能構成一個圖
- **連通性**：無向圖中任意兩頂點是否存在路徑；有向圖分為強連通和弱連通
- **連通分量**：極大連通子圖，即不能再加入其他頂點而保持連通的子圖
- **割點與橋**：刪除後會增加連通分量數量的頂點或邊
- **路徑長度**：路徑中邊的數量（非加權圖）或權重之和（加權圖）
- **最短路徑**：兩頂點之間所有路徑中長度最小的路徑
- **環的基本性質**：簡單環、自環、奇環、偶環的定義與判定
- **樹的等價定義**：連通無環圖、極小連通圖、極大無環圖、任意兩頂點間恰有一條路徑

#### 重要定義與公式

**定理 1.2 (握手定理)**：
對於無向圖 \( G = (V, E) \)，有：
\[ \sum_{v \in V} deg(v) = 2|E| \]

**推論 1.1**：任何無向圖中，度數為奇數的頂點個數必為偶數。

**證明**：設度數為奇數的頂點集合為 \( V_{odd} \)，度數為偶數的頂點集合為 \( V_{even} \)，則：
\[ \sum_{v \in V_{odd}} deg(v) + \sum_{v \in V_{even}} deg(v) = 2|E| \]
由於右邊是偶數，\( \sum_{v \in V_{even}} deg(v) \) 是偶數（偶數之和），所以 \( \sum_{v \in V_{odd}} deg(v) \) 必為偶數。而奇數之和為偶數，當且僅當奇數的個數為偶數。

**定義 1.6 (連通圖)**：無向圖 \( G = (V, E) \) 是連通的，當且僅當對於任意 \( u, v \in V \)，存在從 \( u \) 到 \( v \) 的路徑。

**定義 1.7 (強連通圖)**：有向圖 \( G = (V, E) \) 是強連通的，當且僅當對於任意 \( u, v \in V \)，存在從 \( u \) 到 \( v \) 的有向路徑，且存在從 \( v \) 到 \( u \) 的有向路徑。

**定理 1.3 (樹的等價定義)**：對於有 \( n \) 個頂點的無向圖 \( G \)，以下陳述等價：
1. \( G \) 是樹（連通且無環）
2. \( G \) 是連通的且恰有 \( n-1 \) 條邊
3. \( G \) 是無環的且恰有 \( n-1 \) 條邊
4. \( G \) 中任意兩個頂點之間恰有一條簡單路徑
5. \( G \) 是極小連通圖（刪除任何一條邊都會變得不連通）
6. \( G \) 是極大無環圖（添加任何一條邊都會產生環）

#### <加深的內容>

**握手定理的深層含義**

握手定理看似簡單，但它揭示了圖的一個基本對稱性。每條邊連接兩個頂點，因此在"計數"時被計算兩次。這種雙重計數（double counting）的技巧在組合數學和圖論中非常常見。例如，我們可以用類似的思想證明：在任何聚會中，握手次數為奇數的人數必為偶數——這就是握手定理的推論 1.1 的現實類比。

這個定理還有實用價值。在檢查圖的輸入數據是否有效時，我們可以先計算所有度數之和，如果不是偶數或者不等於兩倍的邊數，就知道數據有誤。在設計圖演算法時，握手定理也常用於複雜度分析，例如證明某個演算法在所有頂點上的操作總次數與邊數成正比。

**連通性的層次**

連通性不是二元的（是或否），而是有層次的。對於無向圖，我們可以定義 k-連通性：至少需要刪除 k 個頂點才能使圖不連通或變成單個頂點。2-連通圖（沒有割點）和 3-連通圖在網路設計中很重要，因為它們提供了容錯性——即使某些節點失效，網路仍然連通。

對於有向圖，連通性更複雜。除了強連通，還有單向連通（任意兩頂點之間至少有一個方向的路徑）和弱連通（忽略方向後連通）。強連通分量（Strongly Connected Components, SCC）是圖分析的重要工具，可以將複雜的有向圖簡化為一個 DAG（將每個 SCC 縮成一個點）。

**樹的多面性**

樹的等價定義展示了樹的多個面向，每個定義都強調了樹的不同特性。作為演算法設計者，我們需要根據問題選擇最合適的視角：
- 當需要遍歷時，將樹視為連通無環圖，使用 DFS 或 BFS
- 當需要計數時，使用"n 個頂點 n-1 條邊"的性質
- 當需要添加邊時，考慮"極大無環"性質：添加任何邊都會產生環
- 當需要刪除邊時，考慮"極小連通"性質：刪除任何邊都會變得不連通

#### <額外加深加廣的內容>

**1. 相關變體或延伸主題（推論/擴充）**

- **凱萊公式 (Cayley's Formula)**：n 個標號頂點的生成樹個數為 \( n^{n-2} \)。這個公式在組合數學和概率論中有廣泛應用。

- **圖的直徑與半徑**：直徑是圖中任意兩頂點之間最短路徑的最大值；半徑是到其他所有頂點的最大距離的最小值。這些度量在分析網路效率時很有用。

- **圖的譜理論 (Spectral Graph Theory)**：研究圖的鄰接矩陣或拉普拉斯矩陣的特徵值和特徵向量。譜理論揭示了圖的代數性質與組合性質之間的深刻聯繫，在圖劃分、圖著色等問題中有應用。

- **隨機圖理論 (Random Graph Theory)**：研究隨機生成的圖的性質。Erdős-Rényi 隨機圖模型是基礎模型，研究當邊以某個概率獨立出現時圖的性質（如連通性、環的出現等）的相變現象。

**2. 實務應用**

- **網路可靠性分析**：通過分析割點和橋，可以識別網路中的關鍵節點和鏈路。在電力網、通訊網設計中，需要避免單點故障，因此要確保沒有割點或至少提供冗餘路徑。

- **社交網路分析**：圖的直徑反映了"六度分隔理論"——任意兩個人之間通過不超過 6 個中間人就能建立聯繫。研究社交網路的度分布、聚類系數等性質有助於理解信息傳播、社群形成等現象。

- **編譯器優化**：在控制流圖中，支配樹（Dominator Tree）的概念用於優化。一個基本塊 A 支配基本塊 B，表示從入口到 B 的所有路徑都必須經過 A。支配關係形成一棵樹，用於代碼優化和寄存器分配。

**3. 潛在研究問題或開放題（作者補充）**

- **圖重構猜想 (Graph Reconstruction Conjecture)**：給定一個圖的所有 n-1 個頂點的導出子圖（稱為"卡片"），能否唯一確定原圖？這個 1941 年提出的猜想至今未被證明或證偽。

- **圖的最小秩問題**：對於給定的圖，所有與該圖結構匹配的實對稱矩陣的最小秩是多少？這個問題與控制理論、量子化學中的零強制數（zero forcing number）有關。

- **動態圖的連通性維護**：在邊不斷被添加或刪除的情況下，如何高效地維護圖的連通分量？動態連通性問題已有多項式時間的解決方案（如 Holm-de Lichtenberg-Thorup 演算法），但在實際大規模應用中仍有優化空間。

#### 理解檢核

**問題 1**：證明：在任何圖中，度數為奇數的頂點個數必為偶數。

**答案**：設 \( V_{odd} \) 為度數為奇數的頂點集合，\( V_{even} \) 為度數為偶數的頂點集合。根據握手定理：
\[ \sum_{v \in V_{odd}} deg(v) + \sum_{v \in V_{even}} deg(v) = 2|E| \]
右邊 \( 2|E| \) 是偶數。左邊第二項 \( \sum_{v \in V_{even}} deg(v) \) 是偶數之和，必為偶數。因此第一項 \( \sum_{v \in V_{odd}} deg(v) \) 也必須是偶數。奇數之和為偶數，當且僅當奇數的個數為偶數。證畢。

**問題 2**：為什麼樹總是有 n-1 條邊（其中 n 是頂點數）？

**答案**：可以用歸納法證明。
- 基礎情況：n=1 時，一個頂點的樹有 0 = 1-1 條邊，成立。
- 歸納假設：假設對於所有 k < n 的樹，結論成立。
- 歸納步驟：對於 n 個頂點的樹 T，選擇任意一片葉子節點（度為 1 的頂點，樹中一定存在）及其連接的邊 e。刪除這個葉子和邊 e，得到 n-1 個頂點的樹 T'。根據歸納假設，T' 有 (n-1)-1 = n-2 條邊。因此 T 有 n-2+1 = n-1 條邊。證畢。

**問題 3**：什麼是割點？如何找到圖中的所有割點？

**答案**：割點（Articulation Point）是指刪除該頂點及其相連的邊後，圖的連通分量數量增加的頂點。換句話說，割點是圖的"關鍵節點"，刪除它會使圖不連通或連通性降低。

找到所有割點的經典演算法是 Tarjan's Algorithm，基於 DFS：
1. 對圖進行 DFS，記錄每個頂點的訪問時間（discovery time）
2. 計算每個頂點的 low 值：該頂點及其子樹能回溯到的最早祖先的訪問時間
3. 對於非根節點 v，如果存在一個子節點 u 使得 low[u] >= discovery[v]，則 v 是割點（因為從 u 的子樹無法繞過 v 到達 v 的祖先）
4. 對於根節點，如果它有兩個或更多子樹，則它是割點

時間複雜度：\( O(V + E) \)

#### 參考來源

[1] T. H. Cormen, C. E. Leiserson, R. L. Rivest, and C. Stein, "Introduction to Algorithms," 3rd ed., MIT Press, 2009, Chapter 22: Elementary Graph Algorithms.

[2] R. Diestel, "Graph Theory," 5th ed., Springer, 2017.

[3] D. West, "Introduction to Graph Theory," 2nd ed., Prentice Hall, 2001.

---

## 第二章：Graph 的表示法

### 2.1 鄰接矩陣 (Adjacency Matrix)

#### 摘要

鄰接矩陣是一種用二維陣列表示圖的方法，其中矩陣的行和列分別代表頂點，矩陣元素表示兩頂點之間是否有邊以及邊的權重。對於有 n 個頂點的圖，鄰接矩陣是一個 n×n 的二維陣列。這種表示法直觀簡單，適合稠密圖，並且可以在 O(1) 時間內查詢任意兩頂點是否相連。然而，對於稀疏圖，鄰接矩陣會浪費大量空間存儲不存在的邊。理解鄰接矩陣的特性對於選擇合適的圖表示法至關重要。

#### 重點整理

- **定義**：對於圖 \( G = (V, E) \)，鄰接矩陣 \( A \) 是一個 \( |V| \times |V| \) 的矩陣
- **無向圖**：\( A[i][j] = 1 \) 如果 \( (v_i, v_j) \in E \)，否則為 0；矩陣是對稱的（\( A[i][j] = A[j][i] \)）
- **有向圖**：\( A[i][j] = 1 \) 如果存在從 \( v_i \) 到 \( v_j \) 的邊；矩陣不一定對稱
- **加權圖**：\( A[i][j] \) 存儲邊的權重，如果沒有邊則存儲特殊值（如 0, ∞, 或 -1）
- **空間複雜度**：\( O(V^2) \)，不論邊數多少
- **查詢邊是否存在**：\( O(1) \) 時間
- **列出某頂點的所有鄰居**：\( O(V) \) 時間（需要掃描整行）
- **添加/刪除邊**：\( O(1) \) 時間
- **適用場景**：稠密圖、需要頻繁查詢任意兩頂點是否相連、矩陣運算

#### 重要定義與程式碼

**定義 2.1 (鄰接矩陣)**：圖 \( G = (V, E) \) 的鄰接矩陣 \( A = (a_{ij}) \) 定義為：
\[ a_{ij} = \begin{cases} 
w_{ij} & \text{如果 } (v_i, v_j) \in E \text{ 且權重為 } w_{ij} \\
0 \text{ 或 } \infty & \text{如果 } (v_i, v_j) \notin E 
\end{cases} \]

**C++ 實作（無向無權圖）**：

```cpp
#include <iostream>
#include <vector>
using namespace std;

class GraphAdjMatrix {
private:
    int numVertices;  // 頂點數量
    vector<vector<int>> adjMatrix;  // 鄰接矩陣
    
public:
    // 建構子：初始化 n 個頂點的圖
    GraphAdjMatrix(int vertices) {
        numVertices = vertices;
        // 初始化 n×n 矩陣，所有元素為 0（表示沒有邊）
        adjMatrix.resize(numVertices, vector<int>(numVertices, 0));
    }
    
    // 添加邊：無向圖，所以兩個方向都要設定
    void addEdge(int src, int dest) {
        // 檢查頂點索引是否有效
        if (src >= 0 && src < numVertices && dest >= 0 && dest < numVertices) {
            adjMatrix[src][dest] = 1;
            adjMatrix[dest][src] = 1;  // 無向圖：對稱
        }
    }
    
    // 刪除邊
    void removeEdge(int src, int dest) {
        if (src >= 0 && src < numVertices && dest >= 0 && dest < numVertices) {
            adjMatrix[src][dest] = 0;
            adjMatrix[dest][src] = 0;
        }
    }
    
    // 查詢兩頂點是否相連：O(1) 時間
    bool hasEdge(int src, int dest) {
        if (src >= 0 && src < numVertices && dest >= 0 && dest < numVertices) {
            return adjMatrix[src][dest] == 1;
        }
        return false;
    }
    
    // 列出某頂點的所有鄰居：O(V) 時間
    vector<int> getNeighbors(int vertex) {
        vector<int> neighbors;
        if (vertex >= 0 && vertex < numVertices) {
            for (int i = 0; i < numVertices; i++) {
                if (adjMatrix[vertex][i] == 1) {
                    neighbors.push_back(i);
                }
            }
        }
        return neighbors;
    }
    
    // 印出鄰接矩陣
    void printMatrix() {
        cout << "鄰接矩陣表示：\n";
        for (int i = 0; i < numVertices; i++) {
            for (int j = 0; j < numVertices; j++) {
                cout << adjMatrix[i][j] << " ";
            }
            cout << "\n";
        }
    }
    
    // 取得頂點數量
    int getNumVertices() const {
        return numVertices;
    }
};

// 測試程式
int main() {
    // 建立 5 個頂點的無向圖
    GraphAdjMatrix graph(5);
    
    // 添加邊
    graph.addEdge(0, 1);
    graph.addEdge(0, 4);
    graph.addEdge(1, 2);
    graph.addEdge(1, 3);
    graph.addEdge(1, 4);
    graph.addEdge(2, 3);
    graph.addEdge(3, 4);
    
    // 印出矩陣
    graph.printMatrix();
    
    // 測試查詢
    cout << "\n頂點 0 和 1 是否相連？" << (graph.hasEdge(0, 1) ? "是" : "否") << "\n";
    cout << "頂點 0 和 3 是否相連？" << (graph.hasEdge(0, 3) ? "是" : "否") << "\n";
    
    // 列出頂點 1 的鄰居
    cout << "\n頂點 1 的鄰居：";
    vector<int> neighbors = graph.getNeighbors(1);
    for (int n : neighbors) {
        cout << n << " ";
    }
    cout << "\n";
    
    return 0;
}
```

**設計想法**：
1. 使用 `vector<vector<int>>` 實作二維陣列，比 C 風格陣列更安全且支援動態調整
2. 所有操作都加入邊界檢查，避免陣列越界
3. 無向圖的 `addEdge` 需要設定矩陣的兩個對稱位置
4. `getNeighbors` 需要掃描整行，這是鄰接矩陣的主要缺點之一

**加權圖的鄰接矩陣實作**：

```cpp
class WeightedGraphAdjMatrix {
private:
    int numVertices;
    vector<vector<int>> adjMatrix;  // 存儲權重
    const int INF = 1e9;  // 表示無邊（無窮大）
    
public:
    WeightedGraphAdjMatrix(int vertices) {
        numVertices = vertices;
        // 初始化為 INF，表示所有頂點之間都沒有邊
        adjMatrix.resize(numVertices, vector<int>(numVertices, INF));
        // 自己到自己的距離為 0
        for (int i = 0; i < numVertices; i++) {
            adjMatrix[i][i] = 0;
        }
    }
    
    // 添加加權邊
    void addEdge(int src, int dest, int weight) {
        if (src >= 0 && src < numVertices && dest >= 0 && dest < numVertices) {
            adjMatrix[src][dest] = weight;
            adjMatrix[dest][src] = weight;  // 無向圖
        }
    }
    
    // 取得邊的權重
    int getWeight(int src, int dest) {
        if (src >= 0 && src < numVertices && dest >= 0 && dest < numVertices) {
            return adjMatrix[src][dest];
        }
        return INF;
    }
    
    // 印出矩陣（將 INF 顯示為 ∞）
    void printMatrix() {
        cout << "加權鄰接矩陣：\n";
        for (int i = 0; i < numVertices; i++) {
            for (int j = 0; j < numVertices; j++) {
                if (adjMatrix[i][j] == INF)
                    cout << "∞ ";
                else
                    cout << adjMatrix[i][j] << " ";
            }
            cout << "\n";
        }
    }
};
```

#### <加深的內容>

**鄰接矩陣的數學性質**

鄰接矩陣不僅是一種資料結構，它還是一個數學物件，具有豐富的代數性質。最重要的性質之一是：鄰接矩陣的 k 次方 \( A^k \) 的元素 \( (A^k)_{ij} \) 表示從頂點 i 到頂點 j 長度恰好為 k 的路徑數量。這個性質在計算圖的可達性、路徑計數等問題中非常有用。

例如，在社交網路中，\( A^2 \) 的元素 \( (A^2)_{ij} \) 表示用戶 i 和用戶 j 有多少共同好友（2 步路徑）。這可以用於好友推薦：如果兩個用戶不是直接好友但有很多共同好友，系統可以推薦他們互加好友。

對於無向圖，鄰接矩陣是對稱矩陣（\( A^T = A \)），這意味著我們可以利用對稱性節省一半的存儲空間（只存儲上三角或下三角）。對於無環圖，鄰接矩陣經過適當的頂點排序後可以成為上三角矩陣或下三角矩陣，這在某些演算法中很有用。

**空間與時間的權衡**

鄰接矩陣的主要缺點是空間複雜度固定為 \( O(V^2) \)，即使是稀疏圖（邊數遠小於 \( V^2 \)）也需要相同的空間。例如，一個有 10,000 個頂點但只有 20,000 條邊的稀疏圖（社交網路中很常見），鄰接矩陣需要 100,000,000 個整數（約 400 MB 內存），但實際只有 0.02% 的矩陣元素是非零的。

然而，鄰接矩陣在某些操作上有優勢。查詢任意兩頂點是否相連只需 \( O(1) \) 時間，這在需要頻繁查詢邊的存在性的演算法中很重要（如 Floyd-Warshall 演算法）。此外，鄰接矩陣的記憶體訪問模式對 CPU 快取友好（cache-friendly），因為可以順序訪問陣列元素。

在實務中，我們需要根據圖的密度和操作類型來選擇表示法。經驗法則：如果 \( |E| \approx |V|^2 / 2 \)（即邊數接近頂點數的平方的一半），使用鄰接矩陣；否則使用鄰接串列。

#### <額外加深加廣的內容>

**1. 相關變體或延伸主題（推論/擴充）**

- **壓縮稀疏矩陣 (Compressed Sparse Row/Column)**：對於稀疏圖，可以使用 CSR 或 CSC 格式只存儲非零元素，大幅減少空間消耗。這在大規模圖計算（如 PageRank）中很常用。

- **位元矩陣 (Bit Matrix)**：對於無權圖，可以用位元（bit）而不是整數（int）來表示邊的存在，將空間需求降低 32 倍或 64 倍。C++ 的 `std::bitset` 或 `std::vector<bool>` 可以實現。

- **塊稀疏矩陣 (Block Sparse Matrix)**：將大矩陣分割成小塊，只存儲非零塊。這在分散式圖處理系統中用於將圖劃分到多台機器。

**2. 實務應用**

- **圖像處理中的像素鄰接**：將圖像的像素視為頂點，相鄰像素之間有邊。使用鄰接矩陣可以快速計算像素之間的關係，用於圖割（graph cut）演算法進行圖像分割。

- **化學分子結構**：分子可以用圖表示（原子是頂點，化學鍵是邊）。鄰接矩陣用於計算分子的性質，如能量、穩定性等。矩陣的特徵值與分子的電子結構有關。

- **通訊網路的鄰接矩陣表示**：在小型網路（如感測器網路的某個子區域）中，鄰接矩陣可以快速判斷兩個節點是否能直接通訊，用於路由表的計算。

**3. 潛在研究問題或開放題（作者補充）**

- **動態圖的增量矩陣更新**：當圖的邊不斷變化時，如何高效更新鄰接矩陣及其衍生的矩陣（如距離矩陣、可達矩陣）？研究增量式的矩陣分解和更新演算法。

- **鄰接矩陣的並行計算**：在 GPU 或多核 CPU 上，如何高效地對鄰接矩陣進行並行操作（如矩陣乘法、矩陣求冪）以加速圖演算法？

- **近似鄰接矩陣**：對於超大規模圖，能否用低秩矩陣近似鄰接矩陣，保留主要結構信息的同時大幅減少存儲和計算？這與矩陣補全、推薦系統有關。

#### 理解檢核

**問題 1**：為什麼無向圖的鄰接矩陣一定是對稱的？

**答案**：因為無向圖中的邊沒有方向，邊 (u, v) 和邊 (v, u) 是同一條邊。因此在鄰接矩陣中，如果 A[i][j] = 1（表示頂點 i 和 j 之間有邊），那麼 A[j][i] 也必須等於 1。這使得矩陣關於主對角線對稱，即 A[i][j] = A[j][i] 對所有 i, j 成立。

**問題 2**：如何利用鄰接矩陣快速計算一個圖中長度為 2 的路徑數量？

**答案**：計算鄰接矩陣的平方 \( A^2 = A \times A \)。結果矩陣 \( A^2 \) 的元素 \( (A^2)_{ij} \) 表示從頂點 i 到頂點 j 長度恰好為 2 的路徑數量。這是因為矩陣乘法的定義：\( (A^2)_{ij} = \sum_{k} A_{ik} \times A_{kj} \)，其中 \( A_{ik} \times A_{kj} = 1 \) 當且僅當存在邊 (i, k) 和 (k, j)，即存在通過 k 的長度為 2 的路徑。時間複雜度：標準矩陣乘法為 \( O(V^3) \)。

**問題 3**：在什麼情況下鄰接矩陣比鄰接串列更好？

**答案**：
1. **稠密圖**：當邊數接近 \( V^2 \) 時，鄰接矩陣的空間效率與鄰接串列相當，但操作更簡單
2. **頻繁查詢邊的存在性**：鄰接矩陣可以 \( O(1) \) 時間查詢，而鄰接串列需要 \( O(degree) \) 時間
3. **需要矩陣運算**：某些演算法（如 Floyd-Warshall、譜聚類）需要對鄰接矩陣進行矩陣運算
4. **頂點數量較小**：如果圖只有幾百個頂點，\( O(V^2) \) 的空間完全可以接受，而鄰接矩陣的實作更簡單

#### 參考來源

[1] GeeksforGeeks, "Comparison between Adjacency List and Adjacency Matrix representation of Graph," 2025. [Online]. Available: https://www.geeksforgeeks.org/dsa/comparison-between-adjacency-list-and-adjacency-matrix-representation-of-graph/ [Accessed: 29-Dec-2025].

[2] T. H. Cormen, C. E. Leiserson, R. L. Rivest, and C. Stein, "Introduction to Algorithms," 3rd ed., MIT Press, 2009, Section 22.1: Representations of graphs.

[3] R. Sedgewick and K. Wayne, "Algorithms," 4th ed., Addison-Wesley, 2011, Section 4.1: Undirected Graphs.

---

### 2.2 鄰接串列 (Adjacency List)

#### 摘要

鄰接串列是一種用陣列加鏈結串列（或動態陣列）來表示圖的方法，其中陣列的每個元素對應一個頂點，存儲該頂點的所有鄰居。這種表示法空間效率高，特別適合稀疏圖，因為它只存儲實際存在的邊。鄰接串列的空間複雜度為 \( O(V + E) \)，列出某頂點的所有鄰居只需 \( O(degree) \) 時間，這使得圖遍歷演算法（如 DFS、BFS）非常高效。然而，查詢任意兩頂點是否相連需要 \( O(degree) \) 時間，不如鄰接矩陣快。在實務中，大多數圖是稀疏的，因此鄰接串列是最常用的圖表示法。

#### 重點整理

- **定義**：用一個陣列存儲所有頂點，每個頂點關聯一個串列（或向量）存儲其鄰居
- **結構**：`adjList[i]` 存儲頂點 i 的所有鄰居頂點（有向圖）或相鄰頂點（無向圖）
- **空間複雜度**：
  - 無向圖：\( O(V + 2E) = O(V + E) \)（每條邊存儲兩次）
  - 有向圖：\( O(V + E) \)（每條邊存儲一次）
- **查詢邊是否存在**：\( O(degree(v)) \) 時間（需要搜索 v 的鄰居串列）
- **列出某頂點的所有鄰居**：\( O(degree(v)) \) 時間
- **添加邊**：\( O(1) \) 時間（在串列末尾添加）
- **刪除邊**：\( O(degree(v)) \) 時間（需要搜索並刪除）
- **適用場景**：稀疏圖、圖遍歷（DFS/BFS）、大多數圖演算法

#### 重要定義與程式碼

**定義 2.2 (鄰接串列)**：圖 \( G = (V, E) \) 的鄰接串列是一個陣列 \( Adj \)，其中 \( Adj[v] \) 包含所有與 v 相鄰的頂點（對於無向圖）或所有 v 指向的頂點（對於有向圖）。

**C++ 實作（無向無權圖）**：

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class GraphAdjList {
private:
    int numVertices;  // 頂點數量
    vector<vector<int>> adjList;  // 鄰接串列：adjList[i] 存儲頂點 i 的鄰居
    
public:
    // 建構子：初始化 n 個頂點的圖
    GraphAdjList(int vertices) {
        numVertices = vertices;
        adjList.resize(numVertices);  // 創建 n 個空的向量
    }
    
    // 添加邊：無向圖，所以兩個方向都要添加
    // 時間複雜度：O(1)（假設 vector 的 push_back 是攤銷 O(1)）
    void addEdge(int src, int dest) {
        if (src >= 0 && src < numVertices && dest >= 0 && dest < numVertices) {
            adjList[src].push_back(dest);
            adjList[dest].push_back(src);  // 無向圖：雙向添加
        }
    }
    
    // 刪除邊：需要在兩個鄰接串列中刪除
    // 時間複雜度：O(degree)
    void removeEdge(int src, int dest) {
        if (src >= 0 && src < numVertices && dest >= 0 && dest < numVertices) {
            // 從 src 的鄰接串列中刪除 dest
            adjList[src].erase(
                remove(adjList[src].begin(), adjList[src].end(), dest),
                adjList[src].end()
            );
            // 從 dest 的鄰接串列中刪除 src
            adjList[dest].erase(
                remove(adjList[dest].begin(), adjList[dest].end(), src),
                adjList[dest].end()
            );
        }
    }
    
    // 查詢兩頂點是否相連：需要搜索鄰接串列
    // 時間複雜度：O(degree(src))
    bool hasEdge(int src, int dest) {
        if (src >= 0 && src < numVertices && dest >= 0 && dest < numVertices) {
            return find(adjList[src].begin(), adjList[src].end(), dest) 
                   != adjList[src].end();
        }
        return false;
    }
    
    // 取得某頂點的所有鄰居
    // 時間複雜度：O(1)（返回引用）或 O(degree)（複製）
    const vector<int>& getNeighbors(int vertex) {
        static vector<int> empty;  // 當頂點無效時返回空向量
        if (vertex >= 0 && vertex < numVertices) {
            return adjList[vertex];
        }
        return empty;
    }
    
    // 印出鄰接串列
    void printList() {
        cout << "鄰接串列表示：\n";
        for (int i = 0; i < numVertices; i++) {
            cout << "頂點 " << i << ": ";
            for (int neighbor : adjList[i]) {
                cout << neighbor << " ";
            }
            cout << "\n";
        }
    }
    
    // 取得頂點數量
    int getNumVertices() const {
        return numVertices;
    }
    
    // 取得邊數量（無向圖）
    int getNumEdges() const {
        int edges = 0;
        for (int i = 0; i < numVertices; i++) {
            edges += adjList[i].size();
        }
        return edges / 2;  // 無向圖：每條邊被計算兩次
    }
};

// 測試程式
int main() {
    // 建立 5 個頂點的無向圖
    GraphAdjList graph(5);
    
    // 添加邊（與鄰接矩陣範例相同的圖）
    graph.addEdge(0, 1);
    graph.addEdge(0, 4);
    graph.addEdge(1, 2);
    graph.addEdge(1, 3);
    graph.addEdge(1, 4);
    graph.addEdge(2, 3);
    graph.addEdge(3, 4);
    
    // 印出鄰接串列
    graph.printList();
    
    // 測試查詢
    cout << "\n頂點 0 和 1 是否相連？" << (graph.hasEdge(0, 1) ? "是" : "否") << "\n";
    cout << "頂點 0 和 3 是否相連？" << (graph.hasEdge(0, 3) ? "是" : "否") << "\n";
    
    // 列出頂點 1 的鄰居
    cout << "\n頂點 1 的鄰居：";
    const vector<int>& neighbors = graph.getNeighbors(1);
    for (int n : neighbors) {
        cout << n << " ";
    }
    cout << "\n";
    
    cout << "\n圖的統計：";
    cout << "\n頂點數：" << graph.getNumVertices();
    cout << "\n邊數：" << graph.getNumEdges() << "\n";
    
    return 0;
}
```

**設計想法**：
1. 使用 `vector<vector<int>>` 實作，外層 vector 的每個元素是一個內層 vector，存儲一個頂點的鄰居
2. `addEdge` 只需在串列末尾添加，攤銷時間 \( O(1) \)
3. `removeEdge` 使用 STL 的 `erase-remove` idiom，時間 \( O(degree) \)
4. `hasEdge` 使用線性搜索，對於度數小的頂點很快；如果需要更快的查詢，可以將內層 vector 改為 `set` 或 `unordered_set`

**加權有向圖的鄰接串列實作**：

```cpp
#include <iostream>
#include <vector>
using namespace std;

// 邊的結構：目標頂點 + 權重
struct Edge {
    int dest;     // 目標頂點
    int weight;   // 權重
    
    Edge(int d, int w) : dest(d), weight(w) {}
};

class WeightedDirectedGraph {
private:
    int numVertices;
    vector<vector<Edge>> adjList;  // 每個頂點存儲 Edge 物件的列表
    
public:
    WeightedDirectedGraph(int vertices) {
        numVertices = vertices;
        adjList.resize(numVertices);
    }
    
    // 添加有向加權邊：只在 src 的鄰接串列中添加
    void addEdge(int src, int dest, int weight) {
        if (src >= 0 && src < numVertices && dest >= 0 && dest < numVertices) {
            adjList[src].push_back(Edge(dest, weight));
        }
    }
    
    // 取得某頂點的所有出邊
    const vector<Edge>& getOutgoingEdges(int vertex) {
        static vector<Edge> empty;
        if (vertex >= 0 && vertex < numVertices) {
            return adjList[vertex];
        }
        return empty;
    }
    
    // 印出鄰接串列
    void printList() {
        cout << "加權有向圖的鄰接串列：\n";
        for (int i = 0; i < numVertices; i++) {
            cout << "頂點 " << i << ": ";
            for (const Edge& e : adjList[i]) {
                cout << "(" << e.dest << ", w=" << e.weight << ") ";
            }
            cout << "\n";
        }
    }
};

int main() {
    WeightedDirectedGraph graph(4);
    
    graph.addEdge(0, 1, 5);
    graph.addEdge(0, 2, 3);
    graph.addEdge(1, 2, 2);
    graph.addEdge(1, 3, 6);
    graph.addEdge(2, 3, 7);
    
    graph.printList();
    
    return 0;
}
```

#### <加深的內容>

**鄰接串列的彈性與擴展性**

鄰接串列的一大優勢是彈性。與鄰接矩陣固定的 \( O(V^2) \) 空間不同，鄰接串列的空間隨著實際邊數線性增長。這意味著即使處理百萬個頂點的圖，只要邊數不多（如社交網路、網頁圖），鄰接串列也能高效存儲。

在實務中，鄰接串列還可以輕易擴展以存儲更多信息。例如：
- 每條邊可以附帶多個屬性（權重、類型、時間戳等）
- 可以為每個頂點存儲額外的元數據（標籤、屬性字典等）
- 可以使用不同的容器實作內層串列：
  - `vector<int>`：最常用，空間效率高，適合順序訪問
  - `set<int>` 或 `unordered_set<int>`：提供 \( O(\log degree) \) 或 \( O(1) \) 的邊查詢，適合需要頻繁檢查邊是否存在的場景
  - `list<int>`：適合頻繁插入/刪除的場景

**圖遍歷的優勢**

鄰接串列為什麼特別適合圖遍歷（DFS/BFS）？因為遍歷的核心操作是"訪問一個頂點的所有鄰居"，這正是鄰接串列的強項。在 DFS 或 BFS 中，我們需要對每個頂點執行這個操作一次，總時間為：
\[ \sum_{v \in V} O(degree(v)) = O(\sum_{v} degree(v)) = O(E) \]

這比鄰接矩陣的 \( O(V^2) \)（需要掃描每個頂點的整行）快得多，特別是對於稀疏圖。事實上，DFS 和 BFS 的標準時間複雜度 \( O(V + E) \) 就是基於鄰接串列表示的。

#### <額外加深加廣的內容>

**1. 相關變體或延伸主題（推論/擴充）**

- **前向星（Forward Star）表示法**：將所有邊存儲在一個連續的陣列中，每個頂點記錄其邊在陣列中的起始位置和結束位置。這種表示法對 CPU 快取非常友好，常用於競賽程式設計。

- **雙向鄰接串列**：對於有向圖，除了存儲出邊（outgoing edges），還可以存儲入邊（incoming edges），這樣可以快速找到指向某頂點的所有邊。這在某些演算法（如計算入度、反向圖遍歷）中很有用。

- **分層鄰接串列**：在大規模圖處理中，將圖劃分為多層，每層使用不同的表示方法或壓縮策略。例如，高度數頂點（hub）使用不同的存儲策略，低度數頂點使用標準鄰接串列。

**2. 實務應用**

- **社交網路圖**：Facebook、Twitter 等社交網路都是稀疏圖（一個用戶通常只有幾百或幾千個好友/關注者，遠小於總用戶數）。鄰接串列是存儲這類圖的標準選擇。

- **網頁圖與網路爬蟲**：網際網路的超連結結構是一個超大規模的有向圖（數十億網頁，數千億超連結）。使用壓縮的鄰接串列（如 WebGraph 框架）可以將整個網頁圖壓縮到數十 GB，使得在單機上分析成為可能。

- **路由協議**：計算機網路中的路由協議（如 OSPF）維護網路拓撲的鄰接串列，用於計算最短路徑。鄰接串列使得動態更新（添加/刪除鏈路）變得高效。

**3. 潛在研究問題或開放題（作者補充）**

- **外存圖演算法（External Memory Graph Algorithms）**：當圖太大無法放入內存時，如何設計高效的外存演算法？鄰接串列的順序訪問特性使得它比鄰接矩陣更適合外存算法，但仍需要精心設計數據佈局和訪問模式。

- **圖壓縮技術**：如何進一步壓縮鄰接串列以節省空間？技術包括：差分編碼（相鄰頂點 ID 的差值）、變長編碼（如 VByte、Gamma 編碼）、圖重排序（將相似頂點放在一起以提高壓縮率）。

- **並行圖演算法的負載均衡**：在多核或分散式環境中，不同頂點的度數差異很大（冪律分布），導致負載不均衡。如何設計負載均衡策略以提高並行圖演算法的效率？

#### 理解檢核

**問題 1**：為什麼說鄰接串列的空間複雜度是 \( O(V + E) \)？請詳細解釋。

**答案**：
- 外層陣列有 \( V \) 個元素（每個頂點一個），空間 \( O(V) \)
- 內層串列總共存儲所有邊的端點：
  - 對於有向圖：每條邊存儲一次（在起點的串列中），總共 \( E \) 個元素，空間 \( O(E) \)
  - 對於無向圖：每條邊存儲兩次（在兩個端點的串列中），總共 \( 2E \) 個元素，空間 \( O(2E) = O(E) \)
- 因此總空間為 \( O(V) + O(E) = O(V + E) \)

對於稀疏圖（\( E = O(V) \)），這是線性空間；即使對於稠密圖（\( E = O(V^2) \)），也比鄰接矩陣的固定 \( O(V^2) \) 空間更靈活。

**問題 2**：如果需要頻繁檢查任意兩頂點是否相連，應該如何改進鄰接串列？

**答案**：有幾種改進方法：
1. **使用 unordered_set 而不是 vector**：將 `vector<int>` 改為 `unordered_set<int>`，這樣查詢邊的存在性從 \( O(degree) \) 降低到平均 \( O(1) \)。代價是增加了空間消耗（hash table 的開銷）和插入時間。

2. **混合表示法**：對於度數大的頂點使用 set，對於度數小的頂點使用 vector。經驗法則：如果度數超過某個閾值（如 64），使用 set。

3. **鄰接矩陣 + 鄰接串列**：同時維護兩種表示法。鄰接矩陣用於快速查詢，鄰接串列用於遍歷。代價是雙倍空間消耗，適合中等規模的圖。

4. **布隆過濾器（Bloom Filter）**：為每個頂點維護一個布隆過濾器，快速（但有誤判率）檢查某條邊是否可能存在，然後再在鄰接串列中確認。

**問題 3**：在什麼情況下鄰接串列比鄰接矩陣更好？

**答案**：
1. **稀疏圖**：當 \( E \ll V^2 \) 時，鄰接串列節省大量空間
2. **圖遍歷**：DFS/BFS 等需要訪問所有鄰居的演算法，鄰接串列效率更高
3. **動態圖**：需要頻繁添加/刪除邊時，鄰接串列更靈活
4. **大規模圖**：百萬級以上頂點的圖，鄰接矩陣的 \( O(V^2) \) 空間無法接受
5. **度數差異大的圖**：如冪律分布的社交網路，大部分頂點度數很小，少數頂點度數很大，鄰接串列能自適應地節省空間

#### 參考來源

[1] GeeksforGeeks, "Implementation of Graph in C++," 2024. [Online]. Available: https://www.geeksforgeeks.org/cpp/implementation-of-graph-in-cpp/ [Accessed: 29-Dec-2025].

[2] T. H. Cormen, C. E. Leiserson, R. L. Rivest, and C. Stein, "Introduction to Algorithms," 3rd ed., MIT Press, 2009, Section 22.1: Representations of graphs.

[3] S. Skiena, "The Algorithm Design Manual," 3rd ed., Springer, 2020, Chapter 7: Graph Traversal.

---

(由於篇幅限制，完整筆記包含第三章到第六章以及附錄的內容將繼續在文件中...)

## 參考文獻

[1] GeeksforGeeks, "Introduction to Graph Data Structure," 2025. [Online]. Available: https://www.geeksforgeeks.org/dsa/introduction-to-graphs-data-structure-and-algorithm-tutorials/ [Accessed: 29-Dec-2025].

[2] GeeksforGeeks, "Comparison between Adjacency List and Adjacency Matrix representation of Graph," 2025. [Online]. Available: https://www.geeksforgeeks.org/dsa/comparison-between-adjacency-list-and-adjacency-matrix-representation-of-graph/ [Accessed: 29-Dec-2025].

[3] GeeksforGeeks, "Time and Space Complexity of DFS and BFS Algorithm," 2025. [Online]. Available: https://www.geeksforgeeks.org/dsa/time-and-space-complexity-of-dfs-and-bfs-algorithm/ [Accessed: 29-Dec-2025].

[4] S. Suhaib, "Navigating Networks Understanding Shortest Path Algorithms (Dijkstra, Bellman-Ford, Floyd-Warshall)," 2025. [Online]. Available: https://notes.suhaib.in/docs/code/graph-theory/theory/shortest-paths-dijkstra-bellman-ford-floyd-warshall/ [Accessed: 29-Dec-2025].

[5] GeeksforGeeks, "Difference between Prim's and Kruskal's algorithm for MST," 2025. [Online]. Available: https://www.geeksforgeeks.org/dsa/difference-between-prims-and-kruskals-algorithm-for-mst/ [Accessed: 29-Dec-2025].

[6] T. H. Cormen, C. E. Leiserson, R. L. Rivest, and C. Stein, "Introduction to Algorithms," 3rd ed., MIT Press, 2009.

[7] R. Sedgewick and K. Wayne, "Algorithms," 4th ed., Addison-Wesley, 2011.

[8] S. Skiena, "The Algorithm Design Manual," 3rd ed., Springer, 2020.

[9] R. Diestel, "Graph Theory," 5th ed., Springer, 2017.

[10] D. West, "Introduction to Graph Theory," 2nd ed., Prentice Hall, 2001.

---

**筆記說明**：

本文件是 Graph 資料結構的完整教學筆記的前半部分（第一章和第二章）。其他章節之後會慢慢繼續更新上來。

預計完整筆記應包含：
- 第三章：Graph 遍歷演算法（DFS、BFS、應用題目）
- 第四章：最短路徑演算法（Dijkstra、Bellman-Ford、Floyd-Warshall）
- 第五章：最小生成樹（Prim、Kruskal）
- 第六章：進階主題（拓撲排序、強連通分量、圖著色、網路流）
- 附錄 A：演算法複雜度總表
- 附錄 B：實務應用案例
- 每個主題至少 5 道研究所等級練習題及詳解

每章節都遵循相同的格式：摘要、重點整理、定義與程式碼、加深內容、額外加深加廣、理解檢核、參考來源。
**Last Updated**：2025年12月29日
