# 说明
基于 docker 搭建 redis cluster  
基于 docker 搭建 redis sentinel   
基于 docker 搭建 redis masterslave   
本文档用于搭建redis cluster/sentinel/masterslave，用于测试使用。



#### clone后最好复制一份到别的目录部署,因为部署后配置文件会被redis自动更改


* 配置文件替换: 进入项目目录后,使用下面命令将 192.168.4.155 替换为 192.168.3.151(自己机器的ip) 注意执行路径,不要在其他目录下执行
* find ./ -name "*.conf" |xargs -t -I{} perl -pi -e 's/192.168.4.155/192.168.3.151/g' {}

------------ 

#### cluster模式
  占用host端口:  8001,8002,8003,8004,8005,8006    
  docker配置为host模式,使用bridge模式会导致容器ip(172.17.*.*)和主机ip(192.168.*.*)重复注册   
          
  进入项目目录后执行下面ip替换命令,下面命令中192.168.3.151(这里填自己机器的ip)    
  * find ./ -name "*.conf" |xargs -t -I{} perl -pi -e 's/192.168.4.155/192.168.3.151/g' {}
  
  启动命令:   
  1.docker-compose -f ./docker-compose.yml up -d   
  
  第二条命令中 ip换为部署机器的ip    
  2.docker run --rm -it inem0o/redis-trib create --replicas 1 192.168.4.155:8001 192.168.4.155:8002 192.168.4.155:8003 192.168.4.155:8004 192.168.4.155:8005 192.168.4.155:8006   
  

---------
 
#### sentinel模式
占用host端口:  6300,6380,6381   26379,26380,26381  
进入项目目录后执行下面ip替换命令,下面命令中192.168.3.151(这里填自己机器的ip)      
* find ./ -name "*.conf" |xargs -t -I{} perl -pi -e 's/192.168.4.155/192.168.3.151/g' {}
* 修改配置文件:   
   1.slave1/redis.conf 最后几行的ip 改为部署机器的ip   
   2.slave2/redis.conf 最后几行的ip 改为部署机器的ip   
   3.修改sentinel1/sentinel.config,sentinel2/sentinel.config,sentinel3/sentinel.config 第66行ip为部署机器ip
   
* 启动命令: 使用host模式  
  1.docker-compose -f ./docker-compose-host.yml up -d master    
  2.docker-compose -f ./docker-compose-host.yml up -d slave_1 slave_2    
  3.docker-compose -f ./docker-compose-host.yml up -d sentinel_1 sentinel_2 sentinel_3    
  
---------

#### master-slave模式
  占用host端口:  63777,63780,63781
  * 启动命令:   
  1.docker run -d  --publish 63777:6379 --volume ./master/redis.conf:/usr/local/etc/redis/redis.conf --name       redis-ms-master redis:6.0.8 redis-server /usr/local/etc/redis/redis.conf     
  2.docker run -d  --publish 63780:6379 --volume ./slave1/redis.conf:/usr/local/etc/redis/redis.conf --name       redis-ms-slave1 redis:6.0.8 redis-server /usr/local/etc/redis/redis.conf    
  3.docker run -d  --publish 63781:6379 --volume ./slave2/redis.conf:/usr/local/etc/redis/redis.conf --name       redis-ms-slave2 redis:6.0.8 redis-server /usr/local/etc/redis/redis.conf
     
