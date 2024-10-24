## 部署 ruoyi cloud 流程

### 1. 修改配置

#### 1.1 修改 bootstrap.yml

``` 

1. 替换 bootstrap.yml 中 nacos 的地址


127.0.0.1                       // 需替换

${RUOYI_NACOS:localhost}        // 替换为
${LOCALHOST:localhost}


```

#### 1.2 修改数据库中的配置

```

# 替换 /docker/mysql/db/*.sql 中的 

    ry_config_xxx.sql
    ry_xxx.sql

127.0.0.1                       // 需替换
localhost                       // 需替换

192.168.10.159                  // 部署机器的真实IP

```

#### 1.3 修改 nginx api 代理配置

```

proxy_pass http://ruoyi-gateway:8080/;          // 需替换

proxy_pass http://192.168.10.159:8080/;         // 部署机器的真实IP


```

### 2. 安装依赖

```

1.1 使用 idea 安装 maven 依赖

1.2 使用 idea 安装 ruoyi-ui 依赖，打包 npm run build:prod

```

### 3. 拷贝 *.jar 和 dist 到指定目录

```

cd docker && sh copy.sh

```

### 4. 添加环境变量

#### 4.1 新增 docker compose 环境变量文件 .env

```

# /docker/.env

# 本机器的ip
LOCALHOST=192.168.10.159


```

#### 4.2 添加环境变量到指定服务配置中

> 不知道就都添加

```

environment:
  RUOYI_NACOS: ruoyi-nacos
  LOCALHOST: ${LOCALHOST}


```


#### 4.3 新增 mysql 初始化 sql 挂载, 否则无法自动执行

```

  ruoyi-mysql:
    container_name: ruoyi-mysql
    image: mysql:5.7
    platform: linux/amd64
    build:
      context: ./mysql
    ports:
      - "3306:3306"
    volumes:
      - ./mysql/conf:/etc/mysql/conf.d
      - ./mysql/logs:/logs
      - ./mysql/data:/var/lib/mysql
      - ./mysql/db:/docker-entrypoint-initdb.d/        # 新增挂载配置
    command: [
      'mysqld',
      '--innodb-buffer-pool-size=80M',
      '--character-set-server=utf8mb4',
      '--collation-server=utf8mb4_unicode_ci',
      '--default-time-zone=+8:00',
      '--lower-case-table-names=1'
    ]
    environment:
      MYSQL_DATABASE: 'ry-cloud'
      MYSQL_ROOT_PASSWORD: password

```
