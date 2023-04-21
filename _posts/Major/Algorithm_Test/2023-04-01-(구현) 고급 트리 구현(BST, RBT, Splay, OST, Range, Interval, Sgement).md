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



# Intro

**앞서 정리한 고급 트리들을 구현해보겠다.  
구현할 트리 : BST, RBT, Splay, OST, Range, Interval, Sgement**

**C++에서 기본적으로 map자료구조를 제공하는데 이는 Tree구조이며 RBT 기반이다.**  

**이번 포스팅에서는 직접 Tree들을 전부 구현해보려고 한다.**

* 다른 트리들 전부 BST가 제일 기초가되는 구조이므로 BST를 완벽히 이해할것을 권장한다.
* 개념들은 따로 정리했었기 때문에 바로 코드를 나타내겠다.(주석 참고)

<br><br>

# BST(Binary Search Tree)

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
	cout << bst.treeDelete(x3)->key << '\n'; // x3 노드 테스트 (단말 노드)

	return 0;
}
```

<br><br>

# RBT(Red-Black Tree)

<br><br>

