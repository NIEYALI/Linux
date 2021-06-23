# [linux]shell 环境的熟悉和使用

### 问题

#### 一、在每次通过远程连接进入系统是，告知用户下面的信息：

>1. 最近一段时间，当前用户，也就是你，登录了多少次
>2. 上一次登录系统，停留了多少时间
>3. 给用户推荐一句名人名言，唐诗三百首之类的
>4. 最好还能告知一下当地当日天气预报
>5. 一个温馨的问候

#### 二、要求显示美观，有一定颜色

### 安装fortune软件

`sudo apt install fortune`

### 代码

### .zlogout文件

```shell
#记录登陆时间
logouttime=`date +%s`

staytime=$[${logouttime}-${logintime}]
echo ${staytime}
#计算时间时分秒
second=$[${staytime}%60]
hour=$[${staytime}/3600]
minute=$[$[${staytime}-${hour}*3600]/60]

#echo 上次在线时长 ${hour} 小时 ${minute} 分 ${second} 秒
#用文件记录时长并保存到记录文件.tips中
echo 上次在线时长 ${hour} 小时 ${minute} 分 ${second} 秒 > .tips

```



### .zlogin文件

```shell
#记录登陆时间
logintime=`date +%s`
#获取当前用户
name=`whoami`
num=`last | grep -w ${name} | wc -l`
#红字绿底闪烁
echo -e "====================\033[42;31;5;1m欢迎登陆\033[0m===================="
echo
#用户名蓝色
echo -e "\033[34;1m${name}\033[0m Hello Yali, Welcom Back!"
echo -e "您总共登陆了 \033[31;1m${num}\033[0m 次"
echo -e "上次在线时长：\033[34;1m${hour}\033[0m 小时 \033[35;1m${minute}\033[0m 分钟 \033[36;1m${second}\033[0m 秒"
echo
#蓝底白字高亮
place=beijing
echo -e "====================\033[44;37;1m天气预报\033[0m===================="
echo
#获取北京天气， 用curl爬取命令，只读取前7行
curl -o ./weather.log wttr.in/${place} 2>/dev/null
cat ./weather.log | head -n 7 | sed 's/Weather report/所在地区/' | sed "s/${place}/北京/"
echo
#紫红字黄底高亮
echo -e "====================\033[43;35;2;1m今日诗句\033[0m===================="
echo
#牛牛背古诗
fortune-zh | cowsay
echo
#温馨问候
echo -e "\033[35;1mHi, I will love you forever!"
echo
```

### 操作流程

- 当前用户：使用`whoami`命令保存在`name`中

- 登录次数

  ```shell
  name=`whoami`
  num=`last | grep -w ${name} | wc -l`
  ```

#### 获取登陆时长

- 记录登录时间：在.zlogin

  ```
  loginime=`date +"%s"` 
  ```

- 记录退出时间：在.zlogout

  ```
  logoutime=`date +"%s"` 
  ```


- 记录登陆时长，时分秒并记录到文件.tips中

  ```
  staytime=$[${logouttime}-${logintime}]

  second=$[${staytime}%60]
  hour=$[${staytime}/3600]
  minute=$[$[${staytime}-${hour}*3600]/60]
  echo 上次在线时长 ${hour} 小时 ${minute} 分 ${second} 秒 > .tips
  ```

### 获取当地天气

- `curl`命令爬取天气信息存储到`weather.log`使用`txt`文件会有列号

  ```shell
  curl -o ./weather.log wttr.in/${place} 2>/dev/null # 获取天气保存到weather.log文件中，将进度信息重定向到/dev/null
  ```


- 读取`weather.log`文件信息，只读取头7行，使用`sed`命令将里面的一些关键词替换，显得美观

  ```
  cat ./weather.log | head -n 7 | sed 's/Weather report/所在地区/' | sed "s/${place}/北京/" # 打印日期
  ```

### 获取诗句并用牛说

`fortune-zh | cowsay`

### 温馨问候

`echo -e "\033[35;1mHi, I will love you forever!"`

### 效果显示

![](C:\Users\yalnie\Desktop\Linux课程\aa.png)

