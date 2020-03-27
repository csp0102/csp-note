# 第2章 面试需要的基础知识

## 2.3 数据结构

### 3.二维数组中的查找

```汉语
题目：在一个二维数组中，每一行和每一个都是递增的，查找指定数
```
**思路**一

1. 初始化col=最后一列，row=第一行
2. 由给定特性，取二维数组**右上角的数**num。（左下角也可）
3. * num>target，这一列都要比target大，故列col--，即搜索范围左移
   * num<target，这一行都要比target小，故行row++，即收缩范围下移
   * num=target，返回

时间复杂度O(m+n)

**思路二**

每一行，利用二分进行查找，二分查找所有行。

时间复杂度O(MlogN),M行N列

**code**

```c++
bool Find(int target, vector<vector<int> > array) {
    if (array.empty())
        return false;
    int col = array[0].size() - 1;//最后一列
    int row = 0;//行
    while (col >= 0 && row<array.size())
    {
        //每次用右上角的数与target比较
        if (array[row][col] == target)
            return true;
        else if (array[row][col]>target)
            col--;//查找范围左移
        else
            row++;//查找范围下移
        //通过col-- 和row++ 来缩小范围
    }
    return false;
}
```



### 4.替换空格

```汉语
题目：替换字符串中的空格为"%20"
```

**思路**

1. 找到字符串中空格的数量
2. 计算原始长度和新字符串长度
3. 一个指针指向原始长度尾部，另一个指针指向新字符串尾部
4. 从后往前扫描

**code**

```c++
class Solution {
public:
	void replaceSpace(char *str, int length) {
		if (!str)
			return;
		int oriLength = 0;
		int newLength;
		int spaceNum = 0;
		//遍历一遍字符串找出空格数量
		for (int i = 0; str[i] != '\0'; i++)
		{
			if (str[i] == ' ')
				spaceNum++;
			oriLength++;
		}
		newLength = oriLength + spaceNum * 2;//每替换一个空格，多两个字符
		if (newLength>length) //这里应该是>=,因为newLength没有算是最后的'\0'，应该多加一个
			return;           //或者 newLength+1>length
		char* pStr1 = str + oriLength;//指向原始字符串最后
		char* pStr2 = str + newLength;//指向新字符串最后
		//替换
		while (pStr1>0 && pStr2>pStr1)
		{
			if (*pStr1 == ' ')
			{
				*(pStr2--) = '0';
				*(pStr2--) = '2';
				*(pStr2--) = '%';
			}
			else
				*(pStr2--) = *pStr1;
			pStr1--;
		}
	}
};
```

**考点**

* char* str 也是可以用下标str[i]来访问的。



### 5.从尾到头打印链表

```汉语
题目：输入一个链表，按链表从尾到头的顺序返回一个ArrayList。
```

**思路一**

利用**栈**结构存储，时间和空间复杂度都为O(N)

**思路二**

既然想到了使用栈，**递归**本质上就是一个站结构

**code**

```c++
//思路一
vector<int> printListFromTailToHead(ListNode* head) 
{
    vector<int> data;
    if (!head)
        return data;
    stack<int> temp_stack;
    while (head != nullptr)
    {
        temp_stack.push(head->val);
        head = head->next;
    }
    while (!temp_stack.empty())
    {
        data.push_back(temp_stack.top());
        temp_stack.pop();//pop() 返回void
    }
    return data;
}

//思路二
void getData(ListNode* head, vector<int>& data)//注意这里是引用，不是值。
{
    if (!head)
        return;
    getData(head->next, data);//先递归输出后面的结点
    data.push_back(head->val);//再输出当前结点
}
vector<int> printListFromTailToHead(ListNode* head) 
{
    vector<int> data;
    if (!head)
        return data;
    getData(head, data);
    return data;
}
```

**考点**

* stack的用法
  * pop(),弹出栈顶，返回void
  * top(),返回栈顶元素，不弹出
  * push()，压栈
* 



### 6.重建二叉树

```汉语
题目：已知二叉树的前序遍历和中序遍历，重建二叉树
```

**思路**

* 前序遍历的第一个数---》根节点

* 在中序遍历中，找到该数，分割出两个数组
  * 该数左边的数为左子树，切割数组
  * 该数右边的数为右子树，切割数组
* 递归，直到数组为单个。

![img](.\剑指offer图片\重建二叉树.jpg)

**code**

```c++
TreeNode* reConstructBinaryTree(vector<int> pre, vector<int> vin) {
    if (pre.empty() || vin.empty() || (pre.size() != vin.size()))
        return nullptr;
    //先序遍历的第一个数为根节点
    int val = pre[0];
    TreeNode* root = new TreeNode(val);
    //结束条件
    if (pre.size() == 1 && vin.size() == 1)
        return root;
    //找到val在中序遍历中的位置
    int i;
    for (i = 0; i<vin.size(); i++)
    {
        if (vin[i] == val)
            break;
    }//此时i就是中序遍历中的根节点的位置
    //构建左子树
    vector<int> preleft(pre.begin() + 1, pre.begin() + 1 + i);
    vector<int> vinleft(vin.begin(), vin.begin() + i);
    root->left = reConstructBinaryTree(preleft, vinleft);
    //构建右子树
    vector<int> preright(pre.begin() + 1 + i, pre.end());
    vector<int> vinright(vin.begin() + 1 + i, vin.end());
    root->right = reConstructBinaryTree(preright, vinright);
    return root;
}
```

**考点**

* 前序遍历与中序遍历
* 递归
* vector的构造



### 7.用两个栈实现队列

```汉语
题目：用两个栈实现一个队列的push()和pop()
```

**思路**

利用栈“先进后出”的特性，来实现队列“先进先出”的特性

**code**

```c++
class Solution
{
public:
    void push(int node) {
        stack1.push(node);
    }

    int pop() {
        if(stack2.empty())
        {//如果stack2为空，先将stack1的数据压到stack2中
         //再输出stack2的栈顶元素。
            while(!stack1.empty())
            {
                stack2.push(stack1.top());
                stack1.pop();
            }
        }
        int val=stack2.top();
        stack2.pop();
        return val;
    }

private:
    stack<int> stack1;
    stack<int> stack2;
};
```

**考点**

* 栈和队列数据结构的特点
* push和pop的返回值



## 2.4 算法和数据操作

### 8.旋转数据的最小值

> 牛客：https://www.nowcoder.com/practice/9f3231a991af4f55b95579b44b7a01ba?tpId=13&tqId=11159&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking

```汉语
题目：把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。
NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。
```

**思路**

不同于书上的解法(太晦涩)，只用比较两个数就可以了，比如上面的3和1，关键就是如何找到1这个数字

**code**

```c++
 int minNumberInRotateArray(vector<int> rotateArray) 
 {
        int min=0;
        if(rotateArray.empty())
            return min;
        min=rotateArray[0];
        for(int i=0;i<rotateArray.size()-1;i++)
        {
            if(rotateArray[i]>rotateArray[i+1])
                //由于旋转数组的特性，这个min只有两个可能，可以直接返回
                return min=min>rotateArray[i+1]?rotateArray[i+1]:min;
        }
    }
```



### 9.斐波拉契数列

```汉语
题目：求第n项斐波拉契数列，第0项为0。第n项为前两项之和
```

**思路一**

递归

**思路二**

迭代

**code**

```c++
//递归
int Fibonacci(int n) 
{
	if(n<=0)
   		return 0;
	if(n==1||n==2)
    	return 1;
	return Fibonacci(n-1)+Fibonacci(n-2);    
}
//迭代一时间空间O(n)
int Fibonacci(int n) 
{
    if(n<0)
        return -1;
    vector<int> data(n+1);
    data[0]=0;
    data[1]=1;
    for(int i=2;i<=n;i++)
        data[i]=data[i-1]+data[i-2];
    return data[n];   
}
//迭代更好的写法,时间复杂度O(n)
int Fibonacci(int n) 
{
	if(n<0)
        return -1;
    if(n<2)
        return n;
    int f=0,g=1;//第0项和第1项
    int sum;
    for(int i=2;i<=n;i++)//从第二项开始
    {
        sum=f+g;//求第n项
        f=g;//更新第n-2项为第n-1项
        g=sum;//更新第n-1项为第n项
    }
    return sum;   
}
```

**考点**

* 递归和迭代

**拓展1**

```汉语
题目一：一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。
题目二：我们可以用2*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？
```

**code**

```c++
int jumpFloor(int number) {
        /*递归
        if(number<=0)
            return 0;
        if(number<=2)
            return number;
        return jumpFloor(number-1)+jumpFloor(number-2);
        */
        
    	//For LOOP
        if(number<=0)
            return 0;
        if(number<=2)
            return number;
        int f=1,g=2;//和斐波拉契数列的初始值不同而已
        int sumOfWay;
        for(int i=3;i<=number;i++)//初始值f和g表示第一项和第二项
        {
            sumOfWay=f+g;
            f=g;
            g=sumOfWay;
        }
        return sumOfWay;
    }
```

**拓展2**

```汉语
一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。（贪心算法）
```

**思路**

> f(n)=f(n-1)+f(n-2)+……f(1)+1//注意最后加1，就是直接跳number级的一种情况
>
> f(n-1)=f(n-2)+……f(1)+1
>
> 两式子相减
>
> f(n)=2f(n-1)

**code**

```c++
int jumpFloorII(int number) {
    /*
        if(number<=0)
            return 0;
        if(number<=2)
            return number;
        vector<int> jumpWay(number+1);
        jumpWay[0]=0;
        jumpWay[1]=1;
        jumpWay[2]=2;
        //f(n)=f(n-1)+f(n-2)+……f(1)+1//注意最后加1，就是直接跳number级的一种情况
        for(int i=4;i<=number;i++)
        {
            jumpWay[i]=0;
            for(int j=1;j<i;j++)
                jumpWay[i]+=jumpWay[j];
            jumpWay[i]+=1;
        }
        return jumpWay[number];
        */

    /*法二 推导+递归
        //f(n)=f(n-1)+f(n-2)+……f(1)+1//注意最后加1，就是直接跳number级的一种情况
        //f(n-1)=f(n-2)+……f(1)+1
        //两式子相减
        //f(n)=2f(n-1)
        if(number<=0)
            return 0;
        if(number==1)
            return 1;
        return 2*jumpFloorII(number-1);
        */
    //法三 推导+迭代
    if(number<=0)
        return 0;
    if(number==1)
        return 1;
    int way=1;
    for(int i=2;i<=number;i++)
        way*=2;
    return way;
}
```

**考点**

贪心算法：https://baijiahao.baidu.com/s?id=1642122740570394361&wfr=spider&for=pc



### 10.二进制数中1的个数

```汉语
题目：输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。
```

**思路一**

每次判断一位是否为1，原数不动，通过flag=1不断左移来判断每一位。

**思路二**

举个例子：一个二进制数**1100**，从右边数起第三位是处于最右边的一个1。减去1后，第三位变成0，它后面的两位0变成了1，而前面的1保持不变，因此得到的结果是1011.我们发现减1的结果是把最右边的一个1开始的所有位都取反了。这个时候如果我们再把原来的整数和减去1之后的结果做与运算，从原来整数最右边一个1那一位开始所有位都会变成0。如**1100&1011=1000**.也就是说，把一个整数减去1，再和原整数做与运算，会把**该整数最右边一个1变成0**.那么一个整数的二进制有多少个1，就可以进行多少次这样的操作。  

```c++
//思路一
int NumberOf1(int n) 
{
    int flag=1;
    int count=0;
    while(flag)
    {
        if(flag&n)//注意这里，不能写成flag&n==1，只要不为零就说明存在1
            count++;
        flag=flag<<1;
    }
    return count;
}
//思路二
int  NumberOf1(int n) 
{
    int count=0;
    while(n)
    {
        count++;
        n=n&(n-1);//独特方法
    }
    return count;
}
```

**考点**

* 判断数字n的某一位是否为1
  * ` if(flag&n)`

**拓展**

1. 判断一个整数是否为2的整数次方
   * 即：这个整数二进制中只有一位是1。根据上面的方法，该数与该数-1相与，得到的结果应该=0。
2. 输入两个数m和n，计算需要改变m的二进制中的多少位才能=n。
   * 即：先将两数异或（不用的位相与得1），再判断结果中有多少位1。

**举一反三**

把一个整数减去1再和原来得数相与（n&(n-1)），得到的结果相当于：把这个整数的二进制表示中的最右边的一个1变成0。很多二进制问题都可以这么解决。



# 第3章 高质量的代码

## 3.1 代码的完整性

### 11.数值的整数次方

```汉语
题目：给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。
保证base和exponent不同时为0
```

**注意**

1. base=0，res=0
2. exponent=0，res=1
3. exponent<0，res为小数
4. 判断浮点数与一个数相等？

**code**

```c++
//判断浮点数相等应该如下
bool equal(double number1,double number2)
{
    if(number1-number2>0.0000001||number1-number2<-0.0000001)
        return false;
    return true;
}
//注意特殊条件O(N)
double Power(double base, int exponent) {
    if(equal(base,0))
        return 0;
    if(exponent==0)
        return 1;
    double res=1;
    for(int i=1;i<=(exponent>0?exponent:-exponent);i++)
        res*=base;
    return exponent>0?res:(double)(1/res);
}
//还有更加快速的做法 快速幂算法O(logN)
double Power(double base, int exponent) {
    if(equal(base,0))
        return 0;
    if(exponent==0)
        return 1;
    int n=exponent>0?exponent:-exponent;//用n保存，因为exponent后面还要用
    double res=base;
    for(int i=1;i<log2f(n);i++)//比如5次方，分为base^2 base^2 base^1
        res*=res;
    if(n&1==1)//如果是奇数幂，还得再乘一次base
        res*=base;
    return exponent>0?res:(double)(1/res);
}
//递归实现快速幂
double Power_Recursion(double base,int n)
{
    if(n==0)
        return 1;
    if(n==1)
        return base;
    double res=Power_Recursion(base,n>>1);
    res*=res;
    if(n&&1==1)//如果是奇数幂，还得再乘一次base
        res*=base;
    return res;
}
double Power(double base, int exponent) 
{
    if(equal(base,0))
        return 0;
    if(exponent==0)
        return 1;
    int n=exponent>0?exponent:-exponent;
    double res=Power_Recursion(base,n);
    return exponent>0?res:(double)(1/res);
}
```

