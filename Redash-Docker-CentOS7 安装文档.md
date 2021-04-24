## 使用docker compose 运行测试<br/>
### docker-compose.yml文件
```
version: "3"
x-redash-environment: &redash-environment
  REDASH_LOG_LEVEL: "INFO"
  REDASH_REDIS_URL: "redis://redis:6379/0"
  REDASH_DATABASE_URL: "postgresql://postgres@postgres/postgres"
  REDASH_RATELIMIT_ENABLED: "false"
  REDASH_MAIL_DEFAULT_SENDER: "redash@example.com"
  REDASH_MAIL_SERVER: "email"
  REDASH_ENFORCE_CSRF: "true"
services:
  server:
    image: redash/redash:latest
    command: server
    depends_on:
      - postgres
      - redis
    environment:
      <<: *redash-environment
      PYTHONUNBUFFERED: 0
  scheduler:
    image: redash/redash:latest
    command: scheduler
    depends_on:
      - server
    environment:
      <<: *redash-environment
  worker:
    image: redash/redash:latest
    command: worker
    depends_on:
      - server
worker:
    image: redash/redash:latest
    command: worker
    depends_on:
      - server
    environment:
      <<: *redash-environment
      PYTHONUNBUFFERED: 0
redis:
  image: redis:3-alpine
postgres:
  image: postgres:9.5.6-alpine
  ports:
    - "15432:5432"
email:
  image: djfarrelly/maildev
  ports:
    - "1080:80"
  restart: unless-stopped
nginx:
  image: redash/nginx:latest
  ports:
    - "80:80"
  depends_on:
    - server
  links:
    - server:redash
```

## 启动
```
docker-compose up -d
```

### 初始化

```
docker-compose run --rm server create_db
```

### 访问服务链接即可


参考链接：https://my.oschina.net/u/4406918/blog/4328331
