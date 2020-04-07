# CNN基础知识

## 加载数据

### 图片生成器

```python
from tensorflow.keras.preprocessing.image import ImageDataGenerator
```

训练照片

```python
#训练图片
train_dir="../Demo_02_horses_vs_humans/data/horse-or-human/horse-or-human/train/"

train_datagen=ImageDataGenerator(
    rescale=1/255,          #归一化
    rotation_range=40,      #旋转0~40度
    width_shift_range=0.2,  #裁剪-水平20%
    height_shift_range=0.2, #裁剪-高度20%
    shear_range=0.2,        #
    zoom_range=0.2,         #放大20%
    horizontal_flip=True,   #水平翻转
    fill_mode="nearest"
)

train_generator=train_datagen.flow_from_directory(
    train_dir,
    target_size=(200,200),    #归一图片大小
    batch_size=32,            #
    class_mode="binary"        #
)
```

校验图片

```python
#测试图片
validation_dir="../Demo_02_horses_vs_humans/data/horse-or-human/horse-or-human/validation/"

validation_datagen=ImageDataGenerator(rescale=1/255)
validation_generator=validation_datagen.flow_from_directory(
    validation_dir,
    target_size=(200,200),    #归一图片大小
    batch_size=16,             #
    class_mode="binary"        #
)
```



## Mode

### 普通定义

```python
model=tf.keras.models.Sequential([
    tf.keras.layers.Conv2D(64,(3,3),activation="relu",input_shape=(28,28,1)),
    tf.keras.layers.MaxPooling2D(2,2),
    tf.keras.layers.Conv2D(64,(3,3),activation="relu"),
    tf.keras.layers.MaxPooling2D(2,2),
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(128,activation="relu"),
    tf.keras.layers.Dense(10,activation="softmax")
])

#查看
model.summary()
```

### 保存与加载

```python
#加载
model=tf.keras.models.load_model("datas/model.h5")
#保存
model.save("datas/model.h5")
```

### 使用已训练权重

#### InceptionV3

```python
from tensorflow.keras.applications.inception_v3 import InceptionV3
#定义已训练Model
pre_trained_model=InceptionV3(
    input_shape=(200,200,3),
    include_top=False,
    weights='imagenet'
    #weights=None
)

#权重文件
'''
inception_v3_local_weights_file="data/inception_v3_weights_tf_dim_ordering_tf_kernels_notop.h5"
pre_trained_model.load_weights(inception_v3_local_weights_file)
'''

#锁定,不在训练
for layer in pre_trained_model.layers:
    layer.trainable=False
last_layer=pre_trained_model.get_layer("mixed7")
last_output=last_layer.output

#x = GlobalAveragePooling2D()(x) # GlobalAveragePooling2D 将 MxNxC 的张量转换成 1xC 张量，C是通道数

x=layers.Flatten()(last_output)
x=layers.Dense(1024,activation="relu")(x)
x=layers.Dropout(0.2)(x)
x=layers.Dense(1,activation="sigmoid")(x)
model=Model(inputs=pre_trained_model.input,outputs=x)
```



### Dropout

```
x=tf.keras.layers.Dropout(0.2)(x)
```



## 设置学习参数



```python
from tensorflow.keras.optimizers import Adam
from tensorflow.keras.optimizers import RMSprop

model.compile(
    #2个类别,二进制交叉熵
    loss="binary_crossentropy",
    #多个类别
    #loss="categorical_crossentropy",
    
    optimizer=RMSprop(lr=0.001),
    #optimizer=Adam(lr=0.001), 
    metrics=["acc"]
)
```



## 训练



```python
model.fit(train_images,train_labels,batch_size=64,epochs=5)
```



```python
history=model.fit_generator(
    train_generator,
    steps_per_epoch=32,     #train_generator中的batch_size=16,  图片总量1024/16=32
    epochs=10,
    verbose=1,
    validation_data=validation_generator,
    validation_steps=16     #validation_generator中的batch_size=32,图片总量256/32=8
)
```



## 调试

```python

acc=history.history["acc"]
val_acc=history.history["val_acc"]
loss=history.history["loss"]
val_loss=history.history["val_acc"]

epochs=range(len(acc))

plt.plot(epochs,acc,"b",color="red",label="Train acc")
plt.plot(epochs,val_acc,"b",label="Test acc")
plt.title("acc")
plt.figure()


plt.plot(epochs,loss,"b",color="red",label="Train loss")
plt.plot(epochs,val_loss,"b",label="Test loss")
plt.title("loss")
plt.legend()

plt.show()
```



## 测试



```python
#测试测试集Loss
test_loss=model.evaluate(test_images,test_labels)
print("test_loss:"+str(test_loss))
```




```python
#打印表报
from sklearn.metrics import classification_report
y_pred = model.predict_classes(test_images)
print(classification_report(y_pred, test_labels))
```



### 使用图片生成器校验

```python
import numpy as np
test_datagen = ImageDataGenerator(rescale=1/255.)
test_dir = "../Demo_02_horses_vs_humans/data/horse-or-human/horse-or-human/validation/"

test_generator = test_datagen.flow_from_directory(test_dir,
                                                 batch_size=256,
                                                 target_size=(200,200),
                                                 shuffle=False,
                                                 class_mode=None)
pred_y = model.predict_generator(test_generator,steps=1,verbose=1)

pred_y = np.squeeze(pred_y)
logits = np.where(pred_y > 0.5,1,0)
print(logits)
```



### 批量测试

```python
import numpy as np
from tensorflow.keras.preprocessing import image
def img_predict(img_file_path,img_file_name,is_print=False):
    if is_print:
        img=plt.imread(img_file_path+img_file_name)
        plt.imshow(img)

    img=image.load_img(path=img_file_path+img_file_name,target_size=(200,200))
    x=image.img_to_array(img)
    x=np.expand_dims(x,axis=0)
    images=np.vstack([x])
    images=images/255.
   
    classes=model.predict(images,batch_size=10)
    
    print(img_file_path+img_file_name+":",end="")
    
    if classes[0]<0.5:
        print("马:",end="")
        if(img_file_name.find("horse")>-1):
            print("正确")
            return 1
        else:
            print("错误")
            return 0
    else:
        print("人:",end="")
        if(img_file_name.find("human")>-1):
            print("正确")
            return 1
        else:
            print("错误")
            return 0
```


```python
import os
root_path ="data/network_image/horses/"
#root_path="../Demo_02_horses_vs_humans/data/horse-or-human/horse-or-human/validation/horses/"

img_file_num=len(os.listdir(root_path))
correct_num=0
 
#先筛选
for file_name in os.listdir(root_path):
    correct_num+=img_predict(root_path,file_name)
    
print("总文件:"+str(img_file_num))
print("正确数:"+str(correct_num))
        
print("-----------完成-------------")
```





# RNN知识