# ECS 36B FINAL STUDY GUIDE

## 1. How parameter can be passed to a function in C++?
### Summary: 3 ways to pass parameter to a function
```c++
// pass by variable
void f(int x); // f only sees a copy of.   
call: f(a);    // It cannot modify value of a.
```
```c++
// pass by pointer
void fp(int *x); // f knows the adress of a. 
call: f(&a);     // It can modify the value of a.
```
```c++
// pass by reference
void fr(int &x); // f knows a reference to a.
call: f(a);      // It can modify the value of a.
```
## 2. How `const` variables and references can be initialized in a class constructor?
- Using an initialization list in the constructor to initialize references and const variables
```c++
//initialize const variaibles
class Value
{ 
  public:
    Value( int x, int y ) : c(y) { i = x; }
    void set(int x) { i = x; }
    int get(void) const { return i; }
    friend ostream& operator << (ostream& os, const Value& v)
    { os << v.i; return os; }
    const int c;
  private:
    int i;
};
```
```c++
// initialize reference
class DataBase;
class Value{
	public:
		Value(Database& db):database(db){}
	private:
		DataBase& database;
		int i;	
};
```

## 3. The accessibility of a base class data member from a member function of a derived class
```c++
class Base{
public:
	int  m_public;  // can be accessed by anybody
protected:
	int  m_protected;  // can be accessed by Base members, friends, and derived classes
private:
	int  m_private;  // can only be accessed by Base members and friends (but not derived classes)
};

class  Derived: public  Base{
public:
	Derived(){
	m_public  =  1;  // allowed: can access public base members from derived class
	m_protected  =  2;  // allowed: can access protected base members from derived class
	m_private  =  3;  // not allowed: can not access private base members from derived class
	}
};

int  main(){
Base base;
base.m_public  =  1;  // allowed: can access public members from outside class
base.m_protected  =  2;  // not allowed: can not access protected members from outside class
base.m_private  =  3;  // not allowed: can not access private members from outside class
}
```
- Private inheritance makes all public and protected members of the base class private in the derived class

## 4. The definitions of different constructors
- A constructor is a member function that is called when an instance of a class is created.
- The constructor can perform any necessary initialization tasks on the object being created.

- The default constructor of a class takes no arguments
- Other constructors can be defined using overloading
```c++
//
// ctors.cpp
//
// Demonstrate the use of a class
// Use of various constructors

#include<iostream>
using namespace std;

class Value
{
  public:
    Value(void) { i = 1; cout << " User-defined default ctor" << endl; }
    Value(int v) { i = v; cout << " ctor with parameters" << endl; }
    Value(const Value& v) { i = v.i; cout << " Copy ctor" << endl; }
    void print(void) { std::cout << i << std::endl; }
    void set(int x) { i = x; }
  private:
    int i;
};

int main()
{
  Value v; // user-defined default constructor: initializes to 1
  v.print();
  Value w(v); // use the default copy constructor
  w.print();
  Value x(4); // use the constructor with parameter
  x.print();
  Value z = x;
  z.print();
}
```
## 5. The concept of  destructor and virtual destructor
- A destructor is a member function that is called when an instance of a class is deleted
- The destructor is used to clean-up any resources that were allocated during the lifetime of the object.
- "When trying to delete a derived object through a base class pointer, and the base class has a non-virtual destructor, the results are undefined"
- Making the base class destructor virtual solves the problem
```c++
// The virtual destructor issue solved using a 
// virtual base class destructor
#include<iostream>
using namespace std;
class A
{
  public:
    A(int i) : val(i) { cout << "A::A called" << endl; }
    int val;
    virtual void print(void)
    { cout << "Value printed using A::print: " << val << endl; }
    virtual ~A(void) { cout << "A::~A called" << endl; }
};

class B : public A
{
  public:
    B(int i) : A(i) { cout << "B::B called" << endl; }
    int x;
    virtual void print(void)
    { cout << "Value printed using B::print: " << val << endl; }
    virtual ~B(void) { cout << "B::~B called" << endl; }
};

int main()
{
  A *pa = new B(3);
  pa->print();
  delete pa;
}

```
## 6. The definition of a default assignment operator
- A default assignment operator (=) is defined by the compiler 
- It assigns each data member using the typical default operators for simple data types
- It may not make sense for pointer members
- The default assignment operator copies all data members
```c++
class string{
	char *p; //string representation
};
string s1, s2;
s2 = s2; //shallow copy: s2.p = s1.p
```

