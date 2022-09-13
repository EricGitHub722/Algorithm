## 第26次CCF

累计得分：**285**

### T1.归一化处理

注意事项，该int时int，该double时double，注意细节

#### 示例代码

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 1010;

double a[N];

int main()
{
	int n;
	double sum = 0;
	cin >> n;
	for (int i = 0; i < n; i ++ ) {
		cin >> a[i];
		sum += a[i];
	}
	double cover_a;
	cover_a = (double)sum / n;	// 平均值 
	double cover_sum = 0;
	for (int i = 0; i < n; i ++ ) {
		cover_sum += (a[i] - cover_a) * (a[i] - cover_a);
	}
	double cover_D;
	cover_D = cover_sum / n;
	cover_D = sqrt(cover_D);
	for (int i = 0; i < n; i ++ ) {
		double temp = a[i] - cover_a;
		double res = temp / cover_D;
		printf("%f\n", res);
	}
	return 0;
}
```

### T2.寻宝 大冒险！

思路：巧用set将当前有树的点都保存起来，先挑选其中一个节点，然后以这个节点为左下角节点，对剩余节点遍历，通过比较和藏宝图中树的数量来判定有没有可能匹配

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 1010;

vector<vector<int>> M(N, vector<int>(N));
set<pair<int, int>> P;

int n, l, s;
int main() {
	int cnt = 0;
	cin >> n >> l >> s;
	for (int i = 0; i < n; i ++ ) {
		int x, y;
		cin >> x >> y;
		P.insert({x, y});
	}
	for (int i = s; i >= 0; i -- ) {
		for (int j = 0; j <= s; j ++ ) {
			cin >> M[i][j];
			if (M[i][j] == 1) {
				cnt ++;
			}
		}
	}
	int res = 0;
	for (auto it = P.begin(); it != P.end(); it ++ ) {
		auto t = *it;
		int px = t.first;
		int py = t.second;
		int temp = 0;
		int flag = 1;
		for (auto iit = P.begin(); iit != P.end(); iit ++ ) {
			auto tt = *iit;
			int x = tt.first;
			int y = tt.second;
			if (x - px >= 0 && x - px <= s && y - py >= 0 && y - py <= s) {
				temp ++;
			}
		}
		
		if (temp == cnt) {
			for (int j = 0; j <= s; j ++ ) {
				if (flag) {
					for (int k = 0; k <= s; k ++ ) {
						if (j + px > l || k + py > l) {
							flag = 0;
							break;
						}
						if (M[j][k] == 1) {
							if (P.count({j + px, k + py}) == 0) {
								flag = 0;
								break;
							}
						}
						else {
							if (P.count({j + px, k + py}) == 1) {
								flag = 0;
								break;
							}
						}
					}
				}
			}
		}
		else {
			flag = 0;
		}
		if (flag) {
			res ++;
		}
	}
	cout << res << endl;
	return 0;
}

```

### T3.角色授权

大模拟真**

#### 实例代码（自己写的70分，知足了）

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 5010;
const int M = 510;

int n, m, q;

vector<string> Name;
vector<vector<string>> Operation(M);
vector<vector<string>> Resource_type(M);
vector<vector<string>> Resource_name(M);

unordered_map<string, string> relate;

vector<string> re_Name;
vector<vector<pair<string, int>>> re_union(M);

vector<string> User;
vector<vector<string>> q_User(N);

vector<string> cur_op;

int main(){
	cin >> n >> m >> q;
	for (int i = 0; i < n; i ++ ) {
		string name;
		cin >> name;
		Name.push_back(name);
		int nv;
		cin >> nv;
		for (int j = 0; j < nv; j ++ ) {
			// 操作数
			string Op;
			cin >> Op;
			Operation[i].push_back(Op); 	
		}
		int no;
		cin >> no;
		for (int j = 0; j < no; j ++ ) {
			string Re_type;
			cin >> Re_type;
			Resource_type[i].push_back(Re_type);
		}
		int nn;
		cin >> nn;
		for (int j = 0; j < nn; j ++ ) {
			string Re_name;
			cin >> Re_name;
			Resource_name[i].push_back(Re_name);
		}
	}
	
	for (int i = 0; i < m; i ++ ) {
		int cnt = -1;
		string name;
		cin >> name;
		for (int j = 0; j < re_Name.size(); j ++ ) {
			if (re_Name[j] == name) {
				cnt = j;
				break;
			}
		}
		if (cnt == -1) {
			cnt = re_Name.size();
			re_Name.push_back(name); 	// 关联的角色名称 
		}
		
		int ns;
		cin >> ns;
		for (int j = 0; j < ns; j ++ ) {
			string Op1, Op2;
			cin >> Op1 >> Op2;
			if (Op1 == "u") {
				re_union[cnt].push_back({Op2, 1});
			}
			else {
				re_union[cnt].push_back({Op2, 2});
			}
		} 
	}
	
	/*
	for (int i = 0; i < 5; i ++ ) {
		cout << "第" << i << endl;
		for (int j = 0; j < re_union[i].size(); j ++ ) {
			auto t = re_union[i][j];
			cout << t.first << " " << t.second << endl;
			//cout << re_union[i][j] << "  ";
		}
		cout << endl;
	} 
	*/
	
	for (int i = 0; i < q; i ++ ) {
		string User_name;
		cin >> User_name;
		User.push_back(User_name);

		int ng;
		cin >> ng;
		for (int j = 0; j < ng; j ++ ) {
			string q_name;
			cin >> q_name;
			q_User[i].push_back(q_name);	//关联的组 
		} 
		
		cur_op.clear();
		// 添加对象 
		for (int j = 0; j < m; j ++ ) {
			for (int k = 0; k < re_union[j].size(); k ++ ) {
				auto t = re_union[j][k];
				string temp = t.first;
				int ty = t.second;
				if (ty == 1 && temp == User_name) {
					cur_op.push_back(re_Name[j]);
					break;
				}
				else if (ty == 2) {
					for (int p = 0; p < q_User[i].size(); p ++ ) {
						if (temp == q_User[i][p]) {
							cur_op.push_back(re_Name[j]);
							break;
						}
					}
				}
			}
		}
		
		int op_len = cur_op.size();
		/*
		for (int k = 0; k < op_len; k ++ ) {
			cout << cur_op[k] << " ";
		}
		cout << endl << endl;
		*/
		
		string wait_Op;
		cin >> wait_Op;
		
		string Op_type;
		cin >> Op_type;
		
		string Op_name;
		cin >> Op_name;
		
		int right = 0;
		
		for (int j = 0; j < op_len; j ++ ) {
			string object = cur_op[j];
			int pos = 0;
			for (int k = 0; k < Name.size(); k ++ ) {
				if (Name[k] == object) {
					pos = k;
					break;
				}
			}
			
			for (int k = 0; k < Operation[pos].size(); k ++ ) {
				if (Operation[pos][k] == wait_Op || Operation[pos][k] == "*") {
					for (int r = 0; r < Resource_type[pos].size(); r ++ ) {
						if (Resource_type[pos][r] == Op_type || Resource_type[pos][r] == "*") {
							if (Resource_name[pos].size() == 0) {
								if (right == 0) {
									cout << "1" << endl;
									right = 1;
								}
							}
							else {
								for (int e = 0; e < Resource_name[pos].size(); e ++ ) {
									if (Resource_name[pos][e] == Op_name) {
										if (right == 0) {
											cout << "1" << endl;
											right = 1;
										}
									}
								}
							}
						}
					}
				}
			}
		}
		
		cur_op.clear();
		if (right == 0)
			cout << "0" << endl;
		right = 0;		
	}
	return 0;
}

