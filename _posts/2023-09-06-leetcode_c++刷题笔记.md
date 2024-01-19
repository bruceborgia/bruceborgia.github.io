---
layout: post
title: Leetcode_C++刷题笔记
tags: [leetcode]
data: 2023-09-06 14:26 +0800
---
### 常规

1.取模操作

求模：规定"a MOD b"的b不能为负数

```cpp
int a = 8;
int b = 3;
a % b; // = 2 当a > b时，不断从a中减去b，直到出现了一个小于b的非负数。
int a = 3;
int b = 8;
a % b;// = 3  当a < b, 且 a > 0时，结果为a。 
int a = -3;
int b = 8;
a % b; // = 5 当a < b, 且 a < 0时，则b不断的加到a上，直到结果是一个小于b的非负数为止。
```





### map相关操作

##### map查找key

使用迭代器进行查找

```c++
map<char, int> hashmap;
hashmap["s"] = 1;
map<char, int>::iterator iter = hashmap.begin();
iter = hashmap.find(s[i]);
if (iter == hashmap.end()) {  // not find
	cout<< "未找到";
}else{
    cout<<"已找到";
}
```



### string 字符串操作

###### string字符串拼接

```c++
string s = "hello";
string s1 = "world";
string s2 = s + s1;
// s2即为 helloworld
```

string 字符串查找和删除

find,rfind,erase

```c++
string s = "LVIII";
int index  = s.rfind("V");
//int index  = s.find("V");
//rfind函数，从右向左查找，find函数从左向右查找
if(index != string::npos){
	cout << "V 的index为：" << index;
    s_size = s.size();
    s.erase(index,s_size);
    // 删除从index 到 s_size的字符，如果只有一个参数则删除前N个字符
    
}else{
	cout<<"not find";
}
```

### 数据类型转换

```cpp
long long result = static_cast<long long>(52936) * 40570;
```

`static_cast<long long>`是一个类型转换操作，它会将括号内的值转换为`long long`类型

# 数据结构

### 堆

从vector中初始化堆

```cpp
vector<int> nums = {1,10,3,3,3};
priority_queuq<int> q(nums.begin(), nums.end());
// 原地堆化
//priority_queue<int> pq(less<int>(), move(nums));
//取出最大值
int x = q.top();
q.pop();
x = x/3.0;
q.push(x);
```

### 哈希表

```cpp
str:unordered_map<int, int> mymap;
for(int i = 0; i < 10; i++){
	mpmap[i] = i;
}
//查找和删除
//查找方式1
if(mymap.find(5) != mymap.end()){
	// key 存在map中, 删除
	mymap.erase(5);
}
// 查找方式2
if(mymap.count(6) > 0){
	mymap.erase(6);
}
// 遍历
for(const auto &pair : mymap){
	// 访问键值对
	std::cout << pait.first << pair.second;
}
for(auto it = mymap.begin(); it != mymap.end(); it++){
	std::cout << it->first << it->second;
}
```

# vector 求和

```cpp
#include <iostream>  
#include <vector>  
#include <numeric>  
  
int main() {  
    std::vector<int> nums = {1, 2, 3, 4, 5};  
    int sum = std::accumulate(nums.begin(), nums.end(), 0);  
    std::cout << "The sum of the numbers is: " << sum << std::endl;  
    return 0;  
}
```

使用`std::accumulate`函数来计算`nums`中所有元素的和。该函数的第一个参数是`nums`的起始迭代器，第二个参数是`nums`的结束迭代器，第三个参数是累加的初始值
