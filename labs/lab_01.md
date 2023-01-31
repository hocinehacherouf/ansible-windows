# Install Ansible on Cygwin

> Open an elevated PowerShell terminal (Run as administrator)

Install Chocolaty:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Install cygwin and cyg-get using Chocolaty:

```powershell
choco install cygwin cyg-get -y
```

Install ansible requirements (on) using cyg-get:

```powershell
cyg-get openssh python38 python38-pip python38-devel libssl-devel libffi-devel gcc-g++ python38-cryptography
```

> Open Cygwin terminal

Install pip module `whell`, `ansible` and `pywinrm`:

```bash
/usr/bin/python3.8.exe -m pip install wheel
/usr/bin/python3.8.exe -m pip install ansible
/usr/bin/python3.8.exe -m pip install "pywinrm>=0.3.0"
```

Check ansible version:

```bash
ansible --version
```

Now you can use ansible on your Windows environment :clap:
