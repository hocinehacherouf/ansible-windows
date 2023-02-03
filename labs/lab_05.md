# Deploy a static web app

## Deploy apache-httpd

1) Create a new role named `httpd`

2) Create a task in this role to install apache-httpd using chocolaty

The package `apache-httpd` should be

- Installed under the path C:\HTTPD
- Listen to port 8080

See [https://community.chocolatey.org/packages/apache-httpd](https://community.chocolatey.org/packages/apache-httpd) to see how how to set this

3) Include this new role your playbook (you can comment/remove previous roles defined in this playbook)

4) Run your playbook

```bash
ansible-playbook --vault-password-file=.vault_pass deploy.yml -i inventories/dev -vvv
```

## Allow the port of apache-httpd

After the deployment, listening to port 8080. This port is not allowed by the firewall of the guest vm.

> Goal: Allow the port 8080 on the firewall of the vm using ansible

To allow a port on the firewall of a windows using ansible, we need to use the module [`win_firewall_rule`](https://docs.ansible.com/ansible/latest/collections/community/windows/win_firewall_rule_module.html#ansible-collections-community-windows-win-firewall-rule-module), included in the ansible collection [`community.windows`](https://docs.ansible.com/ansible/latest/collections/community/windows)

1) Install the collection `community.windows`:

```bash
ansible-galaxy collection install community.windows
```

2) Add a new task in the role httpd that calls the module `win_firewall_rule` to allow the port 8080

4) Run your playbook

After the execution of your playbook, the firewall

5) Open the url http://127.0.0.1:8080 on your browser: You will see the default html page of apache httpd.

## Replace the default html page of apache httpd with a static html file using ansible

1) Create a new role named static-site

2) Add an html file named index.html within the role static-site, under the folder files

3) Add some contents to this html file (e.g. Hello World)

4) Create a task in this role to copy this html file and replace the default html of apache httpd.

- To copy the file, you can use the module [`win_copy_module`](https://docs.ansible.com/ansible/latest/collections/ansible/windows/win_copy_module.html)
- The location of the (remote) defautl html page of apache-httpd is `C:\HTTPD\Apache24\htdocs\index.html`

5) Run your playbook

6) Open the url http://127.0.0.1:8080 on your browser: You will see the content you have added.

## Deploy html file using a jinja template

Same excercie as previously, but this time we will use a jinja template. Here we want to inject the value of a variable in the output index.html

1) Comment the task that you previously created to copy the file index.html

2) Add a new variable named `welcome_message` with value `World` (in vars folder)

3) Add a file named `index.html.j2` under the folder templates and with content:

```jinja
<h1>Hello {{ welcome_message }}</h1>
```

4) Create a task in this role to copy this jinja html template and replace the default html of apache httpd.

- To copy the file, you can use the module [`win_template`](https://docs.ansible.com/ansible/latest/collections/ansible/windows/win_template_module.html)
- The location of the (remote) defautl html page of apache-httpd is `C:\HTTPD\Apache24\htdocs\index.html`

5) Run your playbook

6) Open the url http://127.0.0.1:8080 on your browser: You will see the content you have added.

7) Let's update our html templates to handle conditional f

```jinja
{% if welcome_message == 'World' %}
    <h1>Hello {{ welcome_message }}</h1>
{% else %}
    <h1>Goodbye {{ welcome_message }}</h1>
{% endif %}
```

8) Run your playbook by overriding the value of the variable `welcome_message` using the parameter `extra-vars`:

```bash
ansible-playbook --vault-password-file=.vault_pass deploy.yml -i inventories/dev --extra-vars "welcome_message=Windows" -vvv
```

9) Open the url http://127.0.0.1:8080 on your browser: You will see the content you have added.

