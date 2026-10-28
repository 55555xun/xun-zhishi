# catdog识别

## 一.环境搭建

### 1.1创建虚拟环境（在Anaconda Prompt中执行)

```python
# 创建一个名为 catdog 的虚拟环境，Python 版本指定为 3.10
# -n 表示 name（名字），-y 表示自动确认（不用每次敲 y）
conda create -n catdog python=3.10 -y

# 激活这个环境，激活后命令提示符前面会从 (base) 变成 (catdog)
conda activate catdog

# 验证当前 Python 版本，预期输出 Python 3.10.x
python --version
```

1.conda create：创建新的虚拟环境。虚拟环境的作用是隔离不同项目的依赖，避免版本冲突。

 2.-n catdog：指定环境名称为 catdog。这个名字您可以自定义，但后面所有命令都要保持一致。

 3.python=3.10：指定安装 Python 3.10 版本。选择 3.10 是因为它与 PyTorch 兼容性很好。

 3.-y：自动回答 yes，省去交互式确认。

 4.conda activate：进入指定环境。必须先创建再激活，否则会报EnvironmentNameNotFound。

 5.python --version：验证当前环境的 Python 版本

### 1.2安装依赖库

```python
# 安装 PyTorch 和 torchvision（CPU 版本）
# --index-url 指定从 PyTorch 官方源下载 CPU 版
pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu

# 安装其他工具库
pip install jupyter matplotlib numpy pillow
```

### 1.3启动Jupyter Notebook

```python  
# 创建项目文件夹（路径可以自定义）
mkdir D:\catdog_project

# 切换到该文件夹
cd /d D:\catdog_project

# 启动 Jupyter Notebook，浏览器会自动打开
jupyter notebook
```

1.mkdir：创建文件夹。把项目文件集中在一个文件夹里，方便管理。

 2.cd /d：切换盘符和目录。Windows 下跨盘切换需要 /d 参数。

 3.jupyter notebook：启动 Jupyter 服务。不要关闭这个命令行窗口，它是 Jupyter 的“发动机”。

## 二.Jupter环境报名代码

```python
# 导入操作系统接口模块
import os

# 设置环境变量，允许重复加载 OpenMP 库
# 这是 Windows 下防止 PyTorch 内核崩溃的关键代码，可不用
os.environ["KMP_DUPLICATE_LIB_OK"] = "TRUE"

# 导入 PyTorch 主库
import torch

# 导入 torchvision 视觉库
import torchvision

# 导入绘图库
import matplotlib

# 导入数值计算库
import numpy as np

# 从 PIL 库导入 Image 类，用来处理图片
from PIL import Image

# 打印 PyTorch 版本号，验证安装成功
print("PyTorch:", torch.__version__)

# 打印 Torchvision 版本号
print("Torchvision:", torchvision.__version__)

# 打印成功信息
print("All imports ok!")
```

1.Python 代码本身只能做数学计算和处理内存里的数据，它没办法直接去电脑硬盘上建文件夹、看文件、改系统设置。而 os 模块就是 Python 与操作系统（Windows/Mac/Linux）之间的“传话筒”和“工具箱”

2.torch：PyTorch 主库，提供张量运算、自动求导、神经网络等功能。torch 是一个专门用于深度学习的科学计算库。它提供了三大核心能力：多维数组计算（张量）、自动求导（梯度计算）、以及搭建神经网络的积木块

3.torchvision：PyTorch 视觉库，提供预训练模型、图像变换、常用数据集等。

4.--index-url：指定下载源。因为 PyTorch 的 CPU 版和 GPU 版在不同的源上，不加这个会下载 GPU 版（体积大且没有 GPU 也用不了）。

 5.jupyter：交互式笔记本环境，我们写代码的地方。

 6.matplotlib：绘图库，用来画训练曲线和显示图片。

 7.numpy：数值计算库，处理数组。

 8.pillow：图像处理库（PIL），用来打开和转换图片

9.os.environ["KMP_DUPLICATE_LIB_OK"] = "TRUE"：这是 Windows 上跑 PyTorch 的保命代码。Windows 系统下 Anaconda 自带的 OpenMP 库和 PyTorch 自带的会冲突，导致 Jupyter 内核突然崩溃。这行代码告诉系统“允许重复加载，不要崩”。

 10.torch.__version__：注意是双下划线（前后各两个），不是单下划线。单下划线会报 AttributeError。

 11.print("All imports ok!")：如果这行能打印出来，说明所有库都正常

## 三.数据准备与检查

#### 3.1目录结构规范