**考点**

* 浮点数判断相等
* +=赋初值为0，*=赋初值为1
* 快速幂算法
* 逻辑很简单的题，要注意代码完整性









### 13.在O(1)时间删除链表结点

```汉语
题目：给定单向链表的头指针和一个结点指针，定义一个函数在O(1)时间删除结点。
```

 **传统方法:**

* 从链表头遍历查找到要删除的结点，并在链表中删除改结点。

* 删除:找到该结点之后，令这个结点的前一个结点的next指针指向要删除结点的下一个结点，指针安全之后再删除该结点。

* 时间复杂度:O(n)

**O(1)方法：**

1.  如果该节点不是尾节点，那么可以直接将下一个节点的值赋给该节点，然后令该节点指向下下个节点，再删除下一个节点，时间复杂度为 O(1)。

![1577325668755](.\图片\O（1）删除结点1.png)

2. 否则，就需要先遍历链表，找到节点的前一个节点，然后让前一个节点指向 null，时间复杂度为 O(N)。

![1577325849833](.\图片\O（1）删除结点2.png)

**code**

```c++
void deleteNode(ListNode** pHead,ListNode* pDeleteNode)
{
	//如果链表为空或者要删除的结点为空则返回
	if (*pHead == NULL || pDeleteNode==NULL)
		return ;

	if (pDeleteNode->m_pNextNode!=NULL)//要删除的结点不是尾节点
	{
		ListNode* pNext = pDeleteNode->m_pNextNode;
		pDeleteNode->m_value = pNext->m_value;
		pDeleteNode->m_pNextNode = pNext->m_pNextNode;
		delete pNext;//删除这个结点之前，指向该结点的指针已经安全了。
		pNext = nullptr;
	}
	else if (*pHead==pDeleteNode)//是尾结点，但是只有一个结点，那么这个结点即是头结点，也是尾结点
	{
		delete pDeleteNode;
		pDeleteNode = nullptr;
		*pHead = nullptr;
	}
	else//是尾结点，链表有多个结点。
	{
		//循环遍历 找到要删除结点的上一个结点。
		ListNode* preNode=*pHead;
		while (preNode->m_pNextNode != pDeleteNode)
			preNode = preNode->m_pNextNode;
		preNode->m_pNextNode = nullptr;
		delete pDeleteNode;
		pDeleteNode = nullptr;
	}
}
```

**考点**

* 在删除结点之前，要对该结点进行判断
  1. 链表或要删除的结点是否为空？
  2. 要删除的结点是否为尾结点？不是尾结点O(1)时间删除；是尾结点则找到该结点上一结点。
  3. 链表是否只有一个结点？
* 上述代码基于：该结点一定在链表中，如果未知是否在链表中，则要O(n)的时间遍历整个链表进行判断。
* **删除一个结点之前，要先转移指向改结点的指针，指针安全了才能删除结点。**

------



### 14.调整数组顺序使奇数位于偶数之前

```汉语
题目：输入一个数组，实现一个函数来调整该数组中数组的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分
```

**思路一：**从前往后遍历数组，若遇到偶数则取出，该数后面的所有数前移，再将该数放到最后，依次直到遍历完。时间复杂度O(n^2^)，空间复杂度O(1)。

**思路二：**利用辅助数组，从前往后遍历数组，奇数从前往后放到新数组，偶数从后往前放到新数组。

时间复杂度O(n)，空间复杂度O(n)。

**思路三：**（本题思路）维护头尾两个指针，头指针后移直到遇到偶数，尾指针前移直到遇到奇数，交换两数。依次直到头指针>尾指针。时间复杂度O(n)，空间复杂度O(1)。

**code**

```c++
void swap(int& a, int& b)
{
	int temp = a;
	a = b;
	b = temp;
}
bool isEven(int n)//解耦 提高代码的重用性
{
	return !( n & 1);
}
//调整数组顺序使奇数在偶数前面
void ReorderArr(int* arr, unsigned int length,bool (*func)(int))
{
	if (!arr || length == 0)
		return;
	int* left = arr;
	int* right = arr + length - 1;

	while (left<right)
	{
		//left后移直到指向一个偶数
		while (left < right && !func(*left))
			left++;
		//right前移直到指向一个奇数
		while (left < right && func(*right) )
			right--;
		if (left < right)
			swap(*left, *right);
	}
}

```

**考点：**

* 快速想到解题思路。
* 考虑可拓展性的解法。利用函数指针将**判断标准**和**拆分数组**解耦。判断标准改为例如“正数在前，负数在后”时，可重用代码，为拓展功能提供了便利。

**拓展**

* 若需要奇数与奇数之、偶数与偶数之间的顺序不能乱，则可以借助额外空间

  ```c++
      void reOrderArray(vector<int> &array) {
          //时间空间都为O(n)的方法
          if(array.empty())
              return;
          vector<int> clone=array;
          int pEven=0;//偶数指针
          int pOdd=0;
          //统计偶数个数
          for(int i=0;i<array.size();i++)
          {
              if(array[i]&1==1)
                  pEven++;//找到偶数指针应该在的位置
          }
          //调整顺序
          for(int i=0;i<clone.size();i++)
          {
              if(clone[i]&1==1)
                  array[pOdd++]=clone[i];
              else
                  array[pEven++]=clone[i];
          }
      }
  ```

  

------





## 3.2 代码的鲁棒性

### 15.链表中倒数第k个结点

```汉语
题目：输入一个链表，输出该链表中倒数第k个结点。（从1开始计数，即链表的尾结点时倒数第1个结点）
```

**注意**

* 只有一个链表的头指针，链表长度未知。
* 只能正向遍历。

**思路一：**首先遍历一遍链表，确定链表长度n，第二次遍历到第n-k+1个结点即可。

**思路二：**（本题思路）用两个指针只遍历一次链表。指针A先后移k-1到第k个结点，指针B指向第一个结点；两者保存k-1个距离一起后移，直到A指针到最后一个结点，此时B指针指向倒数第k个结点。

![1577365166796](.\图片\链表中倒数第k个数1.png)

**code**

```c++
//思路一
ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) 
{
        if(!pListHead)
            return nullptr;
        int length=0;//链表长度
        ListNode* head=pListHead;
        while(head)
        {
            length++;
            head=head->next;
        }
        if(k>length)
            return nullptr;
        for(int i=0;i<length-k;i++)
            pListHead=pListHead->next;
        return pListHead;
}
//思路二
ListNode* FindKthToTail(ListNode* pHead, unsigned int k)
{
	//特例判断
	if (!pHead || k == 0)
		return NULL;
	//维护两个指针
	ListNode* pAhead = pHead;
	ListNode* pBehind = pHead;

	//pAhead先后移k-1步
	for (int i = 0; i < k - 1;++i)
	{
		if (pAhead->m_pNextNode)//存在下一个结点才后移
			pAhead = pAhead->m_pNextNode;
		else//不存在 说明list的长度不足k
			return NULL;
	}
	//pAhead和pBehind同时移动直到pAhead到达末尾
	while (pAhead->m_pNextNode)
	{
		pAhead = pAhead->m_pNextNode;
		pBehind = pBehind->m_pNextNode;
	}
	return pBehind;
}
```

**考点**

* 代码的鲁棒性。判断给定链表是否为空。
* 给定k值是否超出链表长度？若不确定，则在pAhead后移时，先判断pAhead是否存在下一个结点。
* 无符号数k为0时，k-1不是-1，而是4294967295（无符号0xFFFFFFFF）
* **当需要多次遍历时，考虑用多指针，只遍历一次**。通常一个指针比另一个指针多走几步，或者一个指针走两步，一个指针走一步。

**拓展**

1. 求链表的中点。用两个指针，一个指针一次走一步，另一个指针一次走两步。当快指针走到尾部时，慢指针正好在中间（奇数，若为偶数，返回中间两个中的任意一个）。
2. 判断一个链表是否为环形链表。同上，当快指针追上了慢指针，说明为环形；若快指针到尾部时都未追上慢指针则不为环形。



### 16.反转链表

```汉语
题目：定义一个函数，输入一个链表的头结点，反转该链表并输出反转后链表的头结点。
```

**思路分析：**

* 正向链表，只能访问当前结点的下一个结点，**无法访问上一个结点**。要反转，因此需要一个指针保存上一个结点。
* 调整当前结点的next指针指向上一个结点后，**链表断开**，无法访问到下一个结点，因此在反转指针之前，需要一个指针保存当前结点的下一个结点。
* 当然，当前结点也是需要一个指针来索引，因此本题需要设置**三个指针**。（或者把传入的头结点当作当前结点）

**code**

```c++
//迭代
ListNode* ReverseList(ListNode* pHead)
{
	if (!pHead)
		return nullptr;
	ListNode* curNode=pHead;//当前结点
	ListNode* preNode = nullptr;//前一个结点
	ListNode* nextNode = nullptr;
	while (curNode)
	{
		nextNode = curNode->m_pNextNode;//保存下一个结点
		curNode->m_pNextNode = preNode;// 让当前节点指向前一个节点位置，反转
		preNode = curNode;//更新指针
		curNode = nextNode;// 当前节点往右继续走
	}
	return preNode;//注意是出入preNode，因为curNode指向了nullptr
}

//递归
ListNode* ReverseList_digui(ListNode* cur, ListNode* pre)
{
	if (!cur)
		return pre;//结束条件
	ListNode* next = cur->m_pNextNode;
	cur->m_pNextNode = pre;
	pre = cur;
	cur = next;
	return ReverseList_digui(cur, pre);
}
ListNode* ReverseList1(ListNode* pHead)
{
	if (!pHead)
		return nullptr;
	ListNode* cur = pHead;
	ListNode* pre = nullptr;
	return ReverseList_digui(cur, pre);
}
```



### 17.合并两个排序的链表

```汉语
题目：输入两个递增排序的链表，合并这两个链表并使新链表中的结点仍然是按照递增排序的。
```

**思路：**

1. 比较两个链表的头结点，头结点小的作为合并链表的头结点；
2. 继续合并两个链表中剩下的结点，他们依然是有序的，再比较两个链表的头结点，取其中小的结点，作为合并链表的下一个结点。
3. 重复比较，直到其中一个链表为空，两一个链表剩下所有结点插入到合并链表中。

**code**

```c++
//迭代
ListNode* MergeTwoList(ListNode* first,ListNode* second)
{
	//输入链表中一个为NULL，则返回另外一个链表
	if (!first)
		return second;
	if (!second)
		return first;
	ListNode* mergeHead=new ListNode(0,nullptr);
	ListNode* temp = mergeHead;
	while (first&&second)
	{
		if (first->m_value > second->m_value)
		{
			temp->m_pNextNode = second;
			second = second->m_pNextNode;
		}
		else
		{
			temp->m_pNextNode = first;
			first = first->m_pNextNode;
		}
		temp = temp->m_pNextNode;//别忘了移动temp的位置
	}
	temp->m_pNextNode = first ? first : second;	//if (!first)
												//	temp->m_pNextNode = second;
												//else
												//	temp->m_pNextNode = first;
	return mergeHead->m_pNextNode;
}

//递归
ListNode* MergeTwoList_Recursive(ListNode* first, ListNode* second)
{
	//终止条件
	if (!first)
		return second;
	if (!second)
		return first;
	ListNode* mergeHead(0);
	if (first->m_value >= second->m_value)
	{
		mergeHead = second;
		mergeHead->m_pNextNode = MergeTwoList_Recursive(first, second->m_pNextNode);
	}
	else
	{
		mergeHead = first;
		mergeHead->m_pNextNode = MergeTwoList_Recursive(first->m_pNextNode, second);
	}
	return mergeHead;	
}
```

**考点**

* 鲁棒性代码
*  递归



### 18.树的子结构

```汉语
题目：输入两棵二叉树A和B，判断A是不是B的子结构。
```

![](.\图片\树的子结构.png)

**思路**

1. 当树AB都存在时，判断当前根结点的值是否相等。
   * 若相等，则判断A是否包含B。
   * 若不相等，则将A树根结点的**左子结点**作为新的根结点，返回1，判断是否包含B。
   * 若新的根结点仍不包含B，则将A树根结点的**右子结点**作为新的根结点，返回1，判断是否包含B。
2. 判断是否包含
   * 递归判断当前结点、左结点、右节点的值是否相等。

**code**

```c++
//递归 判断Tree1是否含有Tree2
bool isTree1HasTree2(BinaryTreeNode* root1, BinaryTreeNode* root2)
{
	if (!root2)//结束条件1
		return true;
	if (!root1)//结束条件2
		return false;
	if (root1->m_value == root2->m_value)//上面两个判断，保证了root1和root2一定是存在的。
		return isTree1HasTree2(root1->m_pLeft, root2->m_pLeft) &&
		isTree1HasTree2(root1->m_pRight, root2->m_pRight);
	else//其余情况都返回false
		return false;
}
//递归 判断树A是否包含树B
bool HasSubtree_Recursive(BinaryTreeNode* root1, BinaryTreeNode* root2)
{
	bool result = false;
	if (root1&&root2)//当根节点都存在才去比较，
	{
		if (root1->m_value == root2->m_value)
			result = isTree1HasTree2(root1,root2);//只有当根节点相等时，才去判断1是否包含2
		if (!result)//如果不包含，则用左结点与2比较
			result=HasSubtree_Recursive(root1->m_pLeft, root2);
		if (!result)//如果左结点也不包含，则用右结点与2比较
			result = HasSubtree_Recursive(root1->m_pRight, root2);
	}
	return result;
}
```

