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


Если при установке docker sdk возникает ошибка вида:
```
fatal: [swarm-worker-2]: FAILED! => {"changed": false, "cmd": ["/usr/bin/python3", "-m", "pip.__main__", "install", "docker"], "msg": "\n:stderr: error: externally-managed-environmen
t\n\n× This environment is externally managed\n╰─> To install Python packages system-wide, try apt install\n    python3-xyz, where xyz is the package you are trying to\n    install.\
n    \n    If you wish to install a non-Debian-packaged Python package,\n    create a virtual environment using python3 -m venv path/to/venv.\n    Then use path/to/venv/bin/python an
d path/to/venv/bin/pip. Make\n    sure you have python3-full installed.\n    \n    If you wish to install a non-Debian packaged Python application,\n    it may be easiest to use pipx
 install xyz, which will manage a\n    virtual environment for you. Make sure you have pipx installed.\n    \n    See /usr/share/doc/python3.11/README.venv for more information.\n\nn
ote: If you believe this is a mistake, please contact your Python installation or OS distribution provider. You can override this, at the risk of breaking your Python installation or OS, by passing --break-system-packages.\nhint: See PEP 668 for the detailed specification.\n"}
```

https://ubuntuhandbook.org/index.php/2024/03/pip-install-error-ubuntu-2404/

Один из рабочих вариантов использовать pipx вместо Pip
Для этого надо проверить установлен ли community.general module в ansible:
`ansible-galaxy collection list` ищем в списке community.general
Если нет, то устанавливаем:
`ansible-galaxy collection install community.general`

После истечения срока действия токена доступа (30-90days) необходимо перевыпустить токен, перейти по ссылке https://github.com/settings/tokens
```
A personal access token (classic) "fct-search.deploy.access.token" 
with delete:packages, repo, workflow, and write:packages scopes 
was recently regenerated for your account. 
Visit https://github.com/settings/tokens for more information.
```
Далее необходимо запустить команду `make docker-login` и заново авторизироваться в ghcr.io/terratensor — ввести имя пользователя и новый токен.
Это необходимо для того, чтобы при обновлении docker images во время deploy сервер имел доступ к docker registry репозиторию и мог скачать обновленные packages, иначе обновление не произойдет.


при добавлении новой ноды, в сатах указываем имя хоста маленькими буквами,ip адрес
запускаем сначала upgrade, потом site