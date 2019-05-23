# Ansible

## Praktische Rollen
* #### [dev-sec.os-hardening](https://github.com/dev-sec/ansible-os-hardening)
  Setzt sichere Default-Werte für die Systemkonfiguration, z. B. kein Root-Login via SSH, Installation von signierten Packages, …
  
* #### [dev-sec.ssh-hardening](https://github.com/dev-sec/ansible-ssh-hardening)
  Setzt sichere Default-Werte für `sshd`, z. B. Verbot von Passwort-Authentifikation, Beschränkung der Login-Versuche, …

## Privilege Escalation
Der `become`-Parameter kann sowohl für einzelne Tasks als auch für ganze Rollen und Playbooks gesetzt werden. Die entsprechenden Tasks werden dann mit Root-Rechten ausgeführt.

## Praktische Tasks

### Mehrere Nutzer hinzufügen

```yml
name: add users
user:
  name: "{{ item }}"
  groups: "{{ admin_group }}"
with_items: "{{ admin_users }}"
become: yes
```

### SSH-Keys hinzufügen

```yml
name: add public ssh keys for all users
authorized_key:
  user: "{{ item }}"
  key: "{{ lookup('file', 'files/' + item + '.key.pub') }}"
with_items: "{{ admin_users }}"
become: yes
```
