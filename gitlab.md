# [Gitlib](https://docs.gitlab.com/omnibus/)

## 启动

```sh
docker run -d \
    -p 8443:443 --publish 80:80 --publish 2222:22 \
    --name gitlab \
    --restart always \
    -v $GITLAB_HOME/config:/etc/gitlab \
    -v $GITLAB_HOME/logs:/var/log/gitlab \
    -v $GITLAB_HOME/data:/var/opt/gitlab \
    gitlab/gitlab-ce
```

## [配置](https://docs.gitlab.com/omnibus/)

```sh
docker exec -t -i gitlab vim /etc/gitlab/gitlab.rb
```

## 重启

```sh
docker restart gitlab
```

## 作为服务

### 编辑 `/usr/lib/systemd/system/gitlab.service`

```json
[Unit]
Description=Gitlab Service
After=docker.service
Requires=docker.service

[Service]
Environment="PORT=-p 80:80 -p 8443:443 -p 2222:22"
Environment="VOLUME=-v /gitlab/config:/etc/gitlab -v /gitlab/logs:/var/log/gitlab -v /gitlab/data:/var/opt/gitlab"
Environment="ENV="
Environment="ARGS="
Environment="IMAGE=gitlab/gitlab-ce"
ExecStartPre=-/usr/bin/docker stop %n
ExecStartPre=-/usr/bin/docker rm %n
ExecStart=/usr/bin/docker run --name %n $PORT $VOLUME $ENV $IMAGE $ARGS
TimeoutStartSec=0
Restart=always

[Install]
WantedBy=multi-user.target
```



## svn迁移到git

```sh
#签出代码为git库
git svn clone http://192.168.8.63/svn/dc -s --authors-file=users.txt
cd dc
#再次更新到最新代码
git svn fetch
#添加git远程地址
git remote add origin https://gitlab.ewinlu.com/unitrans/data-center.git
#同步本地库到远程
git push --set-upstream origin master
```

