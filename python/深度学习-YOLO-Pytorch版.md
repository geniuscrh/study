# 学习网址



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



# 安装图片标注软件LabelImg

<https://zhuanlan.zhihu.com/p/90832346>

安装

```python
pip install PyQt5 -i https://pypi.tuna.tsinghua.edu.cn/simple/（后面这行是国内的清华镜像源，下载速度才会比较快）
pip install pyqt5-tools -i https://pypi.tuna.tsinghua.edu.cn/simple/
pip install lxml -i https://pypi.tuna.tsinghua.edu.cn/simple/
pip install labelImg -i https://pypi.tuna.tsinghua.edu.cn/simple/ （Img中的I要大写，注意）
```

### 启动

```
在anaconda prompt中打开labelImg
```









# YOLO_V3训练







## 下载模板GitHub

```
https://github.com/ultralytics/yolov3

https://gitee.com/yhx0105/yolov3
```





## 添加权重

```
yolov3-tiny.conv.15
yolov3-tiny.pt
yolov3-tiny.weights
三个文件放置在weights
```



**网上下载链接**

```
https://pan.baidu.com/s/1nv1cErZeb6s0A5UOhOmZcA
提取码：t7vp
```





## 添加数据集

- 增加data/Annotations文件夹

  ​	放置xml文件

- 增加data/JPEGImages文件夹

  ​	放置照片

- 复制JPEGImages到images

​	

- 增加data/ImageSets文件夹

  ​	自动生成:训练/校验/测试集


### 数据集(Label)

```python
#细胞
https://github.com/cosmicad/dataset
#口罩
https://gitee.com/geniuscrh/Object_Dete_Masking

https://github.com/X-zhangyang/Real-World-Masked-Face-Dataset
```



## 执行makeTxt.py

```
在data/ImageSets文件夹中生成train.txt   test.txt   trainval.txt  val.txt文件
```



## 执行voc_label.py

1. **修改voc_label.py**

   ```
   classes = ["RBC"]更换成classes = ["mask","unmask"]
   ```

2. **执行voc_label.py**

   ​	生成labels，里面还有那个照片及box信息





## 增加rbc.data与rbc.names

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





## 修改cfg

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



## 训练

```python
%run train.py --data-cfg data/rbc.data --cfg cfg/yolov3-tiny.cfg --epochs 1
```



```
python train.py --data-cfg data/rbc.data --cfg cfg/yolov3-tiny.cfg --epochs 1
```



## 预测

```
python detect.py --data-cfg data/rbc.data --cfg cfg/yolov3-tiny.cfg --weights weights/best.pt --images imgs --output det
```





## 存在问题修订

### 解决内存不足问题

**test.py 与train.py**

```
num_work需要修改为0
```

**test.py 与train.py**

```
batch-size修改大小
```

**cfg/yolov3-tiny.cfg**

```python
random=0从1修改成0   #（是否要多尺度输出。）
```



# 学习



## 已实现的口罩

```
https://github.com/zhengmingzhang/mask-detection

```



<https://blog.csdn.net/qq_21578849/article/details/84980298>

https://blog.csdn.net/lilai619/article/details/79695109>

<https://blog.csdn.net/public669/article/details/98020800>

<https://github.com/ultralytics/yolov3>



































