# CPE49 - UVA 範例程式集

以下為多個 UVA 練習題的程式碼範例，已整理成 Markdown 檔案，每題以標題區分並含原始 C++ 程式碼。

---

## UVA 10420
```cpp
#include <iostream>
#include <map>
#include <string>

using namespace std;
map<string, int> country_count;

int main() 
{
	int N; cin >> N;
	while (N--) 
	{
		string country, name;

		cin >> country; country_count[country]++;
		cin.ignore();

		getline(cin, name);
	}

	for (auto it = country_count.begin(); it != country_count.end(); it++) 
	{
		cout << it->first << " " << it->second << endl;
	}
}
```

---

## UVA 10222
```cpp
#include <iostream>
#include <string>
using namespace std;

string table = "`1234567890-=qwertyuiop[]\\asdfghjkl;'zxcvbnm,./";

char ot[] = { " 234567890-=ertyuiop[]\\dfghjkl;'cvbnm,./" };
char at[] = { " `1234567890qwertyuiop[asdfghjklzxcvbnm," };

int main() 
{
	string str;

	getline(cin, str);

	//for (int i = 0; i < str.size(); i++) 
	//{
	//	char tmp = tolower(str[i]);

	//	if (tmp == ' ')
	//	{
	//		cout << " ";
	//	}
	//	else 
	//	{
	//		cout << table[table.find(tmp) - 2];
	//	}
	//}

	for (int i = 0; i < str.size(); i++)
	{
		char tmp = tolower(str[i]);
		for (int j = 0; j < strlen(ot); j++) 
		{
			if (tmp == ot[j]) { cout << at[j]; }
		}
	}
	cout << "\n";
}
```

---

## UVA 11332
```cpp
#include <iostream>
using namespace std;

int solve( long long int n) 
{
	if (n % 9 == 0) { return 9; }
	else { return n % 9; }
}

int main() 
{
	long long int num;
	while (cin >> num && num) 
	{
		cout << solve(num) << endl;
	}
}
```

---

## UVA 10252 (getline很重要)
```cpp
#include <iostream>
#include <string>
#include <cstring>
using namespace std;

int countA[26], countB[26];

int main()
{
	string A, B;
	while (getline(cin, A) && getline(cin, B)) 
	{
		memset(countA, 0, sizeof(countA));
		memset(countB, 0, sizeof(countB));

		for (int i = 0; i < A.size(); i++) 
		{
			int n = A[i] - 'a';
			countA[n]++;
		}
		for (int i = 0; i < B.size(); i++)
		{
			int n = B[i] - 'a';
			countB[n]++;
		}

		for (int i = 0; i < 26; i++) 
		{
			for (int j = min(countA[i], countB[i]) ; j > 0; j--) 
			{
				cout << (char)('a' + i);
			}
		}
		cout << "\n";
	}
}
```

---

## UVA 272
```cpp
#include <iostream>
#include <string>

using namespace std;

int main() 
{
	string s;
	int count = 0;

	while (getline(cin, s)) 
	{	
		for (int i = 0; i < s.size(); i++) 
		{
			if (s[i] == '\"')
			{
				++count;

				if (count % 2 != 0)
				{
					cout << "``";
				}
				else
				{
					cout << "''";
				}
			}
			else cout << s[i];
		}
		cout << "\n";
	}
}
```

---

## UVA 12019
```cpp
#include <iostream>
using namespace std;
int main() 
{
	int month[13] = { 0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31 };
	string week[7] = {  "Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"};

	int kase; cin >> kase;
	while (kase--) 
	{
		int M, D, days = 0;
		cin >> M >> D; days = D;

		for (int i = 1; i < M; i++) { days += month[i]; }

		cout << week[(days + 5) % 7] << endl;
	}
}
```

---

## Jolly Jumpers (範例)
```cpp
#include <iostream>
#include <cmath>
using namespace std;

