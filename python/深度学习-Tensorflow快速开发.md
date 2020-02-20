# 数据加载

## 打开文件

```python
def unpickle(file):
    import pickle
    with open(file, 'rb') as fo:
        dict = pickle.load(fo, encoding='bytes')
    return dict
```





# 数据处理

```python
#定义矩阵
X_train=np.array(X_train,dtype=np.float32)
#矩阵转型
X_train=X_train.reshape(-1,3,32,32)
#矩阵位置转换
X_train=X_train.transpose(0,2,3,1)

#one-hot
Y_train = np.eye(10)[Y_train.reshape(-1)]
```





# 变量函数

```python
def variable_weight(shape):
    init=tf.contrib.layers.xavier_initializer(seed=1)
    return tf.get_variable(shape=shape,initializer=init,name="w")
    
def variable_bias(shape):
    init=tf.zeros_initializer()
    return tf.get_variable(shape=shape,initializer=init,name="b")
```





# 神经元NN

## 卷积层

```python
def conv(X,kernel_size,out_depth,strides,padding="SAME",scope="conv_layer",act=tf.nn.relu,reuse=None):
    """
    funtion-Args:
        X:数据集，4维tensor(数据集数量n_x,h,w,channels)
        kernel_size:[h,w]
        out_depth:int,输出层数量
        strides:[h,w]
    """
    """
    tf.nn.conv2d:Args:
        input：数据集，4维tensor(数据集数量n_x,h,w,channels)
        filter:卷积核（h,w,in,out)
        strides:步长(1,h,w,1)
        padding:SAME,VALID
    """
    
    in_depth=X.shape[3]
    kernel_shape=kernel_size+[in_depth,out_depth]
    strides=[1,strides[0],strides[1],1]
    
    with tf.variable_scope(scope,reuse=reuse):
        #构造卷积核
        with tf.variable_scope("kernel"):
            kernel=variable_weight(kernel_shape)
        #构造偏置量
        with tf.variable_scope("bias"):
            bias=variable_bias([out_depth])
            
        #生成卷积
     
    
    conv=tf.nn.conv2d(input=X,filter=kernel,strides=strides,padding=padding,name="conv")
        
        
        #偏置相加
        print
        z=tf.nn.bias_add(conv,bias)
        
        #激活
        a=act(z)
    
        return a

tf.reset_default_graph()
X=tf.placeholder(tf.float32,shape=[None,32,32,3],)
a=conv(X=X,kernel_size=[3,3],out_depth=5,strides=[1,1])
```



## Max-pool池

```python
"""
    定义Max-pool池
"""
def max_pool(X,ksize,strides,padding="SAME",name="pool_layer"):
    
    
    """
    tf.nn.max_pool(
        value:数据集，4维tensor(数据集数量n_x,h,w,channels)
        ksize:池化核（1,h,w,1)
        strides:步长(1,h,w,1)
        padding:SAME,VALID
    )
    """
    ksize=[1,ksize[0],ksize[1],1]
    strides=[1,strides[0],strides[1],1]
    
    with tf.variable_scope(name):
        p=tf.nn.max_pool(value=X,ksize=ksize,strides=strides,padding=padding,name=name)
        return p
    
    
tf.reset_default_graph()
X=tf.placeholder(tf.float32,shape=[None,32,32,3],)
a=max_pool(X=X,ksize=[3,3],strides=[1,1])
print(a)
```



## 全连接

```python
"""
    定义全连接
"""
def fc(X,out_depth,act=tf.nn.relu,scope="fc_layer",reuse=None):
    
    #X=(数量，参数)
    #w_shape=(参数，输出)
    in_depth=X.get_shape()[1]
    w_shape=[in_depth,out_depth]
    
    with tf.variable_scope(scope,reuse=reuse):
        with tf.variable_scope("weight"):
            w=variable_weight(w_shape)
        with tf.variable_scope("bias"):
            b=variable_bias([out_depth])
        z=tf.nn.bias_add(tf.matmul(X,w),b,name="fc")
        a=act(z)
        
        return a


```



# 神经网络

## AlexNet

```python
"""
    定义AlexNet
"""
def alernet(X,reuse=None):
    
    with tf.variable_scope("AlexNet",reuse=reuse):
        #net = tf.reshape(X, [-1, 32*32*3])
        #net = fc(net, 10, scope='fc5', act=tf.identity)
        
        
        net=conv(X,[5,5],64,[1,1],padding="VALID",scope='conv1')
        net = max_pool(net,[3,3],[2,2], padding='VALID', name='pool1')
        
        net = conv(net, [5, 5], 64, [1, 1], scope='conv2')
        net = max_pool(net, [3, 3], [2, 2], padding='VALID', name='pool2')
        
        net = tf.reshape(net, [-1, 6*6*64])
       
        
        net = fc(net, 384, scope='fc3')
        net = fc(net, 192, scope='fc4')
        
        
        net = fc(net, 10, scope='fc5', act=tf.identity)
        
   
        
        return net
 
tf.reset_default_graph()
X=tf.placeholder(tf.float32,shape=[None,32,32,3])
net=alernet(X)
print(net)
```



# 计算成本

## softmax

```python
def compute_cost(X,Y):
    """
    	X:(数据集，参数)
    """
    #softmax
    cost=tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(labels=Y,logits=X))
    #sigmoid
    cost=tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(labels=Y,logits=X))
    #保存cost到tensorboard
    tf.summary.scalar("epoch_cost",cost)
    return cost
```

## sigmoid

```python
)
    )
```



# Model

