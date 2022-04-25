#BombLab使用指南
- **文件结构** 

BombLab有三个文件：bomb、bomb.c以及README文件，其中bomb为我们主要关注的文件，它是一个可执行的二进制文件（Linux下的ELF文件），所以我们在实验时，需要使用Linux环境。

- **实验环境与所需工具**

我们打开虚拟机，开启Linux虚拟机，这里我本人使用的是Kali，但是推荐使用Ubuntu，把文件放进虚拟机中。此处需要注意的是，我们尽量在虚拟机中安装gdb（**安装命令为：apt-get install gdb**），为后续实验方便做准备。有不懂gdb命令的可自行查阅文档：[GDB命令文档](http://csapp.cs.cmu.edu/3e/docs/gdbnotes-x86-64.pdf)