# 4-1,张量的结构操作

张量的操作主要包括张量的结构操作和张量的数学运算。

张量结构操作诸如：张量创建，索引切片，维度变换，合并分割。

张量数学运算主要有：标量运算，向量运算，矩阵运算。另外我们会介绍张量运算的广播机制。

本篇我们介绍张量的结构操作。


### 一，创建张量


张量创建的许多方法和numpy中创建array的方法很像。

```python
import tensorflow as tf
import numpy as np 
```

```python
a = tf.constant([1,2,3],dtype = tf.float32)
tf.print(a)
```

```
[1 2 3]
```

```python
b = tf.range(1,10,delta = 2)
tf.print(b)
```

```
[1 3 5 7 9]
```

```python
c = tf.linspace(0.0,2*3.14,100)
tf.print(c)
```

```
[0 0.0634343475 0.126868695 ... 6.15313148 6.21656609 6.28]
```

```python
d = tf.zeros([3,3])
tf.print(d)
```

```
[[0 0 0]
 [0 0 0]
 [0 0 0]]
```

```python
a = tf.ones([3,3])
b = tf.zeros_like(a,dtype= tf.float32)
tf.print(a)
tf.print(b)
```

```
[[1 1 1]
 [1 1 1]
 [1 1 1]]
[[0 0 0]
 [0 0 0]
 [0 0 0]]
```

```python
b = tf.fill([3,2],5)
tf.print(b)
```

```
[[5 5]
 [5 5]
 [5 5]]
```

```python
#均匀分布随机
tf.random.set_seed(1.0)
a = tf.random.uniform([5],minval=0,maxval=10)
tf.print(a)
```

```
[1.65130854 9.01481247 6.30974197 4.34546089 2.9193902]
```

```python
#正态分布随机
b = tf.random.normal([3,3],mean=0.0,stddev=1.0)
tf.print(b)
```

```
[[0.403087884 -1.0880208 -0.0630953535]
 [1.33655667 0.711760104 -0.489286453]
 [-0.764221311 -1.03724861 -1.25193381]]
```

```python
#正态分布随机，剔除2倍方差以外数据重新生成
c = tf.random.truncated_normal((5,5), mean=0.0, stddev=1.0, dtype=tf.float32)
tf.print(c)
```

```
[[-0.457012236 -0.406867266 0.728577733 -0.892977774 -0.369404584]
 [0.323488563 1.19383323 0.888299048 1.25985599 -1.95951891]
 [-0.202244401 0.294496894 -0.468728036 1.29494202 1.48142183]
 [0.0810953453 1.63843894 0.556645 0.977199793 -1.17777884]
 [1.67368948 0.0647980496 -0.705142677 -0.281972528 0.126546144]]
```

```python
# 特殊矩阵
I = tf.eye(3,3) #单位矩阵
tf.print(I)
tf.print(" ")
t = tf.linalg.diag([1,2,3]) #对角阵
tf.print(t)
```

```
[[1 0 0]
 [0 1 0]
 [0 0 1]]
 
[[1 0 0]
 [0 2 0]
 [0 0 3]]
```

```python

```

### 二 ，索引切片


张量的索引切片方式和numpy几乎是一样的。切片时支持缺省参数和省略号。

对于tf.Variable,可以通过索引和切片对部分元素进行修改。

对于提取张量的连续子区域，也可以使用tf.slice.

此外，对于不规则的切片提取,可以使用tf.gather,tf.gather_nd,tf.boolean_mask。

tf.boolean_mask功能最为强大，它可以实现tf.gather,tf.gather_nd的功能，并且tf.boolean_mask还可以实现布尔索引。

如果要通过修改张量的某些元素得到新的张量，可以使用tf.where，tf.scatter_nd。

```python
tf.random.set_seed(3)
t = tf.random.uniform([5,5],minval=0,maxval=10,dtype=tf.int32)
tf.print(t)
```

```
[[4 7 4 2 9]
 [9 1 2 4 7]
 [7 2 7 4 0]
 [9 6 9 7 2]
 [3 7 0 0 3]]
```

```python
#第0行
tf.print(t[0])
```