**考点**

* 双重递归
* 每次使用指针，都要问自己指针是否为空



# 第4章 解决问题的思路

## 4.1 举例让抽象问题形象化、具体化

### 19.二叉树的镜像

```汉语
题目:完成一个函数，输入一个二叉树，该函数输出它的镜像
```

**思路**

1. 前序遍历二叉树的每个结点。
   - 若该结点存在，交换其左右子结点（若孩子结点都不存在，交换也不要紧）
   - 若该结点不存在，返回；
2. 递归输入该结点的左子结点，右子结点。

**code**

```c++
//二叉树的镜像
void mirrorBinaryTree(BinaryTreeNode* root)
{
	if (!root)
		return;

   	/*if (!root->m_pLeft&&!root->m_pRight)
		return;*///当左右孩子都不存在则返回，阔以不用判断，都不存在交换两个空指针
    
	BinaryTreeNode* temp = root->m_pLeft;
	root->m_pLeft = root->m_pRight;
	root->m_pRight = temp;

	mirrorBinaryTree(root->m_pLeft);//这里不用在判断root->m_pLeft是否存在，
	mirrorBinaryTree(root->m_pRight);//因为mirrorBinaryTree开始就已经判断过一次了。
}
```

**考点**

* 考查对二叉树的理解，本题实质上是利用树的遍历算法解决问题
* 将抽象问题形象化，具体可以画图

**拓展**

若要求用循环来实现,则考虑BFS  (BFS重点在于队列,DFS重点在于递归)



### 20.顺时针打印矩阵

```汉语
题目：输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。
```

**code**

```c++
vector<int> printMatrix(int arr[][4], int rows, int cols)
{
	vector<int> result;
	if (!arr || rows == 0 || cols == 0)
		return result;
	int up = 0;
	int down = rows-1;
	int left = 0;
	int right = cols-1;
	while (1)
	{
		//从左往右输出行
		for (int j = left; j <= right; j++)
			result.push_back(arr[up][j]);
		up++;//表示该行已经输出
		if (up>down)
			break;

		//从上往下输出列
		for (int i = up; i <= down; i++)
			result.push_back(arr[i][right]);
		right--;//表示该列已经输出
		if (left>right)
			break;

		//从右往左输出行
		for (int j = right; j >= left; j--)
			result.push_back(arr[down][j]);
		down--;//表示该行已经输出
		if (up>down)
			break;

		//从下往上输出列
		for (int i = down; i >=up; i--)
			result.push_back(arr[i][left]);
		left++;
		if (left>right)
			break;
	}
	return result;
}
```

**考点**

* 考查思维能力
* 上述代码容易看懂



### 21.包含min函数的栈

```汉语
题目：定义一个数据结构，在该类型中实现一个能够得到栈的最小元素的min函数。要求在该栈中，调用push、pop、min函数的时间复杂度都是O(1)。
```

**思路**

*  × 一个数据栈，每次入栈进行排序。 因为不能保证栈先进后出的特性。
*  × 一个数据栈，一个成员变量保存最小值。当最小值出战，如何得到下一个最小值？
*  √ 一个数据栈，一个辅助栈，辅助栈的栈顶元素存放当前数据栈中的最小值。
  * 新元素入数据栈，再比较新元素和上一个最小值，较小值入辅助栈。
  * 数据栈弹出元素，辅助栈也弹出元素。

**code**

```c++
template <typename T>
class StackWithMin
{
private:
	stack<T> m_data;//数据栈
	stack<T> m_min;//辅助栈
public:
	void push(const T& value);
	void pop();
	T& min();
};

template <typename T>
void StackWithMin<T> ::push(const T& value)
{
	m_data.push(value);
    
	if (m_min.size() == 0 || value < m_min.top())//要考虑到栈空的情况
		m_min.push(value);//比较新压入元素和上一个最小元素
	else
		m_min.push(m_min.top());
}

template <typename T>
void StackWithMin<T> ::pop()
{
	//不用这样写
	//if (m_data.size())
	//	m_data.pop();
	//if (m_min.size())
	//	m_min.pop(); 
	
	//从入栈可以看出，m_data和m_min的size一定是一样的
	assert(m_data.size() > 0 && m_min.size() > 0);
	m_data.pop();
	m_min.pop();
}

template <typename T>
T& StackWithMin<T> ::min()
{
	assert(m_min.size() > 0 && m_data.size() > 0);
	return m_min.top();
}
```

**考点**

* 对栈的理解
* 入栈出栈，考虑栈为空的情况，可用assert来避免错误。



### 22.栈的压入、弹出序列

```汉语
题目:输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的数字均不相等。例如：序列1、2、3、4、5是某栈的压栈序列，序列4、5、3、2、1是该压栈序列对应的一个弹出序列，但4、3、5、1、2就不可能是该压栈序列的弹出序列。
```

**思路**

1. 建立一个辅助栈。
2. 循环拿出第一个出栈序列的第一个数，如4。
   * 若所有出栈序列都遍历完，则返回true。
   * 若没有遍历完，则进入第3步。
3. 判断辅助栈栈顶元素是否为4？
   * 不是，剩余压栈序列入辅助栈，直到数字4入栈。
     * 若剩余压栈序列到最后都没有数字4，则返回false；//if (j == len)
     * 剩余压栈序列有数字4，该数压栈之后出栈，出栈序列指针后移，返回第2步继续。
   * 是，该数出栈，出栈序列指针后移，返回第2步继续。

**code**

```c++
bool IsPopOrder(int* push_arr,int* pop_arr,int len)
{
	if (!(push_arr&&pop_arr))
		return false;
	assert(len > 0);
	stack<int> sup_stcak;
	int pop_arr_left;
	int j = 0;
	for (int i = 0; i < len;i++)//出栈序列
	{
		pop_arr_left = pop_arr[i];
		if (sup_stcak.size()==0||sup_stcak.top() != pop_arr_left)
		{			
			while (1)
			{
				if (j == len)//没有数可以进辅助栈了。
					return false;
				sup_stcak.push(push_arr[j]);
				if (push_arr[j++] == pop_arr_left)
					break;
			}
			sup_stcak.pop();
		}
		else
			sup_stcak.pop();
	}
	return true;
}
```

**考点**

* 对栈的理解。



###  23.从上往下打印二叉树（二叉树的层序遍历）

```汉语
题目：从上往下打印出二叉树的每个结点，同一层的结点按照从左到右的顺序打印。
```

**思路**

* 使用队列

1. 把起始结点放入队列中，接下来每次从队列的头部取出一个结点。
2. 遍历这个结点的孩子结点，都依次放入队列。
3. 重复1、2过程，知道队列为空

**code**

```c++
//二叉树的层序遍历
void PrintFromTopToBottom(BinaryTreeNode* root)
{
	if (!root)
		return;
	deque<BinaryTreeNode*> m_deque;//双向队列
    //queue<TreeNode*> Node; 单向队列也行
	m_deque.push_back(root);//先把起始结点放入
	while (!m_deque.empty())
	{
		BinaryTreeNode* curNode = m_deque.front();
		m_deque.pop_front();//头元素出队列
        //Node.pop();，单向队列头元素出队列
		printNode(curNode);//cout << root->m_value << " ";
		if (curNode->m_pLeft)
			m_deque.push_back(curNode->m_pLeft);
		if (curNode->m_pRight)
			m_deque.push_back(curNode->m_pRight);
	}
}
```

**考点**

* 二叉树的层序遍历，要用到队列
* 二叉树以及队列的理解

**拓展**

如何广度优先遍历一个有向图。与该题类似，取出结点后，遍历这个结点的所有子结点，都放入到队列中。



###  24.二叉搜索树的后序遍历序列

```汉语
题目：输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是返回true，否者返回false。假设输入的数组的任意两个数字都不相同。
```

*二叉搜索树：左结点<根<右结点*

**思路**

* **找到二叉树的根**，因为是后序遍历，则序列的最后一个数为根节点
* 根据搜索树特性，将剩下序列分为左右子树对应的序列。
* 递归在左右子树对应序列中继续判断。

**code**

```c++
//输入一个序列，判断改序列是否为一个二叉搜索树的后序遍历
bool VerifySquenceOfBST(int sequence[], int length)
{
	if (!sequence || length < 0)
		return false;
	int root = sequence[length-1];	//取序列最后一个数字，其为二叉树的根节点
	int i=0;	//找到左子树的结点
	for (; i < length - 1;++i)
	{
		if (sequence[i]>root)//找到第一个大于根节点的数为止
			break;
	}
	int j = i;	//找到右子树的结点
	for (; j < length - 1;++j)
	{
		if (sequence[j] < root)//右子树的结点都应该比根节点大，若有小的，则false
			return false;
	}
	//以上是每一次递归需要做的事情
	bool left = true;
	if (i > 0)//说明存在左子树
		left = VerifySquenceOfBST(sequence, i);//输入序列的头和长度
	bool right = true;
	if (i < length - 1)//说明存在右子树
		right = VerifySquenceOfBST(sequence + i, length - 1 - i);
	return (left&&right);
}

//另外一种写法
bool Verify(int sequence[], int first, int last);
bool VerifySquenceOfBST_two(int sequence[], int length)
{
	if (!sequence || length < 0)
		return false;
	return Verify(sequence,0,length-1);
}
bool Verify(int sequence[], int first, int last)
{
	//终止条件
	//如果要判断的序列 只有两个元素或更少，肯定是正确的 比如7 6，6为根，7为右。
	if (last - first <= 1)
		return true;
	int root = sequence[last];
	int curIndex = first;
	while (curIndex < last&&sequence[curIndex] < root)
		curIndex++;
	for (int i = curIndex; i < last;i++)
	{
		if (sequence[i] < root)
			return false;
	}
	return Verify(sequence, first, curIndex - 1) && Verify(sequence, curIndex, last - 1);
}
```

**考点**

* 后序遍历序列的特点，最后一个数为根结点
* 前序遍历序列的第一个数为根结点
* 在使用指针 如first last或者长度length时，有-1等操作，可以通过画图很好的理解。

**举一反三**

如果面试题要求处理一颗二叉树的遍历序列，首先应该找到二叉树的**根结点**，再基于根结点把整个树的遍历序列**拆分**成左子树对应的子序列和右子树对应的子序列，接下来再**递归**地处理这两个序列。如“重建二叉树”。

*即：根结点->拆分->递归*



###  25.二叉树中和为某一值的路径

```汉语 
题目：输入一棵二叉树和一个整数，打印出二叉树中结点值得和为输入整数得所有路径。从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。
```

**思路**

* 要从根结点出发，遍历二叉树，则要用到前序遍历。
* 当用前序遍历访问某一结点，把该结点的值添加到路径上，并累加。
* 如果该结点为叶结点并且路径中结点值刚好等于输入的数，则打印路径。
* 如果该结点不是叶结点，则继续访问它的子结点。
* 当前结点的左右结点访问完后，递归函数自动回到它的父结点，故在函数退出时，要在路径上删除当前结点。

**code**

```c++
vector<vector<int>> res;
void find_Recursive(TreeNode* root, vector<int>& path, int curSum, int exceptedSum)
{
	curSum += root->value;
	path.push_back(root->value);



	bool isLeaf = root->left == nullptr&&root->right == nullptr;
	if (curSum == exceptedSum&&isLeaf)//如果是叶结点且和满足要求，则输出
	{
		/*cout << "和为" << exceptedSum << "的路径为：" << endl;
		for (auto e : path)
			cout << e << " ";*/
		res.push_back(path); //这里要先把path的路径保存住了才行
		//return path;
	}
	if (root->left)//不是叶结点，存在左孩子
		find_Recursive(root->left, path, curSum, exceptedSum);
	if (root->right)//不是叶结点，存在右孩子
		find_Recursive(root->right, path, curSum, exceptedSum);
	path.pop_back();//返回父结点前把该结点的值弹出，因为该值不满足	
}

void FindPath(BinaryTreeNode* root, int exceptedSum)
{
	if (!root)
		return;
	vector<int> path;
	int curSum = 0;
	find_Recursive(root, path, curSum, exceptedSum);
}
```

**考点**

* vector也可以保证先进后出的特性。
* 本题中stack只能访问栈顶元素，存储路径不是最好选择。



## 4.2 分解让问题简单化

### 26.复杂链表的复制

```汉语
题目：请实现函数ComplexListNode* Clone(ComplexListNode* pHead),复制一个复杂链表。在复杂链表中，每个结点除了有一个m_pNext指针指向下一个结点外，还有一个m_pSibling指向链表中的任意结点或者null。
```

**思路一**

分两步，第一步复制原始链表上的每一个结点，并连接；第二步设置每个m_pSibling指针，由于不知道该指针指向的结点的位置，需要从头搜索，对于n个结点，时间复杂度是O(N^2^)。

**思路二**

也是分两步，第一步同上，并用哈希表<**N,N'**>保存结点的对于关系（N'是N复制的结点），第二步设置m_pSibling指针，可以通过哈希表在O(1)时间内找到。时间复杂度O(N)，空间复杂度O(N)。

**思路三（本题）**

![1578890936484](.\图片\复制链表的复制.png)

时间复杂度O(N),空间复杂度O(1)

**code**