## 7. The definition of `this` pointer
- `this` is a pointer to current object

## 8. What is a `const` member function? How is it declared?
- Member function that modift the object X should not be used by f.
- Whenever possible, declare a member function as **const** to enable the use of the member function even when the object is **const.**
```c++
class Value{
	public:
		void print(void)const{
			cout<<i<<endl;
		}
		void set(int x){
			i = x;	
		}
		int get(void) const{
			return i;
		}
	private:
		int i;
}
```
## 9. Declare an array of `double` of size 10 using `new`  and frees the memory allocated above.
```c++
double *a = new double[10];
delete []a;	
```

## 10. The logic and syntax of `try/catch` blocks
- `try/catch` blocks are used when something go wrong
```c++
// Demonstrate the use of exceptions
// use try-catch blocks

#include<iostream>
#include<stdexcept>
using namespace std;

class Value
{ 
  public:
    void print(void) { std::cout << i << std::endl; }
    void set(int x)
    { 
      if ( x < 1 )
        throw invalid_argument("value must be positive");
      i = x;
    }
    int get(void) { return i; }
  private:
    int i;
};

int main()
{
  Value v;

  try
  {
    v.set(-3);
  }
  catch ( invalid_argument &e )
  {
    cerr << "Exception: " << e.what() << endl;
  }
  cerr << "Program terminated normally" << endl;
}
```

## 11. The difference between redefining and overriding a member function.
- When a member function is *redefined* the choice of "which member function to call" is done at compile time, based on the type of the object invoking the function.
- When a member function is overridden (i.e. the function is virtual), the choice is made at runtime.

## 12. The role and syntax of functions declared as `friend` of class.
- A **friend** function is a function that is not a member of a class but has access to the class's private and protected members. Friend functions are not considered class members. They are normal external functions that are given special access privileges. Friends are not in the class's scope. A friend function is declared by the class that is granting access.
- friendship is not symmetric
- friendship is not transitive
```c++
class A{
 friend void set(A&, int x);
};
set(A& a, int x){
	a.i = x;
}
```
## 13. Create class representing x-y plane. Implement an overloaded operator +.
```c++
Class Plane{
	public:
	Plane(const double x,const double y):cor_x(x), cor_y(y){}
	friend Plane operator+(const Plane &a, const Plane &b){
		return Plane(a.cor_x+b.cor_x,a.cor_y+b.cor_y)
	}
	private:
	const double cor_x;
	const double cor_y;
};
```
## 14. Explain the class Penguin cannot be simply derived from the class Bird in the lecture notes
- Using intuition about the concepts being represented by classes can lead to inconsistencies
- Penguins are birds, and they cannot fly.
- We need to ask ourselves two questions before we design class hierarchy
	- Is a B a kind of A?
	- Can a B do everything that an A can do?

## 15. What is polymorphism
- Achieve implicit type-dependent behavior
- Using virtual functions
- Polymorphism can only be achieved if objects are passed via pointers, or reference
- If an object is passed by value, a copy constructor is invoked and the knowledge that the object is derived is lost.
```c++
// dynamic binding(run time)
#include<iostream>
using namespace std;
class A
{
  public:
    A(int i) : val(i) {}
    int val;
    virtual void print(void)
    { cout << "Value printed using A::print: " << val << endl; }
};

class B : public A
{
  public:
    B(int i) : A(i) {}
    virtual void print(void)
    { cout << "Value printed using B::print: " << val << endl; }
};

void f(A *a)
{
  a->print();
}

int main()
{
  A a(1);
  f(&a);
  B b(2);
  f(&b); // ok, b is a kind of A
}
```

