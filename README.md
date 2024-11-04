# KALINUX-SECURITY-RESPONDER

> **If it happens that you got attacked, or your system/server - make this guy regret about it.**
> Not my responsibility.

![image](https://github.com/user-attachments/assets/afb890c2-82d9-4eb7-b99e-ca8beced16d3)


I created this code solely for educational/testing purposes, or to use as a self-defense tool. I prohibit its use for illegal purposes or as a means of attacking someone's property without their consent and I am not responsible for any actions of people who violate these terms.

Everyone has a right to copy, modify, use, and share. The only thing this license prohibits is the use of this code for illegal purposes and for all other purposes that do not fall under the categories of penetration testing, security testing, or training.

The author is not responsible for any actions of people who copied and/or used this code or its fragments.

---

## Table of Contents
- [About the Project](#about-the-project)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Supported Attacks](#supported-attacks)
- [Configuration Options](#configuration-options)
- [Dependencies](#dependencies)
- [License](#license)

---

## About the Project
**KALINUX-SECURITY-RESPONDER** is an advanced Python-based security tool designed for administrators, penetration testers, and developers to simulate various types of network traffic for testing and security training purposes. It features a variety of methods for simulating real-world network attacks, from HTTP Floods to Slowloris and UDP floods, enabling you to stress-test and improve your server’s defenses.

This tool can be useful for self-defense and defensive testing under controlled conditions.

---

## Features
- **Multiple Attack Types**: Perform ICMP, UDP, TCP SYN Floods, HTTP Floods, and Slowloris attacks.
- **Customizable**: Easily configure the number of requests, threads, and attack duration.
- **Parallel Execution**: Execute multiple attack methods in parallel with adjustable threading.
- **Randomized Requests**: Randomize HTTP headers, request paths, and other details for a realistic test.
- **Logging and Reports**: Generates logs for each attack, including any errors encountered.

---

## Installation
1. **Clone the Repository**
   ```bash
   git clone https://github.com/yourusername/KALINUX-SECURITY-RESPONDER.git
   cd KALINUX-SECURITY-RESPONDER
   ```
2. **Install Dependencies**
   Ensure all dependencies are installed before running the script:
   ```bash
   pip install -r requirements.txt
   ```
3. **Configure Execution Permissions** (if necessary)
   ```bash
   chmod +x responder.sh
   ```

---

## Usage
To launch an attack, run the `responder.sh` script and follow the prompts to select your attack type, target, and other configurations.
```bash
./responder.sh
```

### Basic Attack Execution Example
1. **Specify Target Information**  
   - Target IP or domain name.
   - Target port (default is 80).
2. **Choose Attack Type**
   - Select from available attack methods such as `ICMP`, `UDP`, `TCP`, `HTTP`, etc.
3. **Set Configurations**  
   - Define the number of requests, duration, and threading as prompted.

### Supported Commands
- **HTTP Flood**: Sends a high volume of HTTP requests to overload a web server.
- **TCP SYN Flood**: Opens multiple half-open TCP connections to exhaust server resources.
- **ICMP Flood**: Overwhelms a server with ICMP packets.
- **Slowloris**: Maintains open connections to exhaust server’s socket pool.

---

## Supported Attacks
| Attack Type      | Description                                         |
|------------------|-----------------------------------------------------|
| **ICMP Flood**   | Rapid ICMP packet sending to saturate target’s network. |
| **UDP Flood**    | High volume of UDP packets to overload the target.      |
| **TCP SYN Flood**| Initiates many half-open TCP connections.             |
| **HTTP Flood**   | HTTP requests sent to overwhelm the web server.       |
| **Slowloris**    | Maintains many open connections to drain server resources.|
| **T50 Attack**   | Mixed protocol-based flood attack.                     |
| **Siege Attack** | Emulates multiple HTTP requests for load testing.      |

---

## Configuration Options
All configuration options can be set interactively or by editing the script:
- `target_ip`: Set the IP address or domain of the target server.
- `target_port`: Port to target (default is 80).
- `num_requests`: Number of requests per attack.
- `max_threads`: Max number of parallel threads (default 4).
- `attack_duration`: Duration in seconds for each attack run.

---

## Dependencies
This project requires:
- `Python 3.x`
- `aiohttp`
- `colorama`
- `subprocess`
- `asyncio`
- `ThreadPoolExecutor`
- `hping3`
- `siege`
- `t50`

To install dependencies, run:
```bash
pip install aiohttp colorama
```

System packages:
```bash
sudo apt-get install hping3 siege
```

---

## License
This code is licensed under a self-defense and educational use license. You may use, modify, and distribute this code, but **only** for legal, educational, and defensive testing purposes. The author holds no liability for misuse.

---

---

# KALINUX-SECURITY-RESPONDER

> **Если так получилось, что вас атаковали, или вашу систему/сервер - заставьте обидчика пожалеть об этом.**
> Не моя ответственность.

Этот код создан исключительно для образовательных и тестовых целей или для использования в целях самозащиты. Я запрещаю его использование в незаконных целях или для нападения на чужую собственность без согласия владельца и не несу ответственности за любые действия людей, которые нарушают эти условия.

Любой имеет право копировать, модифицировать, использовать и распространять этот код. Единственное, что запрещает эта лицензия - это использование кода в незаконных целях или для любых других целей, которые не входят в категории тестирования безопасности, тестирования проникновения или обучения.

Автор не несет ответственности за любые действия лиц, которые копировали и/или использовали этот код или его фрагменты.

---

## Содержание
- [О проекте](#о-проекте)
- [Функции](#функции)
- [Установка](#установка)
- [Использование](#использование)
- [Поддерживаемые атаки](#поддерживаемые-атаки)
- [Настройки конфигурации](#настройки-конфигурации)
- [Зависимости](#зависимости)
- [Лицензия](#лицензия)

---

## О проекте
**KALINUX-SECURITY-RESPONDER** — это продвинутый инструмент безопасности на Python, предназначенный для администраторов, тестеров и разработчиков для моделирования различных типов сетевого трафика в целях тестирования и обучения. Этот инструмент позволяет имитировать атаки разного типа, включая HTTP Flood, Slowloris и UDP flood, чтобы подвергнуть сервер нагрузочному тестированию и повысить его защиту.

---

## Функции
- **Разнообразие атак**: выполняет ICMP, UDP, TCP SYN Flood, HTTP Flood и атаки Slowloris.
- **Гибкость настройки**: легко настраивается количество запросов, потоков и длительность атаки.
- **Параллельное выполнение**: поддержка многопоточности для одновременных атак.
- **Рандомизация запросов**: случайные HTTP-заголовки и URL для реалистичного теста.
- **Логирование**: создаёт лог-файл для каждой атаки, включая обнаруженные ошибки.

---

## Установка
1. **Клонируйте репозиторий**
   ```bash
   git clone https://github.com/yourusername/KALINUX-SECURITY-RESPONDER.git
   cd KALINUX-SECURITY-RESPONDER
   ```
2. **Установите зависимости**
   Убедитесь, что все зависимости установлены перед запуском скрипта:
   ```bash
   pip install -r requirements.txt
   ```

---

## Использование
Для запуска атаки используйте скрипт `responder.sh` и следуйте инструкциям для выбора типа атаки, цели и других параметров.
```bash
./responder.sh
```

### Пример запуска атаки
1. **Укажите цель**  
   - IP-адрес или домен цели.
   - Порт цели (по умолчанию 80).
2. **Выберите тип атаки**
   - Выберите доступные методы атак, например, `ICMP`, `UDP`, `TCP`, `HTTP` и т.д.
3. **Настройка параметров**  
   - Укажите количество запросов, длительность и потоки.

### Поддерживаемые команды
- **HTTP Flood**: отправка большого объёма HTTP-запросов для перегрузки сервера.
- **TCP SYN Flood**: множественные незавершённые TCP-соединения.
- **ICMP Flood**: отправка ICMP-пакетов для перегрузки сети.
- **Slowloris**: поддержание открытых соединений для исчерпания ресурсов сервера.

---

## Поддерживаемые атаки
| Тип атаки        | Описание                                                  |
|------------------|-----------------------------------------------------------|
| **ICMP Flood**   | Быстрая отправ

ка ICMP-пакетов для перегрузки сети.       |
| **UDP Flood**    | Высокий объём UDP-пакетов для перегрузки цели.            |
| **TCP SYN Flood**| Множество незавершённых TCP-соединений для исчерпания ресурсов.|
| **HTTP Flood**   | HTTP-запросы для перегрузки веб-сервера.                  |
| **Slowloris**    | Поддержание открытых соединений для перегрузки сокетов.   |
| **T50 Attack**   | Атака с использованием смешанных протоколов.              |
| **Siege Attack** | Эмуляция множественных HTTP-запросов для нагрузочного теста. |

---

## Настройки конфигурации
Все настройки можно указать интерактивно или изменяя сам скрипт:
- `target_ip`: IP-адрес или домен цели.
- `target_port`: Целевой порт (по умолчанию 80).
- `num_requests`: Количество запросов на атаку.
- `max_threads`: Максимальное количество потоков (по умолчанию 4).
- `attack_duration`: Длительность атаки в секундах.

---

## Зависимости
Необходимые для работы:
- `Python 3.x`
- `aiohttp`
- `colorama`
- `subprocess`
- `asyncio`
- `ThreadPoolExecutor`
- `hping3`
- `siege`
- `t50`

Установка Python-зависимостей:
```bash
pip install aiohttp colorama
```

Установка системных пакетов:
```bash
sudo apt-get install hping3 siege
```

---

## Лицензия
Код лицензирован для использования в целях самозащиты и обучения. Вы можете использовать, модифицировать и распространять его, но **только** в законных, образовательных и защитных целях. Автор не несет ответственности за злоупотребления.

---
