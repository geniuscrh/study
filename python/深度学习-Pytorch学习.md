







# 插件

## torchsummary

```
torchsummary.summary(net,input_size)
```

## 进程条:tqdm

```
from tqdm.notebook import tqdm

#设置总量
pbar=tqdm(total=*)
#更新进展
pbar.update(1)
```



# 基本知识

## 加载数据

初始化参数

```python
BATCH_SIZE=32
```



```python
#数据归一化及数据增强
train_data_transform=torchvision.transforms.Compose([
    torchvision.transforms.Resize(150),
    torchvision.transforms.CenterCrop(150),
    torchvision.transforms.ToTensor()
    #torchvision.transforms.Normalize()
])

test_data_transform=torchvision.transforms.Compose([
    torchvision.transforms.Resize(150),
    torchvision.transforms.CenterCrop(150),
    torchvision.transforms.ToTensor()
    #torchvision.transforms.Normalize()
])
```



```python
# 加载数据
train_data_datasets=torchvision.datasets.ImageFolder(root="data/horse-or-human/train/",transform=train_data_transform)
test_data_datasets=torchvision.datasets.ImageFolder(root="data/horse-or-human/validation/",transform=test_data_transform)
```



```python
# 生成数据
train_data=torch.utils.data.DataLoader(train_data_datasets,shuffle=True,batch_size=BATCH_SIZE,num_workers=1)
test_data=torch.utils.data.DataLoader(test_data_datasets,shuffle=False,batch_size=BATCH_SIZE,num_workers=1)
```



```python
#显示图片情况
import matplotlib.pyplot as plt
x,label=next(iter(train_data))
print(x.shape,label.shape)
plt.figure(30)
for i in range(3):
    plt.subplot(1,3,i+1)
    plt.imshow(x[i].T)
    plt.title(label[i].item())
```



## Model
```python
class Model(nn.Module):
    def __init__(self):
        super(Model,self).__init__()

        self.conv1=nn.Sequential(
            nn.Conv2d(1,20,5,1),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(2,2))
        
        self.conv2=nn.Sequential(
            nn.Conv2d(20,50,5,1),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(2,2))
        
        self.fc1=nn.Sequential(
            nn.Flatten(),
            nn.Linear(800,512),
            nn.ReLU(inplace=True),
        )
        self.fc2=nn.Sequential(
            nn.Linear(512,10),
            nn.Softmax(dim=1)#dim要设置为1
        )
        
    def forward(self,X):
        x=self.conv1(X)
        x=self.conv2(x)
        
        x=self.fc1(x)
        x=self.fc2(x)
      
        return x
    
model=Model()
torchsummary.summary(model,(1,28,28))
```



### 迁移学习-VGG16

```python
#pretrained=True会自动下载权重
model=torchvision.models.vgg16(pretrained=False)
#加载权重
model_dict_path="data/vgg16.pth"
model_dict=torch.load(model_dict_path)
model.load_state_dict(model_dict)
```



```python
#设置不在训练
for p in model.parameters():
    p.requires_grad=False
```



```python
#修改最后一层
model.classifier[6]=nn.Linear(4096,2)
#最后一层需要学习
for p in model.classifier[6].parameters():
    p.requires1_grad=True
```



## 训练
```python
#过程显示
import sys,time

def progress(i,total_num):
    i+=1
    i=int(i*100/total_num)
    str = '>'*i+' '*(100-i)
    sys.stdout.write('\r'+str+'[%s%%]'%(i))
    sys.stdout.flush()
```

```python
#设置优化器optimizer与损失函数loss
optimizer=optim.Adam(model.parameters(),lr=0.001)
loss=nn.CrossEntropyLoss()
```



