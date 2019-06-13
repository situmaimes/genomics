sratoolkit
cd src
wget https://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.8.2-1/sratoolkit.2.8.2-1-ubuntu64.tar.gz
tar -zxvf sratoolkit.2.8.2-1-ubuntu64.tar.gz
mv sratoolkit.2.8.2-1-ubuntu64 ~/biosoft
# 加入环境变量
echo 'PATH=$PATH:~/biosoft/sratoolkit.2.8.2-1-ubuntu64/bin' >> ~/.bashrc
# 测试
prefetch -v
# 尝试下载，默认存放在家目录下的ncbi文件夹中
prefetch -c SRR390728


fastqc
# 判断系统是否安装java
java -version
# 安装java， 请改成openjdk-9-jdk，下面的是错误演示
sudo apt install  openjdk-9-jre-headless
# 验证
java -version
# openjdk version "9-internal"
# OpenJDK Runtime Environment (build 9-internal+0-2016-04-14-195246.buildd.src)
# OpenJDK 64-Bit Server VM (build 9-internal+0-2016-04-14-195246.buildd.src, mixed mode)
# 安装fastqc
cd src
wget http://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.5.zip
unzip fastqc_v0.11.5.zip
mv FastQC/ ~/biosoft/
cd ~/biosoft/FastQC/
chmod 770 fastqc
# 添加环境变量， 我用sed修改
sed -i '/^PATH/s/\(.*\)/\1:~\/biosoft\/FastQC\//' ~/.bashrc
source ~/.bashrc
fastqc -v
# FastQC v0.11.5
https://www.bioinformatics.babraham.ac.uk/projects/fastqc/




使用bioconda可以更便捷的安装