## Svodd Cluster Provisioning

1. После того как создали новый сервер, на котором ничего еще нет кроме debian запускаем make site

Для устранения ошибки после установки докера:
```
docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: unable to apply apparmor profile: apparmor failed to apply profile: write /proc/self/attr/apparmor/exec: no such file or directory: unknown.
```
Добавлена установка apparmor. https://github.com/docker/for-linux/issues/1199

provisioning/roles/docker/tasks/main.yml
секция  name: Install dependencies

#### Возможно понадобиться перезагрузка сервера после установки apparmor


    2. Запускаем make update для обновления dependencies в хост системе 

    3. Запускаем make authorize дял создания пользователя deploy

Подъём кластера Docker Swarm с Ansible
https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/

https://docs.ansible.com/ansible/latest/collections/community/docker/docker_swarm_module.html#ansible-collections-community-docker-docker-swarm-module

После истечения срока действия токена доступа (30-90days) необходимо перевыпустить токен, перейти по ссылке https://github.com/settings/tokens
```
A personal access token (classic) "fct-search.deploy.access.token" 
with delete:packages, repo, workflow, and write:packages scopes 
was recently regenerated for your account. 
Visit https://github.com/settings/tokens for more information.
```
Далее необходимо запустить команду `make docker-login` и заново авторизироваться в ghcr.io/terratensor — ввести имя пользователя и новый токен.
Это необходимо для того, чтобы при обновлении docker images во время deploy сервер имел доступ к docker registry репозиторию и мог скачать обновленные packages, иначе обновление не произойдет.
