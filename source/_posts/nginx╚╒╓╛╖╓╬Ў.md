title: 线上ngnix日志分析
date: 2016.11.04
categories: nginx #分类
tags: [nginx]
toc: true
---

## 安装goaccess
goaccess是免费开源的nginx日志分析神器，他可以分析很多有用的数据。
goaccess，你值得拥有。

### centos
```
sudo yum install goaccess
```
关于goaccess的使用方法，建议前往goacces官网.

## 配置goaccess.conf文件
必须配置下面三项
* log-format
* date-format
* time-format

示例文件如下：
```
$ vi goaccess.conf
log-format %d:%t %^ %h %s %T  %m %U %H %b
time-format %H:%S:%M
date-format %d/%b/%Y
```
注意，%T和%m之前有两个空格，这个很坑，goaccess对空格很敏感，一个都不能少
## 坑的说明
goaccess对日志格式要求非常严格，自定义的格式，想通过语法检查还是很难的，建议只导出少量关键日志即可。
goaccess必须要有的日志有：

* 时间
* 请求url
* 有效的IP地址

即：

```
a valid IPv4/6 %h
a valid date %d
the request %r
```

一定要听劝。只导出少量数据，差一个空格，goaccess都会无法解析的。！！！！

## 转换nginx日志格式

由于nginx日志有可能无法直接分析公司自定义的格式，因此，建议进行转换。
转换方法示例如下：

```
 awk -F "|" '{print $2,$3,$5,$6,$12,$14}' access_2016xxxx.log > new.log
```

## 开始分析

```
goaccess -f new.log -p goaccess.conf > 20161104.html
```

## 来个示例

```
Total Requests  73049 Unique Visitors 1855  Referrers    0   Log Size  7.18 MiB
  Failed Requests 7     Unique Files    12286 Unique 404   199 Bandwidth 269.59 MiB
  Generation Time 2     Excl. IP Hits   0     Static Files 10  Log File  new.log
 > 1 - Unique visitors per day - Including spiders                                                                                                               Total: 2/2
 Hits  Vis.      %   Bandwidth Avg. T.S. Cum. T.S. Max. T.S. Data
 ----- ---- ------ ----------- --------- --------- --------- ----
 13       3  0.02%   12.20 KiB 110.61 ms   1.44  s 387.00 ms 14/Sep/2016 |
 73029 1852 99.97%  269.58 MiB   5.67  s   4.79  d   4.55 mn 13/Sep/2016 ||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||

   2 - Top requests (URLs)                                                                                                                                 Total: 366/12286
 Hits Vis.      %   Bandwidth Avg. T.S. Cum. T.S. Max. T.S. Data
 ---- ---- ------ ----------- --------- --------- --------- ----
 8008  822 10.96%   45.19 MiB  12.70  s   1.18  d   4.50 mn /
 2708  414  3.71%    8.48 MiB   4.40  s   3.31 hr   3.12 mn /login/index
 2483  508  3.40%    5.56 KiB   1.07  s  44.46 mn   1.41 mn /login

   3 - Top static requests (e.g. jpg, png, js, css..)                                                                                                          Total: 10/10
 Hits Vis.     %   Bandwidth Avg. T.S. Cum. T.S. Max. T.S. Data
 ---- ---- ----- ----------- --------- --------- --------- ----
 22     13 0.03%    4.41 KiB 388.50 ms   8.55  s   5.00  s /robots.txt
 9       9 0.01%    1.56 KiB   0.00 us   0.00 us   0.00 us /favicon.ico
 6       5 0.01%   924.0   B   0.00 us   0.00 us   0.00 us /apple-touch-icon-precomposed.png
 6       5 0.01%   924.0   B   0.00 us   0.00 us   0.00 us /apple-touch-icon.png
 4       4 0.01%   86.51 KiB   2.00 ms   8.00 ms   2.00 ms /r/images/jhcf_image/logo2.png
 1       1 0.00%   178.0   B   0.00 us   0.00 us   0.00 us /sql.txt
 1       1 0.00%   11.94 KiB   4.00 ms   4.00 ms   4.00 ms /r/css/common.css
   4 - HTTP 404 Not Found URLs                                                                                                                               Total: 199/199
 Hits Vis.     %   Bandwidth Avg. T.S. Cum. T.S. Max. T.S. Data
 ---- ---- ----- ----------- --------- --------- --------- ----
 17      0 0.02%  122.59 KiB  10.29 ms 175.00 ms  21.00 ms /robots.txt
 11      0 0.02%   94.39 KiB   9.18 ms 101.00 ms  17.00 ms /wp-login.php
 5       0 0.01%   25.36 KiB 631.60 ms   3.16  s   2.61  s /forum.php
 5       0 0.01%   25.36 KiB 265.40 ms   1.33  s 651.00 ms /index.php
 4       0 0.01%   10.97 KiB   9.25 ms  37.00 ms  11.00 ms /<特征词ID>
 3       0 0.00%    8.24 KiB   9.33 ms  28.00 ms  11.00 ms /fin/cjhb/buy//
   5 - Visitor hostnames and IPs                                                                                                                            Total: 366/1861
 Hits Vis.     %   Bandwidth Avg. T.S. Cum. T.S. Max. T.S. Data
 ---- ---- ----- ----------- --------- --------- --------- ----
 6221    1 8.52%   18.34 MiB  15.27  s   1.10  d   4.55 mn 58.16.112.90    ||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||
 4614    1 6.32%   13.62 MiB   6.03  s   7.72 hr   4.55 mn 111.85.122.1    ||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||
 3848    1 5.27%   11.01 MiB   8.10  s   8.65 hr   4.52 mn 58.16.21.22     ||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||
 2386    1 3.27%  139.25 KiB  27.86 ms   1.11 mn   4.37  s 61.135.189.122  |||||||||||||||||||||||||||||||||||||
 2110    1 2.89%    6.02 MiB   7.04  s   4.13 hr   4.50 mn 111.85.122.222  |||||||||||||||||||||||||||||||||
 2084    1 2.85%    5.89 MiB   9.07  s   5.25 hr   4.50 mn 58.16.24.42     ||||||||||||||||||||||||||||||||
 1380    1 1.89%    2.98 MiB  17.94  s   6.88 hr   4.55 mn 220.197.207.138 |||||||||||||||||||||
```