```
[4 7 4 2 9]
```

```python
#倒数第一行
tf.print(t[-1])
```

```
[3 7 0 0 3]
```

```python
#第1行第3列
tf.print(t[1,3])
tf.print(t[1][3])
```

```
4
4
```

```python
#第1行至第3行
tf.print(t[1:4,:])
tf.print(tf.slice(t,[1,0],[3,5])) #tf.slice(input,begin_vector,size_vector)
```

```
[[9 1 2 4 7]
 [7 2 7 4 0]
 [9 6 9 7 2]]
[[9 1 2 4 7]
 [7 2 7 4 0]
 [9 6 9 7 2]]
```

```python
#第1行至最后一行，第0列到最后一列每隔两列取一列
tf.print(t[1:4,:4:2])
```

```
[[9 2]
 [7 7]
 [9 9]]
```

```python
#对变量来说，还可以使用索引和切片修改部分元素
x = tf.Variable([[1,2],[3,4]],dtype = tf.float32)
x[1,:].assign(tf.constant([0.0,0.0]))
tf.print(x)
```

```
[[1 2]
 [0 0]]
```

```python
a = tf.random.uniform([3,3,3],minval=0,maxval=10,dtype=tf.int32)
tf.print(a)
```

```
[[[7 3 9]
  [9 0 7]
  [9 6 7]]

 [[1 3 3]
  [0 8 1]
  [3 1 0]]

 [[4 0 6]
  [6 2 2]
  [7 9 5]]]
```

```python
#省略号可以表示多个冒号
tf.print(a[...,1])
```

```
[[3 0 6]
 [3 8 1]
 [0 2 9]]
```


以上切片方式相对规则，对于不规则的切片提取,可以使用tf.gather,tf.gather_nd,tf.boolean_mask。

考虑班级成绩册的例子，有4个班级，每个班级10个学生，每个学生7门科目成绩。可以用一个4*10*7的张量来表示。

```python
scores = tf.random.uniform((4,10,7),minval=0,maxval=100,dtype=tf.int32)
tf.print(scores)
```

```
[[[52 82 66 ... 17 86 14]
  [8 36 94 ... 13 78 41]
  [77 53 51 ... 22 91 56]
  ...
  [11 19 26 ... 89 86 68]
  [60 72 0 ... 11 26 15]
  [24 99 38 ... 97 44 74]]

 [[79 73 73 ... 35 3 81]
  [83 36 31 ... 75 38 85]
  [54 26 67 ... 60 68 98]
  ...
  [20 5 18 ... 32 45 3]
  [72 52 81 ... 88 41 20]
  [0 21 89 ... 53 10 90]]

 [[52 80 22 ... 29 25 60]
  [78 71 54 ... 43 98 81]
  [21 66 53 ... 97 75 77]
  ...
  [6 74 3 ... 53 65 43]
  [98 36 72 ... 33 36 81]
  [61 78 70 ... 7 59 21]]

 [[56 57 45 ... 23 15 3]
  [35 8 82 ... 11 59 97]
  [44 6 99 ... 81 60 27]
  ...
  [76 26 35 ... 51 8 17]
  [33 52 53 ... 78 37 31]
  [71 27 44 ... 0 52 16]]]
```

```python
#抽取每个班级第0个学生，第5个学生，第9个学生的全部成绩
p = tf.gather(scores,[0,5,9],axis=1)
tf.print(p)
```

```
[[[52 82 66 ... 17 86 14]
  [24 80 70 ... 72 63 96]
  [24 99 38 ... 97 44 74]]

 [[79 73 73 ... 35 3 81]
  [46 10 94 ... 23 18 92]
  [0 21 89 ... 53 10 90]]

 [[52 80 22 ... 29 25 60]
  [19 12 23 ... 87 86 25]
  [61 78 70 ... 7 59 21]]

 [[56 57 45 ... 23 15 3]
  [6 41 79 ... 97 43 13]
  [71 27 44 ... 0 52 16]]]
```

