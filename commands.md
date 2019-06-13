# mysql
mysql -u root -p

mysql> GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%' IDENTIFIED BY 'mypassword' WITH GRANT OPTION;
mysql> GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.1.3' IDENTIFIED BY 'mypassword’ WITH GRANT OPTION;
flush privileges;
# conda
环境
activate env
conda deactivate
conda create --name your_env_name
conda create --name your_env_name python=3.5
conda create --name your_env_name python=3.5 numpy scipy
conda info --envs
conda env list
conda remove --name your_env_name --all
包
conda install numpy=1.10
conda remove package_name  
conda update package_name  
conda update –all 
conda search search_term
# powershell
大小写不敏感
.\fileName 
clear|cls
ls
conda install -n root -c pscondaenvs pscondaenvs
运行之后可以在powershell里 activate envs
#cmd
常用命令
D:
dir


ssh忽略kownhosts
~/.ssh/config
StrictHostKeyChecking no
UserKnownHostsFile /dev/null




#colab
!apt-get install -y -qq software-properties-common python-software-properties module-init-tools
!add-apt-repository -y ppa:alessandro-strada/ppa 2>&1 > /dev/null
!apt-get update -qq 2>&1 > /dev/null
!apt-get -y install -qq google-drive-ocamlfuse fuse
from google.colab import auth
auth.authenticate_user()
from oauth2client.client import GoogleCredentials
creds = GoogleCredentials.get_application_default()
import getpass
!google-drive-ocamlfuse -headless -id={creds.client_id} -secret={creds.client_secret} < /dev/null 2>&1 | grep URL
vcode = getpass.getpass()
!echo {vcode} | google-drive-ocamlfuse -headless -id={creds.client_id} -secret={creds.client_secret}
!mkdir -p drive
!google-drive-ocamlfuse drive  -o nonempty
import os
os.chdir('drive/BERT')
!ls
!python run_classifier.py \
  --task_name=my \
  --do_train=true \
  --do_eval=true \
  --data_dir=data \
  --vocab_file=chinese_L-12_H-768_A-12/vocab.txt \
  --bert_config_file=chinese_L-12_H-768_A-12/bert_config.json \
  --init_checkpoint=chinese_L-12_H-768_A-12/bert_model.ckpt \
  --max_seq_length=64 \
  --train_batch_size=32 \
  --learning_rate=5e-5 \
  --num_train_epochs=3.0 \
  --output_dir=output 



%matplotlib inline
%matplotlib notebook



linux下
nginx相关：
/etc/nginx/nginx.conf是核心配置文件
nginx -t
service nginx start
service nginx stop
lsof -i:80
kill -9 xxxx

启动uwsgi：uwsgi --ini uwsgi.ini
停止uwsgi：uwsgi --stop uwsgi.pid
重新加载配置：uwsgi --reload uwsgi.pid

cls清屏 exit退出
powershell 
where.exe 
wget
ls

cmd
where



2019.3.24 anaconda最新版本3-2018.12在安装后不能直接conda安装库，显示错误原因为ssherror
解决办法：下载 https://slproweb.com/products/Win32OpenSSL.html 页面的openssl安装包
安装openssl后解决

解决途径来源 https://github.com/ContinuumIO/anaconda-issues/issues/6424
Loading channels: failed
CondaHTTPError: HTTP 000 CONNECTION FAILED for url <https://repo.anaconda.com/pkgs/main/win-64/repodata.json.bz2>
Elapsed: -
An HTTP error occurred when trying to retrieve this URL.
HTTP errors are often intermittent, and a simple retry will get you on your way.
If your current network has https://www.anaconda.com blocked, please file
a support request with your network engineering team.
SSLError(MaxRetryError('HTTPSConnectionPool(host=\'repo.anaconda.com\', port=443): Max retries exceeded with url: /pkgs/main/win-6
4/repodata.json.bz2 (Caused by SSLError("Can\'t connect to HTTPS URL because the SSL module is not available."))'))


powershell conda激活环境的方法
conda install -n root -c pscondaenvs pscondaenvs



hexo new some
hexo d -g



最新的anaconda对powershell默认支持但是在cmd中需要conda activate base
激活conda activate base conda deactivate
权限放开
C:\Users\Si tu m'aimes\.conda\envs\ml

python manage.py makemigrations
python manage.py migrate