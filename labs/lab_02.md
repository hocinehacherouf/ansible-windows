# Configure testing environement by vagrant

> Open an elevated PowerShell terminal (Run as administrator)

Install virtualbox and vagrant using Chocolaty

```powershell
choco install virtualbox vagrant -y
```

Navigate to `vagrant` folder using your terminal

```powershell
cd vagrant
```

Run vagrant

```powershell
vagrant up
```

Remarks:

- Vagrant will download a virtualbox image of a windows-server-core
- Then it will run it under virtualbox

Now you have a windows-server-core vm ready to be used by ansible :clap:
