# 个人博客说明与渲染



## 文件说明

1. config.toml: 全局配置文件；

2. content: 博客内容存档；

3. themes: 定制化主题；



## 渲染代码

1. 包含VPN的渲染代码：

```
DATE=`date +"%Y%m%d"`

git branch && hugo -D && \
mv ./public ../public_master_${DATE} && \
rm -rf ./resources && \
git checkout master && \
rm -rf * && \
mv ../public_master_${DATE}/* ./ && \
rm -rf ../public_master_${DATE}
```


2. 不包含VPN的渲染代码：

```
DATE=`date +"%Y%m%d"`

git branch && \
rm ./content/note/20191101_shadowsocks.md && rm ./content/note/20200301_vpn_2.md && \
hugo -D && \
mv ./public ../public_master_noVPN_${DATE} && \
git restore ./content/note/20191101_shadowsocks.md && git restore ./content/note/20200301_vpn_2.md && \
rm -rf ./resources && \
git checkout master_noVPN && \
rm -rf * && \
mv ../public_master_noVPN_${DATE}/* ./ && \
rm -rf ../public_master_noVPN_${DATE}
```
