
## Python
关于自身数据结构、语法糖等的操作

## Pandas
这一部分主要是用于CSV的操作，由于ADNI中很多表格文件，因此用这个来做

`df.info()` 看表格信息
`df.describe()` 看表格统计信息

## Matplotlib
记录画图

画不出图: 尝试用一般数据, 如果可以画那就是源数据有nan

## Conda

#### 安装pytorch环境
```
conda create -n torch101 python==3.7.0
conda install cudatoolkit=10.1  -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/linux-64
conda install pytorch==1.7.0 torchvision==0.8.0 torchaudio==0.7.0 cudatoolkit=10.1 -c pytorch【这个在pytorch官网可以搜】
其他：matplotlib seaborn scipy pandas numpy scikit-learn wheel tqdm wandb Pillow warning logging  等
```

#### 报错
The NVIDIA driver on your system is too old (found version 8000). CUDA和pytorch不匹配，更换pytorch

联网后pip install, 也需要换源 `-i https://pypi.tuna.tsinghua.edu.cn/simple/`
#### 命令
`conda info --envs`查看当前已经存在的环境

[Anaconda 环境克隆、迁移 ，用Anaconda里面的conda命令创建虚拟环境并克隆环境或者复旧电脑实验环境包、\_conda克隆环境-CSDN博客](https://blog.csdn.net/qq_41656402/article/details/131015711)【conda指令】