int main(){
    int n, tmp;

    while (cin >> n){
        if (n == 1){
            cin >> tmp;
            cout << "Jolly" << endl;
        } else {
            int seq[n];
            int check[n]; // check if 1~n-1 appeared

            for (int i = 0; i < n; i++){ // initially
                check[i] = 0;
            }

            for (int i = 0; i < n; i++){
                cin >> seq[i];

                if (i != 0){
                    tmp = abs(seq[i-1] - seq[i]); 
                    check[tmp] = 1;
                }
            }

            for (int i = 1; i < n; i++){
                if (check[i] == 0){
                    cout << "Not jolly" << endl;
                    break;
                }
                if (i == n-1){
                    cout << "Jolly" << endl;
                }
            }
        }
    }

    return 0;
}
```

---

## UVA 10056
```cpp
#include <iostream>
#include <iomanip>
#include <cmath>
using namespace std;
int main() 
{
	int kase; cin >> kase;
	while (kase--) 
	{
		int n, i_th; //總人數 、 計算第幾個人
		double p; //獲勝機率
		double q, ans;
		cin >> n >> p >> i_th;
		if (p == 0) { ans = 0; }
		else 
		{
			q = 1 - p;
			ans = pow(q, i_th - 1) * p / (1 - pow(q, n));
		}

		cout << fixed<< setprecision(4) << ans << endl;
	}
}
ans = pow(q, i_th - 1) * p / ( 1 - pow(q , n) );
```

---

## UVA 10268
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <sstream>
#include <algorithm>
using namespace std;
int main() 
{
	int x;
	vector<int> v;
	while (cin >> x) 
	{
		cin.ignore();
		string num; getline(cin, num);
		stringstream ss(num);
		v.clear();
		while (ss >> num) 
		{
			v.push_back(stoi(num));
		}

		//v.pop_back();
		reverse(v.begin(), v.end());

		long long int mul = 1;
		int ans = 0;
		for (int i = 0; i < v.size(); i++) 
		{
			ans += v[i] * (i) * mul;

			if(i != 0)
			mul *= x;
		}

		cout << ans << endl;
	}
}
```

---

## UVA 11063
```cpp
#include <iostream>
using namespace std;
int main() 
{
	int n, kase = 1;
	while (cin >> n) 
	{
		int seq[105];
		int check = 1;
		for (int i = 0; i < n; i++) 
		{
			cin >> seq[i];

			if (seq[i] < 1) check = 0; //這邊的篩選條件為 < 1
			if (i != 0 && seq[i - 1] >= seq[i]) check = 0; // 這邊只要 seq[i - 1] >= seq [i] 就篩掉
		}
		int table[20001] = { 0 };

		if(check == 1) //重要
		for(int i = 0; i < n; i++)
			for (int j = i; j < n; j++) 
			{
				int tmp = seq[i] + seq[j];

				if (table[tmp] == 0) table[tmp] = 1;
				else { check = 0; break; }
			}

		if (check == 0) { cout << "Case #" << kase++ << ": It is not a B2-Sequence." << endl; }
		else { cout << "Case #" << kase++ << ": It is a B2-Sequence." << endl; }

		cout << endl;
	}
}
```

---

## UVA 10093
```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;
int main() 
{
	string line;
	vector<int> digit;
	while ( getline(cin, line)) 
	{
		int sum = 0, m = -1, ans = -1;
		digit.clear();

		for (char single : line) 
		{
			if (isdigit(single)) { digit.push_back(single - '0'); }
			else if (isupper(single)) { digit.push_back(single - 'A' + 10); }
			else if (islower(single)) { digit.push_back(single - 'a' + 36); }
		}

		for (int x : digit) 
		{
			m = max(x, m);
			sum += x;
		}
		//cout << "max: " << m << endl;
		//cout << "sum: " << sum << endl;

		for (int i = max(2, m + 1); i <= 62; i++) 
		{
			if (sum % (i - 1) == 0) { ans = i; break; }
		}

		if (ans == -1) { cout << "such number is impossible!" << endl; }
		else { cout << to_string(ans) << endl; }
	}
}
```

---

## UVA 10019
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() 
{
	int kase, number; cin >> kase;
	while (kase--) 
	{
		cin >> number;
		int a = number, count_bin = 0;
		for (; a > 0; a = a / 2)
			count_bin += a % 2;

		string b = to_string(number);
		int count_hex = 0;
		for (int j = 0; j < b.size(); j++) 
		{
			int tmp = b[j] - '0';
			for (; tmp > 0; tmp = tmp / 2)
				count_hex += tmp % 2;
		}

		cout << count_bin << " " << count_hex << endl;
	}
}
```

另一個做法：
```cpp
#include <bits/stdc++.h>
using namespace std;

