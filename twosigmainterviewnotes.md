---
description: >-
  There are 4 sets of questions (ref: 1point3acres), All the code requires test
  infra.
---

# Two Sigma

## Abstract

Normally, there would be 3 rounds interviews. First round has two questions, first question normally is easy and then the next one could be a bit difficult. Then the second round would be one median level problem and a system / OO design question, or only system / OO design. Final round is either difficult problem or a system / OO design.

Afternoon interviews would be behaviour questions mixed up with the coding, system design and brain teaser problems. I believe the goal of the afternoon interviews would be the decision from team manager, who want to know that you can be the good fit to the team, like solving problems together, discussion and etc.

This document references the posts in 1point3acres and the following docs. Thank you guys.

{% embed url="https://www.dropbox.com/s/jvs0ingm5auduah/TwoSigmaOnsite.pdf?dl=0" caption="" %}

[https://gaigai\_kris.gitbooks.io/interview-basic/two\_sigma.html](https://gaigai_kris.gitbooks.io/interview-basic/two_sigma.html)

[https://xiaoguan.gitbooks.io/prepare-for-interview/Two%20Sigma/ts-onsite-2.html](https://xiaoguan.gitbooks.io/prepare-for-interview/Two%20Sigma/ts-onsite-2.html)

## Set 1

### Round 1 Calculator Class & Remove Subtree

#### Question 1: Calculator class \(Reverse Polish Notation\)

* 一个Calculator类
  * 包含一个stack和一个vector
  * 一个 Token 类，包含一个process\(stack\)方法
  * Operand 和 Operator 继承自Token
  * Operand的process是向stack中push这个数字
  * Operator包含一个numOfOperand，process方法是从stack中pop出numOfOperand个数字后进行某种操作，结果再push进stack
* Operator \(abstract base class\)
  * operate\(int val1, int val2\)
* Add, Subtract, Multiply, Divide四个class继承这个Operator base class的写法
  * pay attention to Divide

{% tabs %}
{% tab title="Code" %}
```text
C++ code was copied from somewhere else. 
Java code was created based the c++ code, and it works. 

Design Pattern 

* Factory Pattern
```
{% endtab %}

{% tab title="Java" %}
```java
package questions;

// Calculator
import java.util.*;

abstract class Token<T> {
    public Token() {}
    abstract boolean isOperator();
    abstract void process(Stack<T> stack) throws Exception;
}

class Operand<T> extends Token<T> {
    private final T value;

    public Operand(final T operand) {
        this.value = operand;
    }

    public T getValue() {
        return this.value;
    }

    public boolean isOperator() {
        return false;
    }

    public void process(final Stack<T> stack) {
        stack.push(this.value);
    }
}

abstract class Operator<T> extends Token<T> {
    private final String type;

    public Operator(final String type) {
        this.type = type;
    }

    public String type() {
        return this.type;
    }

    public boolean isOperator() {
        return true;
    }
}

class Add extends Operator<Double> {
    public Add() {
        super("+");
    }

    @Override
    public void process(final Stack<Double> stack) throws IllegalArgumentException {
        if (stack.isEmpty())
            throw new IllegalArgumentException("Input stack should not be empty.");
        final Double num1 = stack.pop();
        if (stack.isEmpty())
            throw new IllegalArgumentException("Input stack should contain at least 2 numbers.");
        final Double num2 = stack.pop();
        stack.add(num1 + num2);
    }
}

class Subtract extends Operator<Double> {
    public Subtract() {
        super("-");
    }

    @Override
    public void process(final Stack<Double> stack) throws IllegalArgumentException {
        if (stack.isEmpty())
            throw new IllegalArgumentException("Input stack should not be empty.");
        final Double num1 = stack.pop();
        if (stack.isEmpty())
            throw new IllegalArgumentException("Input stack should contain at least 2 numbers.");
        final Double num2 = stack.pop();
        stack.add(num1 - num2);
    }
}

class Multiply extends Operator<Double> {
    public Multiply() {
        super("*");
    }

    @Override
    public void process(final Stack<Double> stack) throws IllegalArgumentException {
        if (stack.isEmpty())
            throw new IllegalArgumentException("Input stack should not be empty.");
        final Double num1 = stack.pop();
        if (stack.isEmpty())
            throw new IllegalArgumentException("Input stack should contain at least 2 numbers.");
        final Double num2 = stack.pop();
        stack.add(num1 * num2);
    }
}

class Divide extends Operator<Double> {
    public Divide() {
        super("/");
    }

    @Override
    public void process(final Stack<Double> stack) throws IllegalArgumentException {
        if (stack.isEmpty())
            throw new IllegalArgumentException("Input stack should not be empty.");
        final Double num1 = stack.pop();
        if (stack.isEmpty())
            throw new IllegalArgumentException("Input stack should contain at least 2 numbers.");
        final Double num2 = stack.pop();
        if (num2 == 0) throw new IllegalArgumentException("Input demoninator should not equal to 0.");
        stack.add(num1 / num2);
    }
}

class TokenFactory {
    private Map<String, Operator<Double>> operators;

    public TokenFactory() {
        this.operators = new HashMap<String, Operator<Double>>();
        operators.put("+", new Add());
        operators.put("-", new Subtract());
        operators.put("*", new Multiply());
        operators.put("/", new Divide());
    }

    public Token<Double> getToken(String token) throws IllegalArgumentException {
        if (operators.containsKey(token)) {
            return operators.get(token);
        } else {
            try {
                Double operand = Double.parseDouble(token);
                return new Operand<Double>(operand);
            } catch(Exception e) {
                throw new IllegalArgumentException(e.toString());
            }
        }
    }
}

class RPNCalculator {
    private TokenFactory tokenFactory;
    private Stack<Double> stack;

    public RPNCalculator() {
        this.tokenFactory = new TokenFactory();
        this.stack = new Stack<Double>();
    }

    public Double calculate(List<String> formula) throws IllegalArgumentException {
        if (formula.size() == 0) return 0.0;
        try {
            for (String s : formula) {
                tokenFactory.getToken(s).process(stack);
            }
        } catch (Exception e) {
            throw new IllegalArgumentException(e.toString());
        }
        Double result = stack.pop();
        if (!stack.isEmpty()) {
            throw new IllegalArgumentException("Input is not valid.");
        }
        return result;
    }
}

public class Calculator {
    public static void main(String[] args) {
        List<String> test = Arrays.asList("4", "5", "13", "/", "*");
        RPNCalculator calculator = new RPNCalculator();
        Double r = calculator.calculate(test);
        System.out.println(r);

    }
}
```
{% endtab %}

{% tab title="C++" %}
```cpp
using namespace std;

class Token { 
    public: Token() {}; 
    virtual ~Token() = 0; 
    virtual bool isOperator() const = 0; 
    virtual void process(stack &stk) const = 0; 
}; 
Token::~Token() {}

class Operand : public Token { 
    public: Operand(const string &str) { 
        m_val = stod(str); 
    } 

    double val() const { 
        return m_val; 
    } 

    virtual bool isOperator() const { 
        return false; 
    } 

    virtual void process(stack &stk) const { 
        stk.push(m_val); 
    } 

    private: double m_val; 
};

class Operator : public Token { 
    public: Operator(const string &type) { 
        m_type = type; 
    } 
    const string & type() const { 
        return m_type; 
    } 

    virtual ~Operator() = 0;
    virtual bool isOperator() const { 
        return true; 
    } 

    private: string m_type; 
}; 
Operator::~Operator() {}

class Add : public Operator { 
    public: Add() : Operator("+") {} 
    virtual void process(stack &stk) const { 
        if (stk.empty()) throw runtime_error("illegal operation on Div: stack underflow."); 
        double num2 = stk.top(); 
        stk.pop(); 
        if (stk.empty()) throw runtime_error("illegal operation on Div: stack underflow."); 
        double num1 = stk.top(); 
        stk.pop(); 
        stk.push(num1 + num2); 
    } 
};

class Sub : public Operator { 
    public: Sub() : Operator("+") {} 
    virtual void process(stack &stk) const { 
        if (stk.empty()) throw runtime_error("illegal operation on Div: stack underflow."); 
        double num2 = stk.top(); 
        stk.pop(); 
        if (stk.empty()) throw runtime_error("illegal operation on Div: stack underflow."); 
        double num1 = stk.top(); 
        stk.pop(); 
        stk.push(num1 - num2); 
    } 
};

class Mul : public Operator { 
    public: Mul() : Operator("+") {} 
    virtual void process(stack &stk) const { 
        if (stk.empty()) throw runtime_error("illegal operation on Div: stack underflow."); 
        double num2 = stk.top(); 
        stk.pop(); 
        if (stk.empty()) throw runtime_error("illegal operation on Div: stack underflow."); 
        double num1 = stk.top(); 
        stk.pop(); 
        stk.push(num1 * num2); 
    } 
};

class Div : public Operator { 
    public: Div() : Operator("+") {} 
    virtual void process(stack &stk) const { 
        if (stk.empty()) throw runtime_error("illegal operation on Div: stack underflow."); 
        double num2 = stk.top(); 
        stk.pop(); 
        if (stk.empty()) throw runtime_error("illegal operation on Div: stack underflow."); 
        double num1 = stk.top(); 
        stk.pop(); 
        stk.push(num1 / num2); 
    } 
};

class TokenFactory { 
    public: TokenFactory() { 
        operators["+"] = new Add(); 
        operators["-"] = new Sub(); 
        operators["*"] = new Mul(); 
        operators["/"] = new Div(); 
    }

    Token * create(const string &param) {
        if (operators.find(param) != operators.end())
            return operators[param];
        else
            return new Operand(param);
    }
    private: unordered_map operators; 
};

class RPNCalculator { 
    public: 
        double calculate(const vector &formula) { 
            try { 
                for (int i = 0; i < formula.size(); ++ i) { 
                    m_factory.create(formula[i])->process(m_stk); 
                } 
            } catch(const exception &e) { 
                cout << e.what() << endl; return -1; 
            }
            double result = m_stk.top();
            m_stk.pop();
            if (!m_stk.empty()) {
                cout << "invalid formula. " << endl;
                return -1;
            }
            return result;
        }
    private: 
        TokenFactory m_factory; 
        stack m_stk; 
};

class Calculator {
    int main() { 
        vector f = {"4", "5", "13", "/", "+"}; 
        RPNCalculator c; 
        cout << c.calculate(f) << endl;
        return 0;
    }
}
```
{% endtab %}
{% endtabs %}

1. Follow Ups
   1. token -- operand and operator inherit from token 
   2. unary and ternary operators -- add a variable of NumOperands 
   3. factory design pattern 
      1. Factory design pattern is a class where you can hide the creation logic of all sub-classes. Typically, it would require a type and generate an object of sub-class. Essentially, it’s a mapping from types sub-classes. 
   4. 怎么才能直接给用户binary, 让他们可以自由的添加新的operator
      1. 更好的办法是做XML / JSON的serialization
2. Key Points
   1. Abstract Class
   2. Factory Design Pattern

#### Question 2: Remove Subtree from Tree

* 写一个子函数，要现场编译现场跑. 给定一个数组，数组的每个元素就是一个节点\(struct node\)，大概的长得像下面这样

```text
struct node{
    int parent;
    int val;
    bool valid;
};
```

* parent代表当前node的parent 在数组里的index，root node的parent是-1. 所以node 是child指向parent的。
* 给定一个数组和数组的某个index，删除这个index以及它的子树\(只需要将node里的valid置为false即可\)，只能用O\(n\)的空间复杂度

{% tabs %}
{% tab title="Code" %}
```text
C++ was copied from somewhere else
Java was based on the C++, it works.
```
{% endtab %}

{% tab title="Java" %}
```java
import java.util.*;

class Node {
    public int parent;
    public int val;
    public boolean valid;
    public boolean visited;

    public Node(int parent) {
        this.parent = parent;
        this.val = 0;
        this.valid = true;
    } 
}

class ArrayTree {
    private List<Node> nodes;
    private int size;

    public ArrayTree(List<Integer> nums) {
        nodes = new ArrayList<Node>();
        size = nums.size();

        for (int num : nums) {
            nodes.add(new Node(num));
        }
    }

    public void deleteSubTree(int index) {
        if (index < 0 || index >= nodes.size() || !this.nodes.get(index).valid) {
            return;
        }
        reset();
        nodes.get(index).visited = true;
        nodes.get(index).valid = false;
        size--;
        for (int i = 0; i < nodes.size(); i++) {
            if (nodes.get(i).visited) continue;
            explore(i);
        }
    }

    public int getTreeSize() {
        return this.size;
    }

    private boolean explore(int index) {
        if (nodes.get(index).visited || nodes.get(index).parent == -1) {
            nodes.get(index).visited = true;
            return nodes.get(index).valid;
        }

        nodes.get(index).visited = true;
        boolean isParentValid = explore(nodes.get(index).parent);
        if (nodes.get(index).valid != isParentValid) {
            nodes.get(index).valid = isParentValid;
            size--;
        }
        return isParentValid;
    }

    private void reset() {
        for (Node node : nodes) node.visited = false;
    }
}

public class RemoveSubtree {
    public static void main(String[] args) {
        /* Tree
                      5
                     / \ 
                    1   2
                   /   / \
                  0   3   4
                     /
                    6
       */
        List<Integer> nums = Arrays.asList(1, 5, 5, 2, 2, -1, 3);
        ArrayTree at = new ArrayTree(nums);
        System.out.println(at.getTreeSize());

        at.deleteSubTree(3);
        // 5
        System.out.println(at.getTreeSize());

        at.deleteSubTree(6);
        // 5
        System.out.println(at.getTreeSize());

        at.deleteSubTree(2);

        // 3
        System.out.println(at.getTreeSize());
    }
}
```
{% endtab %}

{% tab title="C++" %}
```cpp
struct Node { 
    int parentIdx; 
    bool valid; 
    bool visited;
    Node(int pi) : parentIdx(pi), valid(true), visited(false) {}
};

class ArrayTree { 
    public: 
    ArrayTree(vector nums) { 
        for (int i : nums) { 
             nodes.push_back(new Node(i)); size ++; 
        } 
    }

    void deleteSubtree(int idx) {
        if (!nodes[idx]->valid) // if already deleted
            return;
        reset(); // reset valid nodes back to unvisited

        nodes[idx]->valid = false;
        nodes[idx]->visited = true;
        size --;

        for (int i = 0; i < nodes.size(); ++ i) {
            if (nodes[i]->visited)
                continue;
            explore(i);
        }
    }

    private: 
    vector nodes; 
    int size;
    bool explore(int idx) {
        // if current is root or is already visited
        if (nodes[idx]->parentIdx == -1 || nodes[idx]->visited) {
            nodes[idx]->visited = true;
            return nodes[idx]->valid;
        }

        nodes[idx]->visited = true;
        // current validness depends on parent validness
        bool isParentValid = explore(nodes[idx]->parentIdx);
        if (nodes[idx]->valid != isParentValid) {
            nodes[idx]->valid = isParentValid;
            size --; // only decrement size when change from valid to invalid
        }

        return isParentValid;
    }

    void reset() {
        for (Node * n : nodes) {
            if (n->valid)
                n->visited = false;
        }
    }
};

int main() {
    vector<int> nums = {1, 5, 5, 2, 2, -1, 3};
    ArrayTree at(nums);
    at.print();

    at.deleteSubtree(3);
    at.print();

    at.deleteSubtree(6);
    at.print();

    at.deleteSubtree(2);
    at.print();

    return 0;
}
```
{% endtab %}
{% endtabs %}

1. Follow Ups
   1. Update tree size
   2. Corner cases
      1. Index is out of range
      2. delete subtree which already removed
   3. Node cannot be changed. 
      1. use a boolean array to store the visited indexes. 

### Round 2 Blocking Queues & Slow Web Diagnose

#### Question 1: Blocking Queues

* Suppose you have two independent blocking queues that will keep getting new data, the new data will always be greater than the last element of the queue it is going to. You can only use getNext\(\) to get the data from these two blocking queues and each data can be fetched only once. Write a program to output all pairs of data from these two blocking queues that have difference smaller than 1.
* Example： 
  * Q1:  {0.2, 1.4, 3.0} 
  * Q2:  {1.0, 1.1, 3.5}
  * output: \[0.2, 1.0\], \[1.4, 1.0\], \[0.2, 1.1\], \[1.4, 1.1\], \[3.0, 3.5\]. 
* Java 面试者的可以用java里面自带blocking queue来做，面试官提示说的。
* ref: [https://www.ibm.com/developerworks/cn/aix/library/au-multithreaded\_structures1/](https://www.ibm.com/developerworks/cn/aix/library/au-multithreaded_structures1/)
* ref: [https://codereview.stackexchange.com/questions/166002/find-pairs-of-consecutive-numbers-fetched-from-blocking-queues](https://codereview.stackexchange.com/questions/166002/find-pairs-of-consecutive-numbers-fetched-from-blocking-queues)

{% tabs %}
{% tab title="Code" %}
```text
Because the data comes from two blocking queues, the implementation of 
non-streaming version would be two queues and call calculatePairs on different 
queue order. 

However, becasue the blocking queue could be blocked, because there is no new 
incoming data. Thus two threads would be the solution. 

C++ code is coyied from somewhere else
Java code is based on the C++ code, and it works.
```
{% endtab %}

{% tab title="C++" %}
```cpp
// this is the final result containing timestamp pairs < 1 vector > result; 
// queue defined to stored numbers from streams list Q1; 
// use list so we can iterate list Q2; 
// the lock shared between threads Lock mutex;
// this is a utility function 
void calculate(list &q1, list &q2, double val) {
    q1.push(val);
    if (!q2.empty()) {
        while (!q2.empty() && val - q2.front() >= 1) q2.pop(); 
        for (double num : q2) { 
            if (abs(val - num) < 1) 
                result.push_back(make_pair(val, num));
            else break;
        } 
    }
}
// function that will be executed by thread 1 
void task1(Stream s) {
    while (true) {
        double val = s.getNext();
        mutex.lock();
        calculate(Q1, Q2, val);
        mutex.unlock(); 
    }
}
// function that will be executed by thread 2 void 
task2(Stream s) {
    while (true) {
        double val = s.getNext();
        mutex.lock();
        calculate(Q2, Q1, val);
        mutex.unlock();
    }
}
int main() {
    // incoming streams
    Stream S1 = {0.2, 1.4, 3.0};
    Stream S2 = {2.0, 2.1, 4.5};
    // create the threads
    thread t1(task1, S1);
    thread t2(task2, S2);

    // start the threads
    t1.join();
    t2.join();
}
```
{% endtab %}

{% tab title="Java" %}
```java
package questions;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Queue;
import java.util.Iterator;
import java.util.LinkedList;
import java.util.List;
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.locks.ReentrantLock;
import java.util.stream.DoubleStream;

class ReadFromQueues implements Runnable {
    private final BlockingQueue<Double> inputQueue;
    private final List<List<Double>> res;

    private final Queue<Double> q1;
    private final Queue<Double> q2;

    private final ReentrantLock lock;

    public ReadFromQueues(BlockingQueue<Double> bq, ReentrantLock lock, List<List<Double>> res, Queue<Double> q1, Queue<Double> q2) {
        this.inputQueue = bq;
        this.lock = lock;
        this.res = res;
        this.q1 = q1;
        this.q2 = q2;
        new Thread(this, "ReadFromQueues").start();
    }

    @Override
    public void run() {
        while (true) {
            try {
                double timestamp = inputQueue.take();
                if (timestamp < 0) break;
                lock.lock();
                calculate(timestamp);
                lock.unlock();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    private void calculate(double ts) {
        q1.add(ts);
        if (!q2.isEmpty()) {
            while (!q2.isEmpty() && ts - q2.peek() >= 1) q2.poll();
            for (double t : q2) {
                if (Math.abs(ts - t) < 1.0) {
                    res.add(Arrays.asList(ts, t));
                    System.out.printf("Debug: Thread %s ts1: %f ts2: %f \n", Thread.currentThread().getId(), ts, t);
                } else break;
            }
        }
    }
}

class WriteToQueue implements Runnable {
    BlockingQueue<Double> bq;
    DoubleStream ds;

    public WriteToQueue(BlockingQueue<Double> bq, DoubleStream ds) {
        this.bq = bq;
        this.ds = ds;
        new Thread(this, "WriteToQueue").start();
    }

    @Override
    public void run() {
        Iterator<Double> iter = this.ds.iterator();
        while (iter.hasNext()) {
            double next = iter.next();
            bq.add(next);
            System.out.printf("Debug: Thread %s value %f \n", Thread.currentThread().getId(), next);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
} 

public class TwoQueueCompare {
    public static void main(String[] args) throws Exception {
        // two streams, push data to two queues
        DoubleStream.Builder b1 = DoubleStream.builder();
        b1.accept(0.2);
        b1.accept(1.4);
        b1.accept(3.0);
        b1.accept(-1.0);
        DoubleStream ds1 = b1.build();

        DoubleStream.Builder b2 = DoubleStream.builder();
        b2.accept(1.0);
        b2.accept(1.1);
        b2.accept(3.5);
        b2.accept(-1.0);
        DoubleStream ds2 = b2.build();

        ReentrantLock lock = new ReentrantLock();
        BlockingQueue<Double> bq1 = new ArrayBlockingQueue<>(4);
        BlockingQueue<Double> bq2 = new ArrayBlockingQueue<>(4);

        Queue<Double> q1 = new LinkedList<>();
        Queue<Double> q2 = new LinkedList<>();

        List<List<Double>> res = new ArrayList<>();

        new WriteToQueue(bq1, ds1);
        new WriteToQueue(bq2, ds2);
        new ReadFromQueues(bq1, lock, res, q1, q2);
        new ReadFromQueues(bq2, lock, res, q2, q1);

        Thread.sleep(10000);

        for (List<Double> l : res) {
            System.out.println(l);
        }
    }
}
```
{% endtab %}
{% endtabs %}

1. Follow Ups
   1. 如果有多个queue，比如10个queue怎么办，
      1. It should use a list to store the data from each blocking queue.
      2. Lock is the same, it should lock before the calculation and release the lock afterwards.
      3. The thread input should be like `(List<List<Double>> queues, int qIndex)`
      4. Each thread need to maintain a list of indexes which point to the un-explored position of other queues. 
      5. Every time a thread get a new data, start from the current index and find the last index suite the condition of each other queues. 

#### Question 2: Slow Web Accessing

![](https://github.com/BitForceStudio/two-sigma-interview/tree/11c3f0b937363050ac4332e823f7bf292900b0f7/.gitbook/assets/image.png)

{% tabs %}
{% tab title="Before Diagnose" %}
1. When does it happen? How frequently ?
2. How slower compare to the normal?
3. Where does it happen? All places or only several places?
4. Is there a monitor on the full stack? 
   1. Host & service health check
   2. Overall host performance \(CPU, Mem, I/O, Disk and etc\)
   3. Time of request
   4. Time of back end request
   5. Database response time
{% endtab %}

{% tab title="Front End\(Client\)" %}
1. Check bandwidth, there are several website to test the bandwidth
2. Check client device performance \(CPU, Mem\)
3. Check DNS, try different ip of the host, Ping the host
4. Java script issue
   1. Render-blocking, React, life-circle state update 
5. CDN? \(content delivery network\)
6. Check the request/response time from Browser Develop Tool. chrome -&gt; develop tools -&gt; network
7. By checking the develop tool, we could know which request took longest / longer time than normal. So it could give us some evidence / guide to the next step.  
{% endtab %}

{% tab title="Back End \(Servers\)" %}
Before diving into the backend diagnoses. I have something to say in front. Backend services should have a series of metrics so that the engineer could targeting the problem more easily.

1. Load Balance
   1. If there was any monitor of the hosts
      1. CPU, Mem, I/O, load
      2. Compare with the historical record, it maybe could give some clue.
   2. Try different request, check whether all request are slow or only some certain ones
      1. Access to the host, check the logs, if there were something like "TraceId" of each request, it would help a lot on find the logs, could just grep it. 
      2. It maybe direct the request to one server. If so, it maybe initial design wasn't optimised or need to change the balance strategy. 
2. Backend Services
   1. Monitor is essential.
      1. Overall cluster / hosts performance, CPU, Mem, I/O, Load and etc. 
      2. Also disk sometime would cause the problem
      3. Check the service health, if some of the services are down, it could be the reason.
   2. Check request from log by some tracer id
      1. There maybe a loop between services for some certain requests
      2. Tracer Id shows the path of the a request, so it is very useful to diagnosis the problem
   3. Check exceptions from the log, it may cause the problem
   4. Use some command like top on a host. 
   5. Enable debug level of the logging, more information, the better.
3. Message Queue
   1. Not all backend has message queue
   2. Check the lag of topics, maybe there are too many waiting requests on a topic. 
   3. Check cluster, like for Kafka, check heath of leader and etc. If leader is down, it maybe struggle on election. 
   4. consumer group, like some servers are down. 
{% endtab %}

{% tab title="Database" %}
As backend services, database / cluster should also has the metrics to help the diagnose and monitor the performance

1. Overall I/O, space, performance
2. Too many connections
3. Database is slow
   1. Should design another cache layer between services and database.
4. Data percentage
   1. Some of the data base performance linked with the consumed space. 
   2. Like Cassandra, when it's doing the compaction, it may slow the performance.
   3. Apply retention policy
5. Some of the nodes in database cluster were down, which raise the internal communications. 
6. Maybe another database cluster and apply sharding. 
{% endtab %}
{% endtabs %}

### Round 3 Wildcard Matching

[https://leetcode.com/problems/wildcard-matching/solution/](https://leetcode.com/problems/wildcard-matching/solution/)

The solution should start from **DP solution**. The testing cases should be added at beginning.

* Step 1: Recursive Solution
* Step 2: DP solution
* Step 3: Backtracking

{% tabs %}
{% tab title="Code" %}
```text
Dp solution and backtrack solution are copied from leetcode
```
{% endtab %}

{% tab title="DP" %}
```java
class Solution {
    public boolean isMatch(String s, String p) {
        int sLen = s.length(), pLen = p.length();

        // base cases
        if (p.equals(s) || p.equals("*")) return true;
        if (p.isEmpty() || s.isEmpty()) return false;

        // init all matrix except [0][0] element as False
        boolean[][] d = new boolean[pLen + 1][sLen + 1];
        d[0][0] = true;

        // DP compute
        for(int pIdx = 1; pIdx < pLen + 1; pIdx++) {
            // the current character in the pattern is '*'
            if (p.charAt(pIdx - 1) == '*') {
                int sIdx = 1;
                // d[p_idx - 1][s_idx - 1] is a string-pattern match
                // on the previous step, i.e. one character before.
                // Find the first idx in string with the previous math.
                while ((!d[pIdx - 1][sIdx - 1]) && (sIdx < sLen + 1)) sIdx++;
                // If (string) matches (pattern),
                // when (string) matches (pattern)* as well
                d[pIdx][sIdx - 1] = d[pIdx - 1][sIdx - 1];
                // If (string) matches (pattern),
                // when (string)(whatever_characters) matches (pattern)* as well
                while (sIdx < sLen + 1) d[pIdx][sIdx++] = true;
            }
            // the current character in the pattern is '?'
            else if (p.charAt(pIdx - 1) == '?') {
                for(int sIdx = 1; sIdx < sLen + 1; sIdx++)
                    d[pIdx][sIdx] = d[pIdx - 1][sIdx - 1];
            }
            // the current character in the pattern is not '*' or '?'
            else {
                for(int sIdx = 1; sIdx < sLen + 1; sIdx++) {
                    // Match is possible if there is a previous match
                    // and current characters are the same
                    d[pIdx][sIdx] = d[pIdx - 1][sIdx - 1] &&
                    (p.charAt(pIdx - 1) == s.charAt(sIdx - 1));
                }
            }
        }
        return d[pLen][sLen];
    }
}
```
{% endtab %}

{% tab title="Backtrack" %}
```java
class Solution {
    public boolean isMatch(String s, String p) {
        int sLen = s.length(), pLen = p.length();
        int sIdx = 0, pIdx = 0;
        int starIdx = -1, sTmpIdx = -1;

        while (sIdx < sLen) {
            // If the pattern caracter = string character
            // or pattern character = '?'
            if (pIdx < pLen && (p.charAt(pIdx) == '?' || p.charAt(pIdx) == s.charAt(sIdx))){
                ++sIdx;
                ++pIdx;
            }
            // If pattern character = '*'
            else if (pIdx < pLen && p.charAt(pIdx) == '*') {
                // Check the situation
                // when '*' matches no characters
                starIdx = pIdx;
                sTmpIdx = sIdx;
                ++pIdx;
            }
            // If pattern character != string character
            // or pattern is used up
            // and there was no '*' character in pattern 
            else if (starIdx == -1) {
                return false;
            }
            // If pattern character != string character
            // or pattern is used up
            // and there was '*' character in pattern before
            else {
                // Backtrack: check the situation
                // when '*' matches one more character
                pIdx = starIdx + 1;
                sIdx = sTmpIdx + 1;
                sTmpIdx = sIdx;
            }
        }

        // The remaining characters in the pattern should all be '*' characters
        for(int i = pIdx; i < pLen; i++)
            if (p.charAt(i) != '*') return false;
        return true;
    }
}
```
{% endtab %}

{% tab title="Testing cases" %}
```java
@Test
public void test_empty_string_match() {
        Assert.assertTrue(is_match('', ''));
        Assert.assertFalse(is_match('', '?'));
        Assert.assertTrue(is_match('', '*'));
        Assert.assertFalse(is_match('', 'a*'));
        Assert.assertTrue(is_match('', '**'));
}

@Test
public void test_exact_match() {
        Assert.assertTrue(is_match('a', 'a'));
        Assert.assertTrue(is_match('abc', 'abc'));
        Assert.assertFalse(is_match('abc', 'abb'));
        Assert.assertFalse(is_match('a', ''));
}

@Test
public void test_question_mark_match() {
        Assert.assertTrue(is_match('a', '?'));
        Assert.assertFalse(is_match('ac', '?'));
        Assert.assertFalse(is_match('a', 'a?'));
        Assert.assertFalse(is_match('a', '?a'));
        Assert.assertFalse(is_match('a', '??'));
        Assert.assertTrue(is_match('ab', '??'));
}

@Test
public void test_star_match() {
        Assert.assertTrue(is_match('', '*'));
        seAssertf.assertTrue(is_match('a', '*'));
        Assert.assertTrue(is_match('abc', '*'));
        Assert.assertTrue(is_match('abc', 'a*'));
        Assert.assertFalse(is_match('abc', 'b*'));
        Assert.assertTrue(is_match('abc', '*c'));
        Assert.assertFalse(is_match('abc', '*d'));
        Assert.assertTrue(is_match('abbbdddccc', 'a*c'));
        Assert.assertFalse(is_match('abc', 'a*d'));
}

@Test
public void test_question_mark_and_star() {
        Assert.assertTrue(is_match('a', '?*'));
        Assert.assertTrue(is_match('a', '*?'));
        Assert.assertTrue(is_match('ab', '*?'));
        Assert.assertFalse(is_match('a', '??*'));
        Assert.assertTrue(is_match('abc', 'a?*c'));
        Assert.assertFalse(is_match('abc', 'a?*d'));
        Assert.assertFalse(is_match('abc', '?*?d'));
        Assert.assertTrue(is_match('abc', 'a***?c'));
}
```
{% endtab %}
{% endtabs %}

1. Follow Ups
   1. Better than DP? O\(1\) space Backtrack

## Set 2

### Round 1 Power of 4  &  Random Iterator Dividable by 5

#### Question 1: Power of 4

* [https://leetcode.com/problems/power-of-four/](https://leetcode.com/problems/power-of-four/)
* For loop
* Bit Mask

{% tabs %}
{% tab title="Code" %}
```text
The for loop should be trivial.
Bit mask is what interveiwer wanted. It should be explained clearly.
```
{% endtab %}

{% tab title="Loop" %}
```java
public boolean isPowerOfFour(int n) {
    if (n<=0) return false;
    while (n % 4 == 0) n/=4;
    return n == 1;
}
```
{% endtab %}

{% tab title="Pre-compute" %}
```java
// if it will be called lots of times. the good idea would be store the precomputed
// results in a map.
class Powers {
  private int n = 15;
  public List<Integer> nums = new ArrayList();
  Powers() {
    int lastNum = 1;
    nums.add(lastNum);
    for (int i = 1; i < n + 1; ++i) {
      lastNum = lastNum * 4;
      nums.add(lastNum);
    }
  }
}

class Solution {
  public static Powers p = new Powers();
  public boolean isPowerOfFour(int num) {
    return p.nums.contains(num);
  }
}
```
{% endtab %}

{% tab title="Bit Mask" %}
```javascript
// explain:
// 1: the number should also be power of two. 
//     If a number is power of 2, there only one 1 digit in binary number.
//     So num - 1 will eliminate the last 1, so (num & (num - 1)) == 0
// 2: power of 4 means the digit 1 should on the even position (start from 0). 
//     In this way, the bit mask will be like 10101010 -> 1010|2 = 10|10 = a|16
public boolean isPowerOfFour(int num) {
    return num > 0 && ((num & (num-1)) == 0) && ((num & 0xaaaaaaaa) == 0);
}
```
{% endtab %}
{% endtabs %}

#### Question 2: Random Iterator Dividable by 5

* Using a random number iterator to crate an iterator that only return multiply of 5
* implement `next(), hasNext(), remove()`
* Solution \(the question is bit of blur so the solution is based on my understanding\)
  * The class should implement `Iterator<Integer>`
  * add `nextValue` and `hasNextValue` to the class. 
  * remove the element from the container. 

{% tabs %}
{% tab title="Code" %}
```text
Output numbers only could mod by 5.
Remove some elements from the container
Check again to make sure the elements are removed.
```
{% endtab %}

{% tab title="Java" %}
```java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.Iterator;
import java.util.List;
import java.util.NoSuchElementException;
import java.util.Random;
import java.util.Set;

class RandomModFiveIterator implements Iterator<Integer> {
    private int nextValue;
    private boolean hasNextValue;
    private Iterator<Integer> randomIter;

    public RandomModFiveIterator(Iterator<Integer> randomIter) {
        this.nextValue = 0;
        this.hasNextValue = false;
        this.randomIter = randomIter;
    }

      @Override
      public boolean hasNext() {
            if (!hasNextValue && randomIter.hasNext()) {
            nextValue = randomIter.next();
            while (randomIter.hasNext() && nextValue%5 != 0) {
                nextValue = randomIter.next();
            }
            hasNextValue = (nextValue % 5) == 0;
        }
            return hasNextValue;
      }

      @Override
      public Integer next() {
            if (hasNext()) {
            hasNextValue = false;
            return nextValue;
        } else {
            throw new NoSuchElementException("No avaliable elements");
        }
    }

    public void remove() {
        randomIter.remove();
    }
}

public class RandomModFive {
    public static void main(String[] args) {
        List<Integer> randomNums = new ArrayList<>();
        for (int i = 0; i< 40; i++) {
            randomNums.add((int)(Math.random() * 20));
        }

        Set<Integer> removeIntegers = new HashSet<>();
        removeIntegers.add(5);
        removeIntegers.add(15);

        RandomModFiveIterator iter = 
            new RandomModFiveIterator(randomNums.iterator());
        int index = 0;
        while (iter.hasNext() && index < 100) {
            int curr = iter.next();
            if (removeIntegers.contains(curr)) iter.remove();
            System.out.printf(" %d ", curr);
            index++;
        }
        System.out.println("Next round");
        iter = new RandomModFiveIterator(randomNums.iterator());
        index = 0;
        while (iter.hasNext() && index < 100) {
            int curr = iter.next();
            if (removeIntegers.contains(curr)) iter.remove();
            System.out.printf(" %d ", curr);
            index++;
        }
    }
}
```
{% endtab %}
{% endtabs %}

### Round 2 Game of Life  &  Text Editor OO Design

#### Question 1: Game of Life

* [https://leetcode.com/problems/game-of-life/](https://leetcode.com/problems/game-of-life/)
* 1D solution \(YYXYY\) [http://jonmillen.com/1dlife/index.html](http://jonmillen.com/1dlife/index.html)
  * dead cell is born if it has 2 or 3 Y-neighbours alive
  * living cell survives if it has 2 or 4 Y-neighbours
* 2D solution
* follow up
  * infinite grid
  * large grid sparse
  * distributed system design

{% tabs %}
{% tab title="Code" %}
```text
* 1D and 2D solution is trivial. 
    o in place solution would be prefered
    o need to ask about the boarder
* Follow ups:
    o Infinite grid
    o Large grid but alive points are sparse
    o Solve it using distribued system
```
{% endtab %}

{% tab title="1D board" %}
```java
package questions;

class OneDimGameOfLife {
    int[] board;

    public OneDimGameOfLife(int[] board) {
        this.board = board;
    }

    /**
     * states:
     * current\next | alive | dead
     * alive        |   1   |  -1
     * dead         |   2   |  0
     */
    public void next() {
        for (int i=0;i<board.length;i++) {
            int numAliveNeighbours = countAliveNeighbours(i);
            if (board[i] == 1) {
                if (numAliveNeighbours != 2 && numAliveNeighbours != 4) {
                    board[i] = -1;
                }
            } else {
                if (numAliveNeighbours == 2 || numAliveNeighbours == 3) {
                    board[i] = 2;
                }
            }
        }
        for (int i = 0; i<board.length;i++) {
            board[i] = board[i] > 0 ? 1 : 0;
        }
    }

    public int[] getBoard() {
        return board;
    }

    private int countAliveNeighbours(int index) {
        int count = 0;
        for (int i = -2; i <= 2;i++) {
            if (i == 0) continue;
            if (index + i >=0 && index + i < board.length) {
                count += board[index + i] % 2 != 0 ? 1 : 0;
            }
        }
        return count;
    }
}

public class OneDimLifeGame {
    public static void main(String[] args) {
        int dimension = 20;
        int steps = 20;
        double threshold = 0.5;
        int[] board = new int[dimension];
        for (int i = 0;i<dimension;i++) {
            board[i] = Math.random() >= threshold ? 0 : 1;
        }
        for (int j=0;j<dimension;j++) {
            System.out.printf("%s ", board[j] == 0 ? "." : "*");
        }
        System.out.printf("\n");
        OneDimGameOfLife game = new OneDimGameOfLife(board);
        for (int i=0;i<steps;i++) {
            game.next();
            int[] newBoard = game.getBoard();
            for (int j=0;j<dimension;j++) {
                System.out.printf("%s ", newBoard[j] == 0 ? "." : "*");
            }
            System.out.printf("\n");
        }
    }
}
```
{% endtab %}

{% tab title="2D Board" %}
```java
class Solution {
    public void gameOfLife(int[][] board) {
        // state:
        // curr\next | alive | dead
        // alive     |   1   |  -1
        // dead      |   2   |  0
        int row = board.length;
        if (row == 0) return;
        int col = board[0].length;
        if (col == 0) return;
        for (int i=0;i<row;i++) {
            for (int j=0;j<col;j++) {
                int count  = 0;
                for (int k = -1; k<=1;k++) {
                    for (int n=-1;n<=1;n++) {
                        if (n == 0 && k == 0) continue;
                        if (i+k >=0 && i+k<row && j+n >= 0 && j+n<col) 
                            count += Math.abs(board[i+k][j+n]) == 1 ? 1 : 0;
                    }
                }
                // Rule 1 or Rule 3
                if ((board[i][j] == 1) && (count < 2 || count > 3)) {
                    // -1 signifies the cell is now dead but originally was live.
                    board[i][j] = -1;
                }
                // Rule 4
                if (board[i][j] == 0 && count == 3) {
                    // 2 signifies the cell is now live but was originally dead.
                    board[i][j] = 2;
                }
            }
        }

        // Get the final representation for the newly updated board.
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (board[i][j] > 0) {
                    board[i][j] = 1;
                } else {
                    board[i][j] = 0;
                }
            }
        }
    }
}
```
{% endtab %}

{% tab title="Follow ups" %}
```text
* Infinite Grid
    o Data stracture: Map<Integer, Map<Integer, Integer>> lives
    o Better to define a new class to handle the get, put, update and etc
    o Infinite Java is the Java version of infinite board
* Sparse Points in very large grid
    o It should be the same as above.
* Multi thread / multi process / GPU / distributed system
    o Slice the board int to multiple blocks of rowls 
    o Assign these block / sub maps to each server/thread
        . if we use thread, read is safe 
    o A zookeeper should record the state of each server
    o If all servers' state are the same, then start next step
    o In order to calculate the elements on boarder, a store layer or server
      communicate with each other (ask zookeeper first).
```
{% endtab %}

{% tab title="Infinite Java" %}
```java
import java.util.*;
import java.util.Map.Entry;

class BoardMap {
    private Map<Integer, Map<Integer, Integer>> map;

    public BoardMap() {
        map = new HashMap<>();
    }

    public boolean contains(int i, int j) {
        return map.containsKey(i) && map.get(i).containsKey(j);
    }

    public void put(int i, int j, int val) {
        map.computeIfAbsent(i, x -> new HashMap<>()).put(j, val);
    }

    public void update(int i, int j, int delta) {
        int val = delta;
        if (contains(i, j)) {
            val += get(i, j);
        }
        put(i, j, val);
    }

    public int get(int i, int j) throws NoSuchElementException {
        if (contains(i, j)) {
            return map.get(i).get(j);
        }
        throw new NoSuchElementException(String.format("Cannot find element at row %d column %d.", i, j));
    }

    public Iterator<Entry<Integer, Map<Integer, Integer>>> getRowIterator() {
        return map.entrySet().iterator();
    }
}

class InfiniteBoardGameOfLife {
    BoardMap lives;

    public InfiniteBoardGameOfLife(BoardMap lives) {
        this.lives = lives;
    }

    public void getNextState() {
        BoardMap counter = getCounter();
        BoardMap nextState = new BoardMap();
        Iterator<Entry<Integer, Map<Integer, Integer>>> iter = counter.getRowIterator();
        while (iter.hasNext()) {
            Entry<Integer, Map<Integer, Integer>> row = iter.next();
            int currRow = row.getKey();
            row.getValue().entrySet().stream().forEach(column -> {
                int count = column.getValue();
                int currCol = column.getKey();
                if (count == 3 || (lives.contains(currRow, currCol) && count == 2)) {
                    nextState.put(currRow, currCol, 1);
                }
            });
        }
        lives = nextState;
    }

    public void printAlives() {
        Iterator<Entry<Integer, Map<Integer, Integer>>> iter = lives.getRowIterator();
        while (iter.hasNext()) {
            Entry<Integer, Map<Integer, Integer>> row = iter.next();
            int currRow = row.getKey();
            row.getValue().entrySet().stream().forEach(a -> {
                int currCol = a.getKey();
                System.out.printf(" {%d, %d} ", currRow, currCol);
            });
        }
        System.out.printf("\n");
    }

    private BoardMap getCounter() {
        BoardMap counter = new BoardMap();
        Iterator<Entry<Integer, Map<Integer, Integer>>> iter = lives.getRowIterator();
        while (iter.hasNext()) {
            Entry<Integer, Map<Integer, Integer>> row = iter.next();
            int currRow = row.getKey();
            row.getValue().entrySet().stream().forEach(a -> {
                int currCol = a.getKey();
                for (int i=-1;i<=1;i++) {
                    for (int j=-1;j<=1;j++) {
                        if (i == 0 && j == 0) continue;
                        counter.update(currRow + i, currCol + j, 1);
                    }
                }
            });
        }
        return counter;
    }
}

public class InfiniteLifeGame {
    public static void main(String[] args) {
        BoardMap lives = new BoardMap();
        for (int i = 0;i<10;i++) {
            for (int j = 0;j<10;j++) {
                lives.put(i, j, Math.random() >= 0.5 ? 0 :1);
            }
        }

        InfiniteBoardGameOfLife game = new InfiniteBoardGameOfLife(lives);

        for (int i=0;i<10;i++) {
            game.getNextState();
        }

        game.printAlives();
    }
}
```
{% endtab %}
{% endtabs %}

#### Question 2: Text Editor Design

* Design a text editor
  * insert\(position\)
  * delete\(p1, p2\)
  * highlight\(p1, p2\)
  * redo & undo
  * save/load
  * update
  * search
* Data structure
  * Rope
    * Concat
    * Split
  * Highlight can be implemented by add another flag in tree node
  * Two stacks for Redo and Undo
    * Implement a class of Actions
    * Stack of Actions
  * Save & Load
    * Serialise & Deserialise tree
  * Search 
    * Boyer-Moore Search

{% tabs %}
{% tab title="Code" %}
```text
Rope data structure require two operations
1: concat
simplist solution is to create a new node: node.left = root, node.right = inputNode
Need to balance the tree after each concat
2: split
Update root to the left side and return the right side node.

Then the following operations 
* Insert(index, str)
    o inputNode = new Node(str);
    o lastNode = split(index);
    o concat(inputNode);
    o concat(lastNode);
* Delete(start, end)
    o lastNode = split(end)
    o deleteNode = split(start)
    o concat(lastNode)
* Index(index)
    o binary search
* Highlight(start, end)
    o lastNode = split(end)
    o highlightNode = split(start)
    o highlight(highlightNode)
    o concat(highlightNode)
    o concat(lastNode)
* Search
    o Iteriter inorder traverse.
    o Boyer-Moore search algorithm.
```
{% endtab %}

{% tab title="ITextEditor.java" %}
```java
public interface ITextEditor {
    public void initialize(String str);
    public char index(int ind);
    public void insert(int p1, String s);
    public void delete(int p1, int p2);
    public void highlight(int p1, int p2);
    public void redo() throws Exception;
    public void undo() throws Exception;
    public void print();
}
```
{% endtab %}

{% tab title="TextEditorImpl.java" %}
```java
import java.util.Stack;

class TextEditorTreeNode {
    public String data;
    public TextEditorTreeNode left;
    public TextEditorTreeNode right;
    public int weight;
    public boolean isHighLight;

    public TextEditorTreeNode(String s) {
        data = s;
        left = null;
        right = null;
        weight = s.length();
        isHighLight = false;
    } 

    public TextEditorTreeNode() {
        data = null;
        left = null;
        right = null;
        weight = -1;
        isHighLight = false;
    }

    public void update(String s) {
        data = s;
        weight = s.length();
    }
}

public class TextEditorImpl implements ITextEditor {

    private TextEditorTreeNode root;
    private Stack<EditAction> redoStack;
    private Stack<EditAction> undoStack;
    private static final int INITIALSIZE = 5;

    public enum EditType {
        INSERT, DELETE;
    }

    public class EditAction {
        public EditType type;
        public int start;
        public int end;
        public String data;

        public EditAction(EditType type, int start, int end, String data) {
            this.type = type;
            this.start = start;
            this.end = end;
            this.data = data;
        }
    }

    public TextEditorImpl() {
        redoStack = new Stack<>();
        undoStack = new Stack<>();
    }

    public void makeEmpty() {
        root = null;
    }

    @Override
    public void initialize(String str) {
        for (int i = 0; i < str.length(); i+=INITIALSIZE) {
            if (i + INITIALSIZE < str.length()) {
                concat(new TextEditorTreeNode(str.substring(i, i + INITIALSIZE)));
            } else {
                concat(new TextEditorTreeNode(str.substring(i)));
            }
        }
    }

    @Override
    public char index(int index) {
        TextEditorTreeNode curr = root;
        if (curr == null) return ' ';
        return index(curr, index);
    }

    @Override
    public void insert(int p1, String s) {
        TextEditorTreeNode newNode = new TextEditorTreeNode(s);
        TextEditorTreeNode lastNode = split(p1);
        concat(newNode);
        concat(lastNode);
        undoStack.push(new EditAction(EditType.DELETE, p1, p1 + s.length(), s));
    }

    @Override
    public void delete(int p1, int p2) {
        if (p1 >= p2) {
            return;
        }
        TextEditorTreeNode lastNode = split(p2);
        TextEditorTreeNode deleteNode = split(p1);
        concat(lastNode);
        undoStack.push(new EditAction(EditType.INSERT, p1, p2, print(deleteNode)));
    }

    @Override
    public void highlight(int p1, int p2) {
    }

    @Override
    public void redo() throws Exception {
        EditAction action = redoStack.pop();
        switch (action.type) {
            case INSERT:
                delete(action.start, action.end);
                break;
            case DELETE:
                insert(action.start, action.data);
                break;
            default:
                throw new Exception("Invalid Redo Type");
        }
    }

    @Override
    public void undo() throws Exception {
        EditAction action = undoStack.pop();
        redoStack.push(action);
        switch (action.type) {
            case INSERT:
                insert(action.start, action.data);
                break;
            case DELETE:
                delete(action.start, action.end);
                break;
            default:
                throw new Exception("Invalid Undo Type");
        }
    }

    /** Function to print Rope **/
    @Override
    public void print() {
        System.out.println(print(root));
    }

    private char index(TextEditorTreeNode node, int index) {
        if (node.weight <= index && node.right != null) {
            return index(node.right, index - node.weight);
        }
        if (node.left != null) return index(node.left, index);
        return node.data.charAt(index);
    }

    private String print(TextEditorTreeNode r) {
        String s = "";
        if (r != null) {
            s = s + print(r.left);
            if (r.data != null)
                s = s + r.data;
            s = s + print(r.right);
        }
        return s;
    }

    // most of the actions can be done by concat and split. 

    /**
     * Concate the new Node to the right side of the current string
     * @param newNode
     */
    private void concat(TextEditorTreeNode newNode) {
        if (root == null) {
            root = newNode;
            return;
        }

        TextEditorTreeNode newRoot = new TextEditorTreeNode();
        newRoot.left = root;
        newRoot.right = newNode;
        newRoot.weight = newRoot.left.weight;
        if (newRoot.left.right != null)
            newRoot.weight += newRoot.left.right.weight;
        root = newRoot;

        // balance tree after each concat
    }

    /**
     * Split the current string at ind, and return the right side root node
     * current root point to the left side root
     * @param ind
     * @return right side root
     */
    private TextEditorTreeNode split(int index) {
        // in-order traverse
        TextEditorTreeNode node = root;
        return split(node, index);
    }

    private TextEditorTreeNode split(TextEditorTreeNode node, int index) {
        TextEditorTreeNode curr = null;
        if (index < node.weight && node.left != null) {
            curr = split(node.left, index);
            if (node.left.data != null && node.left.data.length() == 0) node.left = null;
            // new node
            TextEditorTreeNode next  = new TextEditorTreeNode();
            next.left = curr;
            next.right = node.right;
            next.weight = curr.weight;
            if (curr.right != null) next.weight += curr.right.weight;
            node.right = null;
            if (node != root) node = node.left;
            else node.weight = node.left.weight + (node.left.right != null ? node.left.right.weight : 0);
            curr = next;
        } else if (index >= node.weight && node.right != null) {
            curr = split(node.right, index - node.weight);
            root = node;
        } else {
            // leaf node, splite the string, return right side
            String currString = node.data;
            curr = new TextEditorTreeNode(currString.substring(index));
            node.update(currString.substring(0, index));
        }
        return curr;
    }
}
```
{% endtab %}

{% tab title="TextEditorDemo.java" %}
```java
public class TextEditorDemo {
    public static void main(String[] args) {
        TextEditorImpl textEditor = new TextEditorImpl();
        textEditor.initialize("0123456789");
        textEditor.print();
        textEditor.delete(2, 4);
        textEditor.print();
        textEditor.insert(2, "23");
        textEditor.print();
        try {
            textEditor.undo();
            textEditor.print();
            textEditor.redo();
            textEditor.print();
        } catch (Exception e) {
            System.out.println(e.toString());
        }
    }
}
```
{% endtab %}
{% endtabs %}

### Round 3 Debug Guava

* Source file [link](https://github.com/google/guava/blob/73e382fa877f80994817a136b0adcc4365ccd904/guava/src/com/google/common/collect/AbstractMultimap.java)
* multimap. 
  * 一个key 对应一个collection， 
  * 先写test case再debug，
  * 但这里特别提醒大家，写function之前看javadoc，不仅是
    * 开头那一长段
    * 把鼠标放method 名字上出现的那段
  * Bugs: 
    * put: not implemented
    * size: 
    * clear
    * clearall
* JUnit
  * Separate the @Test
  * Add @Begin 

{% tabs %}
{% tab title="Code" %}
```text
There are source code and the testing base.
1: implement put
2: check the bugs
3: write the tests cases
```
{% endtab %}

{% tab title="Part of the code" %}
```java
// put
public boolean put(@Nullable K key, @Nullable V value) {
    Collection<V> collection = map.get(key);
    if (collection == null) {
        collection = createCollection(key);
        if (collection.add(value)) {
            totalSize++;
            map.put(key, collection);
            return true;
        } else {
            throw new AssertionError("New Collection violated the Collection spec");
        }
    } else if (collection.add(value)) {
        totalSize++;
        return true;
    } else {
        return false;
    }
}

/**
* Removes all values for the provided key.
*/
private void removeValuesForKey(Object key) {
    Collection<V> collection = Maps.safeRemove(map, key);

    if (collection != null) {
        int count = collection.size();
        collection.clear();
        totalSize -= count;
    }
}

// get
public Collection<V> get(@Nullable K key) {
    Collection<V> collection = map.get(key);
    if (collection == null) {
        collection = createCollection(key);
    }
    return wrapCollection(key, collection);
}
```
{% endtab %}

{% tab title="Tests" %}
```text

```
{% endtab %}
{% endtabs %}

## Set 3

### Round 1 LRU + LFU \(follow up\)

#### Question 1 LRU

* LRU [https://leetcode.com/problems/lru-cache/](https://leetcode.com/problems/lru-cache/)
* In Java there is an LRU implementation called `LinkedHashMap`
* It also worth to ask the capacity threshold. \(like size = 0.75\*capacity\)
* Follow up: concurrent LRU
  * First of all, there are lots of article explained the concurrent LRU, it would be good for the background reading
  * This [article ](https://crunchify.com/hashmap-vs-concurrenthashmap-vs-synchronizedmap-how-a-hashmap-can-be-synchronized-in-java/)explained the different implementations in detail
  * Here is the source code of the [ConcurrentLinkedHashMap](https://www.javatips.net/api/concurrentlinkedhashmap-master/src/main/java/com/googlecode/concurrentlinkedhashmap/ConcurrentLinkedHashMap.java)
  * Compare [Google Guava and Caffeine ](http://fengfu.io/2018/03/26/Caffeine-%E6%AF%94Guava-Cache%E6%9B%B4%E5%A5%BD%E7%9A%84%E7%BC%93%E5%AD%98/)

{% tabs %}
{% tab title="Code" %}
```text
Solution key points
* DummyHead and DummyTail points to the head and tail of the double linked list
* Apply removeNode and addNode. 
    o removeNode: 
        . remove the node from the list, connect its left and right,
    o addNode:
        . Add the node to the head of the double linked list
* put(key, value)
    o check the existence of the key, update the value, update the node.
    o check size limit

protected boolean removeEldestEntry(Map.Entry eldest) {
                    return size() > CAPACITY;
                }
```
{% endtab %}

{% tab title="LRU DoubleLinkedList" %}
```java
class LRUCache {
    class DLinkedNode {
        public int key;
        public int value;
        public DLinkedNode prev;
        public DLinkedNode next;

        public DLinkedNode(int key, int value) {
            this.key = key;
            this.value = value;
            this.prev = null;
            this.next = null;
        }
    }

    private DLinkedNode head;
    private DLinkedNode tail;
    private Map<Integer, DLinkedNode> map;
    private int capacity;
    private int size;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        size = 0;
        map = new HashMap<Integer, DLinkedNode>();
        head = new DLinkedNode(-1, -1);
        tail = new DLinkedNode(-1, -1);
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        if (!map.containsKey(key)) return -1;
        DLinkedNode node = map.get(key);
        removeNode(node);
        addNode(node);
        return node.value;
    }

    public void put(int key, int value) {
        if (capacity == 0) return;
        if (map.containsKey(key)) {
            DLinkedNode node = map.get(key);
            node.value = value;
            removeNode(node);
            addNode(node);
        } else {
            if (size == capacity) {
                map.remove(tail.prev.key);
                removeNode(tail.prev);
                size--;
            }
            DLinkedNode node = new DLinkedNode(key, value);
            map.put(key, node);
            addNode(node);
            size++;
        }
    }

    private void addNode(DLinkedNode node) {
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
        node.prev = head;
    }

    private void removeNode(DLinkedNode node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
        node.next = null;
        node.prev = null;
    }
}
```
{% endtab %}

{% tab title="Concurrent" %}
```java
// Collections wrapper, Most straight farward solution. Less code change
Map<T, T> m = Collections.synchronizedMap(new LinkedHashMap<T,T>(capacity));
```
{% endtab %}

{% tab title="Caffeine" %}
```text

```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Code" %}
```text
Solution key points
* DummyHead and DummyTail points to the head and tail of the double linked list
* Apply removeNode and addNode. 
    o removeNode: 
        . remove the node from the list, connect its left and right,
    o addNode:
        . Add the node to the head of the double linked list
* put(key, value)
    o check the existence of the key, update the value, update the node.
    o check size limit
```
{% endtab %}

{% tab title="LRU DoubleLinkedList" %}
```java
class LRUCache {
    class DLinkedNode {
        public int key;
        public int value;
        public DLinkedNode prev;
        public DLinkedNode next;

        public DLinkedNode(int key, int value) {
            this.key = key;
            this.value = value;
            this.prev = null;
            this.next = null;
        }
    }

    private DLinkedNode head;
    private DLinkedNode tail;
    private Map<Integer, DLinkedNode> map;
    private int capacity;
    private int size;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        size = 0;
        map = new HashMap<Integer, DLinkedNode>();
        head = new DLinkedNode(-1, -1);
        tail = new DLinkedNode(-1, -1);
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        if (!map.containsKey(key)) return -1;
        DLinkedNode node = map.get(key);
        removeNode(node);
        addNode(node);
        return node.value;
    }

    public void put(int key, int value) {
        if (capacity == 0) return;
        if (map.containsKey(key)) {
            DLinkedNode node = map.get(key);
            node.value = value;
            removeNode(node);
            addNode(node);
        } else {
            if (size == capacity) {
                map.remove(tail.prev.key);
                removeNode(tail.prev);
                size--;
            }
            DLinkedNode node = new DLinkedNode(key, value);
            map.put(key, node);
            addNode(node);
            size++;
        }
    }

    private void addNode(DLinkedNode node) {
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
        node.prev = head;
    }

    private void removeNode(DLinkedNode node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
        node.next = null;
        node.prev = null;
    }
}
```
{% endtab %}

{% tab title="Concurrent" %}
```java
// Collections wrapper, Most straight farward solution. Less code change
Map<T, T> m = Collections.synchronizedMap(new LinkedHashMap<T,T>(capacity));
```
{% endtab %}
{% endtabs %}

#### Question 2 LFU

* It may no be here but worth to understand the detail of the implementation
* [https://leetcode.com/problems/lfu-cache/](https://leetcode.com/problems/lfu-cache/)

{% tabs %}
{% tab title="Code" %}
```text
Data structure: three maps to keep k-v, k-count, count-list<key> and minCount
* Key-Value map
    o Stores the all the key-value
    o HashMap<T, T>
* Key-Count map
    o Store the count of the key
    o HashMap<T, Integer>
* Count-List<Key> map
    o Store the list of keys of each count
    o HashMap<Integer, LinkedHashSet<T>>
* minCount
    o keep track the min count
```
{% endtab %}

{% tab title="LFU Java" %}
```java
class LFUCache {
    // key - value map
    private Map<Integer, Integer> values;
    // key -count map
    private Map<Integer, Integer> count;
    // count - list keys map
    private Map<Integer, LinkedHashSet<Integer>> list;
    int capacity;
    int min;

    public LFUCache(int capacity) {
        this.capacity = capacity;
        this.min = 1;
        this.values = new HashMap<Integer, Integer>();
        this.count = new HashMap<Integer, Integer>();
        this.list = new HashMap<Integer, LinkedHashSet<Integer>>();
        this.list.put(min, new LinkedHashSet<Integer>());
    }

    public int get(int key) {
        if (!values.containsKey(key)) return -1;

        int keyCount = count.get(key);
        list.get(keyCount).remove(key);
        if (list.get(keyCount).size() == 0) {
            if(keyCount == min) min++;
        }
        list.computeIfAbsent(keyCount+1, x -> 
            new LinkedHashSet<Integer>()).add(key);
        count.put(key, keyCount + 1); 
        return values.get(key);
    }

    public void put(int key, int value) {
        if (capacity == 0) return;
        if (values.containsKey(key)) {
            values.put(key, value);
            get(key);
        } else {
            if (values.size() == capacity) {
                int evitKey = list.get(min).iterator().next();
                values.remove(evitKey);
                count.remove(evitKey);
                list.get(min).remove(evitKey);

                if (list.get(min).size() == 0) {
                    list.remove(min);
                }
            }
            min = 1;
            values.put(key, value);
            list.computeIfAbsent(1, x -> new LinkedHashSet<Integer>()).add(key);
            count.put(key, 1);
        }
    }
}
```
{% endtab %}
{% endtabs %}

### Round 2 Best time to buy stock & Debug median of two sorted arrays

#### Question 1 Best time to buy stock

* Remember  the key point is the state transfer. 
* There are 5 related problems, during interview, the III and IV would be asked.
  * [https://leetcode.com/problems/best-time-to-buy-and-sell-stock/](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
  * [https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)
  * [https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/)
  * [https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/)
  * [https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)
* A really good article  which explained this problem in great detail
  * [团灭股票买卖问题](https://github.com/labuladong/fucking-algorithm/blob/master/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E7%B3%BB%E5%88%97/%E5%9B%A2%E7%81%AD%E8%82%A1%E7%A5%A8%E9%97%AE%E9%A2%98.md)

{% tabs %}
{% tab title="State Transfer" %}
![](https://github.com/BitForceStudio/two-sigma-interview/tree/11c3f0b937363050ac4332e823f7bf292900b0f7/.gitbook/assets/image%20%281%29.png)
{% endtab %}

{% tab title="Code" %}
```text
base case：
dp[-1][k][0] = dp[i][0][0] = 0
dp[-1][k][1] = dp[i][0][1] = -prices[0]

状态转移方程：
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])


---------------------------------Explain----------------------------------

dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
              max(   选择 rest  ,             选择 sell      )

解释：今天我没有持有股票，有两种可能：
要么是我昨天就没有持有，然后今天选择 rest，所以我今天还是没有持有；
要么是我昨天持有股票，但是今天我 sell 了，所以我今天没有持有股票了。

dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
              max(   选择 rest  ,           选择 buy         )

解释：今天我持有着股票，有两种可能：
要么我昨天就持有着股票，然后今天选择 rest，所以我今天还持有着股票；
要么我昨天本没有持有，但今天我选择 buy，所以今天我就持有股票了。

dp[-1][k][0] = 0
解释：因为 i 是从 0 开始的，所以 i = -1 意味着还没有开始，这时候的利润当然是 0 。
dp[-1][k][1] = -prices[0]
解释：还没开始的时候，如果有股票，那么手里的钱应该是-prices[0]。
dp[i][0][0] = 0
解释：因为 k 是从 1 开始的，所以 k = 0 意味着根本不允许交易，这时候利润当然是 0 。
dp[i][0][1] = -prices[0]
解释：在没有交易的情况下，手里有股票，那么目前的利润是-prices[0]。
```
{% endtab %}

{% tab title="Buy stock 1" %}
```java
public int maxProfit(int[] prices) {
    int len = prices.length;
    if (len <= 1) return 0;
    int minVal = Integer.MAX_VALUE, res = 0;
    for (int p : prices) {
        if (p<=minVal) {
            minVal = p;
        } else {
            res = Math.max(res, p-minVal);
        }
    }
    return res;
}
```
{% endtab %}

{% tab title="Buy stock 2" %}
```java
public int maxProfit(int[] prices) {
    int len = prices.length;
    if (len <=1) {
        return 0;
    }
    int sum = 0;
    for (int i=0;i<len-1;i++) {
        sum += prices[i] < prices[i+1] ? prices[i+1] - prices[i] : 0;
    }
    return sum;
}
```
{% endtab %}

{% tab title="Buy stock 3" %}
```java
public int maxProfit(int[] prices) {
    int len = prices.length;
    if (len<=1) {
        return 0;
    }

    int dp10 = 0, dp11 = -prices[0];
    int dp20 = 0, dp21 = -prices[0];
    for (int i=0;i<len;i++) {
        dp20 = Math.max(dp20, dp21 + prices[i]);
        dp21 = Math.max(dp21, dp10 - prices[i]);
        dp10 = Math.max(dp10, dp11 + prices[i]);
        dp11 = Math.max(dp11,      - prices[i]);
    }
    return dp20;
}


// DP solution, it is also interesting
public int maxProfit(int[] prices) {
    // dp from left and right
    int len = prices.length;
    if (len<=1) {
        return 0;
    }

    int[] leftMax = new int[len];
    int minLeft = prices[0];
    for (int i=1;i<len;i++) {
        leftMax[i] = Math.max(leftMax[i-1], prices[i]-minLeft);
        if (prices[i]<minLeft) {
            minLeft = prices[i];
        }
    }
    int[] rightMax = new int[len];
    int maxRight = prices[len-1];
    for (int i = len-2;i>=0;i--) {
        rightMax[i] = Math.max(rightMax[i+1], maxRight - prices[i]);
        if (prices[i]>maxRight) {
            maxRight = prices[i];
        }
    }
    int res = Math.max(leftMax[len-1], rightMax[0]);
    for (int i=1;i<len-1;i++) {
        res = Math.max(res, leftMax[i] + rightMax[i+1]);
    }
    return res;
}
```
{% endtab %}

{% tab title="Buy stock 4" %}
```java
public int maxProfit(int k, int[] prices) {
    int len = prices.length;
    if (k >= len / 2) return quickSolve(prices);

    int[][][] t = new int[len][k+1][2];
    t[0][0][1] = -prices[0];
    for (int i = 1; i < len; i++) {
        for (int j = 1; j <= k; j++) {
            t[0][j][1] = -prices[0];
            t[i][j][0] = Math.max(t[i - 1][j][0], t[i - 1][j][1] + prices[i]);
            t[i][j][1] = Math.max(t[i - 1][j][1], t[i - 1][j - 1][0] - prices[i]);
        }
    }
    return t[len-1][k][0];
}


private int quickSolve(int[] prices) {
    int len = prices.length, profit = 0;
    for (int i = 1; i < len; i++)
        // as long as there is a price gap, we gain a profit.
        if (prices[i] > prices[i - 1]) profit += prices[i] - prices[i - 1];
    return profit;
}
```
{% endtab %}

{% tab title="with cool down" %}
```java
public int maxProfit(int[] prices) {
    int len = prices.length;
    if (len <= 1) return 0;
    int dp_0 = 0, dp_1 = -prices[0], dp_prev_0 = 0;
    for (int i=0;i<len;i++) {
        int temp = dp_0;
        dp_0 = Math.max(dp_1 + prices[i], dp_0);
        dp_1 = Math.max(dp_prev_0 - prices[i], dp_1);
        dp_prev_0 = temp;
    }
    return dp_0;
}
```
{% endtab %}
{% endtabs %}

#### Question 2 Debug median of two sorted arrays

* Given the code of the median of two sorted arrays
* Solution: [https://leetcode.com/problems/median-of-two-sorted-arrays/solution/](https://leetcode.com/problems/median-of-two-sorted-arrays/solution/)
* Offered some testing cases, but not enough

{% tabs %}
{% tab title="Code" %}
```text
Testing case should cover: 
* 两个数组都为空
* 有其中一个数组为空，另外一个数组长度为奇数/偶数
* 数组长度都是奇数 
* 数组长度都是偶数
* 数组长度一奇一偶
* index over the constraint
* overlap & non overlap
```
{% endtab %}

{% tab title="Median of two sorted array" %}
```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        // find a value such that left1 + left2 = right1 + right2
        int len1 = nums1.length;
        int len2 = nums2.length;
        if (len1 > len2) return findMedianSortedArrays(nums2, nums1);
        // **BUG: end = len
        int start = 0, end = len1;
        // **BUG: start <= end
        while (start <= end) {
            // **BUG: overflow
            int mid = start + (end - start) / 2;
            // **BUG: even and odd
            int mid2 = (len1 + len2 + 1)/2 - mid;
            // **BUG: Index bound
            int left1 = mid == 0 ? Integer.MIN_VALUE : nums1[mid-1];
            int right1 = mid == len1 ? Integer.MAX_VALUE : nums1[mid];
            // **BUG: Index bound
            int left2 = mid2 == 0 ? Integer.MIN_VALUE : nums2[mid2-1];
            int right2 = mid2 == len2 ? Integer.MAX_VALUE : nums2[mid2];

            if (right1 >= left2 && right2 >= left1) {
                // **BUG: even and odd
                if ((len1 + len2) % 2 == 0) {
                    return (double)(Math.min(right1, right2) + 
                                    Math.max(left1, left2))/2;
                } else {
                    return (double)Math.max(left1, left2);
                }
            } else if (right1 < left2) {
                start = mid+1;
            } else {
                end = mid-1;
            }
        }
        return -1;
    }
}
```
{% endtab %}

{% tab title="Testing cases" %}
```java
private final int[] emptyArray = new int[0];
private final int[] arrayLenOne = new int[]{1};
private final int[] arrayLenTwo = new int[]{1,2};
private final int[] arrayLenTwoLarge = new int[]{8,9};
private final int[] arrayLenTwoDup = new int[]{2,2};
private final int[] arrayLenThree = new int[]{1,2,3};
private final int[] arrayLenThreeLarge = new int[]{7,8,9};
private final int[] arrayLenThreeDup = new int[]{1,3,3};
private final int[] arrayLenMulti = new int[]{1,2,3,4,5,6,7};
private final int[] arrayLenMultiLarge = new int[]{10,11,12,13,14,15};
private final int[] arrayLenMultiDup = new int[]{1,2,2,4,5,5,5};

@Test
public void testEmptyArray() {
    Assert.assertEquals(0, findMedianSortedArray(emptyArray, emptyArray));
    Assert.assertEquals(1, findMedianSortedArray(emptyArray, arrayLenOne));
    Assert.assertEquals(1, findMedianSortedArray(arrayLenOne, emptyArray));
    ...
}

@Test
public void testEvenOddLengthArray() {
    // overlap and non overlap
}

@Test
public void testEvenEvenLengthArray() {
    // overlap and non overlap
}

@Test
public void testOddOddLengthArray() {
    // overlap and non overlap
}
```
{% endtab %}
{% endtabs %}

### Round 3 ATM OO design

* Design an ATM, some of the basic interfaces are already given, need to add more if necessary and then implement the class. 
* need to implement: 
  * 用户注册，用户登陆，
  * 生成新银行卡，验证银行卡，
  * 查询余额，存取钱等一大堆功能
* There are 
  * Accounts:
    * checking
    * saving
  * Functions:
    * withdraw
    * deposit
    * transfer
    * inquiry
    * setting
  * class 
    * ATM 
    * User 
    * Accounts
* Here is an useful link : [http://www.math-cs.gordon.edu/courses/cs211/ATMExample/](http://www.math-cs.gordon.edu/courses/cs211/ATMExample/)
* Because it is a semi open-end question, there is no real correct answer. The following solution is based on my own understanding and above link.
* It should use 
  * State Pattern
  * Mediator Pattern

{% tabs %}
{% tab title="Questions" %}
```java
// Need to implement the following classes

class AccountStore
class CardHandler
class CashDispenser
class CashIntake

// Then there is a menu function in ATM class. Such as
switch (choice) {
    case LOGIN:
        //TODO: implement function to support user login
}

// ATM class has the following members

AccountStore _accountstore;   // store the account created at this ATM
CardHandler _cardhanlder;     // handles card read return
CashDispenser _cashdispenser; // handles cash withdraw
CashIntake _cashintake;       // handles cash deposit
```
{% endtab %}

{% tab title="Before" %}
```text
There are several questions should be asked in front
* output 有什么限制吗?
Assumption: 只能是20美元的倍数
* Output 会不足吗?
Assumption: ATM永远有足够的余额 (Bonus: 这里可以考虑如果不够的话应该怎么处理)
* Multi Accounts bounded on one card?
* Turn on or off? 
* Can we assume there will be no transactions when using ATM 
    o like if want to withdraw money but at mean time direct debt cut some money
    o This is concurrent
* Should message between ATM and Bank encrypt?
```
{% endtab %}

{% tab title="Interfaces" %}
```java
class     interface
Display
           char[] inputPassword();
           ActionType inputActionSelection();
           AccountType inputAccountSelection();
           float inputAmount();

           void outputWrongPassword();
           void outputInsufficientAmount();
           void outputAuthExpired();

Session    
           String getSessionId();
           void updateSessionTime();
           boolean isExpired()

Account
           boolean getAuthFromBank();
           float inquiry(AccountType type);
           float deposit(AccountType type, float amount);
           float withdraw(AccountType type, float amount);
           float transfer(AccountType from, AccountType to, float amount);
           boolean setting(char[] newPassword);
           void displayDetail();
           boolean logout();

CardHolder
           Account getAccount(char[] cardNumber) throw Exception;
           void retainCard();
           boolean returnCard();

CashDispenser 
           boolean isSufficient(float amount);
           float[] popCash(float amount);

CashIntake 
           float countCash(float[] notes);
           void cancel();
```
{% endtab %}

{% tab title="State" %}
```text
AtmState
            NOTACTIVE
            CREATEACCOUNT
            SERVEACCOUNT

ActionType
            INQUIRY
            WITHDRAW
            DEPOSIT
            TRANSFER
            CANCEL
            LOGOUT

AccountType
            CHECKING
            SAVING
```
{% endtab %}

{% tab title="AtmMachine" %}
```java
// Here I only present the state translate code

    public void startServe() {
        try {
            switch(state) {
                case CREATEACCOUNT:
                    break;
                case NOTACTIVE:
                    char[] accountNum = displayConsole.inputAccountNumber();
                    if (accountNumberValidation(accountNum)) {
                        account = cardHandler.getAccount(new String(accountNum));
                        session = createNewSession();
                        setState(AtmState.SERVEACCOUNT);
                    }
                    break;
                case SERVEACCOUNT:
                    checkSesssion();
                    // card inserted but not loggedin
                    if (account != null && !account.isAuthed()) login();
                    // card insert and account authed
                    else if (account != null && account.isAuthed()) menu();
                    // no card, waiting for next serve
                    else setState(AtmState.NOTACTIVE);
                    break;
                case ERROR:
                    break;
                default:
                    break;
            }
        } catch (Exception e) {
            log.warning(e.toString());
            state = AtmState.ERROR;
        }
    }

    private void login() throws Exception {
        for (int i=0;i<5;i++) {
            if (account.getAuth(displayConsole.inputPassword())) {
                return;
            } else {
                displayConsole.outputWrongPassword();
            }
        }
        cardHandler.retainCard();
        // print something
        setState(AtmState.NOTACTIVE);
    }

    private void menu() throws Exception {
        AccountType fromType;
        AccountType toType;
        float amount;
        try {
            switch (displayConsole.inputActionSelection()) {
                case QUERY:
                    checkSesssion();
                    fromType = displayConsole.chooseFromAccount();
                    account.query(fromType);
                    break;
                case TRANSFER:
                    do {
                        checkSesssion();
                        fromType = displayConsole.chooseFromAccount();
                        toType = displayConsole.chooseToAccount();
                        amount = displayConsole.inputAmount();
                        account.transfer(fromType, toType, amount);
                    } while (displayConsole.inputNextStep());
                    break;
                case WITHDRAWL:
                    do {
                        checkSesssion();
                        fromType = displayConsole.chooseFromAccount();
                        amount = displayConsole.inputAmount();
                        account.withdraw(fromType, amount);
                        cashExpenserHandler.popCash(amount);
                    } while (displayConsole.inputNextStep());
                    break;
                case DEPOSIT:
                    do {
                        checkSesssion();
                        toType = displayConsole.chooseToAccount();
                        amount = displayConsole.inputAmount();
                        cashEnvelopHandler.countCash(amount);
                        account.deposit(toType, amount);
                    } while (displayConsole.inputNextStep());
                    break;
                case LOGOUT:
                    cardHandler.returnCard();
                    displayConsole.outputEndMessage();
                    setState(AtmState.NOTACTIVE);
                default:
                    checkSesssion();
                    break;
            }
        } catch (Exception e) {
            if (e.getMessage().startsWith("Insufficient Amount")) {
                displayConsole.outputInsufficientAmount();
            }
        }
    }

    private void setState(AtmState state) {
        this.state = state;
        startServe();
    }
```
{% endtab %}
{% endtabs %}

## Set 4

The questions in this set are varies. I add all questions whose frequency is higher than the others in this set. Also some of the questions from other set would also be asked in this set.

### Round 1 Refrigerator

* 有几个室友住在一间房子，有好的室友，有坏的室友。
* 好的室友会买东西放在冰箱里，坏的室友会从冰箱里拿东西吃。
* 冰箱里有不同种类的食物，比如牛奶，香蕉，鸡蛋等等。
* 然后在冰箱里，每个种类都有一个数量上限，当然也不可以低于0。
* 他给你一段程序，模拟买和吃的过程，买需要一些时间\(sleep\)，吃也需要一些时间，然后print出实时的数量。
* 每个print里面会有个validation，如果超出了上线，或者低于0，就会出error message。
* 你要做的就是fix这段程序，让程序不出现error message。
* Solution
  * The problem is straight forward
  * We need to add locks on each element. It isn't clear what's the data structure. Here is my assumptions
    * An array to store the count of each element
    * An map to store the count of each element
  * Locks : 
    * **ReentrantLock**
    * Semaphore: Signal, like limit the number of the thread
    * Mutex: 

{% tabs %}
{% tab title="Code" %}
```text
Possible solutions

* It is a producer - consumer model
* Lock
    o Some mates said about mutex, semaphore, sychronization and ReeentrantLock

* The code is based on my search and understanding. I left the eggLock to demo
  that when there is no lock, it may be error message.
```
{% endtab %}

{% tab title="Refrigerator.java" %}
```java
enum Food {
    MILK,
    BANANA,
    EGG
}

class Refrigerator {
    private Map<Food, Integer> limit;
    private Integer milkCount;
    private Integer bananaCount;
    private Integer eggCount;
    private ReentrantLock milkLock;
    private ReentrantLock bananaLock;
    private ReentrantLock eggLock;

    public Refrigerator(Map<Food, Integer> limit) {
        this.limit = limit;
        milkCount = 0;
        bananaCount = 0;
        eggCount = 0;
        milkLock = new ReentrantLock();
        bananaLock = new ReentrantLock();
        eggLock = new ReentrantLock();
    }

    public boolean storeMilk() throws InterruptedException {
        milkLock.lock();
        try {
            if (milkCount >= limit.get(Food.MILK)) {
                return false;
            }
            milkCount++;
            print();
            return true;
        }  finally {
            milkLock.unlock();
        }
    }

    public boolean storeBanana() throws InterruptedException {
        bananaLock.lock();
        try {
            if (bananaCount >= limit.get(Food.BANANA))  { 
                return false;
            }
            bananaCount++;
            print();
            return true;
        } finally {
            bananaLock.unlock();
        }
    }

    public boolean storeEgg() throws InterruptedException {
        if (eggCount >= limit.get(Food.EGG)) return false;
        eggCount++;
        print();
        return true;
    }

    public boolean takeMilk() {
        milkLock.lock();
        try {
            if (milkCount <= 0)  {
                return false;
            }
            milkCount--;
            print();
            return true;
        } finally {
            milkLock.unlock();
        }
    }

    public boolean takeBanana() {
        bananaLock.lock();
        try {
            if (bananaCount <= 0)  {
                return false;
            }
            bananaCount--;
            print();
            return true;
        } finally {
            bananaLock.unlock();
        }
    }

    public boolean takeEgg() {
        if (eggCount <= 0 ) return false;
        eggCount--;
        print();
        return true;
    }

    public void print() {
        validation();
    }

    private boolean validation() {
        synchronized(this) {
            if (eggCount < 0 || eggCount > limit.get(Food.EGG) || 
                milkCount < 0 || milkCount > limit.get(Food.MILK) || 
                bananaCount < 0 || bananaCount > limit.get(Food.BANANA)) {

                System.out.print(
                    String.format("error message: egg %d milk %d banana %d \n", 
                                    eggCount, milkCount, bananaCount));
                return false;
            }
            return true;
        }
    }
}
```
{% endtab %}

{% tab title="RefrigeratorImpl.java" %}
```java
public class RefrigeratorImpl {
    public static void main(String[] args) throws InterruptedException {
        Map<Food, Integer> limit = new HashMap<>();
        limit.put(Food.MILK, 4);
        limit.put(Food.BANANA, 5);
        limit.put(Food.EGG, 9);
        Refrigerator refrigerator = new Refrigerator(limit);

        long pSleepTime = 1;
        long cSleepTime = 1;

        Thread goodMateEgg = new Thread(new Runnable(){
            @Override
            public void run() {
                for (int i=0;i<20;i++) {
                    try {
                        refrigerator.storeEgg();
                        Thread.sleep(pSleepTime >= 0 ? pSleepTime : 400 + (int)(Math.random()*200));
                    } 
                    catch (InterruptedException e) { 
                        e.printStackTrace(); 
                    } 
                }
            }
        });

        Thread goodMateMilk = new Thread(new Runnable(){
            @Override
            public void run() {
                for (int i=0;i<20;i++) {
                    try {
                        refrigerator.storeMilk();
                        Thread.sleep(pSleepTime >= 0 ? pSleepTime : 400 + (int)(Math.random()*200));
                    } 
                    catch (InterruptedException e) { 
                        e.printStackTrace(); 
                    } 
                }
            }
        }); 

        Thread goodMateBanana = new Thread(new Runnable(){
            @Override
            public void run() {
                for (int i=0;i<20;i++) {
                    try {
                        refrigerator.storeBanana();
                        Thread.sleep(pSleepTime >= 0 ? pSleepTime : 400 + (int)(Math.random()*200));
                    } 
                    catch (InterruptedException e) { 
                        e.printStackTrace(); 
                    } 
                }
            }
        }); 

        Thread badMateOne = new Thread(new Runnable(){

            @Override
            public void run() {
                for (int i=0;i<30;i++) {
                    try { 
                        if (refrigerator.takeEgg()) {}
                        else if (refrigerator.takeBanana()) {} 
                        else if (refrigerator.takeMilk()) {}
                        Thread.sleep(cSleepTime >= 0 ? cSleepTime : 400 + (int)(Math.random()*200));
                    } 
                    catch (InterruptedException e) { 
                        e.printStackTrace(); 
                    } 
                }
            }
        });

        Thread badMateTwo = new Thread(new Runnable(){
            @Override
            public void run() {
                for (int i=0;i<30;i++) {
                    try { 
                        if (refrigerator.takeEgg()) {}
                        else if (refrigerator.takeBanana()) {} 
                        else if (refrigerator.takeMilk()) {}
                        Thread.sleep(cSleepTime >= 0 ? cSleepTime : 400 + (int)(Math.random()*200));
                    } 
                    catch (InterruptedException e) { 
                        e.printStackTrace(); 
                    } 
                }
            }
        });

        Thread badMateThree = new Thread(new Runnable(){
            @Override
            public void run() {
                for (int i=0;i<30;i++) {
                    try { 
                        if (refrigerator.takeEgg()) {}
                        else if (refrigerator.takeBanana()) {} 
                        else if (refrigerator.takeMilk()) {}
                        Thread.sleep(cSleepTime >= 0 ? cSleepTime : 400 + (int)(Math.random()*200));
                    } 
                    catch (InterruptedException e) { 
                        e.printStackTrace(); 
                    } 
                }
            }
        });

        goodMateEgg.start();
        goodMateMilk.start();
        goodMateBanana.start();

        badMateOne.start();
        badMateTwo.start();
        badMateThree.start();
    }
}
```
{% endtab %}
{% endtabs %}

### Round 2 news feed system design

* 内部系统供员工抓取想要的新闻，比如自己facebook账号的更新，或者是报纸新闻上的更新
* 前段后端的搭建，
  * 页面自动刷新目前最新的topic，
  * 还有一些filter，比如种类，时间，关键字
  * upcoming calendar event
  * 还需要支持搜索功能

{% tabs %}
{% tab title="Code" %}
```text
Because currently they are all virtual interviews. This problem may not be asked.

It should use pull and push model.

Pull model for the personal websites, like fackbook or twitter
Push model for the general websites, like wsj or nytimes

* Feed system
* Web crawler
* Search
```
{% endtab %}
{% endtabs %}

### Round 3 Super stack & String multiply

#### Question 1 Super stack

* 支持average, min, max, mode之类。这里的mode是出现次数最多的意思
* It looks like mixed lots of small questions into one problem.
* min stack: [https://leetcode.com/problems/min-stack/solution/](https://leetcode.com/problems/min-stack/solution/)
* mode \(LFU\): [https://leetcode.com/problems/lfu-cache/](https://leetcode.com/problems/lfu-cache/)
* \*media: 
  * [https://leetcode.com/problems/find-median-from-data-stream/](https://leetcode.com/problems/find-median-from-data-stream/) 
  * [https://leetcode.com/problems/sliding-window-median/](https://leetcode.com/problems/sliding-window-median/)
* Solution
  * Average: maintain a total sum, average = sum / stack size
  * Min: use a stack to store min
  * Max: use a stack to store max
  * mode: Like LFU cache, but keep the max instead of min.
  * Median: Two queues and a map to track the removed ones

{% tabs %}
{% tab title="Code" %}
```text
Here I only implements the average, min, max and mode.
Because mode is quite complex so I create another class to handle it.
It would be better to design the unit test.
```
{% endtab %}

{% tab title="Mode.java" %}
```java
class Mode {
    private Map<Integer, Integer> valueCount;
    private Map<Integer, LinkedHashSet<Integer>> countList;
    private int max;

    public Mode() {
        valueCount = new HashMap<>();
        countList = new HashMap<>();
        max = 0;
    }

    public void put(int val) {
        if (valueCount.containsKey(val)) {
            int count = valueCount.get(val);
            valueCount.put(val, count + 1);
            countList.get(count).remove(val);
            if (countList.get(count).isEmpty()) countList.remove(count);
            countList.computeIfAbsent(count + 1, x -> new LinkedHashSet<>())
                     .add(val);
            if (max == count) max++;
        } else {
            valueCount.put(val, 1);
            if (max == 0) max++;
            countList.computeIfAbsent(1, x -> new LinkedHashSet<>()).add(val);
        }
    }

    public void remove(int val) {
        if (valueCount.containsKey(val)) {
            int count = valueCount.get(val);
            if (count-1 == 0) {
                valueCount.remove(val);
            } else {
                valueCount.put(val, count-1);
            }

            countList.get(count).remove(val);
            if (count == max && countList.get(count).isEmpty()) max--;
            if (countList.get(count).isEmpty()) countList.remove(count);
            countList.computeIfAbsent(count - 1, x -> new LinkedHashSet<>())
                     .add(val);
        }
    }

    /**
     * @return oldest most frequent element in the stack
     * @throws Exception
     */
    public int getMostFrequentValue() throws Exception {
        if (valueCount.isEmpty()) 
            throw new NoSuchElementException("Stack is empty.");
        return countList.get(max).iterator().next();
    }
}
```
{% endtab %}

{% tab title="SuperStack.java" %}
```java
class SuperStack {
    private long sum;
    private Stack<Integer> stack;
    private Stack<Integer> minStack;
    private Stack<Integer> maxStack;
    private Mode mode;

    public SuperStack() {
        sum = 0;
        stack = new Stack<>();
        minStack = new Stack<>();
        maxStack = new Stack<>();
        mode = new Mode();
    }

    public void push(int val) {
        stack.push(val);
        sum += val;
        if (minStack.empty() || minStack.peek() >= val) minStack.push(val);
        if (maxStack.empty() || maxStack.peek() <= val) maxStack.push(val);

        mode.put(val);
    }

    public int peek() throws EmptyStackException {
        return stack.peek();
    }

    public void pop() throws EmptyStackException {
        int val = stack.pop();
        sum -= val;

        if (val == minStack.peek()) minStack.pop();
        if (val == maxStack.peek()) maxStack.pop();

        mode.remove(val);
    }

    public int min() throws EmptyStackException {
        return minStack.peek();
    }

    public int max() throws EmptyStackException {
        return maxStack.peek();
    }

    public double average() throws EmptyStackException {
        if (stack.empty()) throw new EmptyStackException();
        return (double)sum / stack.size();
    }

    public int mostFrequentValue() throws Exception {
        return mode.getMostFrequentValue();
    }
}
```
{% endtab %}

{% tab title="Demo & test" %}
```text
1: test exceptions
2: test stack pop, push and peek
3: test min & max stack
4: test average
5: test mode
    a: push 
    b: remove
    c: getMostFrequentValue
```
{% endtab %}
{% endtabs %}

#### Question 2 String multiply

* Leetcode 43: 
* Key points
  * multiply a string to a digit
    * Remember to add tailing zeros
  * add two strings

{% tabs %}
{% tab title="Code" %}
```text
* multiply a string and a digit
* add two strings
* loop on shorter string and skip zeros
* better to use StreamBuilder
```
{% endtab %}

{% tab title="Solution" %}
```java
class Solution {
    public String multiply(String num1, String num2) {
        int len1 = num1.length();
        int len2 = num2.length();
        if (len1 == 0 || len2 == 0) {
            return num1 + num2;
        }

        if (num1.equals("0") || num2.equals("0")) {
            return "0";
        }

        String res = "0";
        for (int i=len1-1;i>=0;i--) {
            int n = num1.charAt(i) - '0';
            if (n == 0) continue;
            String oneMultiple = multipleOneDigit(num2, n, len1-i-1);
            res = addString(oneMultiple, res);
        }

        return res;
    }

    private String multipleOneDigit(String num, int n, int tailingZeros) {
        char[] charArray = num.toCharArray();
        int len = charArray.length;
        int carry = 0;
        String res = "";
        for (int i=len-1;i>=0;i--) {
            int c = charArray[i] - '0';
            int curr = c * n + carry;
            res = Integer.toString(curr%10) + res;
            carry = curr/10;
        }
        res = carry > 0 ? Integer.toString(carry) + res : res;
        while(tailingZeros > 0 ) {
            res+="0";
            tailingZeros--;
        }
        return res;
    }

    private String addString(String n1, String n2) {
        int len1 = n1.length();
        int len2 = n2.length();
        if (len1 == 0 || len2 == 0) {
            return n1 + n2;
        }

        int carry = 0;
        String res = "";
        while (len1>0 && len2>0) {
            int d1 = n1.charAt(--len1) - '0';
            int d2 = n2.charAt(--len2) - '0';
            int sum = d1 + d2 + carry;
            res = Integer.toString(sum%10) + res;
            carry = sum/10;
        }

        while (len1>0) {
            int d1 = n1.charAt(--len1) - '0';
            int sum = d1 + carry;
            res = Integer.toString(sum%10) + res;
            carry = sum/10;
        }

        while (len2>0) {
            int d2 = n2.charAt(--len2) - '0';
            int sum = d2 + carry;
            res = Integer.toString(sum%10) + res;
            carry = sum/10;
        }
        if (carry>0) {
            res = Integer.toString(carry) + res;
        }
        return res;
    }
}
```
{% endtab %}
{% endtabs %}

## Random Coding Questions

### Some random questions

* Populate next pointer of a tree: [https://leetcode.com/problems/populating-next-right-pointers-in-each-node/](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)
* Regex matching: [https://leetcode.com/problems/regular-expression-matching/](https://leetcode.com/problems/regular-expression-matching/)
* 理论。给两个set，如果取他们的交集，问了各个方法的优缺点（时间复杂度+空间复杂度）
  * brute force
  * sort one, binary search the other one
  * sort, then two pointers
  * hashmap
  * merge，归并排序duplicates???
  * [https://segmentfault.com/a/1190000003836386](https://segmentfault.com/a/1190000003836386)
* 理论。各种sorting algo 的优缺点。
  * bubble, insertion
  * bucket sort
  * shell 
  * merge sort
  * quick sort

### Statistic City Power & Temp

给一个csv文件，存有array of\(城市，日期，温度，电用量\)。要求知道每个城市每天最高温度和最高电用量。而且需要在知道了结果以后立刻print（比如一个城市到了第二天，就要立刻打印出之前一天的数据），而且需要按照知道答案的顺序print（这里其实有edge case需要考虑不过track一下行数然后建一个queue就好了）。实现很简单，建一个hash table配上一个queue之类的就可以。注意一定要删掉之前存下来的cache，文件特...别....大....，电脑其实没多少内存。面试官有答案，与结果diff。

### Weighted Sampling

Weighted sampling。给一个array of tuple的（weight, data），要求以给定的概率sample数据。 最优答案是binary indexd tree log\(n\)的读写complexity，而且支持insert/delete，都是log\(n\) 我给出普通答案，记住已有的weight然后查找。面试官也认可了。

## Random Design Questions

### Design Google Map

* 主要实现两点之间公共交通路径和所需时间。 
* 主要用到dijkstra's algorithm实现最短步行路径，geohash找最近的k个巴士站。
* 白板implement dijkstra's。
* 如果一条巴士线有很多站，则需要用到binary search找到离source最近的巴士站点。
* Follow up: 
  * 已知所有自行车站到start点和end点的距离，但是自行车站之间的建图代价非常大，如果减低建图的复杂度

### Design Watch

* 这个手表有三种模式\(普通，倒计时，秒表计时\)，只有两个按钮。
* 得用这两个按钮实现切换模式，暂停，开始。
  * 假设两个按钮分别是A, B。A，B一起按就是 nextMode\(\) 。
  * 在倒计时模式下，A是增加时间，B是暂停/开始。
* Solution: 
  * 用一个变量储存现在倒计时的时间，一个变量储存现在的状态。然后每次 call pressA\(\) 就+10s，每次 call pressB\(\) 就切换状态。
  * 如果状态是开始，就 setTimeInterval 每一秒钟 call watch.display\(现在倒计时还剩多少\) 
  * 如果状态是暂停就 clear setTimeInterval。 

### Design Google Doc

* 设计 Google Doc 这种多人同时编辑的，怎么让每个人看到的都是update的version
* [http://stackoverflow.com/questions/5772879/how-do-you-write-a-real-time-webbased-collaboration-tool-such-as-google-docs](http://stackoverflow.com/questions/5772879/how-do-you-write-a-real-time-webbased-collaboration-tool-such-as-google-docs) 
* Operational transformation Instead of editing the doc directly, send operations to server and let server decide the final/combined state.

### Distributed Services Health Check

* 很多机器分布在各个地方，设计一种通信方式是得它们能知道各自的health status. 
* 经面试官提示，各个机器需要把自己的"heart beat"传给其它机器，怎么传heart beat最简单了。
  * 就是各个机器都自己有一个operation counter. 
  * 每次操作operation counter都加1，
  * 然后把这个operation counter要传给其它机器。
  * 其它机器可以根据这个operation counter是不是连续加一来判断是不是漏接收某个机器的信息

### IoT data collection & store

* 是在IoT的环境下，有几百million的sensor采集温度的数据，每个sensor还分city和type，要求设计能够支持两个query
  * 给定时间范围内某一个city或者某一个type的平均温度。
* 本人转专业的系统设计比较水，大概说了一番，感觉存储方面其实相当于问的是类似google bigquery底层大概怎么实现的，
* 中间也会follow到很多关于怎么scale的问题

### Board and Player

* Design a game,
* 有一个grid, grid上有个target，有一些players. 
* Players不知道自己在grid上哪里，player有自己的移动strategy
* 但是输入是告诉你上次的移动是接近target了还是远离target了。 
  * 些是SmartPlayer，他会根据输入决定下一步怎么走
  * 有些是RandomPlayer，他们就是随机走

{% tabs %}
{% tab title="Code" %}
```text
* Game
    o Game class should have a board and some player
    o it should control when start and end
* Board
    o Board should record the players location.
    o interfaces:
        . registerPlayer(Player)
        . int isCloser(Player, Move)
* Player
    o Player should only record the result from prev move, for decision
    o interfaces:
        . int[] nextMove();
        . setResult(int)
```
{% endtab %}

{% tab title="IPlayer" %}
```java
public interface IPlayer {
    public int[] nextMove();
    public void setResult(int isCloser);
}
```
{% endtab %}

{% tab title="RandomPlayer" %}
```java
public class RandomPlayer implements IPlayer {
    public RandomPlayer() {}

    public int[] nextMove() {
        int[] nextMove = new int[2];
        int index = Math.random() >= 0.5 ? 0 : 1;
        nextMove[index] = Math.random() >= 0.5 ? -1 : 1;
        return nextMove;
    }

    public void setResult(int _result) {
    }
}
```
{% endtab %}

{% tab title="SmartPlayer" %}
```java
public class SmartPlayer implements Player {
    private int result;
    private boolean isPrevBad;
    private int[] move;
    public SmartPlayer() {
        result = -2;
        move = new int[2];
        isPrevBad = false;
    }

    public int[] nextMove() {
        if (result == -2) {
            move[1] = 1;
        } else {
            if (result == 0) {
                move[0] = 0;
                move[1] = 0;
                isPrevBad = false;
            } else if (result == 1) {
                if (isPrevBad) {
                    int x = move[0] == 0 ? (move[1] == 1 ? 1 : -1) : 0;
                    int y = move[1] == 0 ? (move[0] == 1 ? -1 : 1) : 0;
                    move[0] = x;
                    move[1] = y;
                    isPrevBad = false;
                }
            } else {
                move[0] = move[0] == 0 ? 0 : (-1 * move[0]);
                move[1] = move[1] == 0 ? 0 : (-1 * move[1]);
                isPrevBad = true;
            }
        }
        return move;
    }

    public void setResult(int result) {
        this.result = result;
    }
}
```
{% endtab %}

{% tab title="IBoard" %}
```java
public interface IBoard {
    public void registerPlayer(Player player);
    public int isCloser(Player player, int[] move);
}
```
{% endtab %}

{% tab title="Board" %}
```java
public class Board implements IBoard {
    private Map<Player, int[]> players;
    private int[] target;
    private int limit;

    public Board(int[] target, int limit) {
        this.target = target;
        this.limit = limit;
        players = new HashMap<>();
    }

    public void registerPlayer(Player player) {
        if (!players.containsKey(player)) {
            int[] location = new int[]{(int)(Math.random()*limit) , 
                                       (int)(Math.random()*limit)};
            players.put(player, location);
        }
    }

    public int isCloser(Player player, int[] move) {
        int[] prev = players.get(player);
        int x = move[0];
        int y = move[1];
        int[] curr = new int[]{prev[0] + x, prev[1]+y};
        players.put(player, curr);
        int diff = distance(curr) - distance(prev);
        return diff < 0 ? 1 : (diff == 0 ? 0 : -1);
    }

    public void print() {
        for (Map.Entry<Player, int[]> entry : players.entrySet()) {
            System.out.println(String.format("Player %s position %d %d", 
                entry.getKey().toString(), 
                entry.getValue()[0], 
                entry.getValue()[1]));
        }
    }

    private int distance(int[] curr) {
        return (curr[0]-target[0])*(curr[0]-target[0]) +
               (curr[1]-target[1])*(curr[1]-target[1]);
    }
}
```
{% endtab %}

{% tab title="Game" %}
```java
class PlayerFactory {

    public PlayerFactory() {
    }

    public Player getPlayer(PlayerType type) {
        if (type == PlayerType.SMART) return new SmartPlayer();
        else return new RandomPlayer();
    }
}

enum PlayerType {
    SMART,
    RANDOM
}

class Game {
    private Board board;
    private Set<Player> players;
    private PlayerFactory playerFactory;
    private int totalSteps;

    public Game(Board board, int totalSteps) {
        this.board = board;
        this.totalSteps = totalSteps;
        playerFactory = new PlayerFactory();
        players = new HashSet<>();
    }

    public void addPlayers(PlayerType type) {
        Player player = playerFactory.getPlayer(type);
        players.add(player);
        board.registerPlayer(player);
    }

    public void start() {
        for (int i=0;i<totalSteps;i++) {
            for (Player player : players) {
                int[] playerNextMove = player.nextMove();
                int result = board.isCloser(player, playerNextMove);
                player.setResult(result);
            }
            board.print();
        }
    }
}

public class GameImpl {
    public static void main(String[] args) {
        Board board = new Board(new int[]{0,0}, 10);
        Game game = new Game(board, 20);
        game.addPlayers(PlayerType.SMART);
        game.addPlayers(PlayerType.RANDOM);
        game.addPlayers(PlayerType.RANDOM);
        game.start();
        board.print();
    }
}
```
{% endtab %}
{% endtabs %}

### Stock subscription notification system

* It should be like the message queue subscription model.
* It feels like the new feed system

## Random Brain Teaser Questions

### Question 1:

* 就是一个train在入站台和出站台的时候需要先减速再加速，问你车的哪个部位在整个站台停留的时间最长。
  * 我大概思考了一下乱说了一个head……后来work out math的时候我发现head和tail的时间是相同的，我就开始觉得应该是midway停留的时间最长，\(这个时候有一个假设是减速和加速的加速度是相同的\)。
  * 面试官说对，然后就问how much longer? 然后给了一个提示用s = 1/2  _a_  t  _t这个公式去推，我大概写了几行跟他说大概是\(sqrt{2} - 1\) longer，他有点惊讶因为他好像觉得我很多步骤跳掉了……anyway他觉得没问题，后来又问如果减速和加速的加速度不同那怎么办，哪个点停留的时间最长？我直接画了两下猜了一个表达式大概就说是要在车的\(a2 / \(a1 + a2\)\)分界的地方……面试官就问为啥？我尝试work out math了半天未果，反正大概就是1/2_  a1  _t1_  t1 = x, 1/2  _a2_  t2 \*t2 = S - x然后去maximize t1 + t2的值，很明显的我的数学水平已然不足以解决这个问题了……
  * 不过面试官说我很惊讶你一开始给的答案就是对的，我就说这个intuitively大概就是这样blah blah，然后他说不用推了我们又开心的聊了一些culture问题结束

## Some other useful links

{% embed url="https://github.com/donnemartin/system-design-primer/blob/master/README.md" caption="" %}

{% embed url="https://juejin.im/post/5c5cf03ef265da2dd218a4c8" caption="" %}

