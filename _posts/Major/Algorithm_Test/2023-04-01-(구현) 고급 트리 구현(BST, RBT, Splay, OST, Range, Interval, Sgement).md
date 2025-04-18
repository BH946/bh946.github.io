---
title:  "[구현] 고급 트리 구현(BST, RBT, Splay, OST, Range, Interval, Sgement)"
categories : AlgorithmTest
tag : [알고리즘]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**앞서 정리한 고급 트리들을 구현해보겠다.  
구현할 트리 : BST, RBT, Splay, OST, Range, Interval, Sgement**

**C++에서 기본적으로 map자료구조를 제공하는데 이는 Tree구조이며 RBT 기반이다.**  

**이번 포스팅에서는 직접 Tree들을 전부 구현해보려고 한다.**

* 다른 트리들 전부 BST가 제일 기초가되는 구조이므로 BST를 완벽히 이해할것을 권장한다.
* 아마도 **BST, RBT 정도만 트리 전체를 구현해볼것 같고 나머지는 간단히 구현**하겠다.
* 개념들은 따로 정리했었기 때문에 바로 코드를 나타내겠다.(주석 참고)
* **[고급 트리 개념](https://bh946.github.io/algorithm/(%EA%B3%A0%EA%B8%89%EA%B0%9C%EB%85%90)-%EA%B3%A0%EA%B8%89-%ED%8A%B8%EB%A6%AC(BST,-RBT,-Splay,-OST,-Range,-Interval,-Sgement)/)**

<br><br>

# BST(Binary Search Tree)

**코드에서 Splay Tree는 나중에 보고, BST만 여기선 이해할 것**

```c++
/*
Binary Search Tree

z노드 삽입 - treeInsert(T,z)
x노드 제거 - treeDelete(t,x)
정렬된 순서로 출력 함수 - inorderTreeWalk()
검색 함수 - treeSearch(x,k)
가장 작은 key - treeMinimum(x)
가장 큰 key - treeMaximum(x)
successor(x), predecessor(x)


Splay Tree의 동작을 위해서
rotation right,left & splay_tree 까지 추가 test
=> 이부분은 나중에 뒤에서 Splay Tree파트에서 다시 참고해볼것!

*/


#include<iostream>
#include<vector>
using namespace std;

struct node {
	int key;
	node* left = nullptr;
	node* right = nullptr;
	node* p = nullptr; // parent 의미
};
typedef node* Node;

class BST {
private:
	Node root = nullptr;
public:
	// 생성자
	BST(int inArr[], int inArrSize) {
		for (int i = 0; i < inArrSize; i++) {
			treeInsert(inArr[i]);
		}
	}
	BST() {}

	// insert
	void treeInsert(int key) {
		Node y = this->root; // 삽입할 z의 부모로 사용하려고 선언한 y노드
		Node z = new node();
		z->key = key;

		// 0. 빈 Tree인지 먼저 체크 (root가 null인제 체크하면 됨)
		if (y == nullptr) { 
			this->root = z; 
			return; 
		}
		
		// 1. 검색은 항상 root부터 (이 시점의 y는 root)
		Node x = y; // y값을 null로 만들지 않기 위해 사용할 변수
		while (x != nullptr) {
			y = x;
			if (y->key > key) x = x->left;
			else x = x->right;
		}

		// 2-1. 자식 -> 부모 연결
		z->p = y; 
		// 2-2. 부모 -> 자식 연결
		if(y->key > z->key)
			y->left = z; 
		else 
			y->right = z;
	}

	// delete
	// case1 - child X (단말 노드)
	// case2 - child 1개
	// case3 - child 2개
	Node treeDelete(Node z) {
		Node y = nullptr; // 제거될 노드
		Node x = nullptr; // 노드 제거하는데 사용
		// 1. case에 따라 y값 기입
		if (z->left == nullptr || z->right == nullptr)
			y = z; // case 1 or case 2
		else
			y = treeSuccessor(z); // case3 => successor 값으로 y 기록
		// 참고 : successor 특징 덕분에 case 1 or case 2로 변경됨

		// case 1 or case 2 만 남으므로 이제 y 제거 가능
		// 2. "자식 입장에서 처리" 시작
		if (y->left != nullptr) // child 있으면 x에 기록
			x = y->left; // why?? "x->p = y->p" 를 통해서 y 제거할거라서
		else
			x = y->right;
		// delete y
		if (x != nullptr) // 참고 : case1은 x==nullptr이므로 pass
			x->p = y->p; // why?? case1은 child가 없어서 애초에 "부모입장에서 처리"만 하면 됨

		// 2. "부모 입장에서 처리" 시작
		// delete y
		if (y->p == nullptr) // y가 root였는지 확인
			this->root = x;
		else if (y == y->p->left)
			y->p->left = x;
		else
			y->p->right = x;

		// 3. case3은 처리가 되었었지만 마지막 key값을 치환안해서 이를 진행
		if (y != z) // case3 이라면
			z->key = y->key; // 대신 삭제된 y에 기록된 key값을 z에 옮겨준다.

		return y; // 삭제 노드 반환
	}

	// inorderSearch
	void inorderTreeWalk(Node z) {
		if (z == nullptr) return;
		inorderTreeWalk(z->left);
		cout << z->key << ' ';
		inorderTreeWalk(z->right);
	}

	// search
	Node treeSearch(Node z, int key) {
		// 1. 끝까지 못찾거나 찾은경우 "반환"
		if (z == nullptr || z->key == key) return z;

		// 2. 나머지 경우는 계속 자리 찾기
		if (z->key > key) treeSearch(z->left, key);
		else treeSearch(z->right, key);
	}

	// successor
	Node treeSuccessor(Node z) {
		// z의 오른쪽 서브트리가 있는 경우
		if (z->right != nullptr) {
			// 오른쪽 서브트리의 min노드 반환
			return treeMinimum(z->right);
		}
		// z의 오른쪽 서브트리가 없는 경우
		else {
			// z가 z부모의 왼쪽 자식일 경우에 반환(종료)
			Node y = z->p;
			while (y != nullptr && z == y->right) {
				z = y;
				y = y->p;
			} // root까지 올라가면 nullptr이므로 잘 종료됨(문제없음)
			return y;
		}
	}

	// predecessor
	Node treePredecessor(Node z) {
		// z의 왼쪽 서브트리가 있는 경우
		if (z->left != nullptr) {
			// 왼쪽 서브트리의 max노드 반환
			return treeMaximum(z->left);
		}
		// z의 왼쪽 서브트리가 없는 경우
		else {
			// z가 z부모의 오른쪽 자식일 경우에 반환(종료)
			Node y = z->p;
			while (y != nullptr && z == y->left) {
				z = y;
				y = y->p;
			} // root까지 올라가면 nullptr이므로 잘 종료됨(문제없음)
			return y;
		}
	}

	// min
	Node treeMinimum(Node z) {
		while (z->left != nullptr)
			z = z->left;
		return z;
	}

	// max
	Node treeMaximum(Node z) {
		while (z->right != nullptr)
			z = z->right;
		return z;
	}

	// getter
	Node rootNode() { return this->root; }


	
	// Splay Tree
	void Splay(Node x) {
		// x가 root가 될때까지
		while (x->p != nullptr) {
			Node y = x->p; // y는 x의 부모

			// zig
			if (y->p == nullptr) { // x의 부모 노드가 root라면
				if (y->left == x)
					rightRotate(y);
				else
					leftRotate(y);
			}
			// zig zag
			else if (y->left == x && y->p->right == y) {
				rightRotate(y);
				leftRotate(y);
			}
			else if (y->right == x && y->p->left == y) {
				leftRotate(y);
				rightRotate(y);
			}
			// zig zig
			else if (y->left == x && y->p->left == y) {
				rightRotate(y->p);
				rightRotate(y);
			}
			else if (y->right == x && y->p->right == y) {
				leftRotate(y->p);
				leftRotate(y);
			}
		}
	}
	// x, y순으로 노드 그려서 Rotation을 하는걸 그림 그려놓고 코드로 구현
	void leftRotate(Node x) {
		// 1. y노드 선언
		Node y = x->right;

		// 2. y의 왼쪽 자식을 x의 오른쪽 자식으로 연결
		// "부모 입장"
		x->right = y->left;
		// "자식 입장"
		if (y->left != nullptr) // 회전할때 y왼쪽 자식이 단말노드가 아닐때
		{
			y->left->p = x; // 해당 자식노드는 새로 x노드와 연결
		}

		// 3. 기존 x부모를 새로 올라가는 y와 연결
		// "자식 입장"
		y->p = x->p; // y가 올라가므로 x부모에 연결
		// "부모 입장"
		if (x == this->root) // rotation하는 x가 root였던건지 확인
		{
			root = y;
		}
		else if (x == x->p->left) // x가 root 아니였으면, y와 잘 연결
		{
			x->p->left = y;
		}
		else
		{
			x->p->right = y;
		}

		// 4. y가 부모, x가 자식 관계 연결
		// "부모 입장"
		y->left = x;
		// "자식 입장"
		x->p = y;
	}

	// x, y순으로 노드 그려서 Rotation을 하는걸 그림 그려놓고 코드로 구현
	void rightRotate(Node y) {
		// 1. x노드 선언
		Node x = y->left;

		// 2. x의 오른쪽 자식을 y의 왼쪽 자식으로 연결
		// "부모 입장"
		y->left = x->right;
		// "자식 입장"
		if (x->right != nullptr) // 회전할때 x오른쪽 자식이 단말노드가 아닐때
		{
			x->right->p = y; // 해당 자식노드는 새로 y노드와 연결
		}

		// 3. 기존 y부모를 새로 올라가는 x와 연결
		// "자식 입장"
		x->p = y->p; // x가 올라가므로 y부모에 연결
		// "부모 입장"
		if (y == this->root) // rotation하는 y가 root였던건지 확인
		{
			root = x;
		}
		else if (y == y->p->left) // y가 root 아니였으면, x와 잘 연결
		{
			y->p->left = x;
		}
		else
		{
			y->p->right = x;
		}

		// 4. x가 부모, y가 자식 관계 연결
		// "부모 입장"
		x->right = y;
		// "자식 입장"
		y->p = x;
	}
};

int main() {
	// BST 생성 test
	int inArr[10] = { 2,4,6,8,10 };
	BST bst = BST(inArr, 5); // BST 생성
	Node rootNode = bst.rootNode();

	// inorderTreeWalk test
	bst.inorderTreeWalk(rootNode);
	cout << '\n';

	// search test
	int k = 8;
	cout << bst.treeSearch(rootNode, k)->key;
	cout << '\n';

	// minimum, maximum test
	cout << bst.treeMinimum(rootNode)->key << ' ';
	cout << bst.treeMaximum(rootNode)->key << '\n';

	// successor, predecessor test
	Node x2 = rootNode->right;
	cout << bst.treeSuccessor(rootNode)->key << ' '; // root 노드 테스트
	cout << bst.treePredecessor(x2)->key << '\n'; // x2 노드 테스트

	// delete test
	cout << bst.treeDelete(x2)->key << ' '; // x2 노드 테스트
	cout << bst.treeDelete(rootNode)->key << ' '; // root 노드 테스트
	Node x3 = x2->right->right->right;

	// Splay test
	bst.Splay(x3); // x3노드 root로 올리기
	cout << bst.rootNode()->key; // x3노드 값 출력시 정답.

	return 0;
}
```

<br><br>

# RBT(Red-Black Tree)

**RBT는 기본적으로 BST와 유사하게 insert, delete를 동작하고 이후에 추가로 균형을 맞추는 동작을 진행한다.**

* insert는 기존 BST에서 구현한 코드에 덧 붙여서 진행했는데,
* delete의 경우에는 기존 BST와는 코드가 조금 다르다. 그렇지만 내부적으로 동작하는건 동일하다.
  * 따로 노드 연결하는 함수 만들어서 사용한 차이가 있음!!
* 추가로 leafNode를 nullptr처럼 사용했다.
  * 아쉬운점은 약간 섞어서 사용했다는점 ㅠㅠ
  * 때문에 leafNode대신 nullptr을 사용해야 동작하는 부분들이 생겼다...
  * 혹시나 재구현 하게 된다면 leafNode로 완전 통일시켜서 구현해보겠다 ㅠㅠ

* 여기서 OST부분 코드는 무시하고 생각!! (뒤에서 OST파트때 다시 참고하면 됨)

<br>

**파일입출력 예시**

<img src="/images/2023-04-01-(구현) 고급 트리 구현(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230422173731550.png" alt="image-20230422173731550" style="zoom: 67%;" /> <img src="/images/2023-04-01-(구현) 고급 트리 구현(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230422173832930.png" alt="image-20230422173832930" style="zoom:67%;" /> 

<br>

**코드**

```c++
/*
* RBT는 어떤 특정한 규칙에 따라 재구성하는게 아니고, 그냥 재구성하는 규칙을
* 있는 그대로 받아들이는게 정신건강에 이롭다...

<성질>
* 노드는 Red or Black 색
* 모든 단말노드(NULL)는 Black 색
* Red 노드의 자식들은 모두 Black 색
* 어떤  노드로부터  이  노드의  모든  단말노드  까지의 경로는  모두  같은  개수의  Black 노드를  가진다.
* Root 노드는 Black 색

구현할 때 leafNode로 모든 단말노드(NULL) 노드를 대체할 생각이다.
구현 함수는 insert, delete만 구현하며 구현에 필요한 부가적인 함수들만 몇개 더 구현하겠다.


OST도 추가로 구현
노드에 size 추가했고, insert, delete, OSselect, OSrank 함수 수정
*/


#include <iostream>
#include <fstream>
using namespace std;

enum Color { RED, BLACK };

struct node {
	int key;
	Color color = BLACK;
	node* left = nullptr;
	node* right = nullptr;
	node* p = nullptr;
	int size; // OST 위해 선언 (leafNode는 size:0)
};
typedef node* Node;

class RBT {
private:
	Node root = nullptr;
	Node leafNode = nullptr;
public:
	// 생성자
	RBT(int inArr[], int inArrSize) {
		this->leafNode = new node();
		this->root = leafNode;
		for (int i = 0; i < inArrSize; i++) {
			treeInsert(inArr[i]);
		}
	}
	RBT() {
		this->leafNode = new node();
		this->root = leafNode; // 초기 root는 null을 의미
	}

	// insert
	void treeInsert(int key) {
		Node y = this->root; // 삽입할 z의 부모로 사용하려고 선언한 y노드
		Node z = new node();
		z->key = key;
		z->color = RED; // insert때 초기값은 RED인 특징
		z->left = this->leafNode; // insert때 자식 초기값은 단말노드
		z->right = this->leafNode;
		z->size = z->left->size + z->right->size + 1; // subtree의 모든 노드 개수

		// 0. 빈 Tree인지 먼저 체크 (root가 null인지 체크하면 됨)
		if (y == this->leafNode) {
			z->color = BLACK; // root노드는 BLACK인 특징
			// z->p = y; // root부모 leafNode 연결
			this->root = z;
			return;
		}

		// 1. 검색은 항상 root부터 (이 시점의 y는 root)
		Node x = y; // y값을 null로 만들지 않기 위해 사용할 변수
		while (x != this->leafNode) {
			y = x;
			if (y->key > key) x = x->left;
			else x = x->right;
			y->size++; // 지나가는 모든 노드들 size값 증가
		}

		// 2-1. 자식 -> 부모 연결
		z->p = y;
		// 2-2. 부모 -> 자식 연결
		if (y->key > z->key)
			y->left = z;
		else
			y->right = z;
		// 일반적인 BST insert 과정 끝(기존 BST코드에 leafNode만 활용했음)

		// red-red 문제 해결
		treeInsertFixUp(z); 
	}

	//참고
	//case 1 -> case 2 or case 3
	//case 2 -> case 3
	//case 3 -> 끝
	void treeInsertFixUp(Node z) {
		// 이제부터 복잡(r-r문제 해결)
		// 탈출 시점은? root일때 or p(z)가 red아닐 때 탈출.(z->p가 red아니면 red-red문제가 아니니까)
		// 반드시!! z가 root인지 확인을 먼저 조건식에 적어야한다!! 왜냐하면 z가 root인 상황인 상태에서
		// p(z)의 RED확인이 먼저 나올경우, p(z)는 단말노드(leafNode) 이기 때문에 color가 존재하지 않아서 에러가 뜨기 때문이다.
		while (z != this->root && z->p->color == RED) {
			Node grandparent, uncle = nullptr; // 할아버지, 삼촌 노드
			bool check = false; // 대칭판단(4,5,6도 구현위해서)

			grandparent = z->p->p;
			if (z->p == grandparent->left) {
				uncle = grandparent->right;
				check = true;
			}
			else {
				uncle = grandparent->left;
				check = false;
			}

			// z의 부모가 할아버지 왼쪽 자식인 경우 - case1,2,3
			// z의 부모가 할아버지 오른쪽 자식인 경우 - case4,5,6
			// => 대칭적으로 다를 뿐 구조는 동일(4,5,6 설명은 생략)

			// case 1 => 삼촌이 RED인 경우
			if (uncle->color == RED) {
				// 현재상태 : 할아버지 BLACK, z부모 RED, z삼촌 RED
				// 따라서 할아버지를 RED, Z부모 BLACK, z삼촌 BLACK으로 변환
				// 이후 할아버지를 새로운 z로 선언해서 다시 while문 반복
				z->p->color = BLACK;
				uncle->color = BLACK;
				grandparent->color = RED;
				z = grandparent;
			}
			// case 2,3 => 삼촌이 BLACK인 경우
			// 참고 : case 5,6인 대칭도 같이 고려
			else {
				// case 2 => z가 z의 부모의 오른쪽 자식 - LEFT ROTATE 로 case 3!!
				if (check && z == z->p->right ||
					!check && z == z->p->left) {
					
					z = z->p;
					if (check) leftRotate(z);
					else rightRotate(z);
				}
				// case 3 => z가 z의 부모의 왼쪽 자식 - RIGHT ROTATE 로 해결
				z->p->color = BLACK; // 부모 R->B
				grandparent->color = RED; // 할아버지 B->R
				if (check) rightRotate(grandparent);
				else leftRotate(grandparent);

			}
		}
		this->root->color = BLACK; // root는 항상 BLACK!!
	}

	void treeDelete(Node z) {
		// OST를 위해 size 갱신 먼저 하겠다.
		if (z->p != leafNode) { // root 아닐때 진행하겠음.
			Node temp = z->p;
			while (temp != nullptr) {
				temp->size--;
				temp = temp->p;
			}
		}
		

		Node y, x;
		Color originColor = z->color;

		// 1) 자식이 2개가 아닌 경우는 z 바로 삭제
		if (z->left == leafNode) {
			x = z->right;
			transLink(z, z->right); // 왼쪽 자식이 leaf니까 오른쪽 자식을 z부모와 연결(z제거)
		}
		else if (z->right == leafNode) {
			x = z->left;
			transLink(z, z->left);
		}
		// 2) 자식이 2개라면 successor 노드를 찾아서 삭제한다.
		else {
			y = treeMinimum(z->right);
			originColor = y->color;
			x = y->right; // y 왼쪽 자식은 있을수가 없음.(successor 특)

			if (y->p == z) {
				// y부모가 z란건 z의 바로 오른쪽 자식이 y
				x->p = y;
			}
			else {
				transLink(y, y->right); // y제거
				y->right = z->right;
				y->right->p = y;
			}
			transLink(z, y);
			y->left = z->left;
			y->left->p = y;
			y->color = z->color;
			y->size = z->size; // OST Size Copy
		}
		
		delete z; // 메모리 해제
		if (originColor == BLACK)
			treeDeleteFixUp(x);
	}

	//참고
	//case 1 -> 2,3,4
	//case 2 -> 1,2,3,4
	//case 2 -> 2,2,2,2....(루트로 반복적으로 다가감)
	//case 3 -> 4
	//case 4 -> 종료
	void treeDeleteFixUp(Node x) {
		// x가 루트이거나 컬러가 레드이면 바로 종료
		while (x != this->root && x->color == BLACK) {
			// left[p[x]] -> case 1,2,3,4
			// right[p[x]] -> case 5,6,7,8
			if (x == x->p->left) {
				// w는 x의 형제노드
				Node w = x->p->right;

				// case 1 : w는 RED
				if (w->color == RED) {
					w->color = BLACK;
					x->p->color = RED;
					leftRotate(x->p);
					w = x->p->right;
				}
				// case 2 : w는 BLACK && W의 자식들도 BLACK
				//if (w->color == BLACK && w->left->color == BLACK && w->right->color == BLACK) {
				if (w->left->color == BLACK && w->right->color == BLACK) {
					w->color = RED;
					x = x->p;
				}
				else {
					// case 3 : w는 BLACK && w의 왼쪽자식이 RED
					//if (w->color == BLACK && w->right->color == BLACK) {
					if (w->right->color == BLACK) {
						w->left->color = BLACK;
						w->color = RED;
						rightRotate(w);
						w = x->p->right;
					}
					// case 4 : w는 BLACK && w의 오른쪽자식이 RED
					//if (w->color == BLACK && w->right->color == RED) {
					w->color = x->p->color;
					x->p->color = BLACK;
					w->right->color = BLACK;
					leftRotate(x->p);
					x = this->root; // case 4 때는 종료!!
				//}
				}
			}
			else { // 대칭 구조이다.
				// w는 x의 형제노드
				Node w = x->p->left;

				// case 5 : w는 RED
				if (w->color == RED) {
					w->color = BLACK;
					x->p->color = RED;
					rightRotate(x->p);
					w = x->p->left;
				}
				// case 6 : w는 BLACK && W의 자식들도 BLACK
				//if (w->color == BLACK && w->left->color == BLACK && w->right->color == BLACK) {
				if (w->left->color == BLACK && w->right->color == BLACK) {
					w->color = RED;
					x = x->p;
				}
				else {
					// case 7 : w는 BLACK && w의 오른쪽자식이 RED
					//if (w->color == BLACK && w->left->color == BLACK) {
					if (w->left->color == BLACK) {
						w->right->color = BLACK;
						w->color = RED;
						leftRotate(w);
						w = x->p->left;
					}
					// case 8 : w는 BLACK && w의 왼쪽자식이 RED
					//if (w->color == BLACK && w->left->color == RED) {
					w->color = x->p->color;
					x->p->color = BLACK;
					w->left->color = BLACK;
					rightRotate(x->p);
					x = this->root; // case 4 때는 종료!!
				//}
				}
			}
		}
		x->color = BLACK;
		this->root->color = BLACK;
	}

	// x, y순으로 노드 그려서 Rotation을 하는걸 그림 그려놓고 코드로 구현
	void leftRotate(Node x) {
		// 1. y노드 선언
		Node y = x->right; 

		// 2. y의 왼쪽 자식을 x의 오른쪽 자식으로 연결
		// "부모 입장"
		x->right = y->left;
		// "자식 입장"
		if (y->left != leafNode) // 회전할때 y왼쪽 자식이 단말노드가 아닐때
		{
			y->left->p = x; // 해당 자식노드는 새로 x노드와 연결
		}

		// 3. 기존 x부모를 새로 올라가는 y와 연결
		// "자식 입장"
		y->p = x->p; // y가 올라가므로 x부모에 연결
		// "부모 입장"
		if (x == this->root) // rotation하는 x가 root였던건지 확인
		{
			root = y;
		}
		else if (x == x->p->left) // x가 root 아니였으면, y와 잘 연결
		{
			x->p->left = y;
		}
		else
		{
			x->p->right = y;
		}

		// 4. y가 부모, x가 자식 관계 연결
		// "부모 입장"
		y->left = x;
		// "자식 입장"
		x->p = y;

		// 4-1.회전에 따라 size도 회전(갱신)
		// x가 자식이므로 x먼저 size 갱신
		x->size = x->left->size + x->right->size + 1;
		y->size = y->left->size + y->right->size + 1;
	}

	// x, y순으로 노드 그려서 Rotation을 하는걸 그림 그려놓고 코드로 구현
	void rightRotate(Node y) {
		// 1. x노드 선언
		Node x = y->left;

		// 2. x의 오른쪽 자식을 y의 왼쪽 자식으로 연결
		// "부모 입장"
		y->left = x->right;
		// "자식 입장"
		if (x->right != leafNode) // 회전할때 x오른쪽 자식이 단말노드가 아닐때
		{
			x->right->p = y; // 해당 자식노드는 새로 y노드와 연결
		}

		// 3. 기존 y부모를 새로 올라가는 x와 연결
		// "자식 입장"
		x->p = y->p; // x가 올라가므로 y부모에 연결
		// "부모 입장"
		if (y == this->root) // rotation하는 y가 root였던건지 확인
		{
			root = x;
		}
		else if (y == y->p->left) // y가 root 아니였으면, x와 잘 연결
		{
			y->p->left = x;
		}
		else
		{
			y->p->right = x;
		}

		// 4. x가 부모, y가 자식 관계 연결
		// "부모 입장"
		x->right = y;
		// "자식 입장"
		y->p = x;

		// 4-1.회전에 따라 size도 회전(갱신)
		// y가 자식이므로 y먼저 size 갱신
		y->size = y->left->size + y->right->size + 1;
		x->size = x->left->size + x->right->size + 1;
	}

	// min
	Node treeMinimum(Node z) {
		while (z->left != leafNode)
			z = z->left;
		return z;
	}

	// search
	Node treeSearch(int x) {
		// x값이 없는 경우는 논외로 생각하고 진행
		Node cur = this->root;
		Node prev = nullptr;

		while (cur != leafNode && cur->key != x) {
			prev = cur;
			if (x < prev->key) cur = cur->left;
			else cur = cur->right;
		}
		return cur;
	}

	void transLink(Node y, Node x) // x를 y부모에 연결해서 y제거 함수
	{
		if (y->p == nullptr)
			this->root = x;
		else if (y == y->p->left)
			y->p->left = x;
		else
			y->p->right = x;

		x->p = y->p;
	}



	// OST 부분 함수 !!
	// OSselect(x, k)->순위 k의 데이터를 검색(데이터)
	// OSrank(T, x)->데이터의 순위를 계산(순위)
	Node OSselect(Node x, int k) { // x는 루트노드
		int r = x->left->size + 1; // 최종적으로 왼쪽자식 size가 k-1 순위
		if (r == k) return x;
		else if (r > k) OSselect(x->left, k);
		else OSselect(x->right, k - r);
	}

	int OSrank(Node x) {
		int r = x->left->size + 1;
		Node y = x;
		while (y != this->root) {
			if (y == y->p->right)
				r += y->p->left->size + 1;
			y = y->p;
		}
		return r;
	}

	// getter
	Node rootNode() { return this->root; }

};

int main() {
	ifstream fin("2.inp");
	ofstream fout("2.out");

	char inChar = '\0';
	int inInt = 0;

	RBT rbt;

	while (true) {
		fin >> inChar >> inInt;
		if (inInt == -1) break;

		if (inChar == 'i') {
			rbt.treeInsert(inInt);
		}
		else if (inChar == 'd') {
			// search 먼저.... 그리고 delete실행..
			Node curNode = rbt.treeSearch(inInt);
			rbt.treeDelete(curNode);
		}
		else if (inChar == 'c') {
			// search 먼저... 이후 색 출력
			Node curNode = rbt.treeSearch(inInt);
			if (curNode->color == 0) {
				fout << "color(" << inInt << "): " << "RED" << '\n';
				cout << "color(" << inInt << "): " << "RED" << '\n';
			}
			else {
				fout << "color(" << inInt << "): " << "BLACK" << '\n';
				cout << "color(" << inInt << "): " << "BLACK" << '\n';
			}

			// OST 부분의 함수 출력
			// 입력받은 노드의 랭크를 구하고, 그 랭크에 해당하는 노드를 구한다.
			// 당연히 입력받은 노드와 구한 노드는 동일할거고 동일하면 잘 동작!!
			// 동일여부는 key가 동일한지로 비교하겠음
			int ranks = rbt.OSrank(curNode);
			Node ranksNode = rbt.OSselect(rbt.rootNode(), ranks);
			cout << "inputRank : " << ranks << '\n';
			cout << "inputNodeKey : "<<curNode->key<<"\t"<<ranks << "rankNodeKey : " << ranksNode->key << '\n';
		}
	}

	// 메모리 해제
	fin.close();
	fout.close();
	return 0;
}
```

<br><br>

# Splay Tree

**`BST + Splay` 형태로 구현했기 때문에, 위에서 보여줬던 `BST` 코드에 추가해서 구현하면 된다.**

**따라서 아래 코드는 Splay 부분만 언급하겠다.**

* 앞에서 언급한 BST 코드에서 Splay 부분도 같이 구현되어 있으니 참고

```c++
/*
* Splay Tree는 기본적으로 균형트리
* 다른점은 RBT와 같은 노드의 색같이 추가 정보가 필요로 하지 않다는 점.
* 다만, 매 연산마다 트리 구조를 재구성 하는 방식으로 진행

기본 동작으론 루트로 보낼 특정 노드를 회전을 통해서 루트로 올린다.
근데, 아래 case1~3개를 지켜서 올리면 더욱 안정적인 트리구조를 형성 가능.

case1 - Zig step
case2 - Zig-Zag step
case3 - Zig-Zig step

Splay 작업은 연산 수행후 매번 노드 적용
Search -> 검색 조차 x찾고, key x 노드를 splay
Insert -> key x를 insert하고 해당 노드에 splay
delete -> key x 노드를 delete하고, 해당 노드 부모를 splay

따라서 BST에 + splay를 하는 형태라 splay만 구현해보겠다.
https://gowoonsori.com/data-structure/tree/splay-tree/

여기선 splay함수형태만
*/

void Splay(Node x) {
	// x가 root가 될때까지
	while (x->p != nullptr) {
		Node y = x->p; // y는 x의 부모

		// zig
		if (y->p == nullptr) { // x의 부모 노드가 root라면
			if (y->left == x)
				rightRotate(y);
			else
				leftRotate(y);
		}
		// zig zag
		else if (y->left == x && y->p->right == y) {
			rightRotate(y);
			leftRotate(y);
		}
		else if (y->right == x && y->p->left == y) {
			leftRotate(y);
			rightRotate(y);
		}
		// zig zig
		else if (y->left == x && y->p->left == y) {
			rightRotate(y->p);
			rightRotate(y);
		}
		else if (y->right == x && y->p->right == y) {
			leftRotate(y->p);
			leftRotate(y);
		}
	}
}
```

<br><br>

# OST(Order Statistic Tree)

**앞에 소개한 RBT 코드에 아래 코드를 추가해서 구현하면 된다.**

**RBT코드가 너무 길어서 OST코드만 아래에 나타내겠으며, RBT코드에는 size부분만 추가해주면 된다.**

* 앞에서 언급한 RBT 코드에서 OST 부분도 같이 구현되어 있으니 참고
* delete 부분에서 size 갱신할때 사실 높이 h만큼 복잡도가 추가로 낭비되게 작성해서 해당 코드가 효율적이진 않다는점 참고..

```c++
/*
Order Statistic Tree(OST)
기본적으로 RBT 기반 => 노드에 size 가 추가된 개념
즉, x->size = x->left->size + x->right->size + 1

OSselect(x,k) -> 순위 k의 데이터를 검색 (데이터)
OSrank(T,x) -> 데이터의 순위를 계산 (순위)

insert, delete의 경우??
insert는 지나간 모든 노드의 size값 1증가
delete는 삭제노드의 부모부터 루트까지 size값 1감소
마지막에 트리구조를 바꾸는 회전을 진행할때는 연산에따라 size값 변경
=> 즉, 이곳은 rotate함수 부분 수정

따라서 insert, delete, rotate함수 수정되고, 수정되는 부분들도 간략히 코드로 나타내겠다.
*/

struct node {
	int key;
	Color color = BLACK;
	node* left = nullptr;
	node* right = nullptr;
	node* p = nullptr;
	int size; // OST 위해 선언 (leafNode는 size:0)
};
typedef node* Node;

Node OSselect(Node x, int k) { // x는 루트노드
	int r = x->left->size + 1; // 최종적으로 왼쪽자식 size가 k-1 순위
	if (r == k) return x;
	else if (r > k) OSselect(x->left, k);
	else OSselect(x->right, k - r);
}

int OSrank(Node x) {
	int r = x->left->size + 1;
	Node y = x;
	while (y != this->root) {
		if (y == y->p->right)
			r += y->p->left->size + 1;
		y = y->p;
	}
	return r;
}

void treeInset(int key){
    // z는 insert되는 노드이다.
    z->size = z->left->size + z->right->size + 1; // subtree의 모든 노드 개수
    // 코드생략...
    
    // 1. 검색은 항상 root부터 (이 시점의 y는 root)
    Node x = y; // y값을 null로 만들지 않기 위해 사용할 변수
    while (x != this->leafNode) {
        y = x;
        if (y->key > key) x = x->left;
        else x = x->right;
        y->size++; // 지나가는 모든 노드들 size값 증가@@@@@
    }
    
    // 코드생략...
}

void treeDelete(Node z) {
    // OST를 위해 size 갱신 먼저 하겠다.
    if (z->p != leafNode) { // root 아닐때 진행하겠음.
        Node temp = z->p;
        while (temp != nullptr) {
            temp->size--;
            temp = temp->p;
        }
    }
    
    // 코드생략 ...
}

void leftRotate(Node x) {
    // 코드생략 ...
    
    // 4-1.회전에 따라 size도 회전(갱신)
    // x가 자식이므로 x먼저 size 갱신
    x->size = x->left->size + x->right->size + 1;
    y->size = y->left->size + y->right->size + 1;
}

void rightRotate(Node y) {
    // 코드생략 ...
    
    // 4-1.회전에 따라 size도 회전(갱신)
    // y가 자식이므로 y먼저 size 갱신
    y->size = y->left->size + y->right->size + 1;
    x->size = x->left->size + x->right->size + 1;
}
```

<br><br>

# Range Tree

**BST + 균형트리 기반이며, BST나 RBT에 추가로 만든게 아니고 따로 직접 구현하였다.**

**트리의 자세한 구조는 개념 부분 게시물 참고@@**

```c++
/*
1차원 Range Tree => 2차원은 그냥 이해하고 넘어가자.
균형트리 기반

Range query - 어떤 구간에 있는 원소에 대해 계산하는 문제(종류 2가지)
- range reporting : 계산 또는 reporting
- range counting : 개수 계산 => 이거 때문에 node에 단말노드 개수 기록

이 Range query를 다루는 자료구조가??? => range tree

insert, delete는 제외하고 Range query 계산 2개만 구현해본다.
물론 초기 input으로 받는 배열은 바로 Range Tree 구조 구성
=> 따로 build함수 만들겠다.
*/

#include<iostream>
#include<vector>
using namespace std;

struct node {
	int key;
	int count;
	node* left = nullptr;
	node* right = nullptr;
};
typedef node* Node;

class RT {
private:
	Node root;
public:
	RT(int inArr[], int size) {
		root = new node();
		root->count = size;
		Node y = new node();
		Node z = new node();
		int keyIndex = 0;
		if (size % 2 == 0) keyIndex = size / 2 - 1; // 짝수
		else keyIndex = size / 2; // 홀수

		root->key = inArr[keyIndex];
		root->left = y;
		buildRT(y, 0, keyIndex, inArr); // 왼 서브트리
		root->right = z;
		buildRT(z, keyIndex + 1, size - 1, inArr); // 오 서브트리
	}

	// x1, x2는 index값으로 넘긴것
	// 대신 비교는 size 크기로 한다는 점
	void buildRT(Node x, int x1, int x2, int inArr[]) {
		Node y = new node();
		Node z = new node();
		int keyIndex = 0;
		int size = (x2 - x1) + 1; // +1까지 해줘야 size 나옴
		if (size % 2 == 0) keyIndex = size / 2 - 1;
		else keyIndex = size / 2;
		keyIndex += x1; // 자리 맞춤

		if (size <= 1) { // 원소 1개라면(단말 노드라는 것)
			x->key = inArr[keyIndex]; // 단말 노드도 기록
			x->count = size; // 단말노드 개수 1개
			return; // 종료 시점!!
		}

		x->key = inArr[keyIndex];
		x->count = size; // 단말노드 개수 기록
		x->left = y;
		buildRT(y, x1, keyIndex, inArr);
		x->right = z;
		buildRT(z, keyIndex + 1, x2, inArr);
	}

	// 어쩌다보니 만들었는데 검색함수 필요없음.
	Node search(int key) {
		Node x = root;
		while (true) {
			// 단말 노드인 경우 종료
			if (x->left == nullptr && x->right == nullptr) break;

			if (x->key < key) x = x->right;
			else x = x->left;
		}
		// key값 찾은 경우 바로 반환
		if (key == x->key) return x;
		else nullptr;
	}

	// x1, x2는 구간정보
	vector<Node> reporting(int x1, int x2) {
		vector<Node> vc;
		Node x = root;
		// split 노드 구하기(무조건 있다고 가정하겠음)
		while (true) {
			// 구간내에 있을경우 탈출
			if (x->key >= x1 && x->key <= x2) break;

			if (x->key < x1) x = x->right;
			else if (x->key > x2) x = x->left;
		}

		// 왼쪽 구간 구하기
		Node y = x->left; // y : tl
		while (y != nullptr) {
			// 단말 노드인 경우 종료
			if (y->left == nullptr && y->right == nullptr) {
				if (y->key >= x1) vc.push_back(y); // tl도 포함인지 확인
				break;
			}

			if (y->key > x1) {
				vc.push_back(y->right);
				y = y->left;
			}
			else {
				y = y->right;
			}
		}

		// 오른쪽 구간 구하기
		Node z = x->right; // z : tr
		while (z != nullptr) {
			// 단말 노드인 경우 종료
			if (z->left == nullptr && z->right == nullptr) {
				if (z->key <= x2) vc.push_back(z); // tr도 포함인지 확인
				break;
			}

			if (z->key < x2) {
				vc.push_back(z->left);
				z = z->right;
			}
			else {
				z = z->left;
			}
		}
		return vc;
	}

	// x1, x2는 구간정보
	int counting(int x1, int x2) {
		vector<Node> vc;
		vc = this->reporting(x1, x2);
		int counts = 0;
		for (int i = 0; i < vc.size(); i++) {
			counts += vc[i]->count;
		}
		return counts;
	}
};

int main() {
	// input이 오름차순
	int inArr[20] = { 2, 3, 5, 9, 14, 17, 21, 24, 27, 29, 35, 47, 58, 64 };
	RT rt = RT(inArr, 14); // RT 구조 생성

	// range reporting
	vector<Node> vr;
	vr = rt.reporting(28, 60); // 28,60 구간에 속하는 원소 구하는 테스트(각각 대표하는 노드만 구하겠음!!)
	for (int i = 0; i < vr.size(); i++)
		cout << vr[i]->key << ' ';
	cout << '\n'; // 35, 29, 47 나왔으면 정답. 실제론 노드 4개인데, 47노드엔 left:47, right:58 노드까지 포함한거임(3개 노드인 이유는 대표하는 노드만 출력했기 때문)

	// range counting
	int counts = rt.counting(28, 60);
	cout << counts; // 4가 나왔으면 정답.

	return 0;
}
```

<br><br>

# Interval Tree

**앞에서 소개한 RBT 코드에 추가해서 구현하려고 하는 Interval Tree이다.  **
**추가해서 구현했기 때문에 기존 RBT에서 쓰이는 함수들이 많고, 굳이 제거하진 않겠다.**

**RBT 코드가 길긴 하지만 이번에는 전체 코드를 소개하겠다.**

* insert 부분에서 max 갱신할때 사실 높이 h만큼 복잡도가 추가로 낭비되게 작성해서 해당 코드가 효율적이진 않다는점 참고..!
* max는 오른쪽 구간일 때 더 내려갈지 판단 용도(좀 더 효율적)

<br>

**파일 입출력 예시(Color는 무시)**

<img src="/images/2023-04-01-(구현) 고급 트리 구현(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230422192737284.png" alt="image-20230422192737284" style="zoom:67%;" />  <img src="/images/2023-04-01-(구현) 고급 트리 구현(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230422192758659.png" alt="image-20230422192758659" style="zoom:67%;" /> 

<br>

**코드**

```c++
/*
Interval Tree for Point Querying -> 어지럽다. (이건 pass)
Interval Tree 구현(RBT 같은 균형트리 기반이다.)
RBT+구간을 기록해서 구현하면 충분!
search하는 부분만 중요해보인다.
=> 하나라도 겹치는지 확인하는 것!! 겹치는 구간 찾아주면 됨.

insert는 그냥 기존 RBT 노드 정보에 폐구간 정보만 추가해보겠음.
intervalSearch 부분만 따로 만들어 보겠다.
max값 갱신은 insert에서 하고, 회전함수에서도 따로 추가로 갱신한다
*/

#include <iostream>
#include <fstream>
using namespace std;

enum Color { RED, BLACK };

struct node {
	int key;
	Color color = BLACK;
	node* left = nullptr;
	node* right = nullptr;
	node* p = nullptr;
	int size; // OST 위해 선언 (leafNode는 size:0)
	// Interval Tree 위해 선언
	int intervalArr[2]; 
	int max;
};
typedef node* Node;

class RBT {
private:
	Node root = nullptr;
	Node leafNode = nullptr;
public:
	// 생성자
	//RBT(int inArr[], int inArrSize) {
		//this->leafNode = new node();
		//this->root = leafNode;
		//for (int i = 0; i < inArrSize; i++) {
			//treeInsert(inArr[i]);
		//}
	//}
	RBT() {
		this->leafNode = new node();
		this->root = leafNode; // 초기 root는 null을 의미
	}

	// insert
	void treeInsert(int inLow, int inHigh) {
		Node y = this->root; // 삽입할 z의 부모로 사용하려고 선언한 y노드
		Node z = new node();
		z->key = inLow;
		z->color = RED; // insert때 초기값은 RED인 특징
		z->left = this->leafNode; // insert때 자식 초기값은 단말노드
		z->right = this->leafNode;
		z->size = z->left->size + z->right->size + 1; // subtree의 모든 노드 개수
		z->intervalArr[0] = inLow;
		z->intervalArr[1] = inHigh;
		z->max = inHigh;

		// 0. 빈 Tree인지 먼저 체크 (root가 null인지 체크하면 됨)
		if (y == this->leafNode) {
			z->color = BLACK; // root노드는 BLACK인 특징
			// z->p = y; // root부모 leafNode 연결
			this->root = z;
			return;
		}

		// 1. 검색은 항상 root부터 (이 시점의 y는 root)
		Node x = y; // y값을 null로 만들지 않기 위해 사용할 변수
		while (x != this->leafNode) {
			y = x;
			if (y->key > inLow) x = x->left;
			else x = x->right;
			y->size++; // 지나가는 모든 노드들 size값 증가
		}

		// 2-1. 자식 -> 부모 연결
		z->p = y;
		// 2-2. 부모 -> 자식 연결
		if (y->key > z->key)
			y->left = z;
		else
			y->right = z;
		// 일반적인 BST insert 과정 끝(기존 BST코드에 leafNode만 활용했음)

		// red-red 문제 해결
		treeInsertFixUp(z);

		
		// 3. root까지 max값 갱신(회전함수에서도 따로 max 갱신함)
		while (z->p != nullptr) {
			int temp = z->p->max;
			temp = max(temp, z->max);
			z->p->max = temp; // update
			z = z->p;
		}

	}

	//참고
	//case 1 -> case 2 or case 3
	//case 2 -> case 3
	//case 3 -> 끝
	void treeInsertFixUp(Node z) {
		// 이제부터 복잡(r-r문제 해결)
		// 탈출 시점은? root일때 or p(z)가 red아닐 때 탈출.(z->p가 red아니면 red-red문제가 아니니까)
		// 반드시!! z가 root인지 확인을 먼저 조건식에 적어야한다!! 왜냐하면 z가 root인 상황인 상태에서
		// p(z)의 RED확인이 먼저 나올경우, p(z)는 단말노드(leafNode) 이기 때문에 color가 존재하지 않아서 에러가 뜨기 때문이다.
		while (z != this->root && z->p->color == RED) {
			Node grandparent, uncle = nullptr; // 할아버지, 삼촌 노드
			bool check = false; // 대칭판단(4,5,6도 구현위해서)

			grandparent = z->p->p;
			if (z->p == grandparent->left) {
				uncle = grandparent->right;
				check = true;
			}
			else {
				uncle = grandparent->left;
				check = false;
			}

			// z의 부모가 할아버지 왼쪽 자식인 경우 - case1,2,3
			// z의 부모가 할아버지 오른쪽 자식인 경우 - case4,5,6
			// => 대칭적으로 다를 뿐 구조는 동일(4,5,6 설명은 생략)

			// case 1 => 삼촌이 RED인 경우
			if (uncle->color == RED) {
				// 현재상태 : 할아버지 BLACK, z부모 RED, z삼촌 RED
				// 따라서 할아버지를 RED, Z부모 BLACK, z삼촌 BLACK으로 변환
				// 이후 할아버지를 새로운 z로 선언해서 다시 while문 반복
				z->p->color = BLACK;
				uncle->color = BLACK;
				grandparent->color = RED;
				z = grandparent;
			}
			// case 2,3 => 삼촌이 BLACK인 경우
			// 참고 : case 5,6인 대칭도 같이 고려
			else {
				// case 2 => z가 z의 부모의 오른쪽 자식 - LEFT ROTATE 로 case 3!!
				if (check && z == z->p->right ||
					!check && z == z->p->left) {

					z = z->p;
					if (check) leftRotate(z);
					else rightRotate(z);
				}
				// case 3 => z가 z의 부모의 왼쪽 자식 - RIGHT ROTATE 로 해결
				z->p->color = BLACK; // 부모 R->B
				grandparent->color = RED; // 할아버지 B->R
				if (check) rightRotate(grandparent);
				else leftRotate(grandparent);

			}
		}
		this->root->color = BLACK; // root는 항상 BLACK!!
	}

	void treeDelete(Node z) {
		// OST를 위해 size 갱신 먼저 하겠다.
		if (z->p != leafNode) { // root 아닐때 진행하겠음.
			Node temp = z->p;
			while (temp != nullptr) {
				temp->size--;
				temp = temp->p;
			}
		}


		Node y, x;
		Color originColor = z->color;

		// 1) 자식이 2개가 아닌 경우는 z 바로 삭제
		if (z->left == leafNode) {
			x = z->right;
			transLink(z, z->right); // 왼쪽 자식이 leaf니까 오른쪽 자식을 z부모와 연결(z제거)
		}
		else if (z->right == leafNode) {
			x = z->left;
			transLink(z, z->left);
		}
		// 2) 자식이 2개라면 successor 노드를 찾아서 삭제한다.
		else {
			y = treeMinimum(z->right);
			originColor = y->color;
			x = y->right; // y 왼쪽 자식은 있을수가 없음.(successor 특)

			if (y->p == z) {
				// y부모가 z란건 z의 바로 오른쪽 자식이 y
				x->p = y;
			}
			else {
				transLink(y, y->right); // y제거
				y->right = z->right;
				y->right->p = y;
			}
			transLink(z, y);
			y->left = z->left;
			y->left->p = y;
			y->color = z->color;
			y->size = z->size; // OST Size Copy
		}

		delete z; // 메모리 해제
		if (originColor == BLACK)
			treeDeleteFixUp(x);
	}

	//참고
	//case 1 -> 2,3,4
	//case 2 -> 1,2,3,4
	//case 2 -> 2,2,2,2....(루트로 반복적으로 다가감)
	//case 3 -> 4
	//case 4 -> 종료
	void treeDeleteFixUp(Node x) {
		// x가 루트이거나 컬러가 레드이면 바로 종료
		while (x != this->root && x->color == BLACK) {
			// left[p[x]] -> case 1,2,3,4
			// right[p[x]] -> case 5,6,7,8
			if (x == x->p->left) {
				// w는 x의 형제노드
				Node w = x->p->right;

				// case 1 : w는 RED
				if (w->color == RED) {
					w->color = BLACK;
					x->p->color = RED;
					leftRotate(x->p);
					w = x->p->right;
				}
				// case 2 : w는 BLACK && W의 자식들도 BLACK
				//if (w->color == BLACK && w->left->color == BLACK && w->right->color == BLACK) {
				if (w->left->color == BLACK && w->right->color == BLACK) {
					w->color = RED;
					x = x->p;
				}
				else {
					// case 3 : w는 BLACK && w의 왼쪽자식이 RED
					//if (w->color == BLACK && w->right->color == BLACK) {
					if (w->right->color == BLACK) {
						w->left->color = BLACK;
						w->color = RED;
						rightRotate(w);
						w = x->p->right;
					}
					// case 4 : w는 BLACK && w의 오른쪽자식이 RED
					//if (w->color == BLACK && w->right->color == RED) {
					w->color = x->p->color;
					x->p->color = BLACK;
					w->right->color = BLACK;
					leftRotate(x->p);
					x = this->root; // case 4 때는 종료!!
				//}
				}
			}
			else { // 대칭 구조이다.
				// w는 x의 형제노드
				Node w = x->p->left;

				// case 5 : w는 RED
				if (w->color == RED) {
					w->color = BLACK;
					x->p->color = RED;
					rightRotate(x->p);
					w = x->p->left;
				}
				// case 6 : w는 BLACK && W의 자식들도 BLACK
				//if (w->color == BLACK && w->left->color == BLACK && w->right->color == BLACK) {
				if (w->left->color == BLACK && w->right->color == BLACK) {
					w->color = RED;
					x = x->p;
				}
				else {
					// case 7 : w는 BLACK && w의 오른쪽자식이 RED
					//if (w->color == BLACK && w->left->color == BLACK) {
					if (w->left->color == BLACK) {
						w->right->color = BLACK;
						w->color = RED;
						leftRotate(w);
						w = x->p->left;
					}
					// case 8 : w는 BLACK && w의 왼쪽자식이 RED
					//if (w->color == BLACK && w->left->color == RED) {
					w->color = x->p->color;
					x->p->color = BLACK;
					w->left->color = BLACK;
					rightRotate(x->p);
					x = this->root; // case 4 때는 종료!!
				//}
				}
			}
		}
		x->color = BLACK;
		this->root->color = BLACK;
	}

	// x, y순으로 노드 그려서 Rotation을 하는걸 그림 그려놓고 코드로 구현
	void leftRotate(Node x) {
		// 1. y노드 선언
		Node y = x->right;

		// 2. y의 왼쪽 자식을 x의 오른쪽 자식으로 연결
		// "부모 입장"
		x->right = y->left;
		// "자식 입장"
		if (y->left != leafNode) // 회전할때 y왼쪽 자식이 단말노드가 아닐때
		{
			y->left->p = x; // 해당 자식노드는 새로 x노드와 연결
		}

		// 3. 기존 x부모를 새로 올라가는 y와 연결
		// "자식 입장"
		y->p = x->p; // y가 올라가므로 x부모에 연결
		// "부모 입장"
		if (x == this->root) // rotation하는 x가 root였던건지 확인
		{
			root = y;
		}
		else if (x == x->p->left) // x가 root 아니였으면, y와 잘 연결
		{
			x->p->left = y;
		}
		else
		{
			x->p->right = y;
		}

		// 4. y가 부모, x가 자식 관계 연결
		// "부모 입장"
		y->left = x;
		// "자식 입장"
		x->p = y;

		// 4-1.회전에 따라 size도 회전(갱신)
		// x가 자식이므로 x먼저 size 갱신
		x->size = x->left->size + x->right->size + 1;
		y->size = y->left->size + y->right->size + 1;

		// 4-2.회전에 따라 max값 갱신
		// x만 max비교 필요
		y->max = max(y->max,x->max);
		int temp = x->intervalArr[1];
		temp = max(temp, x->right->max);
		temp = max(temp, x->left->max);
		x->max = temp;
		
	}

	// x, y순으로 노드 그려서 Rotation을 하는걸 그림 그려놓고 코드로 구현
	void rightRotate(Node y) {
		// 1. x노드 선언
		Node x = y->left;

		// 2. x의 오른쪽 자식을 y의 왼쪽 자식으로 연결
		// "부모 입장"
		y->left = x->right;
		// "자식 입장"
		if (x->right != leafNode) // 회전할때 x오른쪽 자식이 단말노드가 아닐때
		{
			x->right->p = y; // 해당 자식노드는 새로 y노드와 연결
		}

		// 3. 기존 y부모를 새로 올라가는 x와 연결
		// "자식 입장"
		x->p = y->p; // x가 올라가므로 y부모에 연결
		// "부모 입장"
		if (y == this->root) // rotation하는 y가 root였던건지 확인
		{
			root = x;
		}
		else if (y == y->p->left) // y가 root 아니였으면, x와 잘 연결
		{
			y->p->left = x;
		}
		else
		{
			y->p->right = x;
		}

		// 4. x가 부모, y가 자식 관계 연결
		// "부모 입장"
		x->right = y;
		// "자식 입장"
		y->p = x;

		// 4-1.회전에 따라 size도 회전(갱신)
		// y가 자식이므로 y먼저 size 갱신
		y->size = y->left->size + y->right->size + 1;
		x->size = x->left->size + x->right->size + 1;

		// 4-2.회전에 따라 max값 갱신
		// y만 max비교 필요
		x->max = max(x->max,y->max);
		int temp = y->intervalArr[1];
		temp = max(temp, y->right->max);
		temp = max(temp, y->left->max);
		y->max = temp;
	}

	// min
	Node treeMinimum(Node z) {
		while (z->left != leafNode)
			z = z->left;
		return z;
	}

	// search
	Node treeSearch(int x) {
		// x값이 없는 경우는 논외로 생각하고 진행
		Node cur = this->root;
		Node prev = nullptr;

		while (cur != leafNode && cur->key != x) {
			prev = cur;
			if (x < prev->key) cur = cur->left;
			else cur = cur->right;
		}
		return cur;
	}

	void transLink(Node y, Node x) // x를 y부모에 연결해서 y제거 함수
	{
		if (y->p == nullptr)
			this->root = x;
		else if (y == y->p->left)
			y->p->left = x;
		else
			y->p->right = x;

		x->p = y->p;
	}



	// OST 부분 함수 !!
	// OSselect(x, k)->순위 k의 데이터를 검색(데이터)
	// OSrank(T, x)->데이터의 순위를 계산(순위)
	Node OSselect(Node x, int k) { // x는 루트노드
		int r = x->left->size + 1; // 최종적으로 왼쪽자식 size가 k-1 순위
		if (r == k) return x;
		else if (r > k) OSselect(x->left, k);
		else OSselect(x->right, k - r);
	}

	int OSrank(Node x) {
		int r = x->left->size + 1;
		Node y = x;
		while (y != this->root) {
			if (y == y->p->right)
				r += y->p->left->size + 1;
			y = y->p;
		}
		return r;
	}

	// getter
	Node rootNode() { return this->root; }



	// interval 관련 함수
	bool intervalSearch(Node x, int inLow, int inHigh) {
		// 아래와 같은 순서로 진행 (root부터 시작)
		// 1. a,b,c,d 관계로 왼쪽 or 오른쪽 서브트리 결정
		// 2. max값으로 해당 서브트리에 내려갈지 말지를 결정
		
		if (this->root == leafNode) return false;
		int a = x->intervalArr[0];
		int b = x->intervalArr[1];
		int c = inLow;
		int d = inHigh;

		if ((a <= c && c <= b) || (a <= d && d <= b)) return true;
		else { // 구간 내에 겹치지 않는 경우(왼쪽 or 오른쪽에 구간 존재)
			if (c < a) {
				intervalSearch(x->left, inLow, inHigh);
			}
			else if (c > b) {
				// 오른쪽 구간은 max 비교로 더 내려갈지 결정 가능
				if (x->max < d) return false;
				intervalSearch(x->right, inLow, inHigh);
			}
		}
	}

};

int main() {
	ifstream fin("3.inp");
	ofstream fout("3.out");

	char inChar = '\0';
	int inLow = 0;
	int inHigh = 0;

	RBT rbt;

	while (true) {
		fin >> inChar >> inLow >> inHigh;
		if (inLow == -1) break;

		if (inChar == 'i') {
			rbt.treeInsert(inLow, inHigh);
		}
		else if (inChar == 'd') {
			// search 먼저.... 그리고 delete실행..
			Node curNode = rbt.treeSearch(inLow);
			rbt.treeDelete(curNode);
		}
		else if (inChar == 'c') {
			// search 먼저... 이후 색 출력
			Node curNode = rbt.treeSearch(inLow);
			if (curNode->color == 0) {
				fout << "color(" << inLow << "): " << "RED" << '\n';
				cout << "color(" << inLow << "): " << "RED" << '\n';
			}
			else {
				fout << "color(" << inLow << "): " << "BLACK" << '\n';
				cout << "color(" << inLow << "): " << "BLACK" << '\n';
			}
			
			if (rbt.intervalSearch(rbt.rootNode(), inLow, inHigh)) {
				cout << inLow << "," << inHigh<< " 구간 존재O"<< '\n';
			}
			else {
				cout << inLow << "," << inHigh << " 구간 존재X" << '\n';
			}
		}
	}

	// 메모리 해제
	fin.close();
	fout.close();
	return 0;
}
```

<br><br>

# Segment Tree

**대망의 마지막 Tree......ㅠㅠ**  
**이번엔 다른 분의 코드를 보고 구현해보려고 한다.**

**참고 : https://m.blog.naver.com/ndb796/221282210534**

<br>

```c++
/*
Segment Tree for Intervals -> 어지럽다. (이건 pass)
Segment Tree 구현
https://m.blog.naver.com/ndb796/221282210534
=> 이거 코드 참고해서 구현해보기

이번엔 해당 블로그처럼 array 로 트리를 구성해보겠음!! 
이때까지 계속 class로 구현했움 , ,

Segment Tree 만드는 segmentBuild 함수와
구간 합 찾아주는 segmentSum 함수와
값 수정해주는 segmentUpdate 함수를 만들어 보겠다!.!
*/

#include<iostream>
#include<vector>
using namespace std;

int outArr[100];

int segmentBuild(int inArr[], int start, int end, int node) {
	if (start == end) {
		outArr[node] = inArr[start];
		return outArr[node]; // 종료시점
	}

	int mid = (start + end) / 2; // mid 구해서 왼, 오 구간 나눔
	outArr[node] = segmentBuild(inArr,start, mid, node * 2) + segmentBuild(inArr, mid + 1, end, node * 2 + 1);	
	return outArr[node]; // 끝
}


// 구하는 합 구간 left, right
// node가 담당하는 구간 start, end
int segmentSum(int start, int end, int node, int left, int right) {
	// 1. 겹치지 않는 경우 (왼쪽이거나 오른쪽 구간인경우)
	if ((left < start && right < start) || (left > end && right > end)) return 0;
	// 2. left, right가 start, end를 완전히 포함하는 경우
	if (left<=start && right >=end) return outArr[node];
	
	// 3. 나머지 경우 (자식 트리 탐색)
	int mid = (start + end) / 2;
	return segmentSum(start, mid, node * 2, left, right) + segmentSum(mid + 1, end, node * 2 + 1, left, right);
}

// node가 담당하는 구간 start, end
// 수정할 노드 index, 수정할 값 dif
void segmentUpdate(int start, int end, int node, int index, int dif) {
	// 1. 겹치지 않는 경우 (왼쪽이거나 오른쪽 구간인경우)
	if ((start < index && end < index) || (start > index && end > index)) return ;
	// 2. left, right가 start, end를 완전히 포함하는 경우
	if (start <= index && index <= end) outArr[node] += dif;
	// 무조건 종료시점(무한루프 안걸리게)
	if (start == end) return;
	// 3. 나머지 경우 (자식 트리 탐색)
	int mid = (start + end) / 2;
	segmentUpdate(start, mid, node * 2, index, dif);
	segmentUpdate(mid + 1, end, node * 2 + 1, index, dif);
}


int main() {
	// 1. 초기 입력 - 트리 만들기
	int inArr[12] = { 1,9,3,8,4,5,5,9,10,3,4,5 }; // 12개
	segmentBuild(inArr, 0, 11, 1); // outArr 수정(index 1부터 저장)

	// 2. 구간 합 구하기
	// 초기값은 node 1때 start, end범위랑 구하려는 구간 합 left, right 범위
	cout << "4~8 범위 : "<< segmentSum(0, 11, 1, 4, 8)<<'\n'; // 33이 나와야함.

	// 3. 특정 원소 값 수정
	// 연쇄적으로 수정된다. (범위 안에 있는경우 노드들만 수정하면 됨)
	cout << "inArr index 7의 노드값 9에 +8 하기전 outArr[25] : " << outArr[25] << '\n';
	cout << "루트도 연쇄적으로 바뀌기 전 outArr[1] : " << outArr[1] << '\n';
	segmentUpdate(0, 11, 1, 7, 8);
	cout << "inArr index 7의 노드값 9에 +8로 한 후 outArr[25] : " << outArr[25] << '\n';
	cout << "루트도 연쇄적으로 바뀐 후 outArr[1] : " << outArr[1] << '\n';

	return 0;
}
```

<br><br>

# Outro

**`BST` 가 제일 기본이 되는 `이진 검색 트리`이므로 제일 중요!!**  
**`RBT` 도 제일 기본이 되는 `이진 검색 트리 + 균형 트리`이므로 제일 중요!!**  
**`Sgement Tree` 도  `구간합` 하는데 잘 사용해서 제일 중요!!**

* `Splay Tree` 는 `RBT` 트리와 동일하며, 차이점은 Red, Black 정보를 이용하지 않는점이다.
  * 좀 더 단순하므로 구현이 더 좋을 수 있지만 그래도 안정적으로 속도가 더 빠른건 `RBT` 이다.
* `OST, Range Tree, Interval Tree` 등 나머지 트리들은 후순위로 기억해두자.