int main ()
{
	ios::sync_with_stdio(false);
	cin.tie(0);
	
	int i, j, k;
	int t, n;
	
	cin >> t;
	
	for (i=0;i<t;i++) {
		cin >> n;
		
		int b1 = 0, b2 = 0;
		
		for (j=n;j>0;j=j/2)
			b1 = b1 + j % 2;
				
		string x = to_string(n);
		
		int hex[10]={0,1,1,2,1,2,2,3,1,2};
		
		for(j=0;j<x.size();j++)
			b2 = b2 + hex[x[j]-'0'];
		
		cout << b1 << " " << b2 << endl;
	}

	return 0;
}
```

---

## UVA 10931
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main()
{
	int number;
	while (cin >> number && number)
	{
		vector<int> p;
		int a = number, count_1 = 0;
		for (; a > 0; a = a / 2)
		{
			count_1 += a % 2;
			p.push_back(a % 2);
		}

		reverse(p.begin(), p.end());


		cout << "The parity of ";
		for (int i = 0; i < p.size(); i++) { cout << p[i]; }
		cout <<" is " << count_1 << " (mod 2)." << endl;
	}
}
```

---

## UVA 11005
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits> // 用於 LLONG_MAX
using namespace std;

long long int cost[36];
vector<long long int> totalcost;

int main()
{
	int kase;
	cin >> kase;
	for (int i = 0; i < kase; i++)
	{
		if (i != 0) {
			cout << endl;
		}

		// 讀取 36 個字符的印刷成本
		for (int j = 0; j < 36; j++) {
			cin >> cost[j];
		}
		cout << "Case " << (i + 1) << ":" << endl;
		int queryNum;
		cin >> queryNum;

		for (int j = 0; j < queryNum; j++)
		{
			long long int query;
			cin >> query;

			long long int min_cost = LLONG_MAX; // 初始化最小成本為最大值
			totalcost.clear();

			// 計算從 2 到 36 的所有基數成本
			for (int k = 2; k <= 36; k++)
			{
				long long int num = query;
				long long int totalCost = 0;

				// 將數字轉換為基數 k 並計算成本
				while (num > 0)
				{
					int digit = num % k;  // 取得當前位數
					totalCost += cost[digit]; // 加入對應位數的成本
					num /= k;  // 繼續除以基數
				}

				// 記錄當前基數的總成本
				totalcost.push_back(totalCost);

				// 更新最小成本
				min_cost = min(min_cost, totalCost);
			}

			// 輸出成本最低的基數
			cout << "Cheapest base(s) for number " << query << ":";
			for (int a = 0; a < totalcost.size(); a++)
			{
				if (totalcost[a] == min_cost) {
					cout << " " << (a + 2);
				}
			}
			cout << endl;
		}
	}

	return 0;
}
```

---

## UVA 10193 (如果string要轉數字計算，string - '0'才會等於正確的數值 )
```cpp
#include <iostream>
using namespace std;
int main() 
{
	int kase = 0, n; cin >> n;
	while (n--) 
	{
		string A, B; int a = 0, b = 0, mul = 1;
		cin >> A >> B;
		for (int i = A.size() - 1; i >= 0; i--)
		{
			a += (A[i] - '0') * mul;
			mul *= 2;
		}
		mul = 1;
		for (int i = B.size() - 1; i >= 0; i--) 
		{
			b += (B[i] - '0') * mul;
			mul *= 2;
		}

		int x = max(a, b), y = min(a, b);

		while (y > 0)
		{
			int tmp = x % y;
			x = y;
			y = tmp;
		}

		if (x == 1) { cout << "Pair #" << ++kase << ": Love is not all you need!" << endl;}
		else {cout << "Pair #" << ++kase << ": All you need is love!" << endl;}
	}
}
```

---

## UVA 10190
```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() 
{
	long long int n, m;
	while (cin >> n >> m) 
	{
		vector<long long int> list;
		bool isBoring = false;

		if (n < m || n <= 1 || m <= 1) cout << "Boring!" << endl;
		else 
		{
			for (; n > 1; n = n / m)
			{
				if (n % m == 0) { list.push_back(n); }
				else { isBoring = true; break; }
			}
			list.push_back(1);

			if (!isBoring)
			{
				for (int i = 0; i < list.size(); i++)
				{
					if (i != list.size() - 1)
						cout << list[i] << " ";
					else
						cout << list[i];
				}
			}
			else cout << "Boring!";

			cout << endl;
		}
	}
}
```

---

## 判斷 emirp (範例)
```cpp
#include <iostream>
using namespace std;
int p[1000005];
int main() 
{
	p[1] = 1;
	for (int i = 2; i < 1000005; i++)
		if (p[i] == 0)
			for (int j = i + i; j < 1000005; j += i)
				p[j] = 1;

	int N;
	while (cin >> N) 
	{
		int tmp = N, N_inverse = 0;
		while (tmp > 0) 
		{
			N_inverse *= 10;
			N_inverse += tmp % 10;
			tmp /= 10;
		}

		if (p[N]) { cout << N << " is not prime.\n";}
		else if (N != N_inverse && p[N_inverse] == 0) { cout << N << " is emirp.\n";}
		else { cout << N << " is prime.\n";}

	}
}
```

---

## UVA 11576
```cpp
#include <iostream>
#include <string>
using namespace std;

