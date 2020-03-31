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







# YOLO_V1实例





# YOLO_V3实例

学习网址

```python
官方网址
https://pjreddie.com/

https://github.com/ultralytics/yolov3
```