```python
#抽取每个班级第0个学生，第5个学生，第9个学生的第1门课程，第3门课程，第6门课程成绩
q = tf.gather(tf.gather(scores,[0,5,9],axis=1),[1,3,6],axis=2)
tf.print(q)
```

```
[[[82 55 14]
  [80 46 96]
  [99 58 74]]

 [[73 48 81]
  [10 38 92]
  [21 86 90]]

 [[80 57 60]
  [12 34 25]
  [78 71 21]]

 [[57 75 3]
  [41 47 13]
  [27 96 16]]]
```

```python
# 抽取第0个班级第0个学生，第2个班级的第4个学生，第3个班级的第6个学生的全部成绩
#indices的长度为采样样本的个数，每个元素为采样位置的坐标
s = tf.gather_nd(scores,indices = [(0,0),(2,4),(3,6)])
s
```

```
<tf.Tensor: shape=(3, 7), dtype=int32, numpy=
array([[52, 82, 66, 55, 17, 86, 14],
       [99, 94, 46, 70,  1, 63, 41],
       [46, 83, 70, 80, 90, 85, 17]], dtype=int32)>
```


以上tf.gather和tf.gather_nd的功能也可以用tf.boolean_mask来实现。

```python
#抽取每个班级第0个学生，第5个学生，第9个学生的全部成绩
p = tf.boolean_mask(scores,[True,False,False,False,False,
                            True,False,False,False,True],axis=1)
tf.print(p)
```

```
[[[52 82 66 ... 17 86 14]
  [24 80 70 ... 72 63 96]
  [24 99 38 ... 97 44 74]]

 [[79 73 73 ... 35 3 81]
  [46 10 94 ... 23 18 92]
  [0 21 89 ... 53 10 90]]

 [[52 80 22 ... 29 25 60]
  [19 12 23 ... 87 86 25]
  [61 78 70 ... 7 59 21]]

 [[56 57 45 ... 23 15 3]
  [6 41 79 ... 97 43 13]
  [71 27 44 ... 0 52 16]]]
```

```python
#抽取第0个班级第0个学生，第2个班级的第4个学生，第3个班级的第6个学生的全部成绩
s = tf.boolean_mask(scores,
    [[True,False,False,False,False,False,False,False,False,False],
     [False,False,False,False,False,False,False,False,False,False],
     [False,False,False,False,True,False,False,False,False,False],
     [False,False,False,False,False,False,True,False,False,False]])
tf.print(s)
```

```
[[52 82 66 ... 17 86 14]
 [99 94 46 ... 1 63 41]
 [46 83 70 ... 90 85 17]]
```

```python
#利用tf.boolean_mask可以实现布尔索引

#找到矩阵中小于0的元素
c = tf.constant([[-1,1,-1],[2,2,-2],[3,-3,3]],dtype=tf.float32)
tf.print(c,"\n")

tf.print(tf.boolean_mask(c,c<0),"\n") 
tf.print(c[c<0]) #布尔索引，为boolean_mask的语法糖形式
```

```
[[-1 1 -1]
 [2 2 -2]
 [3 -3 3]] 

[-1 -1 -2 -3] 

[-1 -1 -2 -3]
```

```python

```

以上这些方法仅能提取张量的部分元素值，但不能更改张量的部分元素值得到新的张量。

如果要通过修改张量的部分元素值得到新的张量，可以使用tf.where和tf.scatter_nd。

tf.where可以理解为if的张量版本，此外它还可以用于找到满足条件的所有元素的位置坐标。

tf.scatter_nd的作用和tf.gather_nd有些相反，tf.gather_nd用于收集张量的给定位置的元素，

而tf.scatter_nd可以将某些值插入到一个给定shape的全0的张量的指定位置处。

```python
#找到张量中小于0的元素,将其换成np.nan得到新的张量
#tf.where和np.where作用类似，可以理解为if的张量版本

c = tf.constant([[-1,1,-1],[2,2,-2],[3,-3,3]],dtype=tf.float32)
d = tf.where(c<0,tf.fill(c.shape,np.nan),c) 
d
```

```
<tf.Tensor: shape=(3, 3), dtype=float32, numpy=
array([[nan,  1., nan],
       [ 2.,  2., nan],
       [ 3., nan,  3.]], dtype=float32)>
```