```python
D:\catdog_project\data\
  ├── train\
  │    ├── cats\   （存放训练用的猫图，标签为 0）
  │    └── dogs\   （存放训练用的狗图，标签为 1）
  └── validation\
       ├── cats\   （存放验证用的猫图）
       └── dogs\   （存放验证用的狗图）
```

### 3.2检查目录结构

```python
# 导入操作系统接口模块
import os

# 使用 r"" 声明原始字符串，避免反斜杠被当成转义字符
# 指定数据集根目录路径
extract_dir = r"F:\daima\catdog\data"

# 判断该文件夹是否存在
if not os.path.exists(extract_dir):
    # 如果不存在，打印错误信息
    print("❌ 错误：找不到文件夹，请检查路径！")
    # 打印程序实际寻找的绝对路径，方便排错
    print(f"我在找：{os.path.abspath(extract_dir)}")
else:
    # 如果存在，打印成功信息
    print("✅ 找到数据集文件夹！目录结构如下：\n")
    
    # os.walk 递归遍历目录树
    # 每次迭代返回三个值：root(当前路径), dirs(子文件夹列表), files(文件列表)
    for root, dirs, files in os.walk(extract_dir):
        # 计算当前路径相对于根目录的层级深度
        # replace 去掉前缀，count 数剩下的路径分隔符个数
        level = root.replace(extract_dir, "").count(os.sep)
        # 根据层级生成缩进空格（每层缩进2个空格）
        indent = " " * 2 * level
        # 打印当前文件夹的名字（basename 取路径最后一部分）
        print(f"{indent}{os.path.basename(root)}/")
        # 只打印前两层的文件数量，避免刷屏
        if level < 3:
            # len(files) 计算当前文件夹里的文件总数
            print(f"{indent}  -> {len(files)} 个文件")
```

1.r"F:\daima\catdog\data"：r 前缀表示 raw string（原始字符串）。在普通字符串里，\t 会被解析成制表符，\n 会被解析成换行。加 r 后，反斜杠就是反斜杠本身。Windows 路径一定要加 r。

2.os.path.exists(path)：判断文件或文件夹是否存在，返回 True 或 False。

3.os.path.abspath(path)：把相对路径转换成绝对路径。方便你看清楚程序到底在找哪里。

4.os.walk(top)：递归遍历目录树。返回三元组 (root, dirs, files)。root 是当前文件夹路径，dirs 是子文件夹名字列表，files 是当前文件夹的文件名列表。

5.root.replace(a, b)：把字符串 root 中的 a 替换成 b。这里是把前缀 extract_dir 替换成空字符串。

6.count(os.sep)：统计字符串中路径分隔符（Windows 是 \，Mac/Linux 是 /）的个数，用来计算目录深度。

7.os.path.basename(path)：取路径的最后一部分。比如 F:\data\cats 返回 "cats"。

8.len(files)：计算列表元素个数。

## 四.数据管道(Transfroms+Dataset+DataLoader)

### 4.1第一图像预处理流水线

```python
# 从 torchvision 导入 transforms 模块，用于图像变换
from torchvision import transforms

# 定义训练集的图像预处理流水线
train_transform = transforms.Compose([
    # 将图片缩放到 128x128 像素（神经网络要求输入尺寸统一）
    transforms.Resize((128, 128)),
    # 随机水平翻转（50%概率），数据增强，防止过拟合
    # 只在训练集加，验证集绝对不加
    transforms.RandomHorizontalFlip(p=0.5),
    # 把 PIL 图片转换成 PyTorch 张量
    # 同时将像素值从 0~255 缩放到 0.0~1.0
    transforms.ToTensor(),
    # 标准化处理：(像素值 - 均值) / 标准差
    # 让数据分布接近标准正态分布，加速收敛
    transforms.Normalize(
        mean=[0.485, 0.456, 0.406],  # RGB 三通道的均值（ImageNet 统计值）
        std=[0.229, 0.224, 0.225]    # RGB 三通道的标准差
    ),
])

# 定义验证集的图像预处理流水线
val_transform = transforms.Compose([
    # 同样缩放到 128x128
    transforms.Resize((128, 128)),
    # 验证集不能加随机翻转！评估必须可复现、客观
    # 转张量
    transforms.ToTensor(),
    # 同样做标准化
    transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225]),
])
```

1.transforms.Compose([...])：流水线打包器。把里面多个变换按顺序依次执行。

2.transforms.Resize((128, 128))：统一图片尺寸。神经网络的输入维度必须固定，否则没法做矩阵运算。

3.transforms.RandomHorizontalFlip(p=0.5)：数据增强。猫往左看翻过来还是猫，这能增加数据多样性，防止模型“死记硬背”训练集。参数 p 是翻转概率。

