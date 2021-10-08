# Persistent DSU

I was randomly solving problems when I bumped into this interesting problem [New Roads Queries]. The problem statement goes like:

> There are N cities in Byteland but no roads between them. However, each day, a new road will be built. There will be a total of M roads. Your task is to process q queries of the form: "after how many days can we travel from city a to city b for the first time?"

## A small detour

We are all familiar with the problem:
> Given a graph of N nodes and queries of the form (A, B) check whether the 2 nodes A and B are in the same connected component or not.

Although this particular problem can be done using a simple [DFS]/[BFS] on the graph, it can be done easily using the data structure of [Disjoint Set]. Let's see how. 

[Disjoint Set] gives us a way to do two operations very efficiently:

1. Check whether two nodes are part of the same set
2. Merge the sets containing given two nodes

So if we add all the edges to the Disjoint Set one by one using the merge operation, in the end we would have a disjoint sets of nodes. Then all we need to do for each query is to answer whether each node is part of the same set or not.

With path-compression and light-to-heavy merging, we can answer each query in amortised **`log* N`**, where *N* is the number of nodes in the graph and **`log* N`** is the [Inverse Ackermann function].

The code for DSU is also fairly simple and below in an example of the same in C++.
```cpp
class DisjointSet {
  public:
    vector<int> parent;
    vector<int> size;

    DisjointSet(int n) {
      parent.resize(n);
      size.resize(n, 1);
      /** Initially each element belongs to one set which is itself */
      for (int i = 0; i < n; i++) parent[i] = i;
    }

    /** Find the root of any element */
    int root(int a) {
      while (parent[a] != a) {
        parent[a] = parent[parent[a]]; /** Path compression */
        a = parent[a];
      }
      return a;
    }

    /** Find whether two elements belong to the same set */
    int find(int a, int b) { return root(a) == root(b); }

    /** Merge two sets which contain element a, b */
    void merge(int a, int b) {
      int root_a = root(a); int root_b = root(b);
      /** Light to Heavy merging */
      if (root_a != root_b) {
         if (size[root_a] < size[root_b]) {
          parent[root_a] = parent[root_b];
          size[root_b] += size[root_a];
        } else {
          parent[root_b] = parent[root_a];
          size[root_a] += size[root_b];
        }
      }    
    }
};
```

**NOTE:** All [Disjoint Set] uses to store things are two arrays/vectors.

## Coming back

Now that we know what [Disjoint Set] is, let's try to apply this to the problem we have at hand. One notices that the queries we need to anwer are time ⏰ based, which means we need to have a snapshot of the graph or the Disjoint Set (representing the graph) at each point in time. Let's number these Disjoint Sets representing the graphs at each time by D<sub>0</sub>,  D<sub>1</sub>, ...  D<sub>M</sub>. But we need to do this smartly without copying the entire Disjoint Set each time. 

For the time being, let's assume that we have some efficient way to get the copy of the Disjoint Set at any time *t*, still how do we solve the problem at hand ??

💡 On careful observation we can see that if two nodes are connected at time *t<sub>i</sub>*, then they will remain connected in all time *t<sub>j</sub>* where *j >= i*. **That means the function of whether two nodes are connected is monotic over time.**

This brings us to the idea of using [Binary Search] to find the least time *t* where any given two nodes are connected. This will take us **`O(log M)`** time where *M* is the number of edges in the graph. Formally, we are trying to find a Disjoint Set D<sub>i</sub> such that A and B have the same root in D<sub>i</sub> and i is minimum.

We seem to be getting somewhere now....

Now how do we solve the problem of persistence ??

We notice that our [Disjoint Set] just uses arrays. We can replace the arrays in the DSU with a [Persistent Array] to create a **Persistent Disjoint Set** data structure. This will allows us to query the Disjoint Set at any point in time!!

## Show me some code

First we create a generic [Persistent Array]. [Persistent Array] allows us to look at the state of the array by going back in time.

