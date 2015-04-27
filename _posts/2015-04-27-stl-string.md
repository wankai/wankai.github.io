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