4.transforms.ToTensor()：核心函数。做两件事：① 把 PIL 图片对象转成 PyTorch 张量；② 把像素值从 0~255 缩放到 0.0~1.0。形状从 [H, W, C] 变成 [C, H, W]。

5.transforms.Normalize(mean, std)：标准化。对每个通道执行 (x - mean) / std。使用 ImageNet 的统计值是行业惯例。

### 4.2创建Dataset和DataLoader

```python
# 从 torchvision 导入 datasets 模块 
from torchvision import datasets

# 从 torch.utils.data 导入 DataLoader 类
from torch.utils.data import DataLoader

# 定义训练集和验证集的路径（r 前缀防转义）
train_dir = r"F:\daima\catdog\data\train"
val_dir = r"F:\daima\catdog\data\validation"

# 创建训练集 Dataset
# ImageFolder 会自动扫描 root 下的子文件夹，把子文件夹名作为标签
# 并把 transform 应用到每一张读入的图片上
train_dataset = datasets.ImageFolder(
    root=train_dir,               # 根目录路径
    transform=train_transform     # 挂载训练集的预处理流水线
)

# 创建验证集 Dataset
val_dataset = datasets.ImageFolder(
    root=val_dir,                 # 验证集根目录
    transform=val_transform       # 挂载验证集的预处理流水线
)

# 打印类别到索引的映射关系（确认 0 对应 cat，1 对应 dog）
print("类别映射：", train_dataset.class_to_idx)
# 打印训练集样本数
print("训练集大小：", len(train_dataset))
# 打印验证集样本数
print("验证集大小：", len(val_dataset))

# 设置批次大小，一次喂给模型 8 张图
batch_size = 8

# 创建训练集 DataLoader
train_loader = DataLoader(
    dataset=train_dataset,        # 传入训练集 Dataset
    batch_size=batch_size,        # 每批 8 张图
    shuffle=True,                 # 训练集必须打乱顺序！防止模型记住顺序
    num_workers=0                 # Windows 下必须设 0，否则内核会崩
)

# 创建验证集 DataLoader
val_loader = DataLoader(
    dataset=val_dataset,          # 传入验证集 Dataset
    batch_size=batch_size,        # 每批 8 张图
    shuffle=False,                # 验证集不打乱
    num_workers=0                 # 同样设 0
)

# 取出第一批数据验证管道通畅
images, labels = next(iter(train_loader))
# 打印图片张量形状（预期：[8, 3, 128, 128]）
print("图片张量形状：", images.shape)
# 打印标签形状（预期：[8]）
print("标签形状：", labels.shape)
```

1.   PyTorch 的**数据集加载工具箱**，贴标签

2.   torch.utils.data这是 PyTorch 内部专门负责**数据加载和预处理**的模块

3.   datasets.ImageFolder(root, transform)：PyTorch 神器。自动扫描 root 下的子文件夹，把子文件夹名按字母顺序映射成 0, 1, 2...。同时把 transform 应用到每张图上。

4.   train_dataset.class_to_idx：字典属性。返回 {'cats': 0, 'dogs': 1}。非常重要，确保你以后看到预测结果 0 时知道它代表猫。
5.   len(train_dataset)：返回数据集里的图片总数。
6.   DataLoader(dataset, batch_size, shuffle, num_workers)：把 Dataset 包装成可迭代对象。每次迭代返回一个 (images, labels) 元组。
7.   batch_size：每批处理多少张图。图小、内存小可以设小一点（如 8 或 16）。
8.   shuffle：是否打乱顺序。训练集必须 True（防止模型记住图片顺序），验证集必须 False（保证评估一致）。
9.   num_workers：负责读图的子进程数。Windows 上设 0 最稳定，设大于 0 可能崩溃。
10.   iter(loader)：把 DataLoader 变成迭代器。
11.   next(...)：从迭代器取出第一个元素（第一批数据）。
12.   images.shape：返回张量的形状。[8, 3, 128, 128] 表示：8张图，3通道(RGB)，高128像素，宽128像素。
13.   labels.shape：是标签数据的“身份证”

## 五.搭建卷积神经网络(CNN)

### 5.1尺寸推演

```python
输入图片：        [3, 128, 128]
经过 conv_block1: [16, 64, 64]    # 通道 3->16，尺寸 128->64（池化减半）
经过 conv_block2: [32, 32, 32]    # 通道 16->32，尺寸 64->32
经过 conv_block3: [64, 16, 16]    # 通道 32->64，尺寸 32->16
经过 Flatten:     64 * 16 * 16 = 16384
经过全连接层:     16384 -> 128 -> 2
```

### 5.2定义模型

