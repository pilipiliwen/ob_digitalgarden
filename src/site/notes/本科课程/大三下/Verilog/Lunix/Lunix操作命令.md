---
{"dg-publish":true,"permalink":"/本科课程/大三下/Verilog/Lunix/Lunix操作命令/"}
---


# 1. VNC 服务器登录

- 服务器 IP 及配置信息表

| 服务器型号        | 服务器名  | IP 地址       | 配置                               | 备注                           |
| ------------ | ----- | ----------- | -------------------------------- | ---------------------------- |
| DELL R740    | SMW1  | 10.32.86.11 | 2 CPU @ 3.10GHz，共计 40 核心 512G 内存 | 安装系统 RHEL 7.9                |
| DELL R740    | SMW2  | 10.32.86.12 | 同上                               | 同上                           |
| DELL R740    | SMW3  | 10.32.86.13 | 同上                               | 同上                           |
| DELL R740    | SMW4  | 10.32.86.14 | 同上                               | 同上                           |
| DELL R740    | SMW5  | 10.32.86.15 | 同上                               | 同上                           |
| DELL R930    | SMW6  | 10.32.86.16 | 4 CPU @2.2GHz，共计56 核心，256G 内存    | Windows Server R12，微电子工艺教学专用 |
| H3C R4950 G5 | SMW7  | 10.32.86.37 | 2 CPU @2.7GHz， 共计 128 核心， 1T 内存  | 安装系统 RHEL 7.9                |
| H3C R4950 G5 | SMW8  | 10.32.86.38 | 同上                               | 同上                           |
| H3C R4950 G5 | SMW9  | 10.32.86.39 | 同上                               | 同上                           |
| H3C R4950 G5 | SMW10 | 10.32.86.40 | 同上                               | 同上                           |
| 联想 SR665 V3  | SMW11 | 10.32.86.17 | 2CPU @AMD9654共计 192 核心， 1T 内存    | 安装系统 RHEL 8.10               |


- Xshell 创建端口： `vncserver -geometry 1920x1080 ：`
- Xshell 查找端口号：
	- `法一：ps -aux | grep vncserver | grep Xorg| grep bs2022xxxxxxxx`
```
	法二：
	cd 
	cd .vnc/
	ls -al -rt
	看后缀为pid的文件
```
- 退出端口： `vncserver - kill：xxxx (xxxx为你的登录端口号)`

---
- 环境设置： `source /SM01/eda/env_set/bashrc_cds`
- 服务器安装软件及版本一览表

| 软件供应商               | 工具名称        | 缺省使用版本  |
| ------------------- | ----------- | ------- |
| cadence             | VIRTUOSO    | IC618   |
| Cadence             | SIGRITY     | 2017    |
| Cadence             | ASSURA      | 4.14    |
| Cadence             | CONFRML     | 18.2    |
| Cadence             | CTOS        | 14.2    |
| Cadence             | INCISIVE    | 15.2    |
| cadence             | INNOVUS     | 15.2    |
| Cadence             | JLS         | 19.1    |
| Cadence             | SPECTRE     | 19.1    |
| Cadence             | XCELIUM     | 1809    |
| Synopsys            | Finesim     | 2022.12 |
| Synopsys            | Formality   | 2022.12 |
| Synopsys            | Hspice      | 2022.12 |
| Synopsys            | icc         | 2022.12 |
| Synopsys            | Icc2        | 2022.12 |
| Synopsys            | icvalidator | 2022.12 |
| Synopsys            | libcompiler | 2022.12 |
| Synopsys            | pt          | 2022.12 |
| Synopsys            | spyglass    | 2022.12 |
| Synopsys            | starrc      | 2022.12 |
| Synopsys            | syn         | 2022.12 |
| Synopsys            | tx          | 2022.12 |
| Synopsys            | vcs         | 2022.12 |
| Synopsys            | Vcs-mx      | 2022.12 |
| Synopsys            | Verdi       | 2022.12 |
| Mentor<br/>Graphics | Calibre     | 2020.4  |
| Silvaco             | ICCAD       | 2021    |
| Silvaco             | AMS         | 2021    |
| Silvaco             | TCAD        | 2021    |
| Tanner              | Ledit       | 2021    |
| Tanner              | Sedit       | 2021    |
| Tanner              | Tspice      | 2021    |
| Keysight            | ADS 全套      | 2021    |
| COMSOL              | 全套          | 2021    |
| Matlab              | 全套          | 2021a   |
- 工艺库目录：`/SM01/foundrt/xxx/yyy/.`
- 工艺库安装：在工作目录下运行相应的安装脚本
	`/SM01/foundry/smic/N130/pdkinstall.sh`
- 工艺库卸载：在工作目录下运行相应的卸载脚
	`/SM01/foundry/smic/N130/pdkUnstall.sh`

```
	ncverilog使用：
	复制standand_new_file文件夹
	修改question3_1.v中的Verilog代码
	
	source /SM01/eda/env_set/bashrc_cds
	source /SM01/eda/env_set/bashrc_synopsys 
	./ncverilog.verdi.demo.run 
	
	verdi &
	
```
# 2. Lunix 常用操作命令

## 2.1 目录操作
### 2.1.1 切换目录（cd）
```
Cd /                 //切换到根目录
Cd /bin              //切换到根目录下的 bin 目录
Cd ..               //切换到上一级目录或者使用命令：cd ..
Cd ~                 //切换到 home 目录
Cd -                 //切换到上次访问的目录
Cd xx (文件夹名)       //切换到本目录下的名为 xx 的文件目录，如果目录不存在报错
Cd /xxx/xx/x         //可以输入完整的路径，直接切换到目标目录，输入过程中可以使用 tab 键快速补全
```
### 2.1.2 查看目录（ls）
```
ls                   //查看当前目录下的所有目录和文件
ls -a                //查看当前目录下的所有目录和文件（包括隐藏的文件）
ls -l                //列表查看当前目录下的所有目录和文件（列表查看，显示更多信息），与命令"ll"效果一样
ls /bin              //查看指定目录下的所有目录和文件 
```