string s[105];

int main()
{
    int N;
    cin >> N;
    while (N--)
    {
        int row, col, cnt = 0;
        cin >> col >> row;

        string tmp;
        bool isSuccess = false;

        for (int i = 0; i < row; i++)
        {
            cin >> tmp;
            s[i] = tmp;

            if (i == 0) {
                // 第一行直接累加列數
                cnt += col;
                continue;
            }

            isSuccess = false;
            // 比對當前行與前一行
            for (int j = 0; j < col; j++)
            {
                if (isSuccess) break;
                if (s[i][0] == s[i - 1][j]) // 當前行第一個字符與前一行某字符匹配
                {

                    int k = j, x = 0;
                    while (k < col && x < s[i].size() && s[i][x] == s[i - 1][k]) {
                        //cout << "前面的陣列: " << s[i - 1][k] << endl;
                        //cout << "後面的陣列: " << s[i][x] << endl;
                        k++;
                        x++;
                    }

                    if (k == col) { // 成功匹配到尾
                        isSuccess = true;
                        cnt += j; // 計算重疊位置
                    }
                }
            }

            if (!isSuccess) {
                // 如果沒有匹配，直接累加該行列數
                cnt += col;
            }
        }

        cout << cnt << endl;
    }

    return 0;
}
```

---

## UVA 10922
```cpp
#include <iostream>
#include <string>
using namespace std;
int main() 
{
	string s;
	while (getline(cin, s) && s != "0") 
	{
		int degree = (s.length() == 1)? 1 : 0;
		string tmp = s;

		while (s.length() > 1) 
		{
			int sum = 0;

			for (int i = 0; i < s.size(); i++) 
			{
				sum += s[i] - '0';
			}

			s = to_string(sum);
			++degree;
		}

		if ((s[0] - '0') % 9 != 0) { cout << tmp << " is not a multiple of 9." << endl;}
		else { cout << tmp << " is a multiple of 9 and has 9-degree " << degree << "." << endl;}
	}
}
```

---

## UVA 10242
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl "\n"

int main() {
    ios::sync_with_stdio(false); cin.tie(nullptr); cout.tie(nullptr);
    double x1, y1, x2, y2, x3, y3, x4, y4;
    while (cin >> x1 >> y1 >> x2 >> y2 >> x3 >> y3 >> x4 >> y4) {
        double x = 0, y = 0;
        if (x1 == x3 && y1 == y3) { // 重複點是p1, p3
            x = x2 + x4 - x1;
            y = y2 + y4 - y1;
        } else if (x1 == x4 && y1 == y4) { // 重複點是p1, p4
            x = x2 + x3 - x1;
            y = y2 + y3 - y1;
        } else if (x2 == x3 && y2 == y3) { // 重複點是p2, p3
            x = x1 + x4 - x2;
            y = y1 + y4 - y2;
        } else if (x2 == x4 && y2 == y4) { // 重複點是p2, p4
            x = x1 + x3 - x2;
            y = y1 + y3 - y2;
        }
        cout << fixed << setprecision(3) << x << " " << y << endl;
    }
    return 0;
}
```

---

