# datax踩坑记录

## 部署
[部署文档](https://blog.csdn.net/xch_yang/article/details/128223489)

## 修改~/modules/datax-executor/bin/env.properties中指定的datax路径

> 修改以下语句为你的datax.py路径
> PYTHON_PATH=/home/datax_all/datax/bin/datax.py

## 连接数据库失败. 请检查您的 账号、密码、数据库名称、IP、Port或者向 DBA 寻求帮助(注意网络环境)
> [来源](https://github.com/alibaba/DataX/issues/299)
> 1. datax-web默认支持到mysql5.7，不修改libs并重新打包的话，不支持8.0
> 
> 1. 首先检查数据库的连接地址、账号、密码等，如果都正确则参考以下方法
> 
> 1. 因为json配置文件中的writer中的 jdbcUrl 不能使用[]中括号括起来，即表示只能有一个写入的库。
## 提供的配置文件[.../plugin.json]不存在. 请检查您的配置文件
>删除.临时文件即可
>在datax/plugin目录下运行
>find ./* -type f -name ".*er"  | xargs rm -rf