```python
# 导入神经网络模块
import torch.nn as nn

# 定义 SimpleCNN 类，继承自 nn.Module（PyTorch 所有模型的基类）
class SimpleCNN(nn.Module):
    
    # 初始化函数，num_classes=2 表示最终分为 2 类
    def __init__(self, num_classes=2):
        # 调用父类的初始化方法（必须写）
        super().__init__()
        
        # ---- 卷积块1：提取边缘、颜色等低级特征 ----
        self.conv_block1 = nn.Sequential(
            # 二维卷积：输入3通道(RGB)，输出16通道，卷积核3x3，padding=1保持尺寸
            nn.Conv2d(in_channels=3, out_channels=16, kernel_size=3, padding=1),
            # ReLU激活：把负数变成0，引入非线性
            nn.ReLU(),
            # 最大池化：窗口2x2，特征图宽高减半 (128->64)
            nn.MaxPool2d(2),
        )
        
        # ---- 卷积块2：提取形状、纹理等中级特征 ----
        self.conv_block2 = nn.Sequential(
            # 输入16通道，输出32通道
            nn.Conv2d(16, 32, kernel_size=3, padding=1),
            nn.ReLU(),
            # 尺寸减半 (64->32)
            nn.MaxPool2d(2),
        )
        
        # ---- 卷积块3：提取更复杂的特征 ----
        self.conv_block3 = nn.Sequential(
            # 输入32通道，输出64通道
            nn.Conv2d(32, 64, kernel_size=3, padding=1),
            nn.ReLU(),
            # 尺寸减半 (32->16)
            nn.MaxPool2d(2),
        )
        
        # ---- 分类器：把提取到的特征汇总判断 ----
        self.classifier = nn.Sequential(
            # 展平：把 [64, 16, 16] 拉成 64*16*16 = 16384 的一维向量
            nn.Flatten(),
            # 全连接层：16384 -> 128
            nn.Linear(64 * 16 * 16, 128),
            nn.ReLU(),
            # Dropout：训练时随机丢弃 50% 神经元，防止过拟合
            nn.Dropout(0.5),
            # 最终输出 2 个值（猫的分数、狗的分数）
            nn.Linear(128, num_classes),
        )
    
    # 前向传播函数，定义数据在层之间的流向
    def forward(self, x):
        x = self.conv_block1(x)   # 过第一个卷积块
        x = self.conv_block2(x)   # 过第二个卷积块
        x = self.conv_block3(x)   # 过第三个卷积块
        x = self.classifier(x)    # 过分类器
        return x                  # 返回结果，形状 [batch_size, 2]
```

1. nn.Module：PyTorch 所有神经网络模型的父类。继承它就自动获得参数管理、梯度计算等能力。
2.  super().__init__()：调用父类的初始化方法。必须写，否则 PyTorch 内部机制无法工作。
3.  nn.Sequential(...)：把多层网络按顺序打包成一个整体，数据会依次流过每一层。
4.  nn.Conv2d(in_channels, out_channels, kernel_size, padding)：二维卷积层。
5.  in_channels：输入通道数。第一层是 3（RGB），后面的层是上一层的输出通道数。
6.  out_channels：输出特征图数量，可以理解为“提取多少种特征”。
7.  kernel_size=3：3x3 的卷积核，在图片上滑动。
8.  padding=1：在图片边缘补一圈 0，让卷积后尺寸不变（128 保持 128）。
9.  nn.ReLU()：激活函数，把所有负数变成 0。给网络引入非线性能力。
10.  nn.MaxPool2d(2)：池化窗口大小为 2，取每 2x2 区域的最大值，把特征图宽高减半。
11.  nn.Flatten()：把多维特征图拉成一维向量。
12.  nn.Linear(in_features, out_features)：全连接层，相当于矩阵乘法 y = xW + b。
13.  nn.Dropout(0.5)：训练时随机把 50% 的神经元输出置零，防止模型依赖特定的神经元。
14.  forward(self, x)：前向传播函数。PyTorch 会自动调用它，你不需要手动调用。

### 5.3实例化模型并验证

```python
# 选择设备：有 GPU 用 GPU，没有就用 CPU
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
# 打印使用的设备
print("使用设备：", device)

# 实例化模型，并移动到设备上
model = SimpleCNN().to(device)

# 用假数据测试模型形状
# 生成 4 张 3 通道 128x128 的随机图片
dummy = torch.randn(4, 3, 128, 128).to(device)
# 前向传播
output = model(dummy)
# 打印输出形状（预期：[4, 2]）
print("输出形状：", output.shape)

# 统计模型参数量
# model.parameters() 返回所有可训练参数的迭代器
# p.numel() 返回单个参数张量的元素总数
total_params = sum(p.numel() for p in model.parameters())
# 带千位分隔符打印
print(f"模型总参数量：{total_params:,}")
```

