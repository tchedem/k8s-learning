Will working with kubernate, we can have frequently to change context or namespace. Doing it with the `kubectl` command is lengthy, can be overwhelming and sometimes confusing. 

That is the reason why some guys develop tools like

`kubectx` (context) and `kubens` (namespace).

#### Kubectx:

Installation:
```bash
sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx
sudo ln -s /opt/kubectx/kubectx /usr/local/bin/kubectx
```

Syntax:
```bash
# To list all contexts:
kubectx

# To switch to a new context:
kubectx <context_name>

# To switch back to previous context:
kubectx -

# To see current context:
kubectx -c
```

#### Kubens:

This tool allows users to switch between namespaces quickly with a simple command.

Installation:
```bash
sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens
```

Syntax:
```bash
# To switch to a new namespace:
kubens <new_namespace>

# To switch back to previous namespace:
kubens -
```