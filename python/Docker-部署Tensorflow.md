# 环境部署

## 安装

```
docker run -p 8887:8888 -v /usr/local/tensorflow/root/:/tf/ -v /usr/local/tensorflow/datasets/:/root/.keras/ tensorflow/tensorflow:nightly-py3-jupyter
```

## 挂载位置

```python
在Docker中
#tf根目录位置
/tf/
#kaeras数据集位置
/root/.keras/datasets/fashion-mnist/train-labels-idx1-ubyte.gz
```







# 配置Jupyter

## 进入jupyter

```
docker exec -it dockerID /bin/bash
```



## 安装Vim

```python
apt-get update
apt-get install vim
```



## 生成密码

```python
jupyter notebook password
#先在 /root/.jupyter/jupyter_notebook_config.josn中找到生成的密文，复制他
```

sha1:3c6a11c209aa:705246607e6a9e06825af666f5d136ccc123381f

## 生成配置文件

```python
jupyter notebook --generate-config
#这时候会生成配置文件，在 ~/.jupyter/jupyter_notebook_config.py
```



## 修改配置文件

```
c.NotebookApp.ip='0.0.0.0'
c.NotebookApp.password = u'sha1:3c6a11c209aa:705246607e6a9e06825af666f5d136ccc123381f'
c.NotebookApp.open_browser = False
c.NotebookApp.port =8888
```



# 配置其他功能

自动



## 安装sklearn

```
pip install sklearn -i https://pypi.tuna.tsinghua.edu.cn/simple
```