1. torch.device("cuda" if ... else "cpu")：条件表达式选择设备。

   torch.cuda.is_available() 返回 True 表示有 NVIDIA GPU，False 表示没有。

2. .to(device)：把模型或张量搬到指定设备。如果设备是 CPU，就相当于不动。

3.  torch.randn(4, 3, 128, 128)：生成符合标准正态分布的随机张量，形状是 [4, 3, 128, 128]。

4.  model.parameters()：返回模型里所有可训练参数的迭代器。

5.  p.numel()：number of elements，返回一个张量里的元素总数。

6.  f"{total_params:,}"：f-string 格式化，:, 表示用千位分隔符（如 2,121,122）

## 六.损失函数与优化器

```python
# 导入优化器模块
import torch.optim as optim

# 定义损失函数：交叉熵损失
# 它内部会先对模型输出做 Softmax（转概率），再计算与真实标签的差距
# 所以模型的最后一层不要加 Softmax
criterion = nn.CrossEntropyLoss()

# 定义优化器：Adam
# Adam 会自适应调整每个参数的学习率，比原始 SGD 收敛更快
optimizer = optim.Adam(
    model.parameters(),    # 告诉优化器要更新哪些参数
    lr=1e-3                # 学习率 = 0.001，每步调整参数的幅度
)
```

1. nn.CrossEntropyLoss()：分类任务的标准损失。注意：它内部已经包含了 Softmax，所以模型的最后一层不要再加 nn.Softmax()。

2. • optim.Adam(params, lr)：Adam 优化器。它维护每个参数的动量和自适应学习率，通常比 SGD 更快收敛。

   ​	params：要优化的参数。一般传 model.parameters() 或某层的 parameters()。

   ​	lr：学习率。太小收敛慢，太大会震荡。1e-3 是 Adam 的经典默认值。

## 七.训练循环

### 7.1训练一个epoch函数

```python
def train_one_epoch(model, loader, criterion, optimizer, device):
    # 切换到训练模式（启用 Dropout 等）
    model.train()
    # 初始化累计损失
    
  running_loss = 0.0
    # 初始化累计正确数
    correct = 0
    # 初始化累计样本数
    total = 0
    
    # 循环取出每一批数据
    for images, labels in loader:
        # 把图片搬到设备
        images = images.to(device)
        # 把标签搬到设备
        labels = labels.to(device)
        
        # 1. 清空上一轮累积的梯度（必须！）
        optimizer.zero_grad()
        # 2. 前向传播：模型算出预测
        outputs = model(images)
        # 3. 计算损失（预测和真实答案的差距）
        loss = criterion(outputs, labels)
        # 4. 反向传播：自动算出每个参数的梯度
        loss.backward()
        # 5. 优化器根据梯度更新参数
        optimizer.step()
        
        # 累加损失：loss.item() 是平均值，乘样本数得到总量
        running_loss += loss.item() * images.size(0)
        # 取每个样本分数最大的类别索引
        preds = outputs.argmax(dim=1)
        # 累加预测正确的数量
        correct += (preds == labels).sum().item()
        # 累加样本数
        total += labels.size(0)
    
    # 返回平均损失和准确率
    return running_loss / total, correct / total
```

1. model.train()：切换到训练模式。启用 Dropout、BatchNorm 等层。忘了会严重影响结果。
2. • optimizer.zero_grad()：PyTorch 默认会累加梯度，所以每轮必须先清零，否则梯度会越加越大。
3. • model(images)：调用 forward 方法。PyTorch 会自动触发前向传播。
4. • loss.backward()：反向传播。自动计算所有参数的梯度，保存在 p.grad 里。
5. • optimizer.step()：用梯度更新参数。
6. • loss.item()：从张量里取出 Python 数字。
7. • outputs.argmax(dim=1)：在维度 1（类别维度）上取最大值索引。形状 [8, 2] -> [8]。
8. • (preds == labels)：逐元素比较，得到布尔张量。
9. • .sum().item()：求和后取出 Python 数字。

### 7.2评估函数

```python
# 装饰器：让下面的函数不计算梯度，节省内存，加快速度
@torch.no_grad()
def evaluate(model, loader, criterion, device):
    # 切换到评估模式（关闭 Dropout）
    model.eval()
    # 初始化累计损失
    running_loss = 0.0
    # 初始化累计正确数
    correct = 0
    # 初始化累计样本数
    total = 0
    
    # 遍历验证集
    for images, labels in loader:
        # 搬到设备
        images = images.to(device)
        labels = labels.to(device)
        
        # 前向传播（不需要 backward 和 step）
        outputs = model(images)
        # 计算损失
        loss = criterion(outputs, labels)
        
        # 累计损失
        running_loss += loss.item() * images.size(0)
        # 取预测类别
        preds = outputs.argmax(dim=1)
        # 累计正确数
        correct += (preds == labels).sum().item()
        # 累计样本数
        total += labels.size(0)
    
    # 返回平均损失和准确率
    return running_loss / total, correct / total
```

