---
layout: post
title:  "1242. [S/W 문제해결 응용] 1일차 - 암호코드 스캔"
subtitle: ""
date:   2019-11-30 15:47:23 +0900
categories: algorithm
tags : swea
---
### [문제링크]({{"https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV15JEKKAM8CFAYD"}})

## [풀이]

{% highlight c %}
#include <iostream>
#include <cstring>
using namespace std;
int T, N, M;
int mul = 1;
char arr[2001][501];
char buf[2001][2001];
bool check[10][10][10][10][10][10][10][10];
int hashtable[10000];
char str[10][8] = { "0001101", "0011001", "0010011", "0111101", "0100011", "0110001", "0101111", "0111011", "0110111", "0001011"};
char hexbin[16][5] = {"0000", "0001", "0010", "0011", "0100", "0101", "0110", "0111", "1000", "1001", "1010", "1011", "1100", "1101", "1110", "1111"};

unsigned long _hash(const char *str)
{
	unsigned long hash = 5381;
	int c;

	while (c = *str++)
	{
		hash = (((hash << 5) + hash) + c) % 10000;
	}

	return hash % 10000;
}

typedef struct Trie {
	bool isleaf;
	int val;
	Trie *child[2];
}Trie;
Trie nodes[100]; int mcount;
Trie root;
Trie * new_node() { return &nodes[mcount++]; }

void insert(Trie * ptr, char* str, int v) {
	if (*str == '\0') {
		ptr->isleaf = true;
		ptr->val = v;
	}
	else{
		if (ptr->child[*str - '0'] == '\0')
			ptr->child[*str - '0'] = new_node();
		insert(ptr->child[*str - '0'], str + 1, v);
	}
}
int _find(Trie * ptr, char * str) {
	if (ptr->isleaf == true) return ptr->val;
	if (ptr->child[*str - '0'] == '\0') return -1;
	return _find(ptr->child[*str - '0'], str + mul);
}

void hex_to_bin(char * hex, char *buf) {
	int bufcnt = 0;
	int i = 0, j = 0, h;
	while (*hex != '\0') {
		h = (*hex >= 'A') ? (*hex - 'A' + 10) : (*hex - '0');
		for (int k = 0; k < 4; k++)
			buf[bufcnt++] = hexbin[h][k];
		hex++;
	}
	buf[bufcnt] = '\0';
}
bool valid(int arr[]) { // check가 true면 이미 더해진 값, false 반환
	bool ret = check[arr[0]][arr[1]][arr[2]][arr[3]][arr[4]][arr[5]][arr[6]][arr[7]];
	if (ret == false) {
		return check[arr[0]][arr[1]][arr[2]][arr[3]][arr[4]][arr[5]][arr[6]][arr[7]] = true;
	}
	return !check[arr[0]][arr[1]][arr[2]][arr[3]][arr[4]][arr[5]][arr[6]][arr[7]];
}
void init_trie() {
	for (int i = 0; i < 10; i++) {
		insert(&root, str[i], i);
	}
}
void init() {
	mcount = 0;
	memset(check, 0, sizeof(check));
	for (int i = 0; i < 10000; i++)
		hashtable[i] = 0;
}

int main() {
	ios_base::sync_with_stdio(false);
	cin.tie(NULL); cout.tie(NULL);
	init_trie();
	cin >> T;
	for (int i = 1; i <= T; i++) {
		init();
		int sol = 0, key;
		cin >> N >> M;
		for (int i = 0; i < N; i++) {
			cin >> arr[i];
			key = _hash(arr[i]);
			if (hashtable[key] == 1) {
				i--;
				N--;
			}
			else
				hashtable[key] = 1;
		}

		//for (int i = 0; i < N; i++) {
		//	// 16진수 문자열 -> 2진수로 변환
		//	//arr[i]
		//	
		//}

		
		for (int i = 0; i < N; i++) {
			// 각 행에 대해서 암호코드 찾기 시작
			// 행을 이진 스트링으로  변환
			bool zeroStr = true;

			hex_to_bin(arr[i], buf[i]);
			
			for (mul = 1; mul <= 8; mul++) {// 1배 ~ 8배

				int cipher[8], ctop = 0;

				for (int padding = 0; padding < 7 * mul; padding++) {

					for (int index = padding; index <= strlen(buf[i]) - 7 * mul; index += 7 * mul) {
						// index를 4로 나누면 원래 hex index
						
						if (buf[i][index] != '0') zeroStr = false;
						int temp = _find(&root, buf[i] + index);
						if (temp == -1) {
			 				ctop = 0;
						}
						else {
							cipher[ctop++] = temp;
						}
						if (ctop == 8) {
							// i 이후에 있는 암호 문자열 중에서 
							// start index : (index - 7 * mul * 7) / 4 ~ end index : (index) / 4
							/*cout << "start : " << (index - 7 * mul * 7) / 4 << ", end index : " << index / 4 << endl;
							cout << arr[i] + (index - 7 * mul * 7) / 4 << endl;
							cout << arr[i] + index / 4 << endl;*/
							int start_index = (index - 7 * mul * 7) / 4;
							int end_index = (index) / 4;
							for (int row = i + 1; row < N; row++) {

								if (strncmp(arr[row] + start_index, arr[i] + start_index, end_index - start_index) == 0) {
									//cout << "";
									for (int i = start_index; i <= end_index; i++)
										arr[row][i] = '0';
								}
							}


							// index - 7 * 8, index - 7 * 7,index - 7 * 6,index - 7 * 5,index - 7 * 4, 
							// index - 7 * 3, index - 7 * 2,index - 7 * 1,index - 7 * 0

							int checksum = (cipher[0] + cipher[2] + cipher[4] + cipher[6]) * 3 + (cipher[1] + cipher[3] + cipher[5]) + cipher[7];
							if (checksum % 10 == 0 && valid(cipher)) {
								for (int v = 0; v < 8; v++)
									sol += cipher[v];
								/*if (check[sum] == 0) {
									sol += sum;
									check[sum] = 1;
								}*/
							}
						}
					}
				}
				if (zeroStr) break;
			}
		}
		cout << "#" << i << " " << sol << endl;
	}
}
{% endhighlight %}