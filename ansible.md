#  Ansible - чеклист


---

## 1. Приоритезация переменных (Variable Precedence)

Ansible имеет чёткий порядок приоритета переменных — чем выше в списке, тем сильнее значение.

### 📊 Порядок приоритета (от низшего к высшему)

| Уровень | Пример | Приоритет |
|----------|---------|------------|
| 1️⃣ | `defaults/main.yml` в роли | 🔽 самый низкий |
| 2️⃣ | `vars/main.yml` в роли | |
| 3️⃣ | Переменные, объявленные в инвентаре (inventory vars, group_vars, host_vars) | |
| 4️⃣ | Переменные, объявленные в плейбуке (`vars:`) | |
| 5️⃣ | Переменные, переданные через `include_vars` / `set_fact` | |
| 6️⃣ | Переменные, определённые в командной строке через `--extra-vars` (`-e`) | 🔼 самый высокий |

### 💡 Пример

```bash
ansible-playbook site.yml -e "env=prod version=2.1"
```

## 2. Handlers

Handlers — это задачи, которые выполняются только если были вызваны.
Обычно используются для перезапуска сервисов после изменений.

```
# tasks/main.yml
- name: Копируем конфиг nginx
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: restart nginx    # вызвать хендлер

# handlers/main.yml
- name: restart nginx
  service:
    name: nginx
    state: restarted
```

### Хендлеры вызываются один раз в конце плейбука, даже если нотификация сработала несколько раз.


## 3. Структура роли

Роль — это самодостаточный набор логики, шаблонов и переменных, который можно переиспользовать.

```
roles/
└── nginx/
    ├── defaults/
    │   └── main.yml        # Переменные по умолчанию (низкий приоритет)
    ├── vars/
    │   └── main.yml        # Жёстко заданные переменные (высокий приоритет)
    ├── tasks/
    │   └── main.yml        # Основные задачи роли
    ├── handlers/
    │   └── main.yml        # Объявленные хендлеры
    ├── templates/
    │   └── nginx.conf.j2   # Jinja2 шаблоны конфигов
    ├── files/
    │   └── index.html      # Файлы для копирования
    ├── meta/
    │   └── main.yml        # Зависимости роли
    └── README.md           # Документация по роли

```
## 4. Пример модуля

| Коллекция              | Назначение                    | Пример модуля                                                                      |
| ---------------------- | ----------------------------- | ---------------------------------------------------------------------------------- |
| `ansible.builtin`      | Встроенные модули Ansible     | `ansible.builtin.copy`, `ansible.builtin.file`, `ansible.builtin.service`          |
| `ansible.posix`        | POSIX/Linux утилиты           | `ansible.posix.firewalld`, `ansible.posix.mount`, `ansible.posix.sysctl`           |
| `community.general`    | Расширенные модули сообщества | `community.general.archive`, `community.general.ufw`, `community.general.ini_file` |
| `community.docker`     | Работа с Docker               | `community.docker.docker_container`, `community.docker.docker_image`               |
| `community.mysql`      | Работа с MySQL                | `community.mysql.mysql_db`, `community.mysql.mysql_user`                           |
| `community.postgresql` | Работа с PostgreSQL           | `community.postgresql.postgresql_db`, `community.postgresql.postgresql_user`       |
| `kubernetes.core`      | Kubernetes API                | `kubernetes.core.k8s`, `kubernetes.core.helm`                                      |


```
#!/usr/bin/python

from ansible.module_utils.basic import AnsibleModule
import os

def run_module():
    # Определяем аргументы, которые модуль принимает
    module_args = dict(
        path=dict(type='str', required=True)
    )

    # Создаём экземпляр модуля
    module = AnsibleModule(
        argument_spec=module_args,
        supports_check_mode=True
    )

    path = module.params['path']

    # Проверяем существование файла
    if os.path.exists(path):
        result = dict(
            changed=False,
            message=f"Файл '{path}' существует."
        )
    else:
        result = dict(
            changed=False,
            message=f"Файл '{path}' не найден!"
        )

    # Возвращаем результат
    module.exit_json(**result)


def main():
    run_module()

if __name__ == '__main__':
    main()

```