## UVA 10642
```cpp
#include <iostream>
using namespace std;
static auto fast_io = []
{
	ios::sync_with_stdio(false);
	cout.tie(nullptr);
	cin.tie(nullptr);
	return 0;
}();
int main()
{
	int t, x1, y1, x2, y2;
	int Case = 0;
	cin >> t;
	while (t--)
	{
		int cnt = 0;
		cin >> x1 >> y1 >> x2 >> y2;
			
		while (x1) --x1, ++y1, --cnt;
		while (x2) --x2, ++y2, ++cnt;
		for (int i = y1 + 1; i <= y2; ++i) cnt += i;
		cout << "Case " << ++Case << ": " << cnt << "\n";
	}
}
```

---

## UVA 10057
```cpp
#include <iostream>
#include <algorithm>
#include <map>
using namespace std;
 
int main() {
    int n;
    while (cin >> n){
        int a[n];
        for (int i = 0; i < n; i++){
            cin >> a[i];
        }
        sort(a, a+n);
        int mid1 = a[(n-1)/2];
        int mid2 = a[n/2];
        int ans = 0;
        for (int i = 0; i < n; i++){
            if (a[i] == mid1 || a[i] == mid2) ans++;
        }
        cout << mid1 << " " << ans << " " << mid2 - mid1 + 1 << "\n";
    }
    return 0;
}
```

---

## UVA 118
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <map>
using namespace std;

bool scant[55][55] = { false };
map<int, char> d_name{ {0, 'N'}, {1, 'E'}, {2, 'S'}, {3, 'W'} };
map<char, int> d_num = { {'N', 0}, {'E', 1}, {'S', 2}, {'W', 3} };
int dir[4][2] = { {0, 1}, {1, 0}, {0, -1}, {-1, 0} };

int main()
{
	int x, y, d, actionNum;
	int x_bound, y_bound;
	char dc;
	string action;

	cin >> x_bound >> y_bound;
	while (cin >> x >> y >> dc)
	{
		bool lost = false;
		d = d_num[dc];

		cin.ignore();
		getline(cin, action);

		for (int i = 0; i < action.size() && !lost; i++)
		{
			if (action[i] == 'L') d = (d - 1 + 4) % 4;
			else if (action[i] == 'R') d = (d + 1) % 4;
			else if (action[i] == 'F')
			{
				x += dir[d][0];
				y += dir[d][1];

				if (x > x_bound || x < 0 || y > y_bound || y < 0)
				{
					x -= dir[d][0];
					y -= dir[d][1];

					if (!scant[x][y])
					{
						lost = true;
						scant[x][y] = true;
					}
				}
			}
		}

		cout << x << " " << y << " " << d_name[d];
		if (lost) cout << " LOST";
		cout << endl;

	}
}
```

---

## UVA 11349
```cpp
#include <iostream>
using namespace std;
 
int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
    int T, n;
    char ch;
    cin >> T;
    for (int Case = 1; Case <= T; Case++){
        cin >> ch >> ch >> n;
        long long a[n][n];
        bool flag = true;
        for (int i = 0; i < n; i++){
            for (int j = 0; j < n; j++){
                cin >> a[i][j];
                if (a[i][j] < 0) flag = false;
            }
        }
        cout << "Test #" << Case << ": ";
        if (!flag){
            cout << "Non-symmetric.\n";
            continue;
        }
         
        for (int i = 0; i <= n/2; i++){
            for (int j = 0; j < n-i; j++){
                if (a[i][j] != a[n-1-i][n-1-j]){
                    flag = false;
                    break;
                }
            }
        }
        if (flag) cout << "Symmetric.\n";
        else cout << "Non-symmetric.\n";
    }
    return 0;
}
```

---

## UVA 10221
```cpp
#include <iostream>
#include <iomanip>
#include <cmath>
using namespace std;
#define PI acos(0.0)*2.0
 
int main() {
    double r, s, a;
    string unit;
    double chord, arc;
    r = 6440.0;
    while (cin >> s >> a >> unit){
        if (unit == "min") a /= 60.0;
        if (a > 180.0) a = 360.0 - a;
        chord = (r+s) * cos((90.0 - a/2.0)/ 180.0 * PI) * 2.0;
        arc = 2.0 * PI * (r+s) * a / 360.0;
        cout << fixed << setprecision(6) << arc << " " << chord << "\n";
    }
    return 0;
}
```

---
