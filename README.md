# lp3
code
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract BankAccount {
    address public owner;
    mapping(address => uint256) public balances;

    event Deposited(address indexed account, uint256 amount);
    event Withdrawn(address indexed account, uint256 amount);
    event Transferred(address indexed from, address indexed to, uint256 amount);

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Only the owner can perform this action");
        _;
    }

    function deposit() public payable {
        require(msg.value > 0, "Deposit amount must be greater than 0");
        balances[msg.sender] += msg.value;
        emit Deposited(msg.sender, msg.value);
    }

    function withdraw(uint256 amount) public {
        require(amount > 0, "Withdrawal amount must be greater than 0");
        require(balances[msg.sender] >= amount, "Insufficient balance");
        balances[msg.sender] -= amount;
        payable(msg.sender).transfer(amount);
        emit Withdrawn(msg.sender, amount);
    }

    function getBalance() public view returns (uint256) {
        return balances[msg.sender];
    }

    function transfer(address to, uint256 amount) public {
        require(to != address(0), "Invalid recipient address");
        require(to != msg.sender, "Cannot transfer to yourself");
        require(balances[msg.sender] >= amount, "Insufficient balance for transfer");

        balances[msg.sender] -= amount;
        balances[to] += amount;

        emit Transferred(msg.sender, to, amount);
    }
}


