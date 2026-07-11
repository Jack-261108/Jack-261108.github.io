---
title: docker 安装Nacos
date: 2024-03-10
tags: [docker]
---

docker 安装Nacos
docker-compose.yml
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128
129
130
131
132
133
134
135
136
137
138
139
140
141
142
143
144
145
146
147
148
149
150
151
152
153
154
155
156
157
158
159
160
161
162
163
164
165
166
167
168
169
170
171
172
173
174
175
176
177
178
179
180
181
182
183
184
185
186
187
188
189
190
191
192
version:
"3"
services:
hoj-nacos:
image:
nacos/nacos-server:1.4.2
container_name:
hoj-nacos
restart:
always
environment:
-
JVM_XMX=384m
-
JVM_XMS=384m
-
JVM_XMN=192m
-
MODE=standalone
-
SPRING_DATASOURCE_PLATFORM=mysql
-
MYSQL_SERVICE_HOST=${MYSQL_HOST:-172.20.0.3}
-
MYSQL_SERVICE_PORT=3306
-
MYSQL_SERVICE_USER=root
-
MYSQL_SERVICE_PASSWORD=${MYSQL_ROOT_PASSWORD:-hoj123456}
# 与上面数据库密码一致
-
MYSQL_SERVICE_DB_NAME=nacos
-
NACOS_AUTH_ENABLE=true
# 开启鉴权
ports:
-
${NACOS_PORT:-8848}:8848
healthcheck:
test:
curl
-f
http://${NACOS_HOST:-172.20.0.4}:8848/nacos/index.html
||
exit
1
interval:
6s
timeout:
10s
retries:
10
# hoj-backend:
#   #仅支持amd64
#   image: registry.cn-shenzhen.aliyuncs.com/hcode/hoj_backend
#   #支持amd64、arm64
#   #image: himitzh/hoj_backend
#   container_name: hoj-backend
#   restart: always
#   depends_on:
#     - hoj-redis
#     - hoj-mysql
#     - hoj-nacos
#   volumes:
#     - ${HOJ_DATA_DIRECTORY}/file:/hoj/file
#     - ${HOJ_DATA_DIRECTORY}/testcase:/hoj/testcase
#     - ${HOJ_DATA_DIRECTORY}/log/backend:/hoj/log/backend
#   environment:
#     - TZ=Asia/Shanghai
#     - JAVA_OPTS=-Xms192m -Xmx384m
#     - BACKEND_SERVER_PORT=${BACKEND_PORT:-6688}
#     - NACOS_URL=${NACOS_HOST:-172.20.0.4}:8848
#     - NACOS_USERNAME=${NACOS_USERNAME:-root} # 登录 http://ip:8848/nacos 分布式配置中心与注册中心的后台的账号
#     - NACOS_PASSWORD=${NACOS_PASSWORD:-hoj123456} # 密码
#     - JWT_TOKEN_SECRET=${JWT_TOKEN_SECRET:-default} # token加密秘钥 默认则生成32位随机密钥
#     - JWT_TOKEN_EXPIRE=${JWT_TOKEN_EXPIRE:-86400} # token过期时间默认为24小时 86400s
#     - JWT_TOKEN_FRESH_EXPIRE=${JWT_TOKEN_FRESH_EXPIRE:-43200} # token默认12小时可自动刷新
#     - JUDGE_TOKEN=${JUDGE_TOKEN:-default} # 调用判题服务器的token 默认则生成32位随机密钥
#     - MYSQL_HOST=${MYSQL_HOST:-172.20.0.3}
#     - MYSQL_PUBLIC_HOST=${MYSQL_PUBLIC_HOST} # 如果判题服务是分布式，请提供当前mysql所在服务器的公网ip
#     - MYSQL_PUBLIC_PORT=${MYSQL_PUBLIC_PORT:-3306}
#     - MYSQL_PORT=3306
#     - MYSQL_DATABASE_NAME=hoj # 改动需要修改hoj-mysql镜像,默认为hoj
#     - MYSQL_USERNAME=root
#     - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD:-hoj123456}
#     - EMAIL_SERVER_HOST=${EMAIL_SERVER_HOST:-smtp.qq.com} # 请使用邮件服务的域名或ip
#     - EMAIL_SERVER_PORT=${EMAIL_SERVER_PORT:-465} # 请使用邮件服务的端口号
#     - EMAIL_USERNAME=${EMAIL_USERNAME:-your_email_username} # 请使用对应邮箱账号
#     - EMAIL_PASSWORD=${EMAIL_PASSWORD:-your_email_password} # 请使用对应邮箱密码
#     - REDIS_HOST=${REDIS_HOST:-172.20.0.2}
#     - REDIS_PORT=6379
#     - REDIS_PASSWORD=${REDIS_PASSWORD:-hoj123456}
#     - OPEN_REMOTE_JUDGE=true # 是否开启各个remote judge
#     # 开启虚拟判题请提供对应oj的账号密码 格式为
#     # username1,username2,...
#     # password1,password2,...
#     - HDU_ACCOUNT_USERNAME_LIST=${HDU_ACCOUNT_USERNAME_LIST}
#     - HDU_ACCOUNT_PASSWORD_LIST=${HDU_ACCOUNT_PASSWORD_LIST}
#     - CF_ACCOUNT_USERNAME_LIST=${CF_ACCOUNT_USERNAME_LIST}
#     - CF_ACCOUNT_PASSWORD_LIST=${CF_ACCOUNT_PASSWORD_LIST}
#     - POJ_ACCOUNT_USERNAME_LIST=${POJ_ACCOUNT_USERNAME_LIST}
#     - POJ_ACCOUNT_PASSWORD_LIST=${POJ_ACCOUNT_PASSWORD_LIST}
#     - ATCODER_ACCOUNT_USERNAME_LIST=${ATCODER_ACCOUNT_USERNAME_LIST}
#     - ATCODER_ACCOUNT_PASSWORD_LIST=${ATCODER_ACCOUNT_PASSWORD_LIST}
#     - SPOJ_ACCOUNT_USERNAME_LIST=${SPOJ_ACCOUNT_USERNAME_LIST}
#     - SPOJ_ACCOUNT_PASSWORD_LIST=${SPOJ_ACCOUNT_PASSWORD_LIST}
#     # 是否强制使用配置文件的remote judge账号覆盖原有系统的账号列表
#     - FORCED_UPDATE_REMOTE_JUDGE_ACCOUNT=${FORCED_UPDATE_REMOTE_JUDGE_ACCOUNT:-false}
#   ports:
#     - ${BACKEND_PORT:-6688}:${BACKEND_PORT:-6688}
#   networks:
#     hoj-network:
#       ipv4_address: ${BACKEND_HOST:-172.20.0.5}
# hoj-frontend:
#   #仅支持amd64
#   image: registry.cn-shenzhen.aliyuncs.com/hcode/hoj_frontend
#   #支持amd64、arm64
#   #image: himitzh/hoj_frontend
#   container_name: hoj-frontend
#   restart: always
#   # 开启https，请提供证书
#   #volumes:
#   #  - ./server.crt:/etc/nginx/etc/crt/server.crt
#   #  - ./server.key:/etc/nginx/etc/crt/server.key
#   # 修改前端logo
#   #  - ./logo.a0924d7d.png:/usr/share/nginx/html/assets/img/logo.a0924d7d.png
#   #  - ./backstage.8bce8c6e.png:/usr/share/nginx/html/assets/img/backstage.8bce8c6e.png
#   environment:
#     - SERVER_NAME=localhost # 域名(例如baidu.com)或localhost(本地)
#     - BACKEND_SERVER_HOST=${BACKEND_HOST:-172.20.0.5} # backend后端服务地址
#     - BACKEND_SERVER_PORT=${BACKEND_PORT:-6688} # backend后端服务端口号
#     - USE_HTTPS=false # 使用https请设置为true
#   ports:
#     - "80:80"
#     - "443:443"
#   networks:
#     hoj-network:
#       ipv4_address: 172.20.0.6
# hoj-judgeserver:
#   #仅支持amd64
#   image: registry.cn-shenzhen.aliyuncs.com/hcode/hoj_judgeserver
#   #支持amd64、arm64
#   #image: himitzh/hoj_judgeserver
#   container_name: hoj-judgeserver
#   restart: always
#   depends_on:
#     - hoj-mysql
#     - hoj-nacos
#   volumes:
#     - ${HOJ_DATA_DIRECTORY}/testcase:/judge/test_case
#     - ${HOJ_DATA_DIRECTORY}/judge/log:/judge/log
#     - ${HOJ_DATA_DIRECTORY}/judge/run:/judge/run
#     - ${HOJ_DATA_DIRECTORY}/judge/spj:/judge/spj
#     - ${HOJ_DATA_DIRECTORY}/judge/interactive:/judge/interactive
#     - ${HOJ_DATA_DIRECTORY}/log/judgeserver:/judge/log/judgeserver
#   environment:
#     - TZ=Asia/Shanghai
#     - JAVA_OPTS=-Xms192m -Xmx384m # 修正JVM参数以便适应单机部署
#     - JUDGE_SERVER_IP=${JUDGE_SERVER_IP:-172.20.0.7}
#     - JUDGE_SERVER_PORT=${JUDGE_SERVER_PORT:-8088}
#     - JUDGE_SERVER_NAME=${JUDGE_SERVER_NAME:-judger-alone} # 判题服务的名字
#     - NACOS_URL=${NACOS_HOST:-172.20.0.4}:8848
#     - NACOS_USERNAME=${NACOS_USERNAME:-root}
#     - NACOS_PASSWORD=${NACOS_PASSWORD:-hoj123456}
#     - MAX_TASK_NUM=${MAX_TASK_NUM:--1} # -1表示最大可接收判题任务数为cpu核心数+1
#     - REMOTE_JUDGE_OPEN=${REMOTE_JUDGE_OPEN:-true} # 当前判题服务器是否开启远程虚拟判题功能
#     - REMOTE_JUDGE_MAX_TASK_NUM=${REMOTE_JUDGE_MAX_TASK_NUM:--1} # -1表示最大可接收远程判题任务数为cpu核心数*2+1
#     - PARALLEL_TASK=${PARALLEL_TASK:-default} # 默认沙盒并行判题程序数为cpu核心数
#   ports:
#     - ${JUDGE_SERVER_PORT:-8088}:${JUDGE_SERVER_PORT:-8088}
#     # - "0.0.0.0:5050:5050" # 一般不开放安全沙盒端口
#   healthcheck:
#     test: curl -f http://${JUDGE_SERVER_IP:-172.20.0.7}:${JUDGE_SERVER_PORT:-8088}/version || exit 1
#     interval: 30s
#     timeout: 10s
#     retries: 3
#   privileged: true # 设置容器的权限为root
#   shm_size: 512mb
#   networks:
#     hoj-network:
#       ipv4_address: 172.20.0.7
# hoj-mysql-checker:
#   #仅支持amd64
#   image: registry.cn-shenzhen.aliyuncs.com/hcode/hoj_database_checker
#   #支持amd64、arm64
#   #image: himitzh/hoj_database_checker
#   container_name: hoj-mysql-checker
#   depends_on:
#     - hoj-mysql
#   links:
#     - hoj-mysql:mysql
#   environment:
#     - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD:-hoj123456}
#   networks:
#     hoj-network:
#       ipv4_address: 172.20.0.8
# hoj-autohealth:  # 监控不健康的容器进行重启
#   restart: always
#   container_name: hoj-autohealth
#   image: willfarrell/autoheal
#   environment:
#     - AUTOHEAL_CONTAINER_LABEL=all
#   volumes:
#     - /var/run/docker.sock:/var/run/docker.sock
# networks:
#    hoj-network:
#      driver: bridge
#      ipam:
#        config:
#          - subnet: ${SUBNET:-172.20.0.0/16}
环境变量
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
# hoj全部数据存储的文件夹位置（默认当前路径生成hoj文件夹）
HOJ_DATA_DIRECTORY=./hoj
# mysql的docker内网ip
MYSQL_HOST=******
# mysql暴露的端口号
MYSQL_PUBLIC_PORT=3306
# mysql的密码
MYSQL_ROOT_PASSWORD=*******
# 如果判题服务是分布式，请提供当前mysql所在服务器的公网ip
MYSQL_PUBLIC_HOST=*****
# nacos的配置
NACOS_HOST=*****
NACOS_PORT=8888
NACOS_USERNAME=nacos
NACOS_PASSWORD=*****
# docker network的配置
#SUBNET=172.20.0.0/16
运行
1
docker-compose up -d
docker 安装2.X版本Nacos
具体参考：
nacos-docker
Docker-compose(mysql8.x):
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
version:
"3.8"
services:
nacos:
image:
nacos/nacos-server:${NACOS_VERSION}
container_name:
nacos-standalone-mysql
env_file:
-
../env/nacos-standlone-mysql.env
volumes:
-
./standalone-logs/:/home/nacos/logs
ports:
-
"8848:8848"
-
"9848:9848"
depends_on:
mysql:
condition:
service_healthy
restart:
always
mysql:
container_name:
mysql
build:
context:
.
dockerfile:
./image/mysql/8/Dockerfile
image:
example/mysql:8.0.30
env_file:
-
../env/mysql.env
volumes:
-
./mysql:/var/lib/mysql
ports:
-
"3306:3306"
healthcheck:
test:
[
"CMD"
,
"mysqladmin"
,
"ping"
,
"-h"
,
"localhost"
]
interval:
5s
timeout:
10s
retries:
10
Docker-compose(mysql5.x):
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
version:
"3.8"
services:
nacos:
image:
nacos/nacos-server:${NACOS_VERSION}
container_name:
nacos-standalone-mysql
env_file:
-
../env/custom-application-config.env
volumes:
-
./standalone-logs/:/home/nacos/logs
-
./init.d/application.properties:/home/nacos/conf/application.properties
ports:
-
"8848:8848"
-
"9848:9848"
depends_on:
mysql:
condition:
service_healthy
restart:
on-failure
mysql:
container_name:
mysql
build:
context:
.
dockerfile:
./image/mysql/5.7/Dockerfile
image:
example/mysql:5.7
env_file:
-
../env/mysql.env
volumes:
-
./mysql:/var/lib/mysql
ports:
-
"3306:3306"
healthcheck:
test:
[
"CMD"
,
"mysqladmin"
,
"ping"
,
"-h"
,
"localhost"
]
interval:
5s
timeout:
10s
retries:
10
环境变量：
1
2
3
4
5
6
7
8
9
10
11
12
13
PREFER_HOST_MODE=hostname
MODE=standalone
SPRING_DATASOURCE_PLATFORM=mysql
MYSQL_SERVICE_HOST=mysql
MYSQL_SERVICE_DB_NAME=nacos2
MYSQL_SERVICE_PORT=
3306
MYSQL_SERVICE_USER=nacos
MYSQL_SERVICE_PASSWORD=nacos
MYSQL_SERVICE_DB_PARAM=characterEncoding=utf8&connectTimeout=
1000
&socketTimeout=
3000
&autoReconnect=
true
&useUnicode=
true
&useSSL=
false
&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=
true
NACOS_AUTH_IDENTITY_KEY=
2222
NACOS_AUTH_IDENTITY_VALUE=
2
xxx
NACOS_AUTH_TOKEN=SecretKey012345678901234567890123456789012345678901234567890123456789
注意在环境变量中指定jvm参数
