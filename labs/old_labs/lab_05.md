# Deploy a static web app on IIS

## Change index.html of the default IIS application

II is already installed on the vm based on the boxe `gusztavvargadr/iis-windows-server`.

Open the url http://127.0.0.1:50080 on your browser: You will see the defaut page of IIS.

1) Create a new role named `lab05`

2) Add an html file on the folder files of the role `lab05`, named `default_index.html` with content:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Ansible Lab 5</title>
    </head>
    <body>
        <p>Hello World</p>
    </body>
</html>
```

3) Add a task to copy the `default_index.html` to the remote directory `C:\\inetpub\\wwwroot\\index.html`

```yml
- name: "Change default website index.html"
  ansible.builtin.win_copy:
    src: index.html
    dest: "C:\\inetpub\\wwwroot\\index.html"
```

4) Include this new role your playbook (you can comment/remove previous roles defined in this playbook)

5) Run your playbook

```bash
ansible-playbook --vault-password-file=.vault_pass deploy.yml -i inventories/dev -vvv
```

6) Open the url http://127.0.0.1:50080 on your browser: You will see the defaut page of IIS has been updated.

## Deploy an IIS application

1) Install the collection `community.windows` if it doesn't exist:

First run:

```bash
ansible-galaxy collection list | grep -i community.windows
```

If the collection `community.windows` is not installed, run:

```bash
ansible-galaxy collection install community.windows
```

2) Add task to create the folder `c:\\inetpub\\wwwroot\\lab05`

3) Add an html file on the folder files of the role `lab05`, named `lab05_index.html` with content:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Ansible Lab 5</title>
    </head>
    <body>
        <p>Hello World. This my new page</p>
    </body>
</html>
```

4) Add a task to copy the `lab05_index.html` to the remote directory `C:\\inetpub\\wwwroot\\lab05\\index.html`

5) Open the documentation of the collection [`community.windows`](https://docs.ansible.com/ansible/latest/collections/community/windows)

6) Create a new IIS pool named `lab05iispool` using the module `win_iis_webapppool`

7) Use the module `win_iis_website` to create a IIS website:

- Named `lab05iissite`
- Linked to the pool `lab05iispool`
- Listing to the port 8080
- physical_path must be `c:\\inetpub\\wwwroot\\lab05`

8) Add a new task that calls the module `community.windows.win_firewall_rule` to allow the port 8080

9) Run your playbook

10) Open the url http://127.0.0.1:8080 on your browser: You will see your new site.

## Deploy dynamic html file using jinja

Same excercie as previously, but this time we will use a jinja template. Here we want to inject the value of a variable in the output index.html

1) Delete the file `lab05_index.html` from files folder.

2) Add a new variable named `welcome_message` with value `World` (in defaults folder)

3) Add a file named `lab05_index.html.j2` under the folder templates and with content:

```jinja
<!DOCTYPE html>
<html>
    <head>
        <title>Ansible Lab 5</title>
    </head>
    <body>
        <p>Hello {{ welcome_message }}. This my new page</p>
    </body>
</html>
```

4) Update the code of the task that copy the file `lab05_index.html` to use the module `win_template` to copy the template `lab05_index.html.j2` to the remote path `C:\\inetpub\\wwwroot\\lab05\\index.html`

5) Run your playbook

6) Open the url http://127.0.0.1:8080 on your browser: You will see the content you have added.

7) Let's update our html templates to handle condition on the value of the variable `welcome_message`

```jinja
<!DOCTYPE html>
<html>
    <head>
        <title>Ansible Lab 5</title>
    </head>
    <body>
        {% if welcome_message == 'World' %}
        <p>Hello {{ welcome_message }}. This my new page</p>
        {% else %}
        <h1>Goodbye {{ welcome_message }}</h1>
        {% endif %}
    </body>
</html>
```

8) Run your playbook by overriding the value of the variable `welcome_message` using the parameter `extra-vars`:

```bash
ansible-playbook --vault-password-file=.vault_pass deploy.yml -i inventories/dev --extra-vars "welcome_message=Windows" -vvv
```

9) Open the url http://127.0.0.1:8080 on your browser: You will see the content you have added.
