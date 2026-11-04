markdown
# Установка Nginx на web-a и web-b через Ansible

## Задача

С помощью Ansible подключиться к ВМ `web-a` и `web-b`, установить Nginx, проверить работу.

## Окружение

- Control node: Ubuntu 22.04, Ansible 2.15
- `web-a` — `<IP_web-a>` (Ubuntu 22.04)
- `web-b` — `<IP_web-b>` (Ubuntu 22.04)
- Доступ по SSH-ключу, пользователь `ubuntu`

## Структура проекта
.
├── inventory.ini
├── install_nginx.yml
└── README.md

text

## Файлы

### `inventory.ini`

```ini
[web]
web-a ansible_host=<IP_web-a>
web-b ansible_host=<IP_web-b>

[web:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3
install_nginx.yml
yaml
---
- name: Install Nginx
  hosts: web
  become: true

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install nginx
      apt:
        name: nginx
        state: present

    - name: Start and enable nginx
      service:
        name: nginx
        state: started
        enabled: true

    - name: Check nginx responds
      uri:
        url: http://localhost
        status_code: 200
Запуск
bash
ansible -i inventory.ini web -m ping
ansible-playbook -i inventory.ini install_nginx.yml
Результат
text
PLAY RECAP *********************************************************************
web-a : ok=5 changed=3 unreachable=0 failed=0
web-b : ok=5 changed=3 unreachable=0 failed=0
Повторный запуск (идемпотентность):

text
web-a : ok=5 changed=0 unreachable=0 failed=0
web-b : ok=5 changed=0 unreachable=0 failed=0
Проверка:

bash
curl -I http://<IP_web-a>
curl -I http://<IP_web-b>
Оба вернули HTTP/1.1 200 OK.

Скриншоты
1. ВМ в облаке
https://screenshots/01-vms.png

2. Успешный запуск playbook
https://screenshots/02-playbook.png

3. Nginx на web-a
https://screenshots/03-web-a.png

4. Nginx на web-b
https://screenshots/04-web-b.png

Вывод
Ansible успешно установил и запустил Nginx на web-a и web-b. Playbook идемпотентен, Nginx отвечает по HTTP на обоих хостах.

text
