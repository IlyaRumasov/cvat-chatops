# ⚙️ Deploy Backend (Provisioning Service)

> Фізичний виконавець інфраструктурних змін. Генерує Bash-скрипти, взаємодіє з Hetzner та Cloudflare API.

## 📋 Опис сервісу

Цей мікросервіс викликається агентом і працює у фоновому режимі. Його завдання — трансформувати JSON-параметри в реальну інфраструктуру. 

Сервіс формує масивний `Cloud-Init` скрипт, який містить усі команди для встановлення Docker, клонування репозиторію CVAT, налаштування Zabbix-агента, брандмауера (UFW) та Fail2Ban. Скрипт кодується в Base64 і передається в Hetzner під час створення ВМ. Паралельно сервіс оновлює або створює A-записи в Cloudflare для підключення домену.

## 🛠 Ключові ноди
* **`[Trigger] Deployment Entrypoint`:** Точка входу від основної схеми агента.
* **`Script: Build Provisioning Script`:** Ядро логіки. Створює Bash-скрипт (з обробкою помилок `trap`) та пакує його в JSON для Hetzner.
* **`[API] Provision Cloud Compute`:** HTTP POST-запит до Hetzner Cloud API для створення сервера.
* **`[API] Check DNS Inventory` / `[API] Patch Host Record`:** Запити до Cloudflare для перевірки та маршрутизації домену на новий IP.
* **`[DB] Init Infrastructure Record`:** Запис початкового статусу (`Installing`) у внутрішню таблицю n8n.