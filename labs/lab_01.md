# Configure Dev environment

## Requiements

- Dedicated Azure resource group
- WSL2
- Visual Studio Code + WSL extension

## Ansible

> Open a WSL terminal

Check if ansible and pywinrm are installed:

```bash
pip show ansible pywinrm
```

If not, install them

```bash
pip install ansible
pip install "pywinrm>=0.3.0"
```

Check ansible version:

```bash
ansible --version
```

Now you can use ansible on your Windows environment :clap:
