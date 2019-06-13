# Numpy
## 理解Python中的数据类型
### 固定类型数组
```python {cmd=true}
import array
L = list(range(10))
A = array.array('i', L)
print(A)
```
### 从Python列表创建数组
```python {cmd=true}
import numpy as np