```python
#初始化
tf.reset_default_graph()

#定义placeholder
X=tf.placeholder(tf.float32,shape=[None,32,32,3])
Y=tf.placeholder(tf.float32,shape=[None,10])

#定义
Z=AlexNet(X)

#计算成本
cost=compute_cost(Z,Y)

#反向计算
optmizer=tf.train.AdamOptimizer(learning_rate=0.001).minimize(cost)


#初始化参数
init=tf.global_variables_initializer()

#初始化Session读取与保存
saver=tf.train.Saver()

with tf.Session() as sess:
        sess.run(init)
        #初始化Tensorboard
        summary_writer=tf.summary.FileWriter("summary")
        summary_writer.add_graph(sess.graph)
        merge=tf.summary.merge_all()
        
        #读取Session
        saver.restore(sess,"Model/")
        
        #学习
        for epoch in range(epochs):
            _,epoch_cost,summary=sess.run([optmizer,cost,merge],feed_dict={X:X_train,Y:Y_train})
			
            #将summary参数写入
            if epoch%50==0:
                summary_writer.add_summary(summary, epoch)
            #过程打印  
            if epoch%100==0 :
                print("ecoch:{},epoch_cost:{}".format(epoch,epoch_cost))
    
        #保存Session
        saver.save(sess,"Model/")
        
        #保存TensorBoard
        summary_writer.close()


```



# 预测

## softmax

```python
#tf.argmax(获取最大的index)，进行比较是否相等
correct_prediction = tf.equal(tf.argmax(Z),tf.argmax(Y))
accuracy = tf.reduce_mean(tf.cast(correct_prediction,"float"))
correct=sess.run(accuracy,feed_dict={X:X_train,Y:Y_train})
print("训练集准确率"+str(correct)))    
```

## sigmoid

```python
predicated = tf.cast(Z > 0.5, tf.float32)
correct_prediction =tf.equal(predicated,Y)
correct=sess.run(accuracy,feed_dict={X:X_train,Y:Y_train})
```





# 常用方法

## 创建占位符

- X.shape=（向量大小，？)
- Y.shape=（分类数，？）

```python
#创建placeholders
def create_placeholders(n_x,n_y):
    """
    为TensorFlow会话创建占位符
    参数：
        n_x - 一个实数，图片向量的大小（64*64*3 = 12288）
        n_y - 一个实数，分类数（从0到5，所以n_y = 6）
    
    返回：
        X - 一个数据输入的占位符，维度为[n_x, None]，dtype = "float"
        Y - 一个对应输入的标签的占位符，维度为[n_Y,None]，dtype = "float"
    
    提示：
        使用None，因为它让我们可以灵活处理占位符提供的样本数量。事实上，测试/训练期间的样本数量是不同的。
    
    """
    
    X = tf.placeholder(tf.float32, [n_x, None], name="X")
    Y = tf.placeholder(tf.float32, [n_y, None], name="Y")
    
    return X, Y

#X = Tensor("X_1:0", shape=(12288, ?), dtype=float32)
#Y = Tensor("Y_1:0", shape=(6, ?), dtype=float32)
```



## random_mini_batches

model

```python
#正常训练的循环
        for epoch in range(num_epochs):
            
            epoch_cost = 0  #每代的成本
            num_minibatches = int(m / minibatch_size)    #minibatch的总数量
            seed = seed + 1
            minibatches = tf_utils.random_mini_batches(X_train,Y_train,minibatch_size,seed)
            
            for minibatch in minibatches:
                
                #选择一个minibatch
                (minibatch_X,minibatch_Y) = minibatch
                
                #数据已经准备好了，开始运行session
                _ , minibatch_cost = sess.run([optimizer,cost],feed_dict={X:minibatch_X,Y:minibatch_Y})
                
                #计算这个minibatch在这一代中所占的误差
                epoch_cost = epoch_cost + minibatch_cost / num_minibatches
                
           
```

   定义函数

```python
Arguments:
X -- input data, of shape (input size, number of examples)
Y -- true "label" vector (containing 0 if cat, 1 if non-cat), of shape (1, number of examples)
mini_batch_size - size of the mini-batches, integer
seed -- this is only for the purpose of grading, so that you're "random minibatches are the same as ours.

Returns:
mini_batches -- list of synchronous (mini_batch_X, mini_batch_Y)
"""

m = X.shape[1]                  # number of training examples
mini_batches = []
np.random.seed(seed)

# Step 1: Shuffle (X, Y)
permutation = list(np.random.permutation(m))
shuffled_X = X[:, permutation]
shuffled_Y = Y[:, permutation].reshape((Y.shape[0],m))

# Step 2: Partition (shuffled_X, shuffled_Y). Minus the end case.
num_complete_minibatches = math.floor(m/mini_batch_size) # number of mini batches of size mini_batch_size in your partitionning
for k in range(0, num_complete_minibatches):
    mini_batch_X = shuffled_X[:, k * mini_batch_size : k * mini_batch_size + mini_batch_size]
    mini_batch_Y = shuffled_Y[:, k * mini_batch_size : k * mini_batch_size + mini_batch_size]
    mini_batch = (mini_batch_X, mini_batch_Y)
    mini_batches.append(mini_batch)

# Handling the end case (last mini-batch < mini_batch_size)
if m % mini_batch_size != 0:
    mini_batch_X = shuffled_X[:, num_complete_minibatches * mini_batch_size : m]
    mini_batch_Y = shuffled_Y[:, num_complete_minibatches * mini_batch_size : m]
    mini_batch = (mini_batch_X, mini_batch_Y)
    mini_batches.append(mini_batch)

return mini_batches
```





# 框架

## 人脸检测:

### Adaboost

### MTCNN

## 关键点检测:

### Dlib

### CLM-framework

## 人脸识别

### AM-softmax

### siamese

### 库:face_recognition:找出图片中的人脸