```python
#定义训练函数
import time

def train(model,train_data):
    start_time=time.time()
    train_loss=[]
    train_acc=[]
    model.train()#打开BN及Dropout
    total_i=len(train_data)
    for i,(X,label) in enumerate(train_data):
        X=torch.autograd.Variable(X)
        label=torch.autograd.Variable(label)
        
        #正向传播
        out=model(X)
        #计算损失值
        loss_value=loss(out,label)
        
        #优化器梯度清零
        optimizer.zero_grad()
        #反向传播,刷新梯度值
        loss_value.backward()
        #优化器运行一步:更新梯度,注意optimizer搜集的是model的参数
        optimizer.step()
        
        #获取数据最大一列
        _,pred=out.max(1)
        #统计正确的数量
        num_correct=int((pred==label).sum())
        
 		#收集loss与acc
        train_loss.append(loss_value.item())
        train_acc.append(num_correct/X.shape[0])
        
        progress(i,total_i)


    print("使用时间:",time.time()-start_time)
    return train_loss,train_acc

```



```python
#开始训练
num_epochs=10
for epoch in range(num_epochs):
    print("")
    print("训练次数:",(i+1))
	train_loss,train_acc=train(model,train_data)

#打印loss与acc
print("")
print("train_loss:",np.array(train_loss).mean())
print("train_acc:",np.array(train_acc).mean())

plt.plot(range(len(train_loss)),train_loss,"b",color="red",label="loss")
plt.title("loss")
plt.figure()

plt.plot(range(len(train_acc)),train_acc,"b",color="red",label="acc")
plt.title("acc")
plt.legend()
plt.show()
```





## 测试

```python
test_acc=[]
model.eval()#关闭BN与Dropout
total_i=len(test_data)
for i,(X,label) in enumerate(test_data):
    X=torch.autograd.Variable(X)
    label=torch.autograd.Variable(label)
    out=model(X)
    _,pred=out.max(1)
    num_correct=int((pred==label).sum())
    test_acc.append(num_correct/X.shape[0])
    progress(i,total_i)

test_acc=np.array(test_acc)
print(test_acc.mean())
plt.plot(range(len(test_acc)),test_acc,"b",color="red",label="acc")
plt.title("acc")
plt.legend()
plt.show()
```





## 网上照片测试

```python
val_data_datasets=torchvision.datasets.ImageFolder("data/network_img/",transform=test_data_transform)
val_data=torch.utils.data.DataLoader(val_data_datasets,shuffle=True,batch_size=BATCH_SIZE)


i=2
x,label=next(iter(val_data))
plt.imshow(x[i].T)
x=x[i].reshape(1,3,150,150)
x=torch.autograd.Variable(x)

_,pred=model(x).max(1)

if int(pred)==0:
    print("这张照片是:马")
else:
    print("这张照片是:人")
```











# YOLO_V3实例

## 学习网址

```python
官方网址
https://pjreddie.com/

https://github.com/ultralytics/yolov3
```

一文看懂YOLO v3

 https://blog.csdn.net/litt1e/article/details/88907542 

已实现(不支持训练)

 https://github.com/ayooshkathuria/pytorch-yolo-v3 

 yolov3的pytorch版官方源码见github： 

 https://github.com/ayooshkathuria/YOLO_v3_tutorial_from_scratch 

从零开始PyTorch项目：YOLO v3目标检测实现
https://www.jiqizhixin.com/articles/2018-04-23-3
https://www.jiqizhixin.com/articles/042602?from=synced&keyword=%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8BPyTorch%E9%A1%B9%E7%9B%AE%EF%BC%9AYOLO%20v3%E7%9B%AE%E6%A0%87%E6%A3%80%E6%B5%8B%E5%AE%9E%E7%8E%B0

史上最详细的Pytorch版yolov3代码中文注释详解（一）
https://blog.csdn.net/qq_34199326/article/details/84072505

官网--学习模型

 https://pjreddie.com/darknet/yolo/ 

训练模型(翻译官网)

 https://blog.csdn.net/syysyf99/article/details/93207020 

<https://zhuanlan.zhihu.com/p/70273745>

训练

 https://www.cnblogs.com/pprp/p/10863496.html 