```c++
//克隆N结点为N'，并将N'插入到N后面
void CloneNodes(ComplexListNode* pHead)
{
	ComplexListNode* pNode = pHead;
	while (pNode)
	{
		ComplexListNode* pClone = new ComplexListNode();
		pClone->m_nValue = pNode->m_nValue;
		pClone->m_pNext = pNode->m_pNext;
		pClone->m_pSibling = nullptr;
		pNode->m_pNext = pClone;
		pNode = pClone->m_pNext;
	}
}
//连接m_pSibling
void ConnectSiblingNodes(ComplexListNode* pHead)
{
	ComplexListNode* pNode = pHead;
	ComplexListNode* pSibling = new ComplexListNode();
	ComplexListNode* pNode_clone = new ComplexListNode();	
	while (pNode)
	{
		pSibling = pNode->m_pSibling;
		pNode_clone = pNode->m_pNext;
		if (pSibling)
			pNode_clone->m_pSibling = pSibling->m_pNext;
		pNode = pNode_clone->m_pNext;
	}
}
//将第二步得到的链表拆开
ComplexListNode* ReconnectNodes(ComplexListNode* pHead)
{
	if (!pHead)
		return nullptr;
	ComplexListNode* pNode = pHead;
	ComplexListNode* cloneHead = pNode->m_pNext;
	while (pNode->m_pNext)
	{
		ComplexListNode* nextNode = pNode->m_pNext;
		pNode->m_pNext = nextNode->m_pNext;
		pNode = nextNode;
	}
	return cloneHead;
}
ComplexListNode* Clone(ComplexListNode* pHead)
{
	CloneNodes(pHead);
	ConnectSiblingNodes(pHead);
	ComplexListNode* result = ReconnectNodes(pHead);
	return result;
}
```

**考点**

* 复制问题分解为多个小问题，逐一解决。
* 分析多种算法的时间和空间复杂度。



### 27.二叉搜索树与双向链表

```汉语
题目：输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。
```

![1578970866966](E:\STUDY\我的笔记\图片\二叉搜索树与双向链表.png)

**思路一**

* 使用中序遍历，这样，根结点的left相当于上一个结点，right相当于下一个结点，与链表类似。
* 使用递归
  * 先转换好左子树
  * 左子树转换的链表的最后一个结点（应该叫上一个节点更好理解），与根结点相连
  * 更新最后一个为当前结点（这一步不好理解）
  * 转换右子树
* 结束条件：当前结点为null

**思路二**（思路清晰）

* 先中序遍历所有节点存到容器中，容器中的顺序就是升序排列的
* 再根据容器中的节点，调整指针，达到双向链表的效果

**code**

```c++
//表示已经转换好的链表的最后一个结点，应该叫上一个节点更好理解
BinaryTreeNode* pLastNode = nullptr;
BinaryTreeNode* Convert(BinaryTreeNode* root)
{
	ConvertNodes(root);
	
	//我们需要返回头节点
	BinaryTreeNode* pHead = pLastNode;
	while (pHead&&pHead->m_pLeft)
		pHead = pHead->m_pLeft;
	return pHead;
}

void ConvertNodes(BinaryTreeNode* root)
{
    //结束条件
	if (!root)
		return;
	BinaryTreeNode* curNode = root;

	//转换左边
	if (curNode->m_pLeft)
		ConvertNodes(curNode->m_pLeft);

	//连接当前结点和左序列的最大结点
	curNode->m_pLeft = pLastNode;
	if (pLastNode)
		pLastNode->m_pRight = curNode;
	pLastNode = curNode;

	//转换右边
	if (curNode->m_pRight)
		ConvertNodes(curNode->m_pRight);
}
//方法二
//1.中序遍历二叉树，将节点存入容器中
void Inorder(TreeNode* root,vector<TreeNode*>& VecNode)
{
    if(!root)
        return;
    if(root->left)//左子树
        Inorder(root->left,VecNode);
    VecNode.push_back(root);//根节点
    if(root->right)//右子树
        Inorder(root->right,VecNode);
}
//2.根据容器顺序调整节点指针
TreeNode* AdjustPointer(vector<TreeNode*>& VecNode)
{
    //TreeNode* lastNode=nullptr;
    for(int i=0;i<VecNode.size()-1;i++)
    {
        VecNode[i]->right=VecNode[i+1];
        VecNode[i+1]->left=VecNode[i];
    }
    return VecNode[0];//返回头指针
}
//综合
TreeNode* Convert(TreeNode* pRootOfTree)
{
    if(!pRootOfTree)
        return nullptr;
    vector<TreeNode*> VecNode;
    Inorder(pRootOfTree,VecNode);
    return AdjustPointer(VecNode);
}
```

**考点**

* 中序遍历的特点：中序遍历二叉搜索树，遍历结果是**升序**排列
* 二叉树、双向链表的理解
* 递归



### 28.字符串的排列（修改）

```汉语
题目：输入一个字符串，打印出该字符串中字符的所有排列。
```

**思路**

以 `abc` 为例：

1. 将字符串分为两部分：**第一个字符**和**剩下的字符**。
2. 不断交换**第一个字符**与剩下字符。
   * 每交换一次，就递归排列**剩下的字符**。
   * 剩下的字符排列完，再交换回来。（第一次a和b交换，第二次a和c交换，第一个字符都是a）
3. 直到第一个字符为‘\0’，则就结束递归。

**思路二**（经典DFS递归回溯法，代码一样，理解不同）

以 `abc` 为例：个数为n（算是重复，比如aac，n=3）

1. （可能）有三个方格要填入数字，第一个方格有三中可能，第二个方格有两种可能，第三个方格有一种可能

   （是图的DFS的变形，图的每个方格都有四种可能的方向）

2. （递归）为当前方格填数，在每种可能中（for循环），选取一个数，然后递归填下一个方格。

   （图的DFS中走下一步）

3. （回溯）当前递归结束后，还原当前方格的数

   （图的DFS中，将走过方格的标记清除）

> 注意：如果有重复的数字，不要紧，使用set进行插入的时候，不会插入重复的排列，所以不用在代码层面考虑重复的情况。

**code**

```c++
//思路一
void swap(char* c1, char* c2)
{
	char temp = *c1;
	*c1 = *c2;
	*c2 = temp;
}
void Permutations_Recursive(char* str,char* begin)
{
	if (*begin == '\0')
		printf("%s\n",str);
	else
	{
		for (char* c = begin; *c != '\0'; ++c)
		{
			// 如果出现重复元素；跳过(除第一个外)
			if (*c == *begin&&c != begin)
				continue;
			//交换,固定第一个数c
			swap(c,begin);

			//排列剩下的元素
			Permutations_Recursive(str, begin + 1);

			//剩下的元素排列完毕，再交换回来
			swap(c, begin);
		}
	}	
}
void Permutations(char* str)
{
	if (!str)
		return;
	Permutations_Recursive(str, str);
}

//思路二！！！
//排列str中begin到end的字符
void Permutation_core(set<string>& strSet,string& str,int begin)
{
    if(begin==str.size()-1)//结束条件，到达最后一个方格，不用在走了。
        strSet.insert(str);//达到条件要做的事情
    else
    {
        //Permutation_core(strVec,str,begin+1);
        for(int i=begin;i<str.size();i++)//当前方格可能的情况
        {
            //跳过重复的字符
            //if(str[i]==str[begin])
            //continue;
            swapChar(str[i],str[begin]);//取第i个数放到第begin个方格上
            Permutation_core(strSet,str,begin+1);//递归下一个方格
            swapChar(str[i],str[begin]);//回溯！！！！
        }
    }
}
//与图的DFS比较
int goal_x = 9, goal_y = 9;     //目标的坐标，暂时设置为右下角
int n = 10 , m = 10;               //地图的宽高，设置为10 * 10的表格
int graph[n][m];        //地图
int used[n][m];         //用来标记地图上那些点是走过的
int px[] = {-1, 0, 1, 0};   //通过px 和 py数组来实现左下右上的移动顺序
int py[] = {0, -1, 0, 1};
int flag = 0;           //是否能达到终点的标志

void DFS(int graph[][], int used[], int x, int y)
{
    // 如果与目标坐标相同，则成功
    if (graph[x][y] == graph[goal_x][goal_y]) {     
        printf("successful");
        flag = 1;
        return ;
    }
    // 遍历四个方向
    for (int i = 0; i != 4; ++i) {    
        //如果没有走过这个格子          
        int new_x = x + px[i], new_y = y + py[i];
        if (new_x >= 0 && new_x < n && new_y >= 0 
            && new_y < m && used[new_x][new_y] == 0 && !flag) {
            
            used[new_x][new_y] = 1;     //将该格子设为走过

            DFS(graph, used, new_x, new_y);      //递归下去

            used[new_x][new_y] = 0;//状态回溯，退回来，将格子设置为未走过
        }
    }
}
```

**考点**

1. 注意重复的字符，需要判断。
2. 剩下的元素组合完，要恢复交换。//回溯
3. 递归的使用。

**拓展**（相关题目）

1. **字符串的组合**

       题目： 字符串的组合，如输入`abc`，则它们的所有组合有`a`、`b`、`c`、`ab`、`ac`、`bc`、`abc`。

   **思路**

求n个字符的长度为m（1<=m<=n）的组合

* 将n个字符分为两个部分：第一个字符和其余所有字符；
  
   * 如果组合包含第一个字符，即以第一个字符开头，则下一步在剩余的字符里选取m-1个字符（用递归）；
* 如果组合不包含第一个字符，则下一步在剩余的n-1个字符里选取m个字符（用递归）。
  
   **code**
   
```c++
   //---字符串的组合----//
void print_combination(vector<char>& vec )
   {
   	for (auto e : vec)
   		cout << e ;
   	cout << endl;
   }
   void combination_Recursion(vector<char>& vec, char* begin,int len)
   {
   	if (len == 0)//此组合的长度满足最开始的要求
   	{
   		print_combination(vec);
   		return;
   	}
   	if (*begin == '\0')//字符串遍历完了
   		return;
   	vec.push_back(*begin);
   	//把当前字符当作组合的一部分
   	combination_Recursion(vec, begin + 1, len - 1);
   	vec.pop_back();//删除当前字符，恢复之前的状态//回溯
   	combination_Recursion(vec, begin + 1, len);	
   }
   void combination(char* str)
   {
   	if (!str)
   		return;
   	int length = strlen(str);
   	vector<char> vec;
   	for (int i = 1; i <= length; i++)
   		combination_Recursion(vec,str,i);//i表示组合的长度，str表示开始的位置
   }
```

   **考点**

* 注意递归的终止条件，*begin == ‘\0'和len == 0，前者相当于已经遍历完整个字符串了，直接返回；后者相当于找到了符合的字符串，所以要打印出来（处理）然后返回。
   * 由于要考虑两种情况，先考虑第一种情况，把当前字符算入组合中，然后需要从vec中删除当前字符（即恢复原状），才考虑第二种情况。

2. **把8个数字放到正方体的8个顶点上**

   ```汉语
   题目：输入一个含有8个数字的数组，判断有没有可能把这8个数字分别放到正方体的8个顶点上，使得正方体上三组对应的面上的4个顶点的和相等。（8个顶点设为a1-a8）
   ```

   **思路**

   这相当于先得到a1-a8这八个数字的**全排列**，然后**判断**有没有某一排列符合题目给定的条件，即a1+a2+a3+a4=a5+a6+a7+a8, a1+a3+a5+a7=a2+a4+a6+a8, a1+a2+a5+a6=a3+a4+a7+a8。

3. **国际象棋**

   ```汉语
   题目：在8x8的国际象棋上排放8个皇后，使其不能互相攻击，即任意两个皇后不处在同行、同列或者在同一对角线。
   ```

   **思路**

   * 定义一个数组col[8]，数组中**第i个元素**表示位于**第i行**的皇后所在的**列号**。
   * 初始化数组为0~7
   * 对数组进行**全排列**（比如（2，5，1，6，0，3，4，7）就满足）
   * 判断是否有两个皇后在对角线上：`行1-行2 = 列1-列2`？即`i-j = col[i]-col[j]` 

**举一反三**

如果题目要求摆放若干个数字，我们可以先求出这些数字的全排列，然后再一一判断每个排列是否满足题目要求。





# 第5章 优化时间和空间效率

## 5.2 时间效率

### 29.数组中出现次数超过一半的数字

```汉语
题目：数组中有一个数字出现的次数超过数组长度的一半。请找出这个数字。
```

**思路一**（我的）

遍历一次数组，利用map记录每个数与对应的个数。时间和空间复杂度均为O(N)

还可以先排序，再找。时间复杂度O(NlogN)

**思路二**

利用快排的思想（partition），找到一个中位数，判断该中位数的个数是否超过一半。时间复杂度均为O(N)

**思路三**（推荐）

利用数组的特性，找到一个数，判断该数的个数是否超过一半。时间复杂度均为O(N)

* 先遍历一遍数组，保存两个值，一个数字result，一个次数times

  - 遍历下一个数字，如果相等，次数+1
  - 如果不等，次数-1
  - 如果次数为0，保存该数，并设times=1
  - 遍历完，**要找的数就是最后一次把次数设为1的数字**

  其实可以想象成不同的数字两两相互抵消，如果有一个数字的次数超过数组长度的一半，平均两个数就有一个数是该数。

* 找到该数之后，在遍历一遍数组，判断该数的个数是否超过一半

tip：如果先将数组排序后再处理，快的排序时间复杂度为O(NlogN)

**code**

