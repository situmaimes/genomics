常用代码：
s.cookies = requests.utils.cookiejar_from_dict(cookies)



库
1. kivy 编写全平台应用 https://kivy.org/#home https://thief.one/2018/05/08/1/
2. wget
3. pandulum 时间处理
4. flashtext 句子中关键词提取
5. fuzzywuzzy 字符串匹配比较
6. pyflux 时间序列分析
7. ipyvolume 在notebook中可视化3d
8. dash 数据可视化
9. gym 开发和对比强化学习算法
10. MoviePy  用python编辑视频
11. JupyterLab
12. Pyfancy-2 美化终端输入
13. Bitstream 二进制数据处理
14. Python Fire 命令行页面创建
15. Face Recognition 人脸识别
16. PyeCharts 图表库
17. PipEnv pip与virtualenv的合成
18. Better Exceptions 调试方便
19. cacheout python缓存库

https://annual2017.pycourses.com/#1


机器学习
X-DeepLearning
bert


from django.contrib.auth.models import User
user = User.objects.get(pk=1)

user.set_password('your_new_password')
user.save()

越过csrf验证
@csrf_exempt

pip freeze > requirements.txt


yellowbrick


jupyter安装自动补全
pip install jupyter_contrib_nbextensions
jupyter contrib nbextension install --user --skip-running-check