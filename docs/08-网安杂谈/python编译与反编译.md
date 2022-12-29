
# 编译
创建一个python文件<br />内容为：
```python
def add(a,b):
    return a + b

if __name__ =='__main__':
    print(add(1,1))
```

运行结果
![image.png](_img/assets/1655796689540-ebf32fbf-e276-4a89-99f9-84894380e96f.png)

使用pip安装库（这里我已经安装）
```
pip install pyinstaller
```
![image.png](_img/assets/1655796697320-d3723584-065c-439e-9185-ac015b2ba8f6.png)

打包为exe文件
```
pyinstaller -F -w test.py
```
![image.png](_img/assets/1655796707175-5a8ca3dd-b64d-4e35-8414-101acfdad854.png)

查看一下文件结构
![image.png](_img/assets/1655796715780-acd6049e-eacb-4d4d-9ed5-15d7e80e4fec.png)


# 反编译
新建一个文件夹命名为test2将编译好的exe文件放入其中<br />在github上下载反编译文件<br />[https://github.com/extremecoders-re/pyinstxtractor](https://github.com/extremecoders-re/pyinstxtractor)

反编译
```
python pyinstxtractor.py test.exe
```
![image.png](_img/assets/1655796734319-22b3b789-b5e5-4986-961d-a3690c155084.png)

反编译后的文件都在这个文件夹中
![image.png](_img/assets/1655796743013-f1013684-e796-4783-979f-f0f4d399dd7a.png)

找到文件
![image.png](_img/assets/1655796749951-6bc8a9fe-e77d-4771-ac58-88727211eb5d.png)

安装uncompyle库（这里我已经安装）
```
pip install uncompyle
```
![image.png](_img/assets/1655796756340-fe1c2d02-61d8-4ea7-b312-33d67adc0257.png)

反编译
```
uncompyle6 test.pyc > test.py
```
![image.png](_img/assets/1655796764671-0292c7f1-adb6-4403-bcf5-36010e76a9a7.png)
![image.png](_img/assets/1655796770404-dd79dad4-46f7-4ffc-8f4d-e51b89ebe003.png)

成功得到test.py文件
