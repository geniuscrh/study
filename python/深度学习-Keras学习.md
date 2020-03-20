# CNN基础知识

## 加载数据

### 图片生成器

```python
from tensorflow.keras.preprocessing.image import  ImageDataGenerator
#训练图片
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
#图片
train_dir="data/horse-or-human/horse-or-human/train/"
train_generator=train_datagen.flow_from_directory(
    train_dir,
    target_size=(200,200),    #归一图片大小
    batch_size=32,            #
    class_mode="binary"       #多个:categorical
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
#导入模块
from tensorflow.keras.applications.inception_v3 import InceptionV3

#加载模型
pre_trained_model=InceptionV3(
    input_shape=(200,200,3),
    include_top=False,      #不含全连接层
    weights=None
)
#加载权重
pre_trained_model.load_weightsd(inception_v3_local_weights_file)
#不在训练
for layer in pre_trained_model.layers:
    layer.trainable=False
#选择最一层
last_layer=pre_trained_model.get_layer("mixed7")
last_output=last_layer.output    

#自定义模型
x=tf.keras.layers.Flatten()(last_output)
x=tf.keras.layers.Dense(1024,activation="relu")(x)
x=tf.keras.layers.Dense(1,activation="sigmoid")(x)

mode=tf.keras.Model(pre_trained_modele.input,x)
```



#### Dropout

```
x=tf.keras.layers.Dropout(0.2)(x)
```





## 训练

loss

```python
#2个类别,二进制交叉熵
loss="banary_crossentropy"
#多个类别
loss="categorical_crossentropy"
```



```python
model.compile(optimizer="adam",loss="sparse_categorical_crossentropy")
model.fit(train_images,train_labels,batch_size=64,epochs=5)
```

```python
model.compile(
    loss="binary_crossentropy",
    optimizer=RMSprop(lr=0.001),
    metrics=["acc"]
)
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



```python
import numpy as np
from tensorflow.keras.preprocessing import image
def img_predict(img_file_path,is_print=False):
    if is_print:
        img=plt.imread(img_file_path)
        plt.imshow(img)

    img=image.load_img(path=img_file_path,target_size=(200,200))
    x=image.img_to_array(img)
    x=np.expand_dims(x,axis=0)
    images=np.vstack([x])
    classes=model.predict(images,batch_size=10)
    print(img_file_path+"：",end="")
    if classes[0]<0.5:
        print("马")
    else:
        print("人")
```
批量测试

```python
import os
#root_path ="../Demo_03_inceptionV3/data/network_image/horses/"
root_path ="data/horse-or-human/horse-or-human/validation/horses/"

#先筛选
for file_name in os.listdir(root_path):
    img_predict(root_path+file_name)
        
print("-----------完成-------------")
```









# RNN知识