1. @torch.no_grad()：装饰器。让函数内的所有操作都不记录梯度，节省内存、加速运算。
2. • model.eval()：切换到评估模式。关闭 Dropout，让所有神经元都参与推理

### 7.3正式训练

```python
# 设置训练轮数
num_epochs = 10
# 用字典记录每轮的指标
history = {"train_loss": [], "train_acc": [], "val_loss": [], "val_acc": []}

# 循环每一轮
for epoch in range(num_epochs):
    # 训练一轮
    tr_loss, tr_acc = train_one_epoch(model, train_loader, criterion, optimizer, device)
    # 验证一轮
    va_loss, va_acc = evaluate(model, val_loader, criterion, device)
    
    # 记录训练损失
    history["train_loss"].append(tr_loss)
    # 记录训练准确率
    history["train_acc"].append(tr_acc)
    # 记录验证损失
    history["val_loss"].append(va_loss)
    # 记录验证准确率
    history["val_acc"].append(va_acc)
    
    # 打印本轮结果（:02d 表示补零到两位，:.4f 表示保留4位小数）
    print(f"Epoch [{epoch+1:02d}/{num_epochs}] "
          f"train_loss={tr_loss:.4f} train_acc={tr_acc:.4f} | "
          f"val_loss={va_loss:.4f} val_acc={va_acc:.4f}")
```

1. range(num_epochs)：生成 0 到 9 的整数序列。
2. • epoch+1：因为 range 从 0 开始，加 1 后显示为 1~10。
3. • :02d：格式化整数，补零到两位（如 01、02）。
4. • :.4f：格式化浮点数，保留 4 位小数

## 八.可视化训练曲线

```python
# 导入绘图库
import matplotlib.pyplot as plt

# 生成 1 到 10 的序列作为横轴
epochs = range(1, len(history["train_loss"]) + 1)

# 创建 1 行 2 列的画布，figsize 控制大小
fig, axes = plt.subplots(1, 2, figsize=(13, 4.5))

# ---- 左图：损失曲线 ----
# 画训练损失，o- 表示圆点加实线，label 用于图例
axes[0].plot(epochs, history["train_loss"], "o-", label="train")
# 画验证损失，s- 表示方块加实线
axes[0].plot(epochs, history["val_loss"], "s-", label="val")
# 设置标题
axes[0].set_title("Loss")
# 设置横轴标签
axes[0].set_xlabel("epoch")
# 显示图例
axes[0].legend()
# 显示网格，透明度 0.3
axes[0].grid(alpha=0.3)

# ---- 右图：准确率曲线 ----
axes[1].plot(epochs, history["train_acc"], "o-", label="train")
axes[1].plot(epochs, history["val_acc"], "s-", label="val")
axes[1].set_title("Accuracy")
axes[1].set_xlabel("epoch")
axes[1].legend()
axes[1].grid(alpha=0.3)

# 自动调整间距，防止重叠
plt.tight_layout()
# 显示图像
plt.show()
```

1. plt.subplots(1, 2, figsize=(13, 4.5))：创建画布和子图网格。1, 2 表示 1 行 2 列。返回 fig（整张画布对象）和 axes（子图对象数组）。
2. • ax.plot(x, y, style, label)：画折线图。"o-" 是带圆点标记的实线，"s-" 是带方块标记的实线。
3. • ax.set_title() / ax.set_xlabel()：设置标题和横轴标签。
4. • ax.legend()：显示图例。
5. • ax.grid(alpha=0.3)：打开网格，透明度 0.3。
6. • plt.tight_layout()：自动调整布局，防止文字重叠。
7. • plt.show()：渲染显示。

## 九.推理(预测单张图片)