/*
以下为70分样例
2 4 5
op 1 open 1 door 0
ap 1 close 1 gate 0
op 1 g sre
op 1 u xiaop
op 2 g aaa u vvv
ap 1 g ggg
xiaoc 2 sre ops open door room302
xiaop 1 ops open door room501
xiaoc 2 sre ops remove door room302
xiaoa 2 aaa ggg close gate room1
xiaoa 2 aaa ggg close door room1
*/
```

### T4.光线追踪

话不多说，直接暴力，怒拿15分（寄！）
#### 菜鸡代码
```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 1010;

typedef struct mirror{
	int qidian_x;
	int qidian_y;
	int zhongdian_x;
	int zhongdian_y;
	int arc;	// 0--\   1--/
	double a;
	int k;
}mirror;

unordered_map<int, mirror> M;
int m;

int dx[4] = {1, 0, -1, 0};
int dy[4] = {0, 1, 0, -1};

int judge(int x, int y) {
	for (auto it = M.begin(); it != M.end(); it ++ ) {
		int tx, ty;
		int min_x = min(it->second.qidian_x, it->second.zhongdian_x);
		int max_x = max(it->second.qidian_x, it->second.zhongdian_x);
		int min_y = min(it->second.qidian_y, it->second.zhongdian_y);
		int max_y = max(it->second.qidian_y, it->second.zhongdian_y);
		if (x > min_x && x < max_x && y > min_y && y < max_y) {
			
			int x1 = abs(x - it->second.qidian_x);
			int y1 = abs(y - it->second.qidian_y);
			int x2 = abs(x - it->second.zhongdian_x);
			int y2 = abs(y - it->second.zhongdian_y);
			if (x1 == y1) {
				if (y2 == x2) {
					return it->second.k;
				}
			}
		}	
	}
	return 0;
}

int main() {
	cin >> m;
	for (int i = 1; i <= m; i ++ ){
		int t;
		cin >> t;
		if (t == 1) {
			int x1, y1, x2, y2;
			double a;
			cin >> x1 >> y1 >> x2 >> y2 >> a;
			int arc;
			if (x1 > x2 && y1 > y2) {
				arc = 1;
			}
			else if (x1 > x2 && y1 < y2) {
				arc = 0;
			}
			else if (x1 < x2 && y1 < y2) {
				arc = 1;
			}
			else arc = 0;
			M[i] = {x1, y1, x2, y2, arc, a, i};
		}
		else if (t == 2) {
			int k;
			cin >> k;
			M.erase(k);
		}
		else {
			int x, y, d, t;
			double I;
			cin >> x >> y >> d >> I >> t;
			int flag = 1;
			while (t -- ) {
				x = x + dx[d];
				y = y + dy[d];
				int j = judge(x, y);
				if (j != 0) {
					auto temp = M[j];
					I = I * temp.a;
					if (I < 1) {
						flag = 0;
						break;
					}
					if (d == 0) {
						if (temp.arc == 0) {
							d = 3;
						}
						else d = 1;
					}
					else if (d == 1) {
						if (temp.arc == 0) {
							d = 2;
						}
						else d = 0;
					}
					else if (d == 2) {
						if (temp.arc == 0) {
							d = 1;
						}
						else d = 3;
					}
					else {
						if (temp.arc == 0) {
							d = 0;
						}
						else d = 2;
					}
				}
			}
			if (flag) {
				cout << x << " " << y << " " << (int)I << endl;
			}
			else {
				cout << "0 0 0" << endl;
			}
		}
	}
	return 0;
}
```
