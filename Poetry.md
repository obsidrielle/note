
## 1. Poetry 的安装

Poetry 是 python 的声明式包管理工具, 假如已经安装了 python，使用以下命令安装 Poetry

```python
pip install poetry
```

## 2. Poetry 的使用
### 2.1 初始化项目

在项目文件夹打开控制台，然后使用 `poetry init` 命令初始化 Poetry 项目。输入后会跳出一连串的互动：

![[Pasted image 20250311230424.png]]

如果不知道什么意思，一直回车即可。这些输入的意义分别是：

- Package name：包名，即项目名
- Version：版本号
- Description：项目秒数
- Author ：作者
- License：开源许可证
- Compatible Python versions：python 版本，这个比较重要，根据你需要的包依赖的 python 版本来选，如果不知道直接默认
- 剩下的跳过即可

### 2.2 管理虚拟环境

在使用 Poetry 时，会创建一个虚拟环境，默认是在 C 盘下的某个目录，因此先使用 `poetry config virtualenvs.in-project true` 命令指定让虚拟环境创建到项目根目录下。

然后使用 `poetry env use python` 便可以创建虚拟环境。

### 2.3 使用虚拟环境

使用 `poetry env activate` 来进入虚拟环境，`exit` 退出虚拟环境。
值得一提的是，上面创建虚拟环境不是必须的，因为这里进入的时候会自动创建一个。下面使用 poetry 的命令都需要在这个虚拟环境使用。包会安装在根目录下面的 `.venv` 文件夹中。

### 2.4 安装包

在安装包之间，防止网络问题，先换镜像源：

```python
poetry source add tsinghua https://pypi.tuna.tsinghua.edu.cn/simple 
# 命令不换行
```

然后使用 `poetry add <package-name>` 来安装包，例如 `poetry add requests`。
类似于 `pip` 的使用方式。

然后 `pyproject.toml` 中的内容也会变化：

![[Pasted image 20250311231225.png]]

还有一种安装的方式是，将要安装的依赖写到 `dependencies` 中，然后使用 `poetry install` 全部安装。

### 2.5 更新包

更新包使用 `poetry update` 即可。如果是更新指定模块，使用 `poetry update <package-name>`。

### 2.6 移除包

使用 `poetry remove <package-name>` 移除包，`poetry` 会自动处理依赖问题，所以可以放心地移除包。

### 2.7 列出安装的包

使用 `poetry show` 命令。

![[Pasted image 20250311231500.png]]

### 2.8 结语

使用 `poetry` 管理 python 的包比使用 pip 和 conda 更为方便且更加轻量化，而且用起来也不难。若需要把项目迁移到别的地方，只需要一个 `pyproject.toml` 就可以很好地处理依赖问题了。