second code

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract StudentData {
    struct Student {
        uint256 studentId;
        string name;
        uint256 age;
        string course;
    }

    Student[] public students;
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    function addStudent(uint256 _studentId, string memory _name, uint256 _age, string memory _course) public {
        require(msg.sender == owner, "Only the owner can add students");
        students.push(Student(_studentId, _name, _age, _course));
    }

    function getStudentCount() public view returns (uint256) {
        return students.length;
    }

    fallback() external {
        revert("Fallback function called. This contract does not accept Ether.");
    }
}
/*

DAA codes
fibonacci 
iterative 


#include <bits/stdc++.h>
#include <chrono>
using namespace std;
using namespace std::chrono;
int fib(long long n) {
  long long a = 0, b = 1, c, i;
  if (n == 0)
    return a;
  for (i = 2; i <= n; i++) {
    c = a + b;
    a = b;
    b = c;
  }
  return b;
}

int main() {
	auto start = high_resolution_clock::now();
  long long n;
  cout << "Enter number til you want to print:" << endl;
  cin >> n;
  cout << "Fibbonaci series is: ";
  for (long long i = 0; i <= n; i++) {
    cout << fib(i) << " ";
  }
  cout<<endl;
	auto stop = high_resolution_clock::now();
	auto duration = duration_cast<microseconds>(stop - start);
	cout << "Time taken by function: "<< duration.count() << " microseconds" << endl;
  return 0;
}

RECURSION

#include <bits/stdc++.h>
#include <chrono>
using namespace std;
using namespace std::chrono;
int fib(long long int n) {
  if (n <= 1)
    return n;
  return fib(n - 1) + fib(n - 2);
}

int main() {
auto start = high_resolution_clock::now();
long long int n;
  cout << "Enter til what index you want to print fibbonaci series: " << endl;
  cin >> n;
  cout << "Fibbonaci series is: ";
  for (long long int i = 0; i <= n; i++) {
    cout << fib(i) << " ";
  }
  cout<<endl;
  auto stop = high_resolution_clock::now();
  auto duration = duration_cast<microseconds>(stop - start);
	cout << "Time taken by function: "<< duration.count() << " microseconds" << endl;
	return 0;
}

Huffman :-

#include <iostream>
#include <string>
#include <queue>
#include <unordered_map>
using namespace std;

// A Tree node
struct Node
{
	char ch;
	int freq;
	Node *left, *right;
};

// Function to allocate a new tree node
Node* getNode(char ch, int freq, Node* left, Node* right)
{
	Node* node = new Node();

	node->ch = ch;
	node->freq = freq;
	node->left = left;
	node->right = right;

	return node;
}

// Comparison object to be used to order the heap
struct comp
{
	bool operator()(Node* l, Node* r)
	{
		// highest priority item has lowest frequency
		return l->freq > r->freq;
	}
};

// traverse the Huffman Tree and store Huffman Codes
// in a map.
void encode(Node* root, string str,
			unordered_map<char, string> &huffmanCode)
{
	if (root == nullptr)
		return;

	// found a leaf node
	if (!root->left && !root->right) {
		huffmanCode[root->ch] = str;
	}

	encode(root->left, str + "0", huffmanCode);
	encode(root->right, str + "1", huffmanCode);
}

// traverse the Huffman Tree and decode the encoded string
void decode(Node* root, int &index, string str)
{
	if (root == nullptr) {
		return;
	}

	// found a leaf node
	if (!root->left && !root->right)
	{
		cout << root->ch;
		return;
	}

	index++;

	if (str[index] =='0')
		decode(root->left, index, str);
	else
		decode(root->right, index, str);
}

// Builds Huffman Tree and decode given input text
void buildHuffmanTree(string text)
{
	// count frequency of appearance of each character
	// and store it in a map
	unordered_map<char, int> freq;
	for (char ch: text) {
		freq[ch]++;
	}

	// Create a priority queue to store live nodes of
	// Huffman tree;
	priority_queue<Node*, vector<Node*>, comp> pq;

	// Create a leaf node for each character.and add it
	// to the priority queue.
	for (auto pair: freq) {
		pq.push(getNode(pair.first, pair.second, nullptr, nullptr));
	}

	// do till there is more than one node in the queue
	while (pq.size() != 1)
	{
		// Remove the two nodes of highest priority
		// (lowest frequency) from the queue
		Node *left = pq.top(); pq.pop();
		Node *right = pq.top();	pq.pop();

		// Create a new internal node with these two nodes
		// as children and with frequency equal to the sum
		// of the two nodes' frequencies. Add the new node
		// to the priority queue.
		int sum = left->freq + right->freq;
		pq.push(getNode('\0', sum, left, right));
	}

	// root stores pointer to root of Huffman Tree
	Node* root = pq.top();

	// traverse the Huffman Tree and store Huffman Codes
	// in a map. Also prints them
	unordered_map<char, string> huffmanCode;
	encode(root, "", huffmanCode);

	cout << "Huffman Codes are :\n" << '\n';
	for (auto pair: huffmanCode) {
		cout << pair.first << " " << pair.second << '\n';
	}

	cout << "\nOriginal string was :\n" << text << '\n';

	// print encoded string
	string str = "";
	for (char ch: text) {
		str += huffmanCode[ch];
	}

	cout << "\nEncoded string is :\n" << str << '\n';

	// traverse the Huffman Tree again and this time
	// decode the encoded string
	int index = -1;
	cout << "\nDecoded string is: \n";
	while (index < (int)str.size() - 2) {
		decode(root, index, str);
	}
}

// Huffman coding algorithm
int main()
{
	string text;
	cout<<"Enter your string: "<<endl;
	getline(cin,text);

	buildHuffmanTree(text);

	return 0;
}

Fractional knapsnack :-

#include <bits/stdc++.h>
using namespace std;

class Item {
    public:
    int profit, weight;
    Item(int profit, int weight)
    {
        this->profit = profit;
        this->weight = weight;
    }
};

static bool cmp(Item a,Item b)
{
    double r1 = (double)a.profit / (double)a.weight;
    double r2 = (double)b.profit / (double)b.weight;
    return r1 > r2;
}

void fractionalKnapsack(int W, vector<Item> arr, int N)
{
    sort(arr.begin(), arr.end(), cmp);

    cout << "Sorted Items (Profit/Weight Ratio):" << endl;
    for (int i = 0; i < N; i++) {
        cout << "Item " << i + 1 << ": Profit = " << arr[i].profit << ", Weight = " << arr[i].weight << ", Ratio = " << (double)arr[i].profit / arr[i].weight << endl;
    }

    double finalvalue = 0.0;

    for (int i = 0; i < N; i++) {
        if (arr[i].weight <= W) {
            W -= arr[i].weight;
            finalvalue += arr[i].profit;
            cout << "Selected Item " << i + 1 << " fully." << endl;
        }
        else {
            double fraction = (double)W / (double)arr[i].weight;
            finalvalue += arr[i].profit * fraction;
            cout << "Selected part of Item " << i + 1 << ": " << fraction << endl;
            break;
        }
    }

    cout << "Maximum value in knapsack: " << finalvalue<<endl;
}

int main()
{
    int W;
    int n , a , b;
	
	cout<<"Enter bag weight: "<<endl;
	cin>>W;   
    cout<<"Enter the no of objects: "<<endl;
    cin>>n;
   
    vector<Item> arr;
   
    for(int i=0;i<n;i++)
    {
        cout<<"Enter profit "<<i+1<<": "<<endl;
        cin>>a;
        cout<<"Enter weight "<<i+1<<": "<<endl;
        cin>>b;
        arr.push_back({a,b});
    }
   
    // Item arr[] = { { 60, 10 }, { 100, 20 }, { 120, 30 } };
    fractionalKnapsack(W, arr, n);
    return 0;
}

01knapsack BB :-

#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Item {
    int profit;
    int weight;
};

bool compare(Item a, Item b) {
    double ratio1 = (double)a.profit / a.weight;
    double ratio2 = (double)b.profit / b.weight;
    return ratio1 > ratio2;
}

class Node {
public:
    int level;
    int profit;
    int weight;
    double bound;

    Node() {}
    Node(int l, int p, int w) : level(l), profit(p), weight(w) {}
};

int bound(Node u, int n, int W, vector<Item>& items) {
    if (u.weight >= W) return 0;

    int profit_bound = u.profit;
    int j = u.level + 1;
    int total_weight = u.weight;

    while (j < n && total_weight + items[j].weight <= W) {
        total_weight += items[j].weight;
        profit_bound += items[j].profit;
        j++;
    }

    if (j < n) {
        profit_bound += (W - total_weight) * (double)items[j].profit / items[j].weight;
    }

    return profit_bound;
}

int knapsack(int W, vector<Item>& items, int n) {
    sort(items.begin(), items.end(), compare);

    vector<Node> queue;
    Node u, v;
    u.level = -1;
    u.profit = u.weight = 0;
    queue.push_back(u);

    int maxProfit = 0;

    while (!queue.empty()) {
        u = queue.back();
        queue.pop_back();

        if (u.level == -1) v.level = 0;
        if (u.level == n - 1) continue;

        v.level = u.level + 1;
        v.weight = u.weight + items[v.level].weight;
        v.profit = u.profit + items[v.level].profit;

        if (v.weight <= W && v.profit > maxProfit) {
            maxProfit = v.profit;
        }

        v.bound = bound(v, n, W, items);

        if (v.bound > maxProfit) {
            queue.push_back(v);
        }

        v.weight = u.weight;
        v.profit = u.profit;

        v.bound = bound(v, n, W, items);

        if (v.bound > maxProfit) {
            queue.push_back(v);
        }
    }

    return maxProfit;
}

int main() {
    int W, n;
    cout << "Enter bag weight: " << endl;
    cin >> W;
    cout << "Enter the number of objects: " << endl;
    cin >> n;

    vector<Item> items;

    for (int i = 0; i < n; i++) {
        int profit, weight;
        cout << "Enter profit " << i + 1 << ": " << endl;
        cin >> profit;
        cout << "Enter weight " << i + 1 << ": " << endl;
        cin >> weight;
        items.push_back({profit, weight});
    }

    int maxProfit = knapsack(W, items, n);
    cout << "The maximum value that can be obtained is: " << maxProfit << endl;

    return 0;
}

01knapsackDP :-

#include <iostream>
#include <vector>
using namespace std;

struct Item {
    int profit;
    int weight;
};

void knapsack(int W, vector<Item>& items, int n) {
    vector<vector<int>> dp(n + 1, vector<int>(W + 1, 0));

    for (int i = 1; i <= n; i++) {
        for (int w = 0; w <= W; w++) {
            if (items[i - 1].weight <= w) {
                dp[i][w] = max(dp[i - 1][w], dp[i - 1][w - items[i - 1].weight] + items[i - 1].profit);
            } else {
                dp[i][w] = dp[i - 1][w];
            }
        }
    }

    int w = W;
    for (int i = n; i > 0; i--) {
        if (dp[i][w] != dp[i - 1][w]) {
            cout << "\nSelected item " << i << " "<<endl;
            w -= items[i - 1].weight;
        }
    }
    cout << endl;

    cout << "The maximum value that can be obtained is: " << dp[n][W] << endl;
}

int main() {
    int W, n;
    cout << "Enter bag weight: " << endl;
    cin >> W;
    cout << "Enter the number of objects: " << endl;
    cin >> n;

    vector<Item> items;

    for (int i = 0; i < n; i++) {
        int profit, weight;
        cout << "Enter profit " << i + 1 << ": " << endl;
        cin >> profit;
        cout << "Enter weight " << i + 1 << ": " << endl;
        cin >> weight;
        items.push_back({profit, weight});
    }

    knapsack(W, items, n);
    return 0;
}

n-Queen :-

#include <iostream>
using namespace std;

bool isattack(int board[][4], int r, int c) {
    for (int i = 0; i < r; ++i) {
        if (board[i][c] == 1) {
            return true;
        }
    }

    int i = r - 1;
    int j = c - 1;
    while (i >= 0 && j >= 0) {
        if (board[i][j] == 1) {
            return true;
        }
        --i;
        --j;
    }

    i = r - 1;
    j = c + 1;
    while (i >= 0 && j < 4) {
        if (board[i][j] == 1) {
            return true;
        }
        --i;
        ++j;
    }

    return false;
}

bool solve(int board[][4], int row) {
    for (int i = 0; i < 4; ++i) {
        if (!isattack(board, row, i)) {
            board[row][i] = 1;
            if (row == 3) {
                return true;
            } else {
                if (solve(board, row + 1)) {
                    return true;
                } else {
                    board[row][i] = 0;
                }
            }
        }
    }
    return false;
}

void printboard(int board[][4]) {
    for (int i = 0; i < 4; ++i) {
        for (int j = 0; j < 4; ++j) {
            cout << board[i][j] << "  ";
        }
        cout << "\n";
    }
}

int main() {
    int board[4][4] = {0};
    int start_column;

    cout << "Enter the starting column for the first queen (0-3): ";
    cin >> start_column;

    if (start_column < 0 || start_column > 3) {
        cout << "Invalid input" << endl;
        return 1;
    }

    board[0][start_column] = 1;
    if (solve(board, 1)) {
        cout << "Queens problem solved!!!" << endl;
        cout << "Board Configuration:" << endl;
        printboard(board);
    } else {
        cout << "Queens problem can not be solved!!!" << endl;
    }

    return 0;
}

quicksort:-


#include <cstdlib>
#include <iostream>
#include <vector>
using namespace std;

int randomPartition(int low, int high, vector<int> &arr) {
  srand(time(NULL));
  int random = low + rand() % (high - low + 1);

  int pivot = arr[random];

  // Swap the random to the last
  swap(arr[random], arr[high]);

  int i = low;
  for (int j = low; j <= high - 1; j++) {
    if (arr[j] < pivot) {
      swap(arr[j], arr[i]);
      i++;
    }
  }

  swap(arr[i], arr[high]);
  return i;
}

void randomQuickSort(int low, int high, vector<int> &arr) {
  if (low < high) {
    int pivot = randomPartition(low, high, arr);
    randomQuickSort(low, pivot - 1, arr);
    randomQuickSort(pivot + 1, high, arr);
  }
}

int partition(int low, int high, vector<int> &arr) {
  int pivot = arr[high];
  int i = low;

  for (int j = low; j <= high - 1; j++) {
    if (arr[j] < pivot) {
      swap(arr[j], arr[i]);
      i++;
    }
  }

  swap(arr[i], arr[high]);
  return i;
}

void quickSort(int low, int high, vector<int> &arr) {
  if (low < high) {
    int pivot = partition(low, high, arr);
    quickSort(low, pivot - 1, arr);
    quickSort(pivot + 1, high, arr);
  }
}

int main() {
  cout << "Quick Sort Program" << endl;

  // Quick Sort
  vector<int> arr1 = {8, 2, 1, 3, 6, 2, 4, 5, 2};
  quickSort(0, arr1.size() - 1, arr1);

  cout << "Sorted Array Normal Quick Sort: ";
  for (auto num : arr1) {
    cout << num << " ";
  }
  cout << endl;

  // Random Quick Sort
  vector<int> arr2 = {8, 2, 1, 3, 6, 2, 4, 5, 2};
  randomQuickSort(0, arr2.size() - 1, arr2);

  cout << "Sorted Array Random Quick Sort: ";
  for (auto num : arr2) {
    cout << num << " ";
  }
  cout << endl;

  return 0;
}


*\
