---

---
# 目录
## [py文件打包为exe文件](#PyInstaller的使用)


## PyInstaller的使用

安装

    C:\Python38\Scripts\pip.exe install PyInstaller

开始打包

    C:\Python38\Scripts\pyinstaller.exe pyfile.py
    C:\Python38\Scripts\pyinstaller.exe pyfile.spec

常见错误：  
1.次数不够，在spec文件中增加次数，添加以下代码：

    # -*- mode: python ; coding: utf-8 -*-
    import sys
    sys.setrecursionlimit(5000)
    block_cipher = None

然后py替换运行spec文件