```python

```

```python
#如果where只有一个参数，将返回所有满足条件的位置坐标
indices = tf.where(c<0)
indices
```

```
<tf.Tensor: shape=(4, 2), dtype=int64, numpy=
array([[0, 0],
       [0, 2],
       [1, 2],
       [2, 1]])>
```

```python
#将张量的第[0,0]和[2,1]两个位置元素替换为0得到新的张量
d = c - tf.scatter_nd([[0,0],[2,1]],[c[0,0],c[2,1]],c.shape)
d
```

```
<tf.Tensor: shape=(3, 3), dtype=float32, numpy=
array([[ 0.,  1., -1.],
       [ 2.,  2., -2.],
       [ 3.,  0.,  3.]], dtype=float32)>

```

```python
#scatter_nd的作用和gather_nd有些相反
#可以将某些值插入到一个给定shape的全0的张量的指定位置处。
indices = tf.where(c<0)
tf.scatter_nd(indices,tf.gather_nd(c,indices),c.shape)
```

```
<tf.Tensor: shape=(3, 3), dtype=float32, numpy=
array([[-1.,  0., -1.],
       [ 0.,  0., -2.],
       [ 0., -3.,  0.]], dtype=float32)>
```

```python

```

### 三，维度变换


维度变换相关函数主要有 tf.reshape, tf.squeeze, tf.expand_dims, tf.transpose.

tf.reshape 可以改变张量的形状。

tf.squeeze 可以减少维度。

tf.expand_dims 可以增加维度。

tf.transpose 可以交换维度。



tf.reshape可以改变张量的形状，但是其本质上不会改变张量元素的存储顺序，所以，该操作实际上非常迅速，并且是可逆的。

```python
a = tf.random.uniform(shape=[1,3,3,2],
                      minval=0,maxval=255,dtype=tf.int32)
tf.print(a.shape)
tf.print(a)
```

```
TensorShape([1, 3, 3, 2])
[[[[135 178]
   [26 116]
   [29 224]]

  [[179 219]
   [153 209]
   [111 215]]

  [[39 7]
   [138 129]
   [59 205]]]]
```

```python
# 改成 （3,6）形状的张量
b = tf.reshape(a,[3,6])
tf.print(b.shape)
tf.print(b)
```

```
TensorShape([3, 6])
[[135 178 26 116 29 224]
 [179 219 153 209 111 215]
 [39 7 138 129 59 205]]
```




```python
# 改回成 [1,3,3,2] 形状的张量
c = tf.reshape(b,[1,3,3,2])
tf.print(c)
```

```
[[[[135 178]
   [26 116]
   [29 224]]

  [[179 219]
   [153 209]
   [111 215]]

  [[39 7]
   [138 129]
   [59 205]]]]
```

```python

```

如果张量在某个维度上只有一个元素，利用tf.squeeze可以消除这个维度。

和tf.reshape相似，它本质上不会改变张量元素的存储顺序。

张量的各个元素在内存中是线性存储的，其一般规律是，同一层级中的相邻元素的物理地址也相邻。

```python
s = tf.squeeze(a)
tf.print(s.shape)
tf.print(s)
```

```
TensorShape([3, 3, 2])
[[[135 178]
  [26 116]
  [29 224]]

 [[179 219]
  [153 209]
  [111 215]]

 [[39 7]
  [138 129]
  [59 205]]]
```

```python
d = tf.expand_dims(s,axis=0) #在第0维插入长度为1的一个维度
d
```

```
<tf.Tensor: shape=(1, 3, 3, 2), dtype=int32, numpy=
array([[[[135, 178],
         [ 26, 116],
         [ 29, 224]],

        [[179, 219],
         [153, 209],
         [111, 215]],

        [[ 39,   7],
         [138, 129],
         [ 59, 205]]]], dtype=int32)>
```


tf.transpose可以交换张量的维度，与tf.reshape不同，它会改变张量元素的存储顺序。

tf.transpose常用于图片存储格式的变换上。

