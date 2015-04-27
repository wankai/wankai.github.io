## 为什么要有string对象？

传统的C字符串无法动态分配，而字符串天然是动态的，所以必须要有string对象

## 如果是你，怎么设计string类？

可能如下

```c++
class string {
 public:
  // 省略
 private:
  size_t length_;
  size_t capcity_;
  char* data_;
};
```

但是标准库并不是这样设计的，它有两个不同点
* string只有`char* data`一个成员，其余的信息放在字符串指针前面，组成头部。