### 2.1.3 创建目录（mkdir）
```
mkdir tools          //在当前目录下创建一个名为tools的目录
mkdir /bin/tools     //在指定目录下创建一个名为tools的目录
```
### 2.1.4 删除目录（rm）
```
rm 文件名              //删除当前目录下的文件
rm -f 文件名           //删除当前目录的的文件（不询问）
rm -r 文件夹名         //递归删除当前目录下此名的目录
rm -rf 文件夹名        //递归删除当前目录下此名的目录（不询问）
rm -rf *              //将当前目录下的所有目录和文件全部删除
```
### 2.1.5 修改目录 （mv）
```
mv 当前目录名 新目录名        //修改目录名，同样适用与文件操作
mv /usr/tmp/tool /opt       //将/usr/tmp目录下的tool目录剪切到 /opt目录下面
mv -r /usr/tmp/tool /opt    //递归剪切目录中所有文件和文件夹
```
### 2.1.6 搜索目录（find）
```
find /bin -name 'a*'        //查找/bin目录下的所有以a开头的文件或者目录
```
### 2.1.7 查看当前目录（pwd）
```
pwd                         //显示当前位置路径
```
## 2.2 文件操作
### 2.2.1 新增文件（touch）
```
touch  a.txt         //在当前目录下创建名为a的txt文件（文件不存在），如果文件存在，将文件时间属性修改为当前系统时间
```
### 2.2.2 编辑文件（vi、vim）
```
vi 文件名              //打开需要编辑的文件
--进入后，操作界面有三种模式：命令模式（command mode）、插入模式（Insert mode）和底行模式（last line mode）
命令模式
-刚进入文件就是命令模式，通过方向键控制光标位置，
-使用命令"dd"删除当前整行
-使用命令"/字段"进行查找
-按"i"在光标所在字符前开始插入
-按"a"在光标所在字符后开始插入
-按"o"在光标所在行的下面另起一新行插入
-按"："进入底行模式
插入模式
-此时可以对文件内容进行编辑，左下角会显示 "-- 插入 --""
-按"ESC"进入底行模式
底行模式
-退出编辑：      :q
-强制退出：      :q!
-保存并退出：    :wq
## 操作步骤示例 ##
1.保存文件：按"ESC" -> 输入":" -> 输入"wq",回车     //保存并退出编辑
2.取消操作：按"ESC" -> 输入":" -> 输入"q!",回车     //撤销本次修改并退出编辑
## 补充 ##
vim +10 filename.txt                   //打开文件并跳到第10行
vim -R /etc/passwd                     //以只读模式打开文件
```
### 2.2.3 查看文件
```
cat a.txt          //查看文件最后一屏内容
less a.txt         //PgUp向上翻页，PgDn向下翻页，"q"退出查看
more a.txt         //显示百分比，回车查看下一行，空格查看下一页，"q"退出查看
tail -100 a.txt    //查看文件的后100行，"Ctrl+C"退出查看
```
## 2.3 文件权限
```
文件权限简介：'r' 代表可读（4），'w' 代表可写（2），'x' 代表执行权限（1），括号内代表"8421法"
##文件权限信息示例：-rwxrw-r--
-第一位：'-'就代表是文件，'d'代表是文件夹
-第一组三位：拥有者的权限
-第二组三位：拥有者所在的组，组员的权限
-第三组三位：代表的是其他用户的权限

普通授权    chmod +x a.txt    
8421法     chmod 777 a.txt     //1+2+4=7，"7"说明授予所有权限
```
## 2.4 打包与解压
```
.zip、.rar        //windows系统中压缩文件的扩展名
.tar              //Linux中打包文件的扩展名
.gz               //Linux中压缩文件的扩展名
.tar.gz           //Linux中打包并压缩文件的扩展名


tar -zcvf 打包压缩后的文件名 要打包的文件
参数说明：z：调用gzip压缩命令进行压缩; c：打包文件; v：显示运行过程; f：指定文件名;
示例：
tar -zcvf a.tar file1 file2,...      //多个文件压缩打包


tar -zxvf a.tar                      //解包至当前目录
tar -zxvf a.tar -C /usr------        //指定解压的位置
unzip test.zip             //解压*.zip文件 
unzip -l test.zip          //查看*.zip文件的内容 
```

## 2.5 其它常用指令
### 2.5.1 查找（find）
```
find . -name "*.c"     //将目前目录及其子目录下所有延伸档名是 c 的文件列出来
find . -type f         //将目前目录其其下子目录中所有一般文件列出
find . -ctime -20      //将目前目录及其子目录下所有最近 20 天内更新过的文件列出
find /var/log -type f -mtime +7 -ok rm {} \;     //查找/var/log目录中更改时间在7日以前的普通文件，并在删除之前询问它们
find . -type f -perm 644 -exec ls -l {} \;       //查找前目录中文件属主具有读、写权限，并且文件所属组的用户和其他用户具有读权限的文件
find / -type f -size 0 -exec ls -l {} \;         //为了查找系统中所有文件长度为0的普通文件，并列出它们的完整路径
```
### 2.5.2 复制 （cp）
```
cp [选项] 源文件 目标文件

**复制文件到目标目录**
cp file.txt /path/to/destination/

**复制文件并重命名**
cp file.txt /path/to/destination/newfile.txt
```

### 2.5.3 终端放大缩小
```
放大：ctrl+shift+=  
缩小：ctrl±
```














