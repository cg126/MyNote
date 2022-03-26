# WSL2 下安装pytorch


1. 安装Nvidia驱动（Nvidia官网下载）
2. 安装WSL以及linux发行版（微软官方文档）
3. 安装nvidia-cuda-toolkit（via apt install）
4. 安装Pytorch（via conda or pip）
5. 验证

### Cuda & Cudnn：

Cuda使得显卡进行复杂的并行运算
Cudnn是针对深度卷积神经网络的加速库

### conda与pip的联系：

在conda创造的环境中使用pip安装的软件仍在conda的环境中

## 验证：

```python
python3
import torch
torch.cuda.is_available()
```