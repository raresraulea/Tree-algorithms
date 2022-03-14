
#include <iostream>
#include <vector>
#include <queue>
#include <ctime>
#include <cstdlib>
#include <algorithm>
#define NMAX 101

using namespace std;

int directie = -1; //-1 stanga si 1 dreapta

template <class T>
struct node
{
	T data;
	node<T>* left;
	node<T>* right;
};

template <class T>
struct BinaryTree
{
	node<T>* root;
	int* ud; //ultima directie generata pentru fiecare nod -> pastrez arborele echilibrat 

	//constructor cand nu cunosc numarul de noduri
	BinaryTree()
	{
		root = NULL;
		ud = new int[NMAX];
		for (int i = 1; i < NMAX; i++)
		{
			ud[i] = 0;
		}
	}
	//constructor pentru cand cunosc numarul de noduri => economie memorie la tabloul de directii
	BinaryTree(int nrNoduri)
	{
		root = NULL;
		ud = new int[nrNoduri];
		for (int i = 1; i < nrNoduri + 1; i++)
			ud[i] = 0;
	}
	//functie de inserare astfel incat arborele construit sa ramana echilibrat
	void insert(node<T>*& root, T val)
	{
		if (root == NULL)
		{
			node<T>* nodeToInsert = new node<T>();
			nodeToInsert->data = val;
			root = nodeToInsert;

		}
		else
		{
			if (ud[root->data] % 2 == 0)
			{
				ud[root->data]++;
				cout <<  root->data << " ->stanga" << endl;
				insert(root->left, val);
			}
			else
			{
				ud[root->data]++;
				cout << root->data << " -> dreapta" << endl;
				insert(root->right, val);
			}
		}
	}
	//functie de cautare a unei chei in arbore => returneaza true/false
	bool cauta(node<T>* root, T val)
	{
		//VARIANTA 1

		/*if (root == NULL) return false;
		if (root->data == val) return true;
		bool rez_subarboreST = cauta(root->left, val);
		if (rez_subarboreST) return true;
		bool rez_subarboreDR = cauta(root->right, val);
		if (rez_subarboreDR) return true;*/

		//VARIANTA 2

		if (root == NULL) return false;
		if (root->data == val) return true;
		else
			return (cauta(root->left, val) || cauta(root->right, val));
	}
	void traversare_preordine(node<T>* root)
	{
		if (root == NULL) return;
		cout << root->data << ' ';
		traversare_inordine(root->left);
		traversare_inordine(root->right);
	}
	void traversare_inordine(node<T>* root)
	{
		if (root == NULL) return;
		traversare_inordine(root->left);
		cout << root->data << ' ';
		traversare_inordine(root->right);
	}
	void traversare_postordine(node<T>* root)
	{
		if (root == NULL) return;
		taversare_inordine(root->left);
		traversare_inordine(root->right);
		cout << root->data << ' ';
	}
	//functie care returneaza inaltimea arborelui
	int findHeight(node<T>* root)
	{
		//conventional: inaltimea arborelui = numarul maxim de muchii intre radacina si oricare frunza
		//=> inaltimea unui arbore cu un nod = 0 si inaltimea unui arbore nul = -1
		//deci orice  nod frunza va avea inaltimea 0, iar copiii lui NULLI vor avea inaltimea -1
		//pentru a putea returna inaltimea unei frunze ca inaltimea(copil) + 1 
		if (root == NULL) return -1; 

		return max(findHeight(root->left), findHeight(root->right)) + 1;
	}
	//functie de afisare a unui nivel (nodurile nivelului se gasesc in coada parametru)
	void afisare_nivel(queue <node<T>*> q, int nivel)
	{
		cout << "NIVEL " << nivel << ": ";
		while (!q.empty())
		{
			cout << q.front()->data << ' ';
			q.pop();
		}
		cout << endl;
	}
	//functie de afisare a tuturor nivelurilor
	void traversare_niveluri(node<T>* root)
	{
		cout <<endl <<  "TRAVERSARE NIVELURI: " << endl;
		queue<node<T>*> q; //retine nodurile nivelului curent
		queue<node<T>*> q2; //retine copiii nivelului curent
		int nivel = 0;
		q.push(root);

		int height = findHeight(root);

		for (int i = 0; i <= height; i++) // i = nivel curent
		{
			afisare_nivel(q, i);
			//mostenirea copiilor lui q in q2
			while (!q.empty())
			{
				auto crt = q.front();
				if (crt->left != NULL) q2.push(crt->left);
				if (crt->right != NULL) q2.push(crt->right);
				q.pop();
			}
			q = q2;
			q2 = {}; //golire queue C++11
		}
	}
	bool echilibrat(node<T>* root)
	{
		if (root == NULL) return true;

		int left_height = findHeight(root->left);
		int right_height = findHeight(root->right);

		if (abs(left_height - right_height) <= 1 && echilibrat(root->left) && echilibrat(root->right)) 
			return true;

		return false;
	}
	bool strict(node<T>* root)
	{
		if (root == NULL) return true;
		if (root->left && root->right == NULL || root->left == NULL && root->right)
			return false;
		return strict(root->left) && strict(root->right);
	}
	int frunze(node<T>* root)
	{
		if (root == NULL)
			return 0;
		else if (root->left == NULL && root->right == NULL)
			return 1;
		return frunze(root->left) + frunze(root->right);
	}
};

int main()
{
	BinaryTree<int>* BT = new BinaryTree<int>();
	srand(time(NULL));
	int noduri= 10;
	for (int x{}; x < noduri; x++)
	{
		cout << "Inserez " << x + 1 << endl;
		BT->insert(BT->root, x+1);
	}
	
	cout << endl <<  "CAUT 10 VALORI: " << endl;
	for (int i = 0; i < 10; i++)
	{
		int nr = rand() % 20;
		cout.width(2);
		cout << nr << ": " << BT->cauta(BT->root, nr) << endl;
	}
	cout << endl << "ECHILIBRAT: " << BT->echilibrat(BT->root) << endl;
	cout << endl << "STRICT: " << BT->strict(BT->root) << endl; //pentru 2^k - 1 valori introduse e strict
	BT->traversare_niveluri(BT->root);
	cout << endl << "NR FRUNZE: " << endl;
	cout << BT->root->data << " are " << BT->frunze(BT->root) << " frunze" << endl;
	cout << BT->root->left->data << " are " << BT->frunze(BT->root->left) << " frunze" << endl;
}

