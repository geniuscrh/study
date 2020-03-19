# 学习

## Mode

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

```python
#加载
model=tf.keras.models.load_model("datas/model.h5")
#保存
model.save("datas/model.h5")
```

## 训练

```python
model.compile(optimizer="adam",loss="sparse_categorical_crossentropy")

model.fit(train_images,train_labels,batch_size=64,epochs=5)
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



# Fashion_Mnist例子