## 16. What is Run-Time Type Identification(RTTI)? Give an example using the `dynamic_cast` operator.
- **RTTI** is a features that exposes information about an object's data type at runtime. We can use `#include<typeinfo>` and call `typeid()` to check the data type.
- If the base and derived classes cannot be modified, then RTTI is needed
- If type information must be obtained at run time, pointers can be tested using a **dynamic_cast**
```c++
// Use of downcasting with dynamic_cast

#include<iostream>
using namespace std;

class Shape
{
  public:
    virtual void draw(void) = 0;
};

class Square : public Shape
{
  public:
    virtual void draw(void) { cout << "Square::draw" << endl; }
};

class Circle: public Shape
{
  public:
    virtual void draw(void) { cout << "Circle::draw" << endl; }
};

void drawSquares(Shape **p, int size)
{
  Square *sp;
  for ( int i = 0; i < size; i++ )
  {
    cout << "drawSquares: element " << i << ": ";
    sp = dynamic_cast<Square*>(p[i]);
    if ( sp )
      sp->draw();
    else
      cout << " is not a Square" << endl;
  }
}

int main()
{
  Shape *shapes[4]; // array of pointers to shapes
  shapes[0] = new Square;
  shapes[1] = new Circle;
  shapes[2] = new Circle;
  shapes[3] = new Square;

  drawSquares(shapes,sizeof(shapes)/sizeof(Shape*));
}

```
## 17. What is the role of a factory member function? Why should it be a static function?
- The factory member function creates the appropriate object (depending on the parameter value) and returns a pointer to the new object
- It provides more intuitive names
- Static factory methods can access the private members of that particular class
- Static factory methods are not required to create new objects when every time it is invoked
```c++
// Also check the example hw2p1 aircraft,cpp
// factory.cpp
//
#include<iostream>
using namespace std;

class Shape
{
  public:
    virtual void print(void) const = 0;
    static Shape *makeShape(int i);
};

class Square : public Shape
{
  public:
    virtual void print(void) const { cout << "Square::print" << endl; }
};

class Circle: public Shape
{
  public:
    virtual void print(void) const { cout << "Circle::print" << endl; }
};

class Triangle: public Shape
{
  public:
    virtual void print(void) const { cout << "Triangle::print" << endl; }
};

Shape *Shape::makeShape(int i)
{
  if ( i == 0 ) return new Square;
  if ( i == 1 ) return new Circle;
  if ( i == 2 ) return new Triangle;
  return 0;
}

int main()
{
  int i;
  cin >> i;
  Shape *p = Shape::makeShape(i);
  if ( p != 0 )
    p->print();
}
```

## 18. What is generic programming?
- Generic programming: using the same algorithm for different objects
- Function templates
```c++
// Template function finding the maximum over 3 dataTypes
template<typename T>
T max(T v1, T v2, Tv3){
	T m = v1;
	if(v2>m){
		m = v2;
	}
	if(v3>m){
		m = v3;
	}
	return m;
}
```

```c++
// Template function switch dataTypes
template< typename T >
void sort( T &v1, T &v2 )
{
	if(v2<v1){
		T temp = v1;
		v1 = v2;
		v2 = temp;
	}
}
```
## 19. The definitions of the STL sequential and associative containers
- Sequential containers:
	- vector: similar to an array. Can be resized dynamically at runtime. Elements can be efficiently added at the end. Provide random access to elements
	- array: vector of fixed size
	- deque: "double-ended queue". Similar to a vector, but elements can be efficiently added/removed at both ends. Provide random access to elements.
	- list: doubly linked list. Elements can be efficiently added/removed anywhere. Provides sequential access to elements.
- Associative containers
	- set: A collection of objects. No duplicates are allowed. Efficient look up of elements.
	- multiset: A collection of objects with possible duplicates. Efficient lookup of elements.
	- map: A set of key-value pairs. No duplicates allowed. Access elements using the key. Can be used to implement a dictionary.
	- multimap: A set of key-value pairs with duplicates allowed
- Container adapters
	- stack: A Last-In-First-Out sequence of objects
	- queue: A First-In-First-Out sequence of objects
	- priority_queue: A sequence of objects ordered by priority. The order is preserved as elements added/deleted.

