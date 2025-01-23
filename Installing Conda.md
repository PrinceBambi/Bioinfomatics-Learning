# 获取安装脚本
`cd dir` \#进入安装目录

`wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-x86_64.sh` \#从urls下载Miniconda3-latest-linux-x86_64.sh

# 安装并激活
`bash Miniconda3-latest-Linux-x86_64.sh` \#安装conda

`source ~/.bashrc` \#激活conda

# 配置conda
`conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/cloud/bioconda/` \#配置channel镜像

`conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/cloud/conda-forge/` \#配置channel镜像

`conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/pkgs/free/` \#配置channel镜像

`conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/pkgs/main/` \#配置channel镜像

`conda config --set show_channel_urls yes` \#设置安装包时显示channel urls

# 安装包
`conda install name -y` \#安装名为name的软件

`conda install name=version -y` \#安装特定版本的软件

`name --help` \#出现帮助信息，表明安装成功

# 使用conda环境
`conda info --envs` \#输出conda环境列表

`conda create -n name python=version pkg1 pgk2` \#创建名为name的conda环境，设置python版本，安装需要的包

`conda activate name` \#激活创建的conda环境

`conda deactivate` \#退出conda环境


