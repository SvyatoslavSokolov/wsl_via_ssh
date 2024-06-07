# WSL SSH Configuration with NVIDIA-SMI Access

This guide will help you configure your Windows Subsystem for Linux (WSL) machine to allow SSH connections and X11 forwarding, enabling access to NVIDIA GPU resources.

## Prerequisites

- WSL installed on your Windows machine with an Ubuntu distribution.

## Steps

### 1. Configure SSH Server in WSL

Open the `sshd_config` file:

```bash
sudo nano /etc/ssh/sshd_config
```

Uncomment and modify the following lines:

- Change the default SSH port to a different one, for example 2200. This prevents conflicts with your local machine's SSH server. (if needed)
- Allow password authentication (if needed).

The modified section should look like this:

```bash
Port 22
PasswordAuthentication yes
```

Save and close the file (use `Ctrl + X`, then `Y`, then `Enter`).

### 2. Create Port Forwarding Rule in Windows

Open PowerShell as Administrator and run these commands:

1. Add a port proxy to forward incoming connections on port 2200 to your WSL machine's SSH server (port 22):

```powershell
netsh interface portproxy add v4tov4 listenaddress=192.168.1.90 listenport=2200 connectaddress=172.21.154.95 connectport=22
```

Replace `192.168.1.90` with your Windows machine's IP address and `172.21.154.95` with your WSL machine's IP address (you can find this by running `ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'` in WSL).

2. Add a firewall rule to allow incoming connections on port 2200:

```powershell
netsh advfirewall firewall add rule name=”Open Port 2200 for WSL2” dir=in action=allow protocol=TCP localport=2200
```

### 3. Configure NVIDIA-SMI Access in WSL

Add the following line to your `~/.bashrc` file to enable access to NVIDIA GPU resources:

```bash
echo 'export PATH=/usr/lib/wsl/lib:$PATH' >> ~/.bashrc
```

Apply the changes by sourcing the `.bashrc` file or restarting your WSL shell.

## Usage

1. Start your SSH server in WSL with `sudo service ssh start`.
2. Connect to your WSL machine's SSH server from your local machine using an SSH client (e.g., PuTTY or OpenSSH), connecting to port 2200.
3. Run GPU-accelerated applications as needed on your WSL machine, and they will display their output on your local machine's screen.
 
#

# Настройка SSH и доступа к NVIDIA-SMI в WSL

Этот гайд поможет вам настроить подсистему Windows для Linux (WSL) для разрешения подключений по протоколу SSH и перенаправления X11, что позволит получить доступ к ресурсам графического процессора NVIDIA.

### 1. Настройка SSH-сервера в WSL

Откройте файл `sshd_config`:

```bash
sudo nano /etc/ssh/sshd_config
```

Раскомментируйте и измените следующие строки:

- Измените порт SSH по умолчанию на другой, например 2200. Это предотвращает конфликты с локальным сервером SSH вашей машины. (если это необходимо)
- Разрешите аутентификацию по паролю (если это необходимо).

Изменённый раздел должен выглядеть так:

```bash
Port 2200
PasswordAuthentication yes
```

Сохраните и закройте файл (используйте `Ctrl + X`, затем `Y`, затем `Enter`).

### 2. Создание правила переадресации портов в Windows

Откройте PowerShell с правами администратора и выполните следующие команды:

1. Добавьте прокси-порт для перенаправления входящих подключений на порт 2200 на сервер SSH вашей машины WSL (порт 22):

```powershell
netsh interface portproxy add v4tov4 listenaddress=192.168.1.90 listenport=2200 connectaddress=172.21.154.95 connectport=22
```

Замените `192.168.1.90` на IP-адрес вашей Windows машины и `172.21.154.95` на IP-адрес вашей машины WSL (вы можете узнать его, выполнив команду `ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'` в WSL).

2. Добавьте правило брандмауэра для разрешения входящих подключений на порт 2200:

```powershell
netsh advfirewall firewall add rule name=”Open Port 2200 for WSL2” dir=in action=allow protocol=TCP localport=2200
```

### 3. Настройка доступа к NVIDIA-SMI в WSL

Добавьте следующую строку в ваш файл `~/.bashrc`, чтобы включить доступ к ресурсам GPU NVIDIA:

```bash
echo 'export PATH=/usr/lib/wsl/lib:$PATH' >> ~/.bashrc
```

Примените изменения, перезагрузив файл `.bashrc` или перезапустив вашу оболочку WSL.

### Использование

1. Запустите сервер SSH в WSL с помощью команды `sudo service ssh start`.
2. Подключитесь к серверу SSH вашей машины WSL из локальной машины, используя клиент SSH (например, PuTTY или OpenSSH), подключаясь к порту 2200.
3. При необходимости запускайте GPU-ускоренные приложения на вашей машине WSL, и их вывод будет отображаться на экране вашей локальной машины.```