```c++
//自己最先想到的方法
//时间复杂度O(N),空间复杂度O(N)
void MoreThanHalfNum1(int* arr,int len)
{
	//检查空数组
	if (!arr)
		return;
	map<int, int> arr_num;//保存数字中的数组与对应的个数
	for (int i = 0; i < len; i++)
	{
		if (arr_num.find(arr[i]) == arr_num.end())//说明arr_num中没有该数字
			arr_num.insert(pair<int, int>(arr[i], 1));
		else
			arr_num[arr[i]]++;//如果存在该数字 那就将该数字的个数+1
		//判断是否满足了条件
		if (arr_num[arr[i]] > (len / 2))
		{
			cout << arr[i] << "出现了" << arr_num[arr[i]] << "次" << endl;
			cout << "超过了数组的一半" << endl;
			return;//可以结束了
		}
	}
	cout << "数组中没有超过一半的数字" << endl;
}


//剑指offer,利用数组的特点来做
void CheckNum(int* arr, int len, int num)
{
	int times = 0;
	for (int i = 0; i < len; i++)
	{
		if (arr[i] == num)
			times++;
		if (times * 2 > len)
		{
			cout << "找到该数" << num<<endl;
			return;
		}
	}
	cout << "未找到该数" << endl;
}

//剑指offer,利用数组的特点来做
void MoreThanHalfNum2(int* arr, int len)
{
	if (!arr)
		return;
	int result = arr[0];
	int times = 1;
	//找出一个result
	for (int i = 1; i < len; i++)
	{
		if (times == 0)
		{
			result = arr[i];
			times = 1;
		}
		else if (result == arr[i])
			times++;
		else
			times--;
	}
	//判断这个result的次数是否达到要求
	CheckNum(arr, len, result);
}

```

**考点**

* 对时间复杂度的理解，每想到一种方法，都应该分析它的时间复杂度。
* 思维的全面性，是否对无效输入做出相应的处理。





### 30.最小的k个数

```汉语
题目：输入n个整数，找出其中最小的k个数字。
```

**思路一**

很容易想到，直接排序，然后输出。时间复杂度O(NlogN)

**思路二**
还是利用**partition**函数找到一个数，其下标为k-1，则其左边的数都比这个数小。算上该数就是最小的k个数字。

时间复杂度O(N)。缺点：会改变输入的数组，并且不适合大数据场景。

**思路三**

