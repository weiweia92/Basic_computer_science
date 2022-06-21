## CONDA

### Conda

**Package Manager+Environment Manager**  

![](pic\1597196730200-ae2a99fa-3216-4083-9275-e83565727a23.jpeg)

conda包和环境管理器包含于Anaconda的所有版本当中。  

Package Manager：当安装一个包的时候，依赖包  

Environment Manager：create multiple virtual environment  

conda为Python项目而创造，但可适用于上述的多种语言。  

#### System

- 更新conda:`conda update conda`

- 更新anaconda:`conda update anaconda`

- 更新python:`conda update python `

- 查看conda版:`conda --version`

- 查看当前系统中已经安装了哪些环境:`conda info -e`

- 查看当前系统中已安装了哪些环境:`conda env list`

- 通过yml文件创建新的conda环境:`conda env create -f environment.yml`

- 切换到名字为ai37的环境下:`source activate ai37`

- 跳出ai37环境，返回到住环境:`source deactivate ai37`

- 删除名为ai37的环境:`conda remove --name ai37 --all`

#### Environment & package

- 查看当前环境所有包:`conda list`

- 查看某个环境已安装的包:`conda list -n <env_name> `

- 在当前环境下安装包:`conda install <package_name>`

- 搜索package信息:`conda search <package_name>`

- 更新指定环境的某个包:`conda update -n <env_name> <package_name>`

- 删除指定环境的某个包:`conda remove -n <env_name> <package_name> `

- 安装package到指定的环境, 如果不用-n指定环境名称，则被安装在当前活跃环境,也可以通过-c指定通过某个channel安装:`conda install -n py35 numpy `

#### Channels

channels:the path or the location where conda looks for the packages that we want to install.  

![](pic\1597197975996-3587c143-6fcb-48fd-ae67-502e93efc08e.jpeg)

- 查看当前channel:`conda config --show channels`

- 安装特定channel下的包:`conda install -c pytorch pytorch` (此pytorch channel仅使用了一次，并没有加入到channel list中) 

- 想默认channel中添加 condaforge.com 网址`conda config --add channels conda-forge`      
    - condaforge.com :A community-led collection of recipes, build infrastructure and distributions for the conda package manager

- 往默认channel中填加新的channel:`conda config -add channels pytorch`  
                              `conda install pytorch`      
                              `conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/`   
                              `conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/`  
                              `conda config --set show_channel_urls yes`  
                              
**We deactivate the environment,not the channel**

### pip vs conda\
### Anaconda

`Anaconda`是一个包含180+的科学包及其依赖项的发行版本。其包含的科学包包括：`conda, numpy, scipy, ipython notebook`等  

`Anaconda->conda`+环境管理器+1000+开源库  

### pip

`pip`是用于安装和管理软件包的包管理器。`pip`编写语言：`Python`。

`Python`中默认安装的版本：`Python 2.7.9`及后续版本默认安装命令为 `pip,Python 3.4`及后续版本默认安装命令为 `pip3`  

#### 1.依赖项检查

- `pip`：  
    - 不一定会展示所需其他依赖包      
    - 安装包时或许会直接忽略依赖项而安装，仅在结果中提示错误。  

- `conda`：  
    - 列出所需其他依赖包。  
    - 安装包时自动安装其依赖项。  
    - 可以便捷地在包的不同版本中自由切换。  

#### 2.环境管理

`pip`：维护多个环境难度较大  

`conda`：比较方便地在不同环境之间进行切换，环境管理较为简单  

#### 3.对系统自带Python的影响  

`pip`：在系统自带`Python`中包的更新/回退版本/卸载将影响其他程序    

`conda`：不会影响系统自带`Python`    

#### 4.适用语言

`pip`：仅适用于`Python ` 

`conda`：适用于`Python, R, Ruby, Lua, Scala, Java, JavaScript, C/C++, FORTRAN  `

`conda`结合了`pip`和`virtualenv`的功能   