```python
# Batch,Height,Width,Channel
a = tf.random.uniform(shape=[100,600,600,4],minval=0,maxval=255,dtype=tf.int32)
tf.print(a.shape)

# 转换成 Channel,Height,Width,Batch
s= tf.transpose(a,perm=[3,1,2,0])
tf.print(s.shape)
```

```
TensorShape([100, 600, 600, 4])
TensorShape([4, 600, 600, 100])

```

```python

```

### 四，合并分割


和numpy类似，可以用tf.concat和tf.stack方法对多个张量进行合并，可以用tf.split方法把一个张量分割成多个张量。

tf.concat和tf.stack有略微的区别，tf.concat是连接，不会增加维度，而tf.stack是堆叠，会增加维度。

```python
a = tf.constant([[1.0,2.0],[3.0,4.0]])
b = tf.constant([[5.0,6.0],[7.0,8.0]])
c = tf.constant([[9.0,10.0],[11.0,12.0]])

tf.concat([a,b,c],axis = 0)
```

```
<tf.Tensor: shape=(6, 2), dtype=float32, numpy=
array([[ 1.,  2.],
       [ 3.,  4.],
       [ 5.,  6.],
       [ 7.,  8.],
       [ 9., 10.],
       [11., 12.]], dtype=float32)>
```

```python
tf.concat([a,b,c],axis = 1)
```

```
<tf.Tensor: shape=(2, 6), dtype=float32, numpy=
array([[ 1.,  2.,  5.,  6.,  9., 10.],
       [ 3.,  4.,  7.,  8., 11., 12.]], dtype=float32)>
```

```python
tf.stack([a,b,c])
```

```
<tf.Tensor: shape=(3, 2, 2), dtype=float32, numpy=
array([[[ 1.,  2.],
        [ 3.,  4.]],

       [[ 5.,  6.],
        [ 7.,  8.]],

       [[ 9., 10.],
        [11., 12.]]], dtype=float32)>
```

```python
tf.stack([a,b,c],axis=1)
```

```
<tf.Tensor: shape=(2, 3, 2), dtype=float32, numpy=
array([[[ 1.,  2.],
        [ 5.,  6.],
        [ 9., 10.]],

       [[ 3.,  4.],
        [ 7.,  8.],
        [11., 12.]]], dtype=float32)>
```

```python
a = tf.constant([[1.0,2.0],[3.0,4.0]])
b = tf.constant([[5.0,6.0],[7.0,8.0]])
c = tf.constant([[9.0,10.0],[11.0,12.0]])

c = tf.concat([a,b,c],axis = 0)
```

tf.split是tf.concat的逆运算，可以指定分割份数平均分割，也可以通过指定每份的记录数量进行分割。

```python
#tf.split(value,num_or_size_splits,axis)
tf.split(c,3,axis = 0)  #指定分割份数，平均分割
```

```
[<tf.Tensor: shape=(2, 2), dtype=float32, numpy=
 array([[1., 2.],
        [3., 4.]], dtype=float32)>,
 <tf.Tensor: shape=(2, 2), dtype=float32, numpy=
 array([[5., 6.],
        [7., 8.]], dtype=float32)>,
 <tf.Tensor: shape=(2, 2), dtype=float32, numpy=
 array([[ 9., 10.],
        [11., 12.]], dtype=float32)>]
```

```python
tf.split(c,[2,2,2],axis = 0) #指定每份的记录数量
```

```
[<tf.Tensor: shape=(2, 2), dtype=float32, numpy=
 array([[1., 2.],
        [3., 4.]], dtype=float32)>,
 <tf.Tensor: shape=(2, 2), dtype=float32, numpy=
 array([[5., 6.],
        [7., 8.]], dtype=float32)>,
 <tf.Tensor: shape=(2, 2), dtype=float32, numpy=
 array([[ 9., 10.],
        [11., 12.]], dtype=float32)>]
```

```python

```

如果对本书内容理解上有需要进一步和作者交流的地方，欢迎在公众号"Python与算法之美"下留言。作者时间和精力有限，会酌情予以回复。

![image.png](./data/Python与算法之美logo.jpg)