## 20. Assume a `vector<int>` contains the numbers{1,2,3,4,5}, write a statement insert function so that the element 6 is placed between 3 and 4.
```c++
vector<int> v(10);
v.push_back(1);
v.push_back(2);
v.push_back(3);
v.push_back(4);
v.push_back(5);
vector<int>::iterator it=v.begin()+3;
v.insert(it,6);
```
## 21. What is a function object
- A function object is a C++ class or struct that is used to encapsulate a function
- Function objects can be passed as parameters to algorithms to perform selection of elements

## 22. Write a function object can be used as a predicate with the 	`partition` algorithm to partition a `vector<int>` into even and odd numbers.
```c++
class oddEven{
	public:
	bool operator()(int i){
		return i%2==0;
	}
};
```
## 23. Write a `while` loop that reads integers from standard input until the end of file is reached and prints them one per line on standard output.
```c++
#include<iostream>
using namespace std;
int main()
{
	int a = 0;
	cin>>a;
	while(cin){
		cout<<a<<endl;
		cin>>a;
	}
}
```

## 24. Write a program that reads strings from stdin, inserts them in a set container, and prints them sorted on stdout.
```c++
#include<iostream>  // read inputs
#include<algorithm> // sort() 
#include<string> // string
#include<set> // set
using namespace std;
int main()
{
  set<string> s;
  string line;
  cin>>line;
  while (cin) //read strings from stdin
  {
    s.insert(line); // insert them into set containers
    cin>>line;
  }
  sort(s.begin(),s.end());
  set<string>:: iterator it = s.begin();
  for (; i != s.end(); i++ )
    cout << *i << " "; // Print them in sorted order
  cout << endl;
}
```
## 25. What is a binary tree?
- A data structure in which each node is connected to at most two other nodes
- A binary tree is a node connected to 0,1 or 2 binary trees
- Some nodes have two children, some one, some none (leaf nodes)
```c++
class Node{
	public:
	int val;
	Node *left, *right;
	Node(int i): val(i), left(0),right(0){}
};
class Tree{
	public:
	Node *root;
	Tree(void): root(0){}
};
```
## 26. What is a binary search tree?
- If node values can be ordered, they can be arranged in a binary search tree
- Values in a left subtree are smaller than the parent node
- Values in a right subtree are larger than the parent node 
```c++
class Node
{
  public:
    Node(int i) : val(i), left(0), right(0) {}
    int val;
    Node *left, *right;
};

class Tree
{
  public:
    Tree(void) : root(0) {}
    void insertNode(int i);
  private:
    Node *root;
    void insertNodeAt(Node*& p, int i);
};

void Tree::insertNode(int i)
{
  insertNodeAt(root,i);
}

void Tree::insertNodeAt(Node*& p, int i)
{
  if ( p == 0 )
  {
    p = new Node(i);
  }
  else
  {
    if ( i < p->val )
    {
      // insert in the left subtree
      insertNodeAt(p->left, i);
    }
    else if ( i > p->val )
    {
      // insert in the right subtree
      insertNodeAt(p->right, i);
    }
    else
    {
      // i == value of current node: i == p->val
      // do nothing: no duplicates
    }
  }
}
    
int main()
{
  Tree t;  
  t.insertNode(43);
  t.insertNode(11);
  t.insertNode(82);
  t.insertNode(77);
  t.insertNode(47);
  t.insertNode(65);
  t.insertNode(25);
}
```
## 27. Express the cost of finding an element in a balanced binary search tree, as a function of the number N of elements in the tree.
- Cost of insertion: O(log N)
- Cost of search: O(log N)
- Memory cost: O(N)
- The cost of finding an element can become large O(N) in the worst case(ordered sequence)

## 28. What is a binary heap?
- A heap is a data structure based on a binary tree
- In a min-heap, every element is smaller than its two children
- In a  max-heap, every element is a larger than its two children
- There is no implied order between siblings

## 29. How to add an element in a given binary search tree?
- The only place where nodes can be added are either nodes with only one child, or leaf nodes (with no children)
- Insertion: find the null pointer where the new value should be added
- Implementation in Q26

## 30. Write a for loop to print on stdout all the elements of a list declared as `list<string> a`
```c++
list<string>::iterator it;
for(it = a.begin(); it!=a.end();it++){
	cout<<*it<<endl;
}
```