**NOTE:** Actually, the below array is only Partially Persistent which is good enough for the requirements of this problem.

```cpp
template <typename T>
class PersistentArray {
  public:
    vector<vector<pair<int, T>>> arr;
    
    PersistentArray() {}

    PersistentArray(int n) { arr.resize(n); }

    PersistentArray(vector<T>& initial_array) {
      arr.resize(initial_array.size());
      for (int i = 0; i < initial_array.size(); i++) {
        arr[i].push_back({ 0, initial_array[i] });
      }
    }

    void resize(int n) { arr.resize(n); }

    void resize(int n, T a) {
      arr.resize(n);
      for (int i = 0; i < n; i++) {
        arr[i].push_back({ 0, a });
      }
    }

    void clear() { arr.clear(); }

    void push_back(T item, int index, int time) {
        arr[index].push_back({ time, item }); 
    }

    T at(int index, int time) {
      return prev(
        upper_bound(
            arr[index].begin(),
            arr[index].end(),
            make_pair(time, numeric_limits<T>::max())
        )
      )->second;
    }
};
```

Using the [Persistent Array] we just created, we create the **Persistent Disjoint Set**.

```cpp
class PersistentDisjointSet {
  public:
    PersistentArray<int> parent;
    PersistentArray<int> size;

    PersistentDisjointSet(int n) {
      parent.resize(n);
      size.resize(n, 1);
      for (int i = 0; i < n; i++) parent.push_back(i, i, 0);
    }

    int root(int a, int t) {
      int temp = a;
      /** Note we are not doing path compression here. */
      while (parent.at(a, t) != a) {
        a = parent.at(a, t);
      }
      return a;
    }

    int find(int a, int b, int t) {
      return root(a, t) == root(b, t);
    }

    void merge(int a, int b, int t) {
      int root_a = root(a, t); int root_b = root(b, t);
      if (root_a != root_b) {
         if (size.at(root_a, t) < size.at(root_b, t)) {
          parent.push_back(parent.at(root_b, t), root_a, t);
          size.push_back(size.at(root_b, t) + size.at(root_a, t), root_b, t);
        } else {
          parent.push_back(parent.at(root_a, t), root_b, t);
          size.push_back(size.at(root_a, t) + size.at(root_b, t), root_a, t);
        }
      }
    }
};
```

**NOTE:** The **Persistent Disjoint Set**. is almost exactly the same as [Disjoint Set], just a dimension of time has been added to it.

With these two data structures, we can easily solve the question at hand.

```cpp

void solve() {
  PersistentDisjointSet pds(N);
  for (int i = 0; i < edges.size(); i++) {
    /** Merge every two nodes in the context of the time */
    pds.merge(edges[i].first - 1, edges[i].second - 1, i + 1);
  }

  for (auto [from, to]: queries) {
    int low = 0, high = M + 1;
    int answer = -1;
    int mid = (high + low) / 2;
    /** Binary searching over time */
    while (low <= high) {
      mid = (high + low) / 2;
      if (pds.find(from - 1, to - 1, mid)) {
        high = mid - 1;
        answer = mid;
      } else {
        low = mid + 1;
      }
    }
    cout << answer << "\n";
  }
}
```

Here is the link to the [full solution].

[New Roads Queries]: https://cses.fi/problemset/task/2101
[Disjoint Set]: https://www.hackerearth.com/practice/notes/disjoint-set-union-union-find/
[Inverse Ackermann function]: https://en.wikipedia.org/wiki/Ackermann_function#Inverse
[DFS]: https://en.wikipedia.org/wiki/Depth-first_search
[BFS]: https://en.wikipedia.org/wiki/Breadth-first_search
[Persistent Array]: https://en.wikipedia.org/wiki/Persistent_array
[Binary Search]: https://en.wikipedia.org/wiki/Binary_search_algorithm
[full solution]: https://github.com/MadaraUchiha-314/competitive-coding/blob/main/cses/problems/task/2101/solution/solution.cpp