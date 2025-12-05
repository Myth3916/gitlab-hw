
# Домашнее задание к занятию 2 «Кластеризация и балансировка нагрузки» — Шаров Олег

## Задание 1

Настроена балансировка с помощью HAProxy на **4-м уровне (L4/TCP)** с алгоритмом **roundrobin** между двумя простыми Python-серверами.

### Этапы выполнения

1. Созданы два HTML-файла в разных каталогах :
   - `index.html`:  
     ```text
     Server 1 Port 8888
     ```
   - `index.html`:  
     ```text
     Server 2 Port 9999
     ```

2. Запущены два Python-сервера каждый из своего каталога, там, где созданы файлы index.html:
   ```bash
   python3 -m http.server 8888 --bind 127.0.0.1 
   python3 -m http.server 9999 --bind 127.0.0.1 
   ```

3. Установлен и настроен HAProxy с балансировкой на порту `1325`.

4. Выполнена проверка: несколько `curl`-запросов к `http://127.0.0.1:1325` показывают чередование ответов от двух серверов.

### Конфигурационный файл HAProxy (`/etc/haproxy/haproxy.cfg`)

```cfg
global
    log /dev/log local0
    chroot /var/lib/haproxy
    user haproxy
    group haproxy
    daemon

defaults
    log global
    mode tcp
    option dontlognull
    timeout connect 5000
    timeout client  50000
    timeout server  50000

listen web_tcp
    bind :1325
    mode tcp
    balance roundrobin
    server s1 127.0.0.1:8888 check inter 3s
    server s2 127.0.0.1:9999 check inter 3s
```

### Скриншоты

![Запущенные Python-серверы на портах 8888 и 9999](img/1.png)

![Конфигурация HAProxy: секция listen web_tcp с балансировкой roundrobin на 4-м уровне](img/2.png)

![Проверка балансировки: чередование ответов от s1 и s2 при запросах к HAProxy на порту 1325](img/3.png)