利用红黑树来存储k个最小的数。时间复杂度O(NlogK）

* 创建一个大小的k的容器
* 遍历输入数据，若容器未满，则存入，已满则比较。
* 比较新数据与k个数据中的最大值
  * 若新数据小，则新数据替换掉最大值
  * 反之，继续下一位。
* 重读上一步直到结束。

由于需要多次对k个数据进行查找最大值、插入数据、删除数据等操作，可以使用红黑树来存储（插入和删除只需要O(logK)时间，而找到最大值只需要O(1)时间）

突然想到，在替换时，为什么要删除掉最大值再插入新数据呢？因为不能改变红黑树中节点的值。

 **code**

```c++
using intSet = multiset<int , greater<int>>;//greater<int>降序排列，默认less<int>升序
void GetLastNum(vector<int>& data, int k, intSet& leastNum)
{
	if (data.size()<k||k<1)//当1<=k<=data.size()时才行
		return;
	for (auto e : data)
	{
		//先装满k个数
		if (leastNum.size() < k)
			leastNum.insert(e);
		//后面的数与k个数中的最大数字比较
		else
		{
			if (e < *leastNum.begin())//比最大数小就替换最大数
			{
				leastNum.erase(leastNum.begin());
				leastNum.insert(e);
			}
		}
	}
}//leastNum中存有最小的k个数
//输入vector<int> data{4,5,1,6,2,7,3,8};k=4
//leastNum{4,3,2,1}
```

**考点**

* 时间复杂度的分析
* 考察对堆、红黑树等数据结构的理解。当需要在某数据容器中频繁查找以及替换最大值时，二叉树是一个好的选择，并能想到**用红黑树和堆**来实现。（红黑树和堆 都是基于二叉树来实现的。在使用红黑树时，可以STL中的set和multiset）

**拓展**

* 该题也可以改成**最大的k个数**，找最大的k个数与**找第k大的数**类似（使用思路三）
* 本题与**找出最小的第k个数**类似（使用思路三）
* 思路二找到的最小的k个数是没有排序的，而思路三找的最下的k个数是排好序的。



### 31.连续子数组的最大和

```汉语
题目：输入一个整型数组，数组里有正数也有负数，数组中有一个或者连续的多个整数组成一个子数组，求所有子数组的和的最大值。要求时间复杂度为O(N)
```

**思路**

**应用动态规划**思想
$$
f(i) = \begin{cases}  
arr[i] & i=0 或 f(i-1)\leq0 \\
f(i-1)+arr[i] & i>0并且f(i-1)>0
\end{cases}
$$



用递归方式分析动态规划的问题，再基于循环去编码

**code**

```c++
//right code
int GetMaxOfSubArray(int* arr, int len)
{
	assert(len>0);
	assert(arr);

	int result = INT_MIN;//不能设置为零。有可能初始数据全是负数
	int sum = 0;

	for (int i =0 ; i < len; i++)
	{
		if (sum <= 0)
			sum = arr[i];
		else
			sum = sum+arr[i];//f(i)=f(i-1)+arr[i]//不用管这个arr[i]是正还是负数
		if (sum > result)//保存其中的最大值//result就是上一次的sum，现在比较取大的就行了。
			result = sum;
	}
	return result;
}
```

**考点**

* 时间复杂度的分析
* 动态规划思想，往往用循环来编码



### 32.从1到n整数中1出现的次数

```汉语
题目：输入一个整数n，求从1到n这n个整数的十进制表示中1出现的次数。例如输入12，从1到12这些整数中包含1的数字有1、10、11、12，1一共出现了5次。
```

**思路一**

最容易想到的是从1到n遍历每个数（O(N)），对每个数再逐位进行判断是否为1.(O(logN))。总共的时间复杂度为O(NlogN)。很显然不是一个好算法。

**思路二（非剑指offer思路）**

依次分析数字`1`在个位、十位、百位……出现的次数。时间复杂度O(logN)

例如103这样的数字：

- 个位数是1的有001，011，021，031，041，051，061，071，081，091，101，共11个
- 十位数是1的有010，011，012，013，014，015，016，017，018，019，共10个
- 百位数是1的有100，101，102，103

**由此可见，对每一位来说，1出现的次数与这一位本身、这一位的所有高位、这一位的所有低位有关。**

再看例子：

假设百位上为0, 1, 和 >=2 三种情况:

* n=3141092，highNum= 3141**0**，lowNum=92。百位上1的个数应该为 3141 *100 次
* n=3141192，highNum= 3141**1**，lowNum=92。百位上1的个数应该为 3141 *100 + (92+1) 次
* n=3141592，highNum= 3141**5**，lowNum=92。百位上1的个数应该为 (3141+1) *100 次

**code**

```c++
int NumberOfBetween12N(int n)
{
	int num = 0;
	for (int i = 1; i <= n; i *=10)
	{
		int highNum = n /i;//31410(包含当前位，也可以highnum/10，则不包含当前位)
		int lowNum = n % i;//92
		int curNum = highNum % 10;//当前位0

		//if (curNum == 0)
		//	num += highNum/10*i;
		//else if (curNum == 1)
		//	num += highNum/10*i + lowNum + 1;
		//else
		//	num += highNum/10*i + i;//(highNum+1)*i

		//上面if/else可用下面一句话代替
		num += (highNum + 8) / 10 * i + (curNum == 1 ? lowNum + 1 : 0);
	}
	return num;
}
```

**考点**

* 获取每位的数，先取余%，再取整/，如213，分别得到3、1、2；
* 若有多种情况要分别讨论时，找机会合并
  * 如分为=0，=1，>=2时，考虑能后用**（a+8）/10**来解决

**拓展**

如果是要求从m到n整数中1出现的次数，则用**f(n)-f(m)**。



### 33.把数组排出最小的数

```汉语
题目：输入一个正整数数组，把数组里所有数字拼接起来排成一个数，找到所有数中最小的一个。如输入{3，32，321}，则最小的数为321323。
```

**思路一**

利用28.进行一个全排列，在找出排列中的最小值。时间复杂度O(N!)。

**思路二**

比如21和3 组合213<321，则21应该排在3的前面。时间复杂度O(NlogN)

* （排序）所以，我们更应该注意的是元素之间的**前后位置关系**，可以先将数组按某一规则进行排序。时间复杂度O(NlogN)
* （拼接）数字的拼接问题，将数字转换为字符串再进行操作。

**code**

```c++
bool cmp(int a, int b)
{
	string str_a = to_string(a);
	string str_b = to_string(b);
	return str_a + str_b < str_b + str_a;
}

string GetMinNum(vector<int>& numbers)
{
	if (numbers.empty())
		return 0;
	//sort(numbers.begin(),numbers.end(),cmp);
	sort(numbers.begin(), numbers.end(), //#include <algorithm>
		[](int a, int b) {return to_string(a) + to_string(b) < to_string(b) + 		             to_string(a); });
	string res;
	for (auto e:numbers)
		res += to_string(e); //#include <string>
	return res;
}
```

**考点**

* 按照某一规则对数组进行排序。sort函数的使用。
* 大数问题，两个int型数据相加，结果可能超过int表示的范围，我们可以考虑使用字符串。



## 5.3 时间效率与空间效率的平衡

### 34.丑数

```汉语
题目：我们把只包含因子2、3、5的数称为丑数(ugly number)。求按从小到大的顺序的第1500个丑数。例如6、8是丑数，但14不是，因为14包含7。1为第一个丑数
```

丑数=a* 2+b* 3+c* 5

**思路一**

从1到n依次判断每个数是否为丑数，直到找到第1500个。效率太低，因为不是丑数的数，我们也进行了判断，判断是要花费时间的。

**思路二**

创建数组保存已经找到的丑数，这个数组保存丑数，用空间换时间。

关键：如何找到并**按序保存**丑数呢？

1. 创建三个指针i2，i3，i5，初始指向第一个丑数。

2. 三个指针指向的数乘以2，3，5，取最小值作为下一个丑数 //min(res[i2]*2, min(res[i3] * 3, res[i5] * 5))

3. 被选中的指针+1
4. 返回第二步，直到找到第1500个

![](.\剑指offer图片\丑数.png)

**code**

```c++
int GetUglyNumber(int index)
{
	if (index <= 0)
		return -1;
	int i2 = 0;
	int i3 = 0;
	int i5 = 0;

	vector<int> res(index);
	res[0] = 1;//初始值
	for (int i = 1; i < index; i++)//从1开始，因为res[0]=1
	{
		res[i] = min(res[i2]*2, min(res[i3] * 3, res[i5] * 5));//选择最小的数加入
		//若被选中，则下标+1，指向当前值
		if (res[i] == res[i2] * 2) i2++;
		if (res[i] == res[i3] * 3) i3++;
		if (res[i] == res[i5] * 5) i5++;
	}
	return res[index-1];
}


//如何判断一个数 是否是丑数
bool isUglyNum(int num)
{
	while (num % 2 == 0)
		num /= 2;
	while (num % 3 == 0)
		num /= 3;
	while (num % 5 == 0)
		num /= 5;
	return (num == 1) ? true : false;
}
```

**考点**

* 什么是丑数？如上代码。
* 空间换时间的讨论。
* 一个元素要被多次使用，可考虑用多个指针。



### 35.第一次出现一次的字符

```汉语
题目：在字符串中找到第一个只出现一次的字符，如输入“abaccdeff”，则输出‘b’。
```

**思路**

第一次遍历字符串，利用map保存每个字符与对应的个数，时间复杂度O(N)。

第二次遍历字符串，找到第一个个数为1的字符，时间复杂度O(N)。

**code**

```c++
char GetFirstNotReChar(char* pString)
{
	if (!pString)
		return '\0';
	map<char, int> c_num;
	char* str = pString;
	while(*str !='\0')
	{
		if (c_num.find(*str) == c_num.end())//不存在
			c_num.insert(pair<char, int>(*str, 1));
		else
			c_num[*str]++;
		str++;
	}
	str = pString;
	while (*str != '\0')//不能用for(auto e:c_num),因为map自动排序了
	{
		if (c_num[*str] == 1)
			return *str;
		str++;
	}
	return '\0';
}

//下面的好一点
char GetFirstNotReChar2(string pString)
{
	if (pString.empty())
		return '\0';
	map<char, int> c_num;
	for(int i=0;i<pString.size();i++)
	{
		if (c_num.find(pString[i]) == c_num.end())//不存在
			c_num.insert(pair<char, int>(pString[i], 1));
		else
			c_num[pString[i]]++;
	}
	for (int i = 0; i<pString.size(); i++)
	{
		if (c_num[pString[i]] == 1)
			return pString[i];
	}
	return '\0';
}
//测试
void test2()
{
	string s;
	getline(cin,s);//可以读空格
	cout << GetFirstNotReChar2(s) << endl;
}
```

**考点**

* 1.new int[] 是创建一个int型数组，数组大小是在[]中指定
    int * p = new int[3]; //申请一个动态整型数组，数组的长度为[]中的值
  2.new int()是创建一个int型数，并且用()括号中的数据进行初始化,例如：
    int *p = new int(10); // p指向一个值为10的int数。

* ```c++
  //可以读空格
  //法一
  string s;
  getline(cin,s);
  //法二
  char* inputStr = new char();
  cin.getline(inputStr,100);//需要指定长度
  ```

* ```c++
  char* inputStr = new char();
  cin.getline(inputStr,100);//需要指定长度
  cout << GetFirstNotReChar(inputStr) << endl;
  //此时delete inputStr 出错！！！！！！！！why？？？？？
  ```

**举一反三**

* **当需要频繁查找数据时，可用map保存这些数据，可以在O(1)时间找到。**

  - 比如，在字符串A中删除在字符串B中出现过的字符。

  可以用map保存字符串B中出现过的字符，在遍历A时，查找map中是否存在该字符。

  - 再比如，删除字符串A中重复的字符串，如google -> gole

  同上，扫描两遍。

* 如需要**判断多个字符**是不是在字符串中**出现**过，或者统计多个字符在字符串中出现的**次数**，可以考虑用map。

  或者使用`unsigned int hashTable[256]`创建一个简单的哈希表。256是因为char类型占八位，有256种可能。比如hashTable['A']++。

  

### 36.数组中的逆序数

```汉语
题目：在数组中的两个数如果前面一个数字大于后面一个数字，则这两个数字组成一个逆序对，输入一个数组，返回这个数组的逆序对的总数。
```

**思路一**

选定一个数，逐个和剩下的数比较，用n个数，时间复杂度为O(N^2)

**思路二**

根据归并排序的思想

在归并排序过程中，比较是否逆序。时间复杂度O(NlogN)

![](C:\Users\Administrator\Desktop\剑指offer图片\归并排序.png)

![1581164067732](C:\Users\Administrator\Desktop\剑指offer图片\逆序数对.png)

**code**

```c++
int InversePairsCount_Recursion(vector<int>& data, vector<int>& copy_data,int start,int end )
{
	if (start == end)
	{
		copy_data[start] = data[end];//✳必需要！下面不用再更新data，将copy_data当作新的输入
		return 0;
	}
		
	int mid = (start + end) >>1;       //✳注意这里copy_data, data是反过来的，这样就不用更新了
	int leftCount = InversePairsCount_Recursion(copy_data, data, start,mid);//左边数组内部逆序数对个数
	int rightCount= InversePairsCount_Recursion(copy_data, data, mid+1, end);//右边数组内部逆序数对个数
	//接下来计算组间的逆序数对个数
	int count = 0;
	int leftIndex = mid;//两个指针
	int rightIndex = end;//向前移动直到该部分的头
	int copyIndex = end;//指向辅助数组的尾部
	while (leftIndex>=start&&rightIndex>=(mid+1))
	{
		if (data[leftIndex] > data[rightIndex])
		{
			//左边部分的大，存在逆序数对rightIndex-mid个，
			//将左边数放到辅助数组的末尾，左边指针前移
			copy_data[copyIndex--] = data[leftIndex--];
			count += rightIndex - mid;
		}
		else
		{
			//如果左边的数小，在该对不是逆序数对
			//将右边数放入辅助数组末尾，右边指针前移
			copy_data[copyIndex--] = data[rightIndex--];
		}
		//原则就是让辅助数组升序排列，在升序排列过程中，得到逆序数对
	}
	//while结束，说明有一部分已经遍历完毕，我们不知是哪一个
	//将剩下的全部放到辅助数组中去
	while (leftIndex >= start)  copy_data[copyIndex--] = data[leftIndex--];
	while (rightIndex >= (mid+1)) copy_data[copyIndex--] = data[rightIndex--];
	////逆序数对找完，更新data中start到end的元素
	////就是给原数据排序（升序）
	//for (int i = start; i <= end; i++)
	//	data[i] = copy_data[i];
	//因为上面两处✳，这里就不用再更新了。
    
	return leftCount + rightCount + count;
}

int InversePairsCount(vector<int>& data)
{
	if (data.empty())
		return 0;
	vector<int> copy_data(data);
	return InversePairsCount_Recursion(data,copy_data,0,data.size()-1);
}
```

**考点**

* 递归算法和归并排序，在归并排序过程中找到逆序数。当前本题在组合数组时，是从后往前面赋值。
* 递归算法不要去考虑每一级做了什么，只考虑这一级要做什么。



### 37.两个链表的第一个公共节点

```汉语
题目:输入两个链表，找到它们的第一个公共节点。，两条链表的长度分别m,n
```

**思路一**

最直接的解法就是暴力解法，在链表A中找到一个结点，遍历所有链表B的结点判断是否相同。

没有利用额外空间，时间复杂度为O(m*n)

**思路二**

仔细思考发现，由于一个结点只允许有一个next结点，若两条链表有公共节点，必然**呈现Y字型**，后面重合了。

那我们可以从尾部入手，往前判断，直到最后一个相同的结点。

* 怎么从后往前遍历链表呢？反转？
* no！**借助stack“先进后出”的原理来实现**。
* 将两条链表分别放入两个栈中，比较栈顶元素即可，相同则都弹出，直到最后一个相同的结点

空间复杂度O(m+n)，时间复杂度O(m+n)=A放到栈O(m)+B放到栈O(n),**同一量级复杂度相加。**

**思路三（本题）**

由Y字型出发，让长链表的头指针先后移length长-length短，再比较。时间复杂度O(m+n)

1. 分别遍历一遍，找出两链表的长度
2. 让长链表指针先走，与短链表并起
3. 比较两数，同时后移，直到相等

![1581249845038](C:\Users\Administrator\Desktop\在家学习\剑指offer图片\两个链表的第一个公共节点.png)

**code**

```c++
ListNode* FindFirstCommonNode(ListNode* pHead1, ListNode* pHead2)
{
	/*1分别遍历一遍，找出两链表的长度*/
	if (!pHead1 || !pHead2)
		return nullptr;
	int length1=0;
	int length2 = 0;
	ListNode* pTemp = pHead1;
	while (pTemp != nullptr)
    {
		length1++;
		pTemp=pTemp->pNext;
	}
	pTemp = pHead2;
	while (pTemp != nullptr)
	{
		length2++;
		pTemp = pTemp->pNext;
	}
	/*2让长链表指针先走，与短链表并起*/
	if (length1 > length2)
	{
		for (int i = 0; i < length1 - length2; i++)
			pHead1 = pHead1->pNext;
	}
	else 
	{
		for (int i = 0; i < length2 - length1; i++)
			pHead2 = pHead2->pNext;
	}
	/*3比较两数，同时后移，直到相等*/
	while (pHead1&&pHead2)
	{
		if (pHead1->value == pHead2->value)
			return pHead1;
		pHead1 = pHead1->pNext;
		pHead2 = pHead2->pNext;
	}
	return nullptr;
}
```

**考点**

* 时间复杂度，同一量级复杂度相加。

**拓展**

* 二叉树中两个叶子结点的最低公共祖先。7.2会讲到。



# 第6章 面试中的各项能力

## 6.3 知识迁移能力

### 38.数字在排序数组中出现的次数

```汉语
题目：统计一个数字在排序数组中出现的次数。例如输入排序数组{1，2，3，3，3，3，4，5}和数字3，输出4。
```

**思路一**

时间复杂度O(N)的两种解法

1. 直接顺序遍历数组，找到k出现的次数。

2. 利用二分查找找到k，然后从这个位置向左和向右分别查找k的个数，由于数组可能全是k，因此时间复杂度还是O(N)

**思路二**

对二分查找进行迁移、改变。时间复杂度为O(logN)

* 二分查找找到k
* 判断这个k是否第一个k（判断这个k**前面的数**是否是k）
  * 是第一个k，直接返回下标
  * 如不是第一个k，在这个k前面再次二分查找k
* 同理找到最后一个k

**code**

```c++
//二分查找递归
int BinarySearch_Recursion(vector<int>& data, int left, int right, int num)
{
	if (left > right)
		return -1;
	int mid = left + ((right - left) >> 1);
	if (data[mid] > num)
		return BinarySearch_Recursion(data, left, mid-1, num);//这里是mid-1
	else if (data[mid] < num)
		return BinarySearch_Recursion(data, mid + 1, right, num);
	else
		return mid;
}
int FindFirstK(vector<int>&data,int left,int right ,int k)
{
	int index = BinarySearch_Recursion(data, left, right, k);
	if (index <= 0)//可能不存在
		return index;
	while (data[index - 1] == k)//这里index-1要判断,可能超出左边界
	{
		index = BinarySearch_Recursion(data, left, index-1, k);
		if (index == 0)//这里index-1要判断,可能超出左边界
			break;
	}	
	return index;
 }
int FindLastK(vector<int>&data, int left, int right, int k)
{
	int index = BinarySearch_Recursion(data, left, right, k);
	if (index == data.size() - 1||index==-1)//可能不存在
		return index;
	while (data[index + 1] == k)//index+1也要判断，可能超出右边界
	{
		index = BinarySearch_Recursion(data, index + 1, right, k);
		if (index == data.size() - 1)//index+1也要判断，可能超出右边界
			break;
	}
	return index;
}
int GetNumOfK(vector<int>& data, int k)
{
	if (data.empty())
		return 0;
	int first = FindFirstK(data,0,data.size()-1,k);
	int last = FindLastK(data, 0, data.size() - 1, k);
	if (first == -1)
		return 0;
	return last - first + 1;
}
```

**考点**

* 知识的迁移能力
* 利用二分查找用来查找重复数字的第一个和最后一个



### 39.二叉树的深度

```汉语
题目：输入一颗二叉树的根结点，求该树的深度。树最长的路径极为树的深度。
```

**思路**

典型递归解法

**code**

```c++
int TreeDepth(BinaryTreeNode* root)
{
	if (!root)
		return 0;
	int leftDepth = TreeDepth(root->leftNode);
	int rightDepth = TreeDepth(root->rightNode);
	return leftDepth > rightDepth ? leftDepth + 1 : rightDepth + 1;
}
```

**拓展**

```汉语
题目：判断一个二叉树是否为平衡二叉树。二叉树的任意结点的左右子数的深度相差不超过1，即为平衡二叉树
```

**思路一**

判断平衡二叉树与树的深度有关。**双重递归**。

* 求左右子树的深度
* 比较深度
* 判断左右子数是否为平衡二叉树

缺点：需要重复遍历结点多次。

**思路二**

只需要遍历每个结点一次。即采用后序遍历

* 先判断左右子树是否为平衡二叉树，且求出各自深度
* 比较左子树深度和右子树深度，来判断当前结点是否为平衡二叉树。

**code**

```c++
bool IsBalanced(BinaryTreeNode* root,int* depth)
{
	if (!root)
	{
		*depth = 0;
		return true;
	}
	int leftDepth;
	int rightDepth;
	if (IsBalanced(root->leftNode, &leftDepth) && 
		IsBalanced(root->rightNode, &rightDepth))
	{
		//在左、右子树都是平衡二叉树的基础上
		int dif = leftDepth - rightDepth;
		if (dif<=1||dif>=-1)
		{
			*depth = 1+(leftDepth > rightDepth ? leftDepth : rightDepth );
			return true;
		}
	}
	return false;
}
```

**考点**

* 判断一颗二叉树是否为平衡二叉树，需要判断**当前结点的树、左子树、右子数**是否平衡。而不是只判断当前结点。
* 若为先序遍历，则先计算根节点，再计算左右子结点。比如计算深度，计算根结点的深度需要遍历子结点，而在计算子结点的深度的时候，又**重复遍历了孩子结点**。
* 若为后序遍历（思路二），先计算孩子结点，再计算根节点，比如深度，此时计算根结点的深度，就不需要再遍历孩子结点了，直接孩子结点的深度+1。这**就是优势**！
* 对树的理解。



### 40.数组中只出现一次的数字

```
题目：一个整数数组里除了两个数字之外，其他的数字都出现了两次。请在O(n)时间、O(1)空间找出这两个数。
```

**思路**

利用异或运算。与0异或相同，与1异或相反

提示：先考虑数组中只有一个数字出现了一次，其他数字都出现了两次

* 依次异或数组中的每一个数字：`0^data[0]^data[1]^data[2]`,由于异或具有结合和交换律，先将相同的异或，结果全部为0，0再与最后一个只出现一次的数字异或，结果就是答案。

本题：将数组分为上述的两个数组

* 由提示启发，依次异或整个数组得result_OR
* 找到result_OR中第一个为1的位置indexOf1（从低位开始）
  * 在这一位上，要找的两个数肯定是不同的。因为相同的异或为0
* 由indexOf1位上得数是否位1，将数组分为两组
  * 由于相同的数每一位都相同，依次必然分在同一组。
* 在两组内分别异或，结果就是答案。

**code**

```c++
//判断number的indexBit位是不是1
bool IsBit1(int number, unsigned int indexBit)
{
	number = number >> indexBit; //0010 1在第一位，最低位是第0位
	return number & 1;
}

//找到number第一个为1的位置（从低位开始）
int FindFirstBitIs1(int number)
{
	int index = 0;
	while ((number & 1) == 0 && index < 8 * sizeof(int))
	{
		index++;
		number = number >>1;//0010->001
	}
	return index;
}

void FindNumberAppearOnce(vector<int> data, int* num1, int* num2)
{
	if (data.empty())
		return;
	int result_OR = 0;
	//整体异或求结果
	for (int i = 0; i < data.size(); i++)
		result_OR ^= data[i];//"^"就是异或
	//求结果求第一个为1的位置
	int indexOf1 = FindFirstBitIs1(result_OR);
	//根据第一个为1的位置分组，再各自异或求得num1、num2
	for (int i = 0; i < data.size(); i++)
	{
		if (IsBit1(data[i], indexOf1))
			*num1 ^= data[i];//将indexBit1这一位为1的数分为一组
		else
			*num2 ^= data[i];
	}
}
```

**考点**

* 此题解法比较巧妙，将简单的问题迁移到复炸问题上。
* 二进制和位运算的考察。判断某一位是否位1。



### 41.和为s的两个数VS和为s的多个数

```汉语
题目：输入一个递增排序数组和一个数s，在数组中查找这两个数，使得两数之和为s，输出一对即可。
```

**思路**

O(N)时间、头尾指针。

* 头尾指针对应数相加
  * 和大于s，则尾指针前移
  * 和小于s，则头指针后移
* 知道头尾指针相遇

**code**

```c++
//和为s的两个数
bool FindTwoNumWithSum(vector<int>& data, int sum, int* num1, int* num2)
{
	//data是升序数组
	if (data.empty()||!num1||!num2)
		return false;
	int small = 0;//头指针
	int big = data.size() - 1;//尾指针
	while (small < big)
	{
		*num1 = data[small];
		*num2 = data[big];//这里不能些num2=&data[big]
		if ((*num1 + *num2) > sum)
			big--;//两数之和大于sum则尾指针前移
		else if ((*num1 + *num2) < sum)
			small++;//两数之和小于sum则头指针后移
		else
			return true;
	}
}
```

**拓展**

```汉语
题目2：输入一个正数s，打印升序数组中所有和为s的连续多个数（至少两个数）例如输入15，1+2+3+4+5=4+5+6=7+8=15
```

**思路**

与上题类似，快慢指针

* 慢指针指向数组中第一个数，快指针指向第二个数
* 从慢指针到快指针数之和
  * 小于s，则快指针后移，为了包含更多的数。
  * 大于s，则慢指针后移，减少一些数。
  * 等于s，则答应两指针之间的数，并且慢指针前移，继续寻找
* 如果`big<data.size()-1&&small<big`，返回上一步继续。

**code**

```c++
void FindContinueNumWithSum(vector<int>& data, int sum)
{
	if (data.empty()||sum<3)//这里按理应该是data[0]和data[1]
		return;
	int small = 0;//慢指针
	int big = 1;//快指针
	int cursum = data[small] + data[big];
	//注意结束条件
	//因为是升序的，如sum=15，data[samll]最大到7就可以了
	while (big<data.size()-1&&small<big)//这里的判断还可以缩小范围，但是这么些不容易报错
	{
		if (cursum < sum)
			cursum += data[++big];//++big,先后移再加
		else if (cursum > sum)
			cursum -= data[small++];//small++，先减在后移
		else//找到了
		{
			for (int i = small; i <= big; i++)
				cout << data[i] << " ";
			cout << endl;
			cursum -= data[small];
			small++;//加这两句 是因为继续找。
		}	
	}
}
```

**考点**

* **data[++big]**和**data[small++]**的意义是不一样的

* 在函数参数中有**int* num**这样的传出参数，在函数内部应该

  ```c++
  *num2 = data[big];//这里不能些num2=&data[big]
  ```

  

### 42.翻转单词顺序 VS 左旋转字符串

```汉语
题目：输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。（标点符号当作字符处理）
```

**思路**

先对整体做翻转，再对每个单词做翻转。以空格符号来区分单词

**code**

```c++
void swap(char& s1, char& s2)
{
	auto temp = s1;
	s1 = s2;
	s2 = temp;
}
//翻转单词
string Reverse(string str, int begin, int end)
{
	if (str.empty()||begin>end||begin<0||end>str.size()-1)
		return nullptr;
	while (begin < end)
		swap(str[begin++],str[end--]);
	return str;
}
//翻转句子 student. a am I
string ReverseSentence(string str)
{
	if (str.empty())
		return "";
	str = Reverse(str, 0, str.size() - 1);//先整体翻转
	int end = 0;
	int begin = 0;
	while (end < str.size())
	{
		if (str[end] == ' ')
		{
			str=Reverse(str, begin, end-1);
			begin = end + 1;
		}
		end++;	
	}
	return str;
}
```

**拓展**

```汉语
题目：左旋转字符串 如输入student，2  输出udentst
```

**思路**

三次翻转

1. 翻转前n个
2. 翻转后size-n个
3. 整体翻转

**code**

```c++
//左旋转字符串
string LeftRotatesString(string str,int n)
{
	if (str.empty()||n<1||n>str.size()-1)
		return str;
	str = Reverse(str, 0, n-1);
	str = Reverse(str,n,str.size()-1);
	str = Reverse(str,0,str.size()-1);

	return str;
}
```

**考点**

* 考察对字符串的编程能力



## 6.4 抽象建模能力

### 43.n个骰子的点数

```汉语
题目：把n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入你，打印出所有可能的s以及概率
```

**思路**

利用动态规划的思想，设n个骰子和为s记作`f(n,s)`

* 若第n个骰子为1，则`f(n,s)`=`f(n-1,s-1)`
* 若第n个骰子为2，则`f(n,s)`=`f(n-1,s-2)`  
* ……

故`f(n,s)`=`f(n-1,s-1)`+`f(n-1,s-2)`+`f(n-1,s-3)`+`f(n-1,s-4)`+`f(n-1,s-5)`+`f(n-1,s-6)`

初始值f(1,1)=1,f(1,2)=1,f(1,3)=1,f(1,4)=1,f(1,5)=1,f(1,6)=1,

**code**

```c++
//f(n,s)=f(n-1,s-1)+f(n-1,s-2)+f(n-1,s-3)+f(n-1,s-4)+f(n-1,s-5)+f(n-1,s-6)
//初始值 f(1,1)=1,f(1,2)=1,f(1,3)=1,f(1,4)=1,f(1,5)=1,f(1,6)=1,
//f(n,s)表示n个骰子和为s的次数

//动态规划的思想
//递归
int Probability_Recursion(int n, int sum)
{
	int maxNum = 6;
	if (n < 1 || sum<n || sum>maxNum*n)
		return 0;
	//初始值
	if (n == 1) return 1;
	//传递函数/递推公式
	int n_sum = Probability_Recursion(n - 1, sum - 1) + 
        		Probability_Recursion(n - 1, sum - 2) +
				Probability_Recursion(n - 1, sum - 3) + 
        		Probability_Recursion(n - 1, sum - 4) +
				Probability_Recursion(n - 1, sum - 5) + 
        		Probability_Recursion(n - 1, sum - 6);
	return n_sum;
}

/*
func：打印n个骰子和为s的所有可能
para：n表示骰子个数
return：无
*/
void Probability(int n)
{
	int maxNum = 6;
	int total = pow(maxNum,n);
	for (int sum = n; sum <= n*maxNum; sum++)
		cout << "f(" << n << "," << sum << ")=" << (float)Probability_Recursion(n, sum)/total << endl;
}

-----------------------------------------------------------------------------------------

//方法二 使用循环
void Probability_Loop(int n)
{
	if (n < 1)
		return;
	int maxNum = 6;
	//数组，下标表示点数之和，值为出现的次数
	vector<int> sum(n*maxNum+1);//最小1，最大数字n*maxNum
	//初始化，即一个骰子的情况 1~6各出现一次
	for (int i = 1; i <= maxNum; i++)
		sum[i] = 1;
	for (int i = 2; i <= n; i++)//从第二个骰子到第n个骰子
	{
		//对于i个骰子，可能的和为i~i*manNum，如三个骰子和为3~18
		for (int s = i*maxNum; s >= i; s--)
		{
			//反过来计算，因为新的sum[s]与之前的sum[s-1]等都有关
			int lasts1 = s - 1 > 0 ? sum[s - 1] : 0;
			int lasts2 = s - 2 > 0 ? sum[s - 2] : 0;
			int lasts3 = s - 3 > 0 ? sum[s - 3] : 0;
			int lasts4 = s - 4 > 0 ? sum[s - 4] : 0;
			int lasts5 = s - 5 > 0 ? sum[s - 5] : 0;
			int lasts6 = s - 6 > 0 ? sum[s - 6] : 0;//if (n < 1 || sum<n || sum>maxNum*n) return 0;		
			sum[s] = lasts1 + lasts2 + lasts3 + lasts4 + lasts5 + lasts6;
		}
		//第i个骰子计算完之后，sum[i]之前的数字都应该为0，因为和不可能为i-1
		sum[i - 1] = 0;//这一步很关键
	}
	//打印
	int total = pow(maxNum, n);
	for (int i = n; i <= maxNum*n; i++)
		cout << "f(" << n << "," << i << ")=" << (float)sum[i]/ total << endl;
}
```

**考点**

* 动态规划思想和循环，两种代码之间的关系
  * 递归思想简单，但速度慢。当本体n取11时，循环一瞬间完成，而递归需要秒级时间
* 数学建模的能力
  * 用什么数据结构
  * 分析模型中的内在规律



### 44.扑克牌的顺子

```汉语
题目：从扑克牌中随机抽五张牌，判断是不是一个顺子。2~10为数字本身，A为1，J为11，Q为12，K为13，而大小王可以看成任意数字。
```

**思路**

把抽到的五张牌的五个数字当作一个数组，即判断这个数组的数字是不是连续的。满足以下三点则为顺子

* 大小王为0，可以重复。
* 其他牌不能重复，重复则不可能为顺子。
* 牌中除王以外，最大值与最小值之差<5。（小于数组长度）

**code**

```c++
/*
func：判断5张扑克牌是否是顺子
para：输入的5张牌，牌面1-13，0（大小王）
return：是否是顺子
*/
/*
满足以下3个条件则为顺子
1.大小王为0，可以重复
2.其余牌不可重复
3.最大值与最小值之差<5
*/
bool IsContinuous(vector<int>& data)
{
	if (data.size() != 5)
		return false;
	vector<int> count(14,0);//记录每张牌的张数
	int max = -1;//这样给初值
	int min = 14;
	for (int i = 0; i < 5; i++)
	{
		count[data[i]]++;
		if (data[i] == 0) continue;//大小王直接跳过
		if (count[data[i]] > 1) return false;//其余牌如果多余1张，不可能为顺子
		if (data[i] > max)
			max = data[i];//更新最大值
		if (data[i] < min)
			min = data[i];//更新最小值
	}
	if ((max - min) < 5)
		return true;
	return false;
}
```

**考点**

* 在寻找数组中最大最小值时，应这样给初值

  ```c++
  int max=INT_MIN;(或者等于已知数组的最小值-1)
  int min=INT_MAX;(或者等于已知数组的最大值+1)
  if (data[i] > max)
      max = data[i];//更新最大值
  if (data[i] < min)
      min = data[i];//更新最小值
  ```

* 考查抽象建模能力。



### 45.圆圈中最后剩下的数字

```汉语
题目：0、1、2……n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈中删除第m个数字，求出这个圆圈里最后剩下的一个数字

//情景再现
/*
每年六一儿童节,牛客都会准备一些小礼物去看望孤儿院的小朋友,今年亦是如此。HF作为
牛客的资深元老,自然也准备了一些小游戏。其中,有个游戏是这样的:首先,让小朋友们围成
一个大圈。然后,他随机指定一个数m,让编号为0的小朋友开始报数。每次喊到m-1的那个小
朋友要出列唱首歌,然后可以在礼品箱中任意的挑选礼物,并且不再回到圈中,从他的下一个小
朋友开始,继续0...m-1报数....这样下去....直到剩下最后一个小朋友,可以不用表演,并且拿
到牛客名贵的“名侦探柯南”典藏版(名额有限哦!!^_^)。请你试着想下,哪个小朋友会得到这份礼
品呢？(注：小朋友的编号是从0到n-1)
*/
```

**思路一**

利用STL中list做环形链表，来模拟圆圈。

**code**

```c++
//list迭代器后移n次
void MoveIter(list<int>& data,list<int>::iterator& it,int n)
{
	while (n-- > 0)
	{
		it++;
		if (it == data.end())
			it = data.begin();			
	}
}
/*
func：从n个数中，每次删除第m个数，返回最后剩下的数字
para：n个数，第m个
return：最后剩下的数字
*/
int LastRemaining(int n, int m)
{
	if (n < 1 || m < 1)
		return -1;
	list<int> data;
	//list放入数据
	for (int i = 0; i < n; i++)
		data.push_back(i);
	auto it = data.begin();
	while (data.size() > 1)
	{
		MoveIter(data, it, m - 1);//m-1，后移m-1即为第m个
		it = data.erase(it);
		if (it == data.end())//如果删除了最后一个元素
			it = data.begin();
	}
	return *it;
}
```

**思路二**(很好的比较迭代和递归)

利用数学知识找映射关系，设**隐函数**f(n，m)为**在n个数字中每次删除第m个数字最后剩下的数字**

则有以下f(n，m)的公式如下，具体推导过程省略，不是太懂。
$$
f(n,m) = \begin{cases}  
0 & n=1  \\
(f(n-1,m)+m)mod(n) & n>1
\end{cases}
$$

```c++
//迭代版本
int LastRemaining1(int n, int m)
{
	if (n < 1 || m < 1)
		return -1;
	int last = 0;//i=1
	for (int i = 2; i <=n; i++)
		last = (last + m) % i;
	return last;
}
//递归版本
int LastRemaining2(int n, int m)
{
	if (n < 1 || m < 1)
		return -1;
	if (n == 1)
		return 0;
	return (LastRemaining2(n - 1, m) + m) % n;
}
```

**考点**

* **list**的运用，其中**删除一个元素**要注意的问题

* ```c++
  //测试删除元素后 指针指向的位置
  list<int> data{ 0,1,2,3,4 };
  auto it = data.begin();
  
  //1
  MoveIter(data, it, 4);
  cout << *it << endl;//*it=4
  it = data.erase(it);//it指向end()
  cout << *it;//所以这里访问不到的。
  
  //2
  MoveIter(data, it, 3);
  it = data.erase(data.begin(), it);//[0，3），删除了 0 1 2
  cout << *it << endl;//指向2后面的3，即指向最后一个被删除元素的后面一个元素
  ```

* 迭代和递归的比较

* 数学推导能力。

* **环形链表**，如果迭代器指向end，则将其指向begin，即可构成环形链表



## 6.5 发散思维能力

### 46.求1+2+……n

```汉语
题目：求1+2+……n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）
```

**思路一**

使用逻辑与&&的短路特性完成递归

**思路二**

使用虚函数特性，代替递归

**思路三**

使用构造函数特性，代替for、while循环

**思路四**

使用函数指针，代替递归

**思路五**

使用模板函数，代替递归

**code**

```c++
//递归求1+2+3+……n
int SumOf1toN(unsigned int n)
{
	/*
	1.常规
	if (n == 1||n==0)
		return n;
	  return n + SumOf1toN(n - 1);
	*/

	/*
	2.使用三目运算符
	return (n==1||n==0)?n: n + SumOf1toN(n - 1);
	*/

	/*
	3.使用逻辑与&&的短路特性
	【总结】条件1 && 条件2 条件1为假时不会执行条件2，直接返回0，条件1为真，执行条件2
		   条件1 || 条件2 条件1为真时不会执行条件2
	*/
	int sum = n;
	n && (sum += SumOf1toN(n - 1));//n=0什么也没做
	return sum;
}
//循环求
int SumOf1toN_1(unsigned int n)
{
	int sum = 0;
	for (int i = 1; i <= n; i++)
		sum += i;
	return sum;
}

//利用虚函数来代替递归
class A;
A* Array[2];//便于取不同的地址
class A
{
public:
	virtual int sum(unsigned int n)
	{
		return 0;
	}	
};
class B :public A
{
public:
	virtual int sum(unsigned int n)
	{
		return Array[!!n]->sum(n - 1) + n;
	}
};
int SumOf1toN_2(unsigned int n)
{
	A a;
	B b;
	Array[0] = &a;
	Array[1] = &b;          // 当n=0，!!n也为0，调用A的sum，
	return Array[1]->sum(n);// 当n >= 1，!!n为1，调用B的sum
}
//利用构造函数 代替 for while
//构造n次，相当于循环n次
class temp
{
private:
	int sum = 0;
	int n = 0 ;
public:
	temp() { n++; sum += n; };
	int GetSum() { return sum; };
};
//没有按照剑指offer上使用static成员
int SumOf1toN_3(unsigned int n)
{
	temp* a = new temp[n];
	int sum =a[0].GetSum();
	delete[]a;
	a = nullptr;
	return sum;
}
```

**考点**

* 思维发散能力
* 构造函数与循环、虚函数与递归
* 逻辑与&&的短路特性与if

* 本题构造函数解法中，没有使用类的静态成员变量和静态成员函数
  * 静态成员用**static**修饰
  * 静态成员变量隶属于**类所有**
  * 静态成员变量需要在类外单独分配空间，即**类外初始化**
  * 每一个**对象都可以访问**静态成员变量
  * 静态成员变量在程序内部位于**全局数据区**
  * 静态成员变量的生命期为**程序运行期**



### 47.不用加减乘除做加法

```汉语
题目：写一个函数，求两个整数之和，要求在函数体内不得使用+ - * /四则运算符号
```

**思路**

利用位运算，假设a=5，b=17，二进制表示a=101，b=10001

1. 两数做不算进位的相加(异或运算)
   * 101 和 10001 不进位加得 100101
   * 其实就是**异或运算**，即**a ^ b**
2. 计算两数进位
   * 101和10001只在个位有进位，得10
   * 其实就是**与运算**，**a&b<<1** (a&b=00001)
3. 如果进位不为零，则将异或运算结果（a ^ b）和进位（a&b<<1）再相加（重复1、2步骤，直到进位为0）

**code**

```c++
/*
func：不用加减乘除做加法运算
para：相加的两个数
return：和
*/
int Add(int num1, int num2)
{
	int sum_nocarry;//无进位计算和
	int carry;//进位
	do
	{
		sum_nocarry = num1^num2;//num1异或num2，相当于无进位相加
		carry = (num1&num2) << 1;//num1和num2相与，在左移一位，相当于求进位
		        //上面是& 而不是&&
		num1 = sum_nocarry;
		num2 = carry;
	} while (num2 != 0);
	return num1;
}
// &&和&   https://blog.csdn.net/violet_echo_0908/article/details/47395875

//上述循环使用递归实现
int Add1(int num1, int num2)
{
	if (num2 == 0)
		return num1;
	return Add(num1^num2, (num1&num2) << 1);
}
```

**考点**

* 两数进行**异或**，相当于**不进位相加**
* 两数先**与运算再左移一位**，相当于**求进位**
* &和&&的比较
  * &&是逻辑与运算符，||是逻辑或运算符，都是**逻辑运算符**，两边只能是**bool**类型
  * &与| 既可以进行**逻辑运算**，又可以进行**位运算**，两边既可以是**bool**类型，又可以是**数值**类型
  * if (A && B) 如果 A 为 false ，整个表达式就为 false，**不再计算 B** 的值了。（短路特性）
    if (A & B) 如果 A 为 false ，整个表达式就为 false，但**还要计算 B** 的值。
  * 逻辑（AND）： true && false ： false
    按位（AND）： 1001 0110 & 1111 1111 ： 1001 0110 （二进制位）

**拓展**

* 不用新的变量实现两数字交换

  ```c++
  //1.基于加减法
  void swap1(int& a, int& b)
  {
  	a = a + b;
  	b = a - b;
  	a = a - b;
  }
  //2.基于异或运算
  void swap2(int& a, int& b)
  {
  	a = a ^ b;
  	b = a ^ b; //(相当于a^b^b)
  	a = a ^ b;
  }
  ```

  



### 48.不能被继承的类 

```汉语
题目：用c++设计一个不能被继承的类
```

**思路一**

常规思路，将类的构造函数和析构函数设为私有函数。(注意，这个解法并**不是单例模式**)

缺点就是只能得到位于堆上的实例。（可以得到多个）

**code**

```c++
//常规解法 不能被继承的类
class SealedClass
{
private:
	SealedClass() { cout << "constructor"<<endl; }
	
	~SealedClass() { cout << "constructor" << endl; }

public:
	static SealedClass* GetInstance()
	{
		return new SealedClass();
	}
	static void DeteleInstance(SealedClass* pIntance)
	{
		delete pIntance;
	}
};

//测试上述代码是否是 单例模式
void test()
{
	SealedClass* p1 = SealedClass::GetInstance();
	SealedClass* p2 = SealedClass::GetInstance();
}//经过测试 输出constructor constructor 被构造了两次，肯定不是单例模式。
```

**思路二**

新奇的解法，利用虚继承,下列代码中，makesealed2这个类就是不能被继承的类，可以在堆和栈中的到实例，类的使用和其他类一样，只是不能被继承。

**code**

```c++
template <class T>
class MakeSealed
{
	friend T;
private:
	MakeSealed() { cout << "MakeSealed constructor" << endl; }
	~MakeSealed() {}
};
class MakeSealed2:virtual public MakeSealed<MakeSealed2>
{
public:
	MakeSealed2() { cout << "MakeSealed2 constructor" << endl; }
	~MakeSealed2() {}
};
```

**考点**

* static成员函数的使用
* 模板类的写法，友元等

**拓展**

**单例模式**

```c++
//懒汉式的单例模式（线程不安全且没有delete造成内存泄漏）
class single
{
private:
	single() { cout << "constructor called" << endl; }
	single(single&) = delete;//禁止拷贝构造
	single operator=(const single&) = delete;//禁止拷贝复制
	static single* m_instance;//一定要是static，说明被类所独有
public:
	static single* GetInstance()//必须也是静态的，类所有，不是对象所有
	{
		if (!m_instance)
			m_instance = new single();//returun new single();是错的！！！
		return m_instance;//单例模式 只有m_instance这一个实例
	}
	~single() { cout << "distructor called" << endl; }
};
single* single::m_instance = nullptr;

//测试上述单例模式
void test1()
{
	single* p1 = single::GetInstance();
	single* p2 = single::GetInstance();
}//只调用一次构造，但是没有析构
```

* 更多单例模式：  https://www.cnblogs.com/sunchaothu/p/10389842.html





# 第7章 面试案例

### 49.把字符串转换成整数

```汉语
题目：把字符串转换为整数，不使用atoi等函数
```

```c++
/*
func:不适用库函数如atoi,实现字符串转为整数
para:输入的字符串
return:转换成的整数
invalid input:nulptr、""、'0'~'9'以及‘+’‘-’之外的字符、nullptr
*/
enum status {kValid=1,kInvalid=0};
int g_status;
long long Str2Int(string str)
{
	g_status = kInvalid;//设置输入是否有效
	long long num = 0;//返回的结果
	if (str.empty())
		return num;
	int i = (str[0] == '+' || str[0] == '-') ? 1 : 0;//如第一位是加减号，从第二位开始

	for (i; i < str.size(); i++)
	{
		if (str[i] > '9' || str[i] < '0')
		{
			num = 0;
			return num;
		}		
		num = num * 10 + str[i] - '0';//char转int的核心
        
		//0x表示每一位都是16进制，一个16进制数用四个比特表示16*4=64位，
		//因此0x后面应该有16位；若以32位为限，则应改为0x7FFFFFFF
		//long long 范围 (signed int)0x8000000000000000~0x7FFFFFFFFFFFFFFF
		//int 范围-0x7FFFFFFF-1~0x7FFFFFFF    0~ffffffff（无符号）
		if (num > 0x7FFFFFFFFFFFFFFF || 
			num < -(0x7FFFFFFFFFFFFFFF-1))
		{
			num = 0;
			return num;
		}

	}
	//能够执行完for说明都有效
	g_status = kValid;	
	return (str[0]=='-') ? 0-num : num;
}
```

**考点**

* long long 范围 (signed int)0x8000000000000000~0x7FFFFFFFFFFFFFFF（64位）

* int 范围-0x7FFFFFFF-1~0x7FFFFFFF    0~ffffffff（无符号）

* 为了防止超出int的表示范围，可以使用long long

* **char与int互转：**

  * ```c++
    //char->int
    char char_a='a';
    int int_a=char_a -'0';
    //int->char
    int int_a = 2;
    char char_a = int_a +'0';
    ```




### 50.树中两个结点的最低公共祖先

```汉语
题目：找到树中两个结点的最低公共祖先
```

**题目一：是二叉搜索树**

* 特点：就是树是排序过的，位于左子树上的结点都比父结点小，而位于右子树的结点都比父结点大。

* 步骤：

  * 我们只需要从根结点开始和两个结点进行比较。

  * 如果当前结点的值比两个结点都大，则最低的共同父结点一定在当前结点的左子树中。

  * 如果当前结点的值比两个结点都小，则最低的共同父结点一定在当前结点的右子树中。

  * 最先找到的结点的值位于输入的两结点之间，则是最低的公共祖先

* 不用递归来实现

**题目二：若为普通的树，甚至不是二叉树**

* 方法：借用容器，找到根节点到某一结点的路径，再从路径中得到最低公共结点，时间及空间复杂度O(N)

  ​			其实还有另外一种方法，不是需要双重递归，要重复遍历结点，时间复杂度为O(N^2)

* 步骤
  * 分别得到根节点到两个输入结点的路径
  * 得到的路径是从根节点开始的
  * 比较两个容器，找到最后一个相同的元素，即为最低公共祖先

**code**

```c++
/*在一个普通的树中找到两个结点的最低公共祖先*/

/*
func：在树种找到根结点到某一结点的路径，并将路径存放在容器中
para：root：树的根节点，node：要找的某一结点,path：存放路劲的容器
return：该结点的子树是否存在node结点
*/
bool GetTreeNodePath(TreeNode* root, TreeNode* node, vector<TreeNode*>& path)
{
    if (!root)
		return false;
    
    path.push_back(root);//先放入
    
	if (root == node)
		return true;
	
	bool hasNode = false;
	if (root->left)
		hasNode = GetTreeNodePath(root->left,node,path);
	if (!hasNode&&root->right)
		hasNode = GetTreeNodePath(root->right,node,path);
	if (!hasNode)
		path.pop_back();//如果不包含就弹出
	return hasNode;
}
/*
func：从两个容器中找到最后一个公共元素
para：两个容器
return：公共结点
*/
TreeNode* findAncestor(vector<TreeNode*>& path1, vector<TreeNode*>& path2)
{
    if (path1.empty()|| path2.empty())
        return nullptr;
    TreeNode* ancestor;
    int i=0;
    while(i<path1.size()&&i<path2.size()&&path1[i]==path2[i])
    {
        ancestor=path1[i];
        i++;
    }
    return ancestor;
}
/*
func:在一颗普通的树中，找到两个结点的最低公共祖先
para:root:根节点，node1：结点1，node2：结点2
return:最低公共祖先结点
*/
TreeNode* GetLastCommonParent(TreeNode* root, TreeNode* node1, TreeNode* node2)
{
	if (!root || !node1 || !node2)
		return nullptr;
	vector<TreeNode*> path1;
	vector<TreeNode*> path2;
	GetTreeNodePath(root, node1, path1);
	GetTreeNodePath(root, node2, path2);
	return findAncestor(path1, path2);
}
```

可以参考https://blog.csdn.net/hackbuteer1/article/details/6686858

**考点**

* 二叉树的递归
* 像上面GetTreeNodePath函数我们并不需要返回值，可以不用返回值接收。

**补充（重要）**

关于二叉树的几个问题

**1.路径问题**

* 输出从根节点开始，和为某一个值的所有路径-------**题25**
  * 需要将所有节点都遍历完。
  * 因为要回溯，在满足条件了，就要将结果先保存起来。
* 输出从根节点开始，到指定节点的路径------**本题**
  * 不用遍历所有节点，满足条件就可以了
  * 因此不是无脑递归，要在不满足条件的情况下递归和回溯。
* 输出从根节点开始，最大（小）深度的路径

**2.重建问题**（在leetcode二叉树专题中）

* 普通树的重建
  * 前序、中序、后序、层序遍历数组，如何重建
* 完全二叉树的重建
  * 前序、中序、后序、层序遍历数组，如何重建

**3.序列化和反序列化问题**











