```python
# 从 PIL 导入 Image 类
from PIL import Image

# 定义类别名，顺序必须和训练时一致（0对应cat，1对应dog）
class_names = ["cat", "dog"]

def predict_image(image_path, model, transform, device):
    # 切换到评估模式
    model.eval()
    # 打开图片并强制转为 RGB 三通道
    # 有些图片可能是灰度图(RGBA)或带透明通道，不加 convert 会报错
    img = Image.open(image_path).convert("RGB")
    # 应用预处理，并增加一个 batch 维度
    # [3, 128, 128] -> [1, 3, 128, 128]
    tensor = transform(img).unsqueeze(0).to(device)
    
    # 不计算梯度，节省内存
    with torch.no_grad():
        # 前向传播，得到 2 个原始分数
        logits = model(tensor)
        # 用 Softmax 把分数转成概率（总和为 1）
        probs = torch.softmax(logits, dim=1)[0]
        # 取概率最大的类别索引
        pred_idx = probs.argmax().item()
    
    # 返回预测类别名、置信度、原图
    return class_names[pred_idx], probs[pred_idx].item(), img
```

1. Image.open(path).convert("RGB")：打开图片并强制转成 RGB 三通道。必须加 convert，否则遇到灰度图或 RGBA 图会报错。
2. • transform(img)：应用验证集预处理（Resize、ToTensor、Normalize）。
3. • .unsqueeze(0)：在第 0 维增加一个维度。模型永远按 batch 处理，单张图需要伪装成 [1, 3, 128, 128]。
4. • torch.softmax(logits, dim=1)：把原始分数转成概率，总和为 1。
5. • probs.argmax().item()：取最大概率的索引，并转成 Python 数字。
6. • probs[pred_idx].item()：取出对应类别的概率值。

### 8.1可视化预测结果

```python
# 导入操作系统模块
import os

# 验证集路径
val_dir = r"F:\daima\catdog\data\validation"

# 收集要展示的图片路径
sample_paths = []
# 遍历两个类别文件夹
for class_name in ["cats", "dogs"]:
    # 拼接类别文件夹路径
    class_dir = os.path.join(val_dir, class_name)
    # 取该类别下的前 3 张图
    for f in os.listdir(class_dir)[:3]:
        # 拼接完整路径并加入列表
        sample_paths.append(os.path.join(class_dir, f))

# 创建 1 行 6 列的画布
fig, axes = plt.subplots(1, 6, figsize=(20, 4))

# 遍历每个子图和对应的图片路径
for ax, path in zip(axes, sample_paths):
    # 预测这张图
    pred_name, conf, img = predict_image(path, model, val_transform, device)
    # 从路径提取真实标签
    # 例如路径 .../cats/xxx.jpg -> dirname 得到 .../cats -> basename 得到 "cats" -> [:-1] 去掉最后的 s
    true_name = os.path.basename(os.path.dirname(path))[:-1]
    # 预测对绿色，错红色
    color = "green" if pred_name == true_name else "red"
    # 显示图片
    ax.imshow(img)
    # 设置标题（:.2% 把小数转成百分比）
    ax.set_title(f"Pred: {pred_name} ({conf:.2%})\nTrue: {true_name}", color=color)
    # 关闭坐标轴
    ax.axis("off")

# 调整间距
plt.tight_layout()
# 显示
plt.show()
```

1. os.listdir(dir)[:3]：列出文件夹下所有文件，取前 3 个。
2. • os.path.dirname(path)：获取路径的文件夹部分。
3. • os.path.basename(...)：获取路径的最后一部分。
4. • [:-1]：切片操作，去掉字符串的最后一个字符。这里是把 "cats" 变成 "cat"。
5. • f"{conf:.2%}"：格式化为百分比，保留两位小数。如 0.9924 -> 99.24%。
6. • ax.axis("off")：关闭坐标轴刻度和边框，让图片更干净。

## 十.保存模型

```python
# 保存当前模型的参数
# 推荐只保存 state_dict（参数字典），而不是整个模型
# 因为整个模型依赖 pickle 序列化，换环境容易加载失败
torch.save(model.state_dict(), "cat_dog_cnn.pth")
# 打印成功信息
print("✅ 模型已保存为 cat_dog_cnn.pth")
```

1. model.state_dict()：返回一个字典，键是参数名，值是参数张量。
2. • torch.save(obj, path)：把 Python 对象序列化保存到本地文件。

### 10.1加载模型

```python
# 先创建一个同结构的模型（此时是随机参数）
loaded_model = SimpleCNN().to(device)
# 加载保存的参数字典
# map_location=device 确保在 CPU 上也能加载 GPU 上训练的模型
loaded_model.load_state_dict(torch.load("cat_dog_cnn.pth", map_location=device))
# 切换到评估模式
loaded_model.eval()
print("✅ 模型已加载")
```

1. torch.load(path, map_location)：加载保存的文件。map_location 指定加载到哪个设备。
2. • model.load_state_dict(dict)：把参数字典灌入模型。

## 十一.迁移学习

### 11.1为什么用迁移学习