<https://blog.csdn.net/qq_21578849/article/details/84980298>

https://blog.csdn.net/lilai619/article/details/79695109

<https://blog.csdn.net/public669/article/details/98020800>

<https://github.com/ultralytics/yolov3>

### 已实现的口罩

```
https://github.com/zhengmingzhang/mask-detection

```





## 安装图片标注软件LabelImg

**参考网址**

<https://zhuanlan.zhihu.com/p/90832346>

**安装**

```python

pip install PyQt5 -i https://pypi.tuna.tsinghua.edu.cn/simple/
pip install pyqt5-tools -i https://pypi.tuna.tsinghua.edu.cn/simple/
pip install lxml -i https://pypi.tuna.tsinghua.edu.cn/simple/
pip install labelImg -i https://pypi.tuna.tsinghua.edu.cn/simple/
```

**启动**

```
在anaconda prompt中打开labelImg
```



## 训练过程

#### 下载模板GitHub

```
https://github.com/ultralytics/yolov3

https://gitee.com/yhx0105/yolov3
```

#### 添加权重

**权重网上下载链接**

```
https://pan.baidu.com/s/1nv1cErZeb6s0A5UOhOmZcA
提取码：t7vp
```



```
yolov3-tiny.conv.15
yolov3-tiny.pt
yolov3-tiny.weights
三个文件放置在weights
```



#### 添加数据集

- 增加data/Annotations文件夹

  ​	放置xml文件

- 增加data/JPEGImages文件夹

  ​	放置照片

- 复制JPEGImages到images

​	

- 增加data/ImageSets文件夹

  ​	自动生成:训练/校验/测试集

#### 数据集(Label)

```python
#细胞
https://github.com/cosmicad/dataset
#口罩
https://gitee.com/geniuscrh/Object_Dete_Masking
https://github.com/X-zhangyang/Real-World-Masked-Face-Dataset
```

#### 增加rbc.data与rbc.names

**rbc.data**

```
classes=2
train=data/train.txt
valid=data/test.txt
names=data/rbc.names
backup=backup/
eval=coco
```

**rbc.names**

```
mask
unmask
```

#### 执行makeTxt.py

```
在data/ImageSets文件夹中生成train.txt   test.txt   trainval.txt  val.txt文件
```

#### 执行voc_label.py

1. **修改voc_label.py**

   ```
   classes = ["RBC"]更换成classes = ["mask","unmask"]
   ```

2. **执行voc_label.py**

   ​	生成labels，里面还有那个照片及box信息

#### 修改cfg

将testing 的batch和subdivisions注释掉，并将training 的batch和subdivisions去掉注释

修改cfg/yolov3-tiny.cfg文件

```python
#一共两处
[convolutional]
size=1
stride=1
pad=1
filters=21    #3*(4+1+class)
activation=linear


[yolo]
mask = 1,2,3
anchors = 10,14,  23,27,  37,58,  81,82,  135,169,  344,319
classes=2   #class
num=6
jitter=.3
ignore_thresh = .7
truth_thresh = 1
#原本为1
random=0
```

#### 训练

```python
%run train.py --data-cfg data/rbc.data --cfg cfg/yolov3-tiny.cfg --epochs 1
```

##### 恢复训练

```
python train.py --data data/coco.data --cfg cfg/yolov3.cfg --resume
```

#### 预测

```
python detect.py --data-cfg data/rbc.data --cfg cfg/yolov3-tiny.cfg --weights weights/best.pt --images imgs --output det
```

#### 存在问题修订

##### 解决内存不足问题

**test.py 与train.py**

```
num_work
num-workers
两处均需要修改为0
```

**test.py 与train.py**

```
batch-size修改大小
```

**cfg/yolov3-tiny.cfg**

```python
random=0从1修改成0   #（是否要多尺度输出。）
```



# SSD实例

## 口罩

```python
已实现
https://github.com/AIZOOTech/FaceMaskDetection
```

