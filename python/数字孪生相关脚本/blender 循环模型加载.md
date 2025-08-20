# blender 循环模型加载

blender版本需要再 3.6 版本上下

```py
import bpy
import os
def batch_import_obj(directory):
    # 遍历给定文件夹及其所有子文件夹
    for root, dirs, files in os.walk(directory):
        for file in files:
            if file.endswith(".obj"):
                file_path = os.path.join(root, file)
                bpy.ops.import_scene.obj(filepath=file_path)

# 设置你想从中导入OBJ文件的父文件夹路径
directory = "C:\\Users\\15031\\Desktop\\xx\\lab5" # 请替换为你的文件夹路径
batch_import_obj(directory)
```

