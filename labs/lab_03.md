# Create an ansible inventory with secrets

> Open Cygwin terminal

## Init git repository

Create a new git repository for your new ansible project:

> Replace `my-awesome-ansible-projet` by any name you desire

```bash
mkdir my-awesome-ansible-projet
cd my-awesome-ansible-projet
git init
```

## Vault

We will create a file containing a password. This file will be used be ansible tp encypt/decrypt secrets:

> Replace `my_vault_password` with a strong password:

```bash
echo 'my_vault_password' > .vault_pass
```

Update permissions on the vault password file:

```bash
chmod 0600 .vault_pass
```

Add the vault password file to .gitignore to avoid it from being in source control:

```bash
echo '.vault_pass' >> .gitignore
```

## Create your ansible inventory

We will create an and inventory targeting the vm creating in lab 02.

The skeleton of our inventory is:

```plain
├── inventories
│   ├── dev
│   │   ├── group_vars
│   │   │   └── all.yml
│   │   ├── host_vars
│   │   │   └── vagrant_backend.yml
│   │   └── hosts
```

Create the file `inventories/dev/host`:

```bash
mkdir -p inventories/dev

cat <<EOF > inventories/dev/host
[backend]
vagrant_backend
EOF
```

Create the file `inventories/dev/group_vars/all.yml`:

```bash
mkdir -p inventories/dev/group_vars

cat <<EOF > inventories/dev/group_vars/all.yml
ansible_connection: winrm
ansible_winrm_transport : ntlm
ansible_winrm_scheme: http
ansible_winrm_server_cert_validation: ignore
EOF
```

Create the file `inventories/dev/group_vars/vagrant_backend.yml`:

```bash
mkdir -p inventories/dev/host_vars

cat <<EOF > inventories/dev/host_vars/vagrant_backend.yml
ansible_host: "127.0.0.1"
ansible_port : 55985
ansible_user: vagrant
ansible_password: vagrant
EOF
```

Our inventory is done. But it contains a sensitive data: the value of `ansible_password` is not encrypted.

Let's encrypt the password of the remote user with `ansible-vault`, using the vault password file:

```bash
ansible-vault encrypt_string --vault-password-file .vault_pass 'vagrant'
```

The output of the command contains the encrypted value of the password. Repalce the value of the variable `ansible_password` (in the file `inventories/dev/group_vars/vagrant_backend.yml`) by the encypted value.

The file `inventories/dev/group_vars/vagrant_backend.yml` should be similar to:

```yml
ansible_host: "127.0.0.1"
ansible_port : 55985
ansible_user: vagrant
ansible_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          61383263656531396135323537636239626664626465333063326431396163306162666664616139
          3032643938653931643565656364383865623763396163300a313632333830636430363033373638
          37643063636361303962626566376361363133616332303136356334356232393235323163393461
          3938346234313264640a646238663438346265303963353831653030623230386139396163313739
          3866
```

Now let test if our inventory works by running a ping to the windows vm.

The command below will execute the module [win_ping](https://docs.ansible.com/ansible/latest/collections/ansible/windows/win_ping_module.html) to check the connectivity of our windows vm:

```bash
ansible --vault-password-file=.vault_pass all -i inventories/dev -m win_ping
```

A successful output will be:

```bash
vagrant_backend | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

```
