# Gitlab

Перед запуском нужно добавить запись в локальный /etc/hosts:

```bash
echo '192.168.56.10    gitlab.localdomain gitlab' >> /etc/hosts
```

Initial root password:

```bash
cat /etc/gitlab/initial_root_password
```

## Run Gitlab runner

Регистрация раннера:
```bash
   docker run --rm -ti --name gitlab-runner --restart always \
     --network host \
     -v /srv/gitlab-runner/config:/etc/gitlab-runner \
     -v /var/run/docker.sock:/var/run/docker.sock \
     gitlab/gitlab-runner:latest register
```

Запуск:
```bash
   docker run -d --name gitlab-runner --restart always \
     --network host \
     -v /srv/gitlab-runner/config:/etc/gitlab-runner \
     -v /var/run/docker.sock:/var/run/docker.sock \
     gitlab/gitlab-runner:latest
```

Конфигурация:
```yaml
    volumes = ["/cache", "/var/run/docker.sock:/var/run/docker.sock"]
    extra_hosts = ["gitlab.localdomain:192.168.56.10"]
```

## Pipeline

```yaml
stages:
  - test
  - build

test:
  stage: test
  image: golang:1.16
  script: 
   - go test .

build:
  stage: build
  image: docker:latest
  script:
   - docker build .
```