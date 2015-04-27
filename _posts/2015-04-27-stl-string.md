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
  size_t capacity_;
  char* data_;
};
```

但是标准库并不是这样设计的，它有两个不同点
* string只有`char* data`一个成员，其余的信息放在data前面，组成头部。于是string变成了一个非常轻量的类。
* 支持Copy On Write机制，复制会延迟到写真正发生时。这需要往头部添加一个原子引用计数。

于是string的头部结构体如下

```c++
struct _Rep_Base {
  size_type _M_length;
  size_type _M_capacity;
  _Atomic_word _M_refcount;
};
```

## 把对象分配器暴露给类型合理吗？

```c++
template<typename _CharT, typename _Traits, typename _Alloc>
class basic_string;
```

* _Rep::_M_is_leaked()              // 通过头部判断该字符串是否已经无用
* _Rep::_M_is_shared()              // 通过头部判断该字符串是否被多人共享 
* _Rep::_M_set_leaked()             // 设置无用标识
* _Rep::_M_set_sharable()           // 标明该字符串正常可共享
* _Rep::_M_set_length_and_sharable(size_type size)  // 空间已分配好，设置相关数据

STL声称容器和内存分配是分离的，但是`Alloc`都成了`basic_string`类型的一部分，这哪是分离啊，明明是合体嘛。
