# 01_file



#### 1、创建目录

os.makedirs可以递归的创建目录结构

```python
import osos.makedirs('dirname', exist_ok=True)
```

exist\_ok = True指定了，如果某个要创建的目录已经存在，也不报错。

#### 2、删除文件或目录

os.remove可以删除一个文件

```lua
os.remove('xx.py')
```

shutil.rmtree() 可以递归的删除某个目录所有的子目录和子文件

```python
import shutilshutil.rmtree('tmp', ignore_errors=True)
```

参数ignore\_errors = True 保证如果目录不为空，不会抛出异常。

#### 3、拷贝目录

要拷贝一个目录里面所有的内容（包括子目录和文件、子目录里面的子目录和文件）到另一个目录中，可以使用shutil的copytree函数

```lisp
form shutil import copytreecopytree('d:/tools/aaa', 'e:/new/bbb')
```

拷贝前，目标目录必须不存在，否则会报错。

拷贝前，如果e:/new这个目录不存在，执行时会创建e:/new目录，再创建e:/new/bbb目录，再拷贝

拷贝前，如果e:/new存在，但是e:/new/bbb不存在，就只会创建e:/new/bbb，再拷贝

#### 4、修改文件名、目录名

```lua
os.rename('d:/tools/aaa', 'd:/tools/bbb')os.rename('d:/tools/first.py', 'd:/tools/second.py')
```

linux系统上，如果重命名之前d:/tools/second.py已存在，则会覆盖。

#### 5、对文件路径的操作

```lua
import ospath = '/user/beazley/data/data.csv'# 获取路径中的文件名部分os.path.basename(pat)	# 'data.csv'# 获取路径中的目录部分os.path.dirname(path)	# '/user/beazley/data'# 文件路径的拼接os.path.join('tmp', 'data', os.path.basename(path))# 'tmp/data/data.csv'
```

#### 6、判断文件、目录是否存在

```lua
os.path.exists('d:/systems/cmd.exe')os.paht.exists('d:/systems')
```

#### 7、判断是否是文件或目录

```lua
os.path.isfile('d:/systems/cmd.exe')os.path.isdir('d:systems')
```

#### 8、文件大小和修改日期

```lua
# 返回文件大小os.path.getsize('file')# 返回文件的最后修改日期，是秒时间os.path.getmtime('file')# 把秒时间转化为日期时间time.ctime(os.path.getmtime('/etc/passwd'))
```

#### 9、取当前工作目录

```lua
cwd = os.getcwd()# 切抽当前工作目录到另外的路径os.chdir(path)
```

#### 10、遍历目录下文件

```python
# 目标目录targetDir = r'd:/tmp/util/dist/check'files = []dirs = [] # dirpath:当前遍历到的目录名# dirnames:存放当前dirpath中的所有子目录名# filenames:存放当前dirpath中的所有文件名for(dirpath, dirnames, filenames) in os.walk(targetDir):    files += filenames    dirs += dirnamesprint(files)print(dirs)
```

获取目录下所有文件的全路径：

```python
targetDir = r'd:/tmp/util/dist/check'for(dirpath, dirnames, filenames) in os.walk(targetDir):    for fn in filenames:        # 把dirpath和每个文件名拼接起来    	fpath = os.path.join(dirpath, fn)
```

取目录中所有的文件和子目录名：

```python
targetDir = r'd:/tmp/util/dist/check'files = os.listdir(targetDir)print(files)
```

如果只需要获取目录中所有的文件，或只需要子目录：

```python
import osfrom os.path import isfile, join, isdirtargetDir = r'd:/tmp/util/dist/check'# 所有的文件print([f for f in os.listdir(targetDir) if isfile(join(targetDir, f))])# 所有的目录print([f for f in os.listdir(targetDir) if isdir(join(targetDir, f))])
```

#### 11、取目录中指定扩展名的文件和子目录

```python
import globexes = glob.glob(r'd:/tmp/*.txt')print(exes)
```

* 注意
1. python 运行的相对路径
2. vscode 运行、调试时路径