1. 小数据集（80 张）从零训练容易过拟合（训练准确率 86%，验证仅 60%）。
2. • ImageNet 预训练模型已经学会了通用的图像特征。
3. • 冻结卷积层，只替换最后的分类层，5 轮即可达 95%+ 准确率。
4. 10.2 加载预训练模型

### 11.2加载预训练模型

```python
# 从 torchvision 导入 models 模块
from torchvision import models

# 加载预训练好的 ResNet18 模型
# weights=... 表示加载在 ImageNet 上训练好的权重
transfer_model = models.resnet18(weights=models.ResNet18_Weights.DEFAULT)

# 冻结所有参数（不训练它们，只用于特征提取）
for param in transfer_model.parameters():
    # requires_grad=False 表示在反向传播时不更新这个参数
    param.requires_grad = False

# 替换最后一层全连接层
# 原来是输出 1000 类（ImageNet），现在改成 2 类（猫、狗）
# fc.in_features 是原模型最后一层的输入维度（ResNet18 是 512）
transfer_model.fc = nn.Linear(transfer_model.fc.in_features, 2)

# 把模型搬到设备上
transfer_model = transfer_model.to(device)

# 打印成功信息
print("✅ 迁移学习模型已就绪")
```

1. models.resnet18(weights=...)：加载 ResNet18 网络。weights=models.ResNet18_Weights.DEFAULT 表示加载默认的预训练权重。
2. • for param in ...: param.requires_grad = False：冻结参数。告诉 PyTorch 在反向传播时不要计算这些参数的梯度，也不要更新它们。这可以节省大量算力，并防止小数据集过拟合。
3. • transfer_model.fc = nn.Linear(...)：替换最后一层全连接层。fc 是 ResNet18 全连接层的属性名。

### 11.3为ResNet18准备224*224的数据通道

```python
# ResNet18 要求输入尺寸是 224x224，重新定义 Transform
train_transform_224 = transforms.Compose([
    # 缩放到 224x224
    transforms.Resize((224, 224)),
    # 随机翻转做数据增强
    transforms.RandomHorizontalFlip(),
    # 转张量
    transforms.ToTensor(),
    # 标准化
    transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225]),
])

# 验证集 Transform（不做随机增强）
val_transform_224 = transforms.Compose([
    transforms.Resize((224, 224)),
    transforms.ToTensor(),
    transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225]),
])

# 重新加载数据集，应用新的 Transform
train_dataset_t = datasets.ImageFolder(root=train_dir, transform=train_transform_224)
val_dataset_t = datasets.ImageFolder(root=val_dir, transform=val_transform_224)

# 重新创建 DataLoader
train_loader_t = DataLoader(train_dataset_t, batch_size=8, shuffle=True, num_workers=0)
val_loader_t = DataLoader(val_dataset_t, batch_size=8, shuffle=False, num_workers=0)

print("✅ 224x224 数据管道已就绪")
```

逻辑和之前的 128x128 管道完全一致，只是把 Resize 换成了 (224, 224)。这体现了数据管道代码结构是通用的，改个尺寸就能复用

### 11.4训练迁移学习模型

```python
# 只优化最后一层全连接层的参数（因为其他层被冻结了）
optimizer_t = optim.Adam(transfer_model.fc.parameters(), lr=1e-3)
# 损失函数同样是交叉熵
criterion_t = nn.CrossEntropyLoss()

# 迁移学习通常 5 轮就够了
num_epochs_t = 5
# 记录指标
history_t = {"train_loss": [], "train_acc": [], "val_loss": [], "val_acc": []}

# 循环 5 轮
for epoch in range(num_epochs_t):
    # 训练一轮（复用之前的 train_one_epoch 函数）
    tr_loss, tr_acc = train_one_epoch(transfer_model, train_loader_t, criterion_t, optimizer_t, device)
    # 验证一轮（复用 evaluate 函数）
    va_loss, va_acc = evaluate(transfer_model, val_loader_t, criterion_t, device)
    
    # 记录数据
    history_t["train_loss"].append(tr_loss)
    history_t["train_acc"].append(tr_acc)
    history_t["val_loss"].append(va_loss)
    history_t["val_acc"].append(va_acc)
    
    # 打印本轮结果
    print(f"Epoch [{epoch+1:02d}/{num_epochs_t}] "
          f"train_loss={tr_loss:.4f} train_acc={tr_acc:.4f} | "
          f"val_loss={va_loss:.4f} val_acc={va_acc:.4f}")
```

1. optimizer_t = optim.Adam(transfer_model.fc.parameters(), ...)：注意传入的是 transfer_model.fc.parameters()，只优化最后一层。因为前面的卷积层被 requires_grad=False 冻结了，不会更新。这样训练速度更快，也不容易过拟合。

测试？
