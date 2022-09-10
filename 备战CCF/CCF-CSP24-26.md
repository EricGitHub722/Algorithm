## 第26次CCF

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
