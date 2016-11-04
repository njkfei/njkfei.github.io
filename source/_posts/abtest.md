title: abtest
date: 2016.03.17
categories: linux #分类
tags: [linux]
toc: true
---
　abtest是重要的性能测试工具．

## 安装
centos环境下．安装方法为：
```
[@iZ28h3eaiotZ ~]$ sudo yum install httpd-tools
```
安装完成后，在命令行下输入ab即可
```
[@iZ28h3eaiotZ ~]$ ab
```

## 使用
-c 一次发送的请求数量，即并发量
-n 请求次数

```
[@iZ28h3eaiotZ ~]$ab -c 1  -n 100 http://localhost:8080/wiki/doku.php
This is ApacheBench, Version 2.3 <$Revision: 655654 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking localhost (be patient).....done


Server Software:        Apache/2.2.15
Server Hostname:        localhost
Server Port:            8080

Document Path:          /wiki/doku.php
Document Length:        21885 bytes

Concurrency Level:      1
Time taken for tests:   5.051 seconds
Complete requests:      100
Failed requests:        0
Write errors:           0
Total transferred:      2241900 bytes
HTML transferred:       2188500 bytes
Requests per second:    19.80 [#/sec] (mean)
Time per request:       50.508 [ms] (mean)
Time per request:       50.508 [ms] (mean, across all concurrent requests)
Transfer rate:          433.47 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    48   50   2.7     50      69
Waiting:       44   46   2.5     45      64
Total:         48   50   2.7     50      69

Percentage of the requests served within a certain time (ms)
  50%     50
  66%     51
  75%     51
  80%     51
  90%     52
  95%     54
  98%     60
  99%     69
 100%     69 (longest request)
```

## 命令行参数
```
Usage: ab [options] [http[s]://]hostname[:port]/path
Options are:
    -n requests     Number of requests to perform
    -c concurrency  Number of multiple requests to make
    -t timelimit    Seconds to max. wait for responses
    -b windowsize   Size of TCP send/receive buffer, in bytes
    -p postfile     File containing data to POST. Remember also to set -T
    -u putfile      File containing data to PUT. Remember also to set -T
    -T content-type Content-type header for POSTing, eg.
                    'application/x-www-form-urlencoded'
                    Default is 'text/plain'
    -v verbosity    How much troubleshooting info to print
    -w              Print out results in HTML tables
    -i              Use HEAD instead of GET
    -x attributes   String to insert as table attributes
    -y attributes   String to insert as tr attributes
    -z attributes   String to insert as td or th attributes
    -C attribute    Add cookie, eg. 'Apache=1234. (repeatable)
    -H attribute    Add Arbitrary header line, eg. 'Accept-Encoding: gzip'
                    Inserted after all normal header lines. (repeatable)
    -A attribute    Add Basic WWW Authentication, the attributes
                    are a colon separated username and password.
    -P attribute    Add Basic Proxy Authentication, the attributes
                    are a colon separated username and password.
    -X proxy:port   Proxyserver and port number to use
    -V              Print version number and exit
    -k              Use HTTP KeepAlive feature
    -d              Do not show percentiles served table.
    -S              Do not show confidence estimators and warnings.
    -g filename     Output collected data to gnuplot format file.
    -e filename     Output CSV file with percentages served
    -r              Don't exit on socket receive errors.
    -h              Display usage information (this message)
    -Z ciphersuite  Specify SSL/TLS cipher suite (See openssl ciphers)
    -f protocol     Specify SSL/TLS protocol (SSL2, SSL3, TLS1, or ALL)
```
