# 加载数据

**dataset**：提供一种方式去获取数据及其label

- ​	如何获取每一个数据及其lable
- ​	告诉我们一共有多少数据

**dataloader：**为后面的网络提供不同的数据形式

**transboard：**

```python
from torch.utils.tensorboard import SummaryWriter
from PIL import Image
import  numpy as np
image_path="dataset/train/ants_images/45472593_bfd624f8dc.jpg"
img_PIL=Image.open(image_path)
img_array = np.array(img_PIL)
writer = SummaryWriter(log_dir='logs')
print(type(img_array))
print(img_array.shape)
writer.add_image("train",img_array,1,dataformats='HWC')
for i in range(99):
    writer.add_scalar('y=2x', 3*i, i)
# writer.add_scalar()
writer.close()
```

**transforms:**对图片进行一些变化

transform.Totensor去看两个问题

1. transform该如何使用
2. 为什么我们需要tensor类型

```python
from PIL import Image
from torch.utils.tensorboard import SummaryWriter
from torchvision import transforms

img_path="dataset/train/ants_images/0013035.jpg"
img=Image.open(img_path)
writer=SummaryWriter("logs")

tensor_trans=transforms.ToTensor()
tensor_img=tensor_trans(img)
print(tensor_img)
writer.add_image("Tensor_img",tensor_img)
writer.close()
```

