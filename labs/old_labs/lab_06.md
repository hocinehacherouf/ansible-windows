# Testing with molecule

## Install Molecule and vagrant driver

```bash
/usr/bin/python3.8.exe -m pip install molecule 'molecule-plugins[vagrant]' molecule-vagrant
```

Check available drivers for molecule

```bash
molecule drivers
```

## Create new role using Molecule

```bash
cd roles

molecule init role mycompany.lab06 --driver-name vagrant
```

## Update Molecule configuraiton

On the directory of your new role, go to the folder `molecule > default`

1) Add a new file `prepare.yml` with content:

```yml
---
- name: Prepare
  hosts: all
  gather_facts: false
  tasks:
    - name: Ignore prepare senario for windows host
      ansible.builtin.debug:
        msg:
        - "No need to run the prepare senario for windows host"
```

The purpose of this file is to override the default senario of Molecule, which is specific for linux hosts.

2) Replace the content of the file `molecule.yml`:

```yml
---
dependency:
  name: galaxy
driver:
  name: vagrant
  provider:
    name: virtualbox
platforms:
  - name: instance
    box: gusztavvargadr/iis-windows-server
    memory: 4096
    cpu: 2
    instance_raw_config_args:
      - "winrm.timeout = 1800"
      - "vm.boot_timeout = 3000"
      - "vm.box_download_insecure = true"
      - "vm.network 'forwarded_port', guest: 8080, host: 8080"
provisioner:
  name: ansible
  connection_options:
    ansible_host: "127.0.0.1"
    ansible_port : 55985
    ansible_user: vagrant
    ansible_password: vagrant
    ansible_connection: winrm
    ansible_winrm_transport : ntlm
    ansible_winrm_scheme: http
    ansible_winrm_server_cert_validation: ignore
  env:
    ANSIBLE_VERBOSITY: 3
verifier:
  name: ansible
```

## Play with molecule commands

> Molecule commands must be ran under the folder of your ansible role.

1) List instances

```bash
molecule list
```

2) Create your testing instance:

```bash
molecule create
```

⚠️ The first time you run molecule create, the command may fail due to a winrm error. Just re-run this command.

Run `molecule list` to see the state of your instance:

```bash
molecule list
```

2) Ran your role against the test instance

```bash
molecule converge
```

Remarks:
- The tasks of your role are empty `molecule converge` will do nothing
- The converge senario will run the create and prepare senarios if they haven't been executed

Run `molecule list` to see the state of your instance:

```bash
molecule list
```

3) Run your tests

Tests are defined on the file `molecule/default/vertify.yml`

```bash
molecule verify
```

## Exercices

For each excercice, you to implement a solution its unit test to verify it

1) Create a file with the content "Hello World" (Hint: For the tests, check if the file exists and verify its content)

2) Create a windows service (Hint: To test if the service exists, first load and register all services then check if expected service name is in the list of services. You can also check the state of this service)

3) Deploy a new IIS application (Hint: To test if the application is responding, first call the url of the application using the module `ansible.windows.win_uri` to store the status code of the response)