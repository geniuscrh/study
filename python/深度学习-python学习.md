# 环境配置

## Anaconda安装

说明：自动安装Python3及jupyter notebook

下载镜像：<https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/>

### 修改conda镜像途径



## 自动补全

**1、安装nbextensions**

```
#使用清华镜像安装
pip install jupyter_contrib_nbextensions -i https://pypi.tuna.tsinghua.edu.cn/simple some-package

jupyter contrib nbextension install --user
```

**2、安装nbextensions_configurator**

```
#使用清华镜像安装
pip install jupyter_nbextensions_configurator -i https://pypi.tuna.tsinghua.edu.cn/simple some-package

jupyter nbextensions_configurator enable --user
```

**3、重启jupyter noteboook，点击Nbextensions。**

**4、勾选hinterland**



## 安装TensorFlow

**创建与启动名为tensorflow的python环境。anaconda默认一个名为base的python环境。**

```python
#python3.7.0,最后成功!
conda create --name tensorflow python=3.7.0
#激活环境
activate tensorflow
```

**通过conda安装**

```python
conda install tensorflow
```



## YellowBrick可视化

### 安装

```python
pip install yellowbrick
```

### 中文无法显示

```python
import matplotlib as plt
plt.rcParams['font.sans-serif']=['SimHei']
```

### 学习网址

 https://www.jianshu.com/p/a76f6d9e0dc3 





















# Python

## 常用

### matplotlib：图像操作

```python
import matplotlib.pyplot as plt # plt 用于显示图片
import matplotlib.image as mpimg # mpimg 用于读取图片
plt.imshow(data)

my_image1 = "5.png"                                            #定义图片名称
fileName1 = "images/fingers/" + my_image1                      #图片地址
image1 = mpimg.imread(fileName1)                               #读取图片
plt.imshow(image1)                                             #显示图片
my_image1 = image1.reshape(1,64 * 64 * 3).T                    #重构图片
my_image_prediction = tf_utils.predict(my_image1, parameters)  #开始预测
print("预测结果: y = " + str(np.squeeze(my_image_prediction)))

```

### h5py

```python
train_dataset = h5py.File('datasets/train_catvnoncat.h5', "r")
train_set_x_orig = np.array(train_dataset["train_set_x"][:]) # your train set features
```



### pandas

```python
#数据分析
info(),describe(),head()

#数据统计
mean：平均值    X.列名.unique()/value_counts():内容统计

#数据操作
X_train.drop("Cabin",axis=1,inplace=True)   #删除
pandas.get_dummies(X_train.Embarked)   		#one-hot
pd.concat([数据,数据],axis=1)		#链接数据，axis=1列操作
    
#填空Na数据:fillna()
X_train["Age"].fillna(X_train["Age"].mean(),inplace=True)  #，inplace是否更新源数据

#逻辑：loc["条件","列名"]
X_train.loc[X_train['Sex']=='female','Sex']=0

#删除筛选
df=df[ ~ df['站点类别'].str.contains('核心站点')]

#关联vlookup
  result = pd.merge(c_df,zd_df.loc[:,['所属站点','站点CUID']],how='left',on = '所属站点')

#列名,重命名
 zyd_df.rename(columns={"资源CUID":"机房/资源点CUID"},inplace=True)
    
    
```



### numpy

```python
#one-hot
Y = np.eye(C)[Y.reshape(-1)].T
```



### pdf转Image:pymupdf

**安装**

```python
pip install pymupdf -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
```

使用

```python
import fitz
_start_time=datetime.datetime.now()
#  打开PDF文件，生成一个对象
doc = fitz.open('pdf1.pdf')
page_index=0
for page in doc:
    rotate = int(0)
    # 每个尺寸的缩放系数为2，这将为我们生成分辨率提高四倍的图像。
    zoom_x = 6.0
    zoom_y = 6.0
    trans = fitz.Matrix(zoom_x, zoom_y).preRotate(rotate)
    pm = page.getPixmap(matrix=trans, alpha=False)
    pm.writePNG('%s.png' % page_index)
    page_index=page_index+1
    break
_end_time=datetime.datetime.now()


print("使用时间:{}秒".format((_end_time-_start_time).seconds))
```

















# TensorFlow学习

## 自定义

```python
#定量
y_hat = tf.constant(36,name="y_hat")
#变量
loss = tf.Variable((y-y_hat)**2,name="loss" )  
#占位符
x=tf.placeholder(tf.int64,name="x")
tf.Session().run(x*2,feed_dict={x:3})
#转码
tf.cast(变量,dtype=tf.float32)
#矩阵惩罚
tf.matmul(矩阵A，矩阵B)

```



## 基本操作

### tf.transpose：转置

```python
input_data = tf.constant([[1, 2, 3], [4, 5, 6]])
print(sess.run(tf.transpose(input_data)))
# [[1 4]
#  [2 5]
#  [3 6]]
```

### tf.reduce_mean：求平均值（降维）

```python
x = [[1,2,3],
      [1,2,3]]
 
xx = tf.cast(x,tf.float32)
 
mean_all = tf.reduce_mean(xx, keep_dims=False)
mean_0 = tf.reduce_mean(xx, axis=0, keep_dims=False)
mean_1 = tf.reduce_mean(xx, axis=1, keep_dims=False)
 
 
with tf.Session() as sess:
    m_a,m_0,m_1 = sess.run([mean_all, mean_0, mean_1])
 
print m_a    # output: 2.0
print m_0    # output: [ 1.  2.  3.]
print m_1    #output:  [ 2.  2.]

```

### tf.argmax:取最大值的index

```python
tf.argmax(input,axis)
test = np.array([[1, 2, 3], [2, 3, 4], [5, 4, 3], [8, 7, 2]])
#axis=0时比较每一列的元素
test[0] = array([1, 2, 3])
test[1] = array([2, 3, 4])
test[2] = array([5, 4, 3])
test[3] = array([8, 7, 2])
# output   :    [3, 3, 1]     

#axis=1的时候，将每一行最大元素所在的索引记录下来，最后返回每一行最大元素所在的索引数组。
test[0] = array([1, 2, 3])  #2
test[1] = array([2, 3, 4])  #2
test[2] = array([5, 4, 3])  #0
test[3] = array([8, 7, 2])  #0

```



### tf.placeholder：占位符

```python
#初始化
X = tf.placeholder(tf.float32, [n_x, None], name="X")
#填入
tf.Session().run(X,feed_dict={X:X_train})
```



### tensorboard:可视化图

```python
#初始化Tensorboard
summary_writer=tf.summary.FileWriter("summary")
summary_writer.add_graph(sess.graph)
merge=tf.summary.merge_all()

#过程
_,epoch_cost,summary=sess.run([optmizer,cost,merge],feed_dict={X:X_train,Y:Y_train})
summary_writer.add_summary(summary, epoch)


#保存TensorBoard
summary_writer.close()
```



### tf.variable_scope:变量域

```python
with tf.variable_scope(""):
```



### tf.train.Saver:管理模型的保存与加载

```python
saver = tf.train.Saver()
#保存
saver=tf.train.Saver(sess,"model/")
# 恢复模型参数
saver.restore(sess, 'model/')
```




