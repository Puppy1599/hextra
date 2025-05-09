---
title: '[第五空间 2021]WebFTP'
date: '2025-05-09T21:09:59+08:00'
weight: 
draft: false
description: '[第五空间 2021]WebFTP'
---

使用 dirsearch 扫描，发现 `/phpinfo.php `

```shell
┌──(hailo㉿kali-Hailo)-[~]
└─$ dirsearch -u 'http://node4.anna.nssctf.cn:28342/?m=login&a=in'                          
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                                                             
 (_||| _) (/_(_|| (_| )                                                                                      
                                                                                                             
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/hailo/reports/http_node4.anna.nssctf.cn_28342/__25-05-02_10-32-14.txt

Target: http://node4.anna.nssctf.cn:28342/

[10:32:14] Starting:                                                                                         
[10:32:21] 301 -  335B  - /.git  ->  http://node4.anna.nssctf.cn:28342/.git/
[10:32:21] 200 -  415B  - /.git/branches/                                   
[10:32:21] 200 -  306B  - /.git/config                                      
[10:32:21] 200 -  623B  - /.git/
[10:32:21] 200 -   73B  - /.git/description
[10:32:21] 200 -   23B  - /.git/HEAD
[10:32:21] 200 -  658B  - /.git/hooks/                                      
[10:32:21] 200 -  464B  - /.git/info/                                       
[10:32:21] 200 -  240B  - /.git/info/exclude                                
[10:32:21] 200 -  173B  - /.git/logs/HEAD                                   
[10:32:21] 301 -  345B  - /.git/logs/refs  ->  http://node4.anna.nssctf.cn:28342/.git/logs/refs/
[10:32:21] 301 -  351B  - /.git/logs/refs/heads  ->  http://node4.anna.nssctf.cn:28342/.git/logs/refs/heads/
[10:32:21] 200 -  488B  - /.git/logs/
[10:32:21] 200 -  173B  - /.git/logs/refs/heads/master
[10:32:21] 200 -  173B  - /.git/logs/refs/remotes/origin/HEAD
[10:32:21] 200 -  566B  - /.git/objects/
[10:32:21] 301 -  353B  - /.git/logs/refs/remotes  ->  http://node4.anna.nssctf.cn:28342/.git/logs/refs/remotes/
[10:32:21] 301 -  360B  - /.git/logs/refs/remotes/origin  ->  http://node4.anna.nssctf.cn:28342/.git/logs/refs/remotes/origin/
[10:32:21] 200 -   41B  - /.git/refs/heads/master
[10:32:21] 200 -  114B  - /.git/packed-refs
[10:32:21] 200 -  481B  - /.git/refs/
[10:32:21] 200 -   33KB - /.git/index
[10:32:21] 301 -  346B  - /.git/refs/heads  ->  http://node4.anna.nssctf.cn:28342/.git/refs/heads/
[10:32:21] 301 -  348B  - /.git/refs/remotes  ->  http://node4.anna.nssctf.cn:28342/.git/refs/remotes/
[10:32:21] 301 -  355B  - /.git/refs/remotes/origin  ->  http://node4.anna.nssctf.cn:28342/.git/refs/remotes/origin/
[10:32:21] 200 -   32B  - /.git/refs/remotes/origin/HEAD
[10:32:21] 301 -  345B  - /.git/refs/tags  ->  http://node4.anna.nssctf.cn:28342/.git/refs/tags/
[10:32:21] 403 -  301B  - /.ht_wsr.txt                                      
[10:32:21] 403 -  304B  - /.htaccess.bak1                                   
[10:32:21] 403 -  304B  - /.htaccess.orig
[10:32:21] 403 -  306B  - /.htaccess.sample                                 
[10:32:21] 403 -  304B  - /.htaccess.save
[10:32:21] 403 -  302B  - /.htaccess_sc                                     
[10:32:21] 403 -  304B  - /.htaccess_orig
[10:32:21] 403 -  303B  - /.htaccessOLD2
[10:32:21] 403 -  305B  - /.htaccess_extra
[10:32:21] 403 -  302B  - /.htaccessOLD                                     
[10:32:21] 403 -  302B  - /.htaccessBAK                                     
[10:32:21] 403 -  294B  - /.htm                                             
[10:32:21] 403 -  295B  - /.html                                            
[10:32:21] 403 -  300B  - /.htpasswds                                       
[10:32:21] 403 -  304B  - /.htpasswd_test
[10:32:21] 403 -  301B  - /.httr-oauth                                      
[10:32:22] 403 -  295B  - /.php3                                            
[10:32:22] 403 -  294B  - /.php                                             
[10:32:32] 403 -  298B  - /cgi-bin/                                         
[10:32:32] 403 -  306B  - /cgi-bin/awstats/                                 
[10:32:32] 403 -  316B  - /cgi-bin/a1stats/a1disp.cgi
[10:32:32] 403 -  308B  - /cgi-bin/awstats.pl
[10:32:32] 403 -  309B  - /cgi-bin/htimage.exe?2,2
[10:32:32] 403 -  308B  - /cgi-bin/htmlscript
[10:32:32] 403 -  308B  - /cgi-bin/index.html
[10:32:32] 403 -  310B  - /cgi-bin/imagemap.exe?2,2
[10:32:32] 403 -  303B  - /cgi-bin/login                                    
[10:32:32] 403 -  307B  - /cgi-bin/login.php
[10:32:32] 403 -  315B  - /cgi-bin/mt7/mt-xmlrpc.cgi
[10:32:32] 403 -  307B  - /cgi-bin/mt/mt.cgi
[10:32:32] 403 -  307B  - /cgi-bin/login.cgi
[10:32:32] 403 -  311B  - /cgi-bin/mt-xmlrpc.cgi
[10:32:32] 403 -  308B  - /cgi-bin/mt7/mt.cgi
[10:32:32] 403 -  305B  - /cgi-bin/php.ini
[10:32:32] 403 -  306B  - /cgi-bin/printenv
[10:32:32] 403 -  309B  - /cgi-bin/ViewLog.asp
[10:32:32] 403 -  304B  - /cgi-bin/mt.cgi
[10:32:32] 403 -  309B  - /cgi-bin/printenv.pl
[10:32:32] 403 -  306B  - /cgi-bin/test-cgi
[10:32:32] 403 -  306B  - /cgi-bin/test.cgi
[10:32:32] 403 -  314B  - /cgi-bin/mt/mt-xmlrpc.cgi                         
[10:32:40] 200 -   10KB - /LICENSE                                          
[10:32:41] 200 -   14KB - /logo                                             
[10:32:44] 200 -   18KB - /phpinfo.php                                      
[10:32:46] 200 -    3KB - /README.md                                        
[10:32:46] 301 -  337B  - /Readme  ->  http://node4.anna.nssctf.cn:28342/Readme/
[10:32:47] 403 -  303B  - /server-status                                    
[10:32:47] 403 -  304B  - /server-status/                                   
[10:32:52] 200 -   33KB - /upload                                           
                                                                             
Task Completed
```

发现 `flag`：`NSSCTF{ffb4dc5b-e235-4bb6-b4e2-9db5036124cb}`

![PixPin_2025-05-02_10-32-41](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-02_10-32-41.7snf5tbbpw.webp)
