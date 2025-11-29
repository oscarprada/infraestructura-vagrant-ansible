# Proyecto Final – Infraestructura con Vagrant + Ansible + Nginx + Prometheus + Grafana

Este proyecto implementa una infraestructura completa de servidores utilizando **Vagrant**, **VirtualBox**, **Ubuntu 22.04**, **Ansible**, **Nginx**, **Prometheus** y **Grafana**.  
El objetivo es desplegar y automatizar:

- Un servidor web (VM \proyecto\) con **Nginx**.
- Un servidor de monitoreo (VM \monitor\) con **Prometheus + Grafana**.

Todo se configura automáticamente mediante playbooks de **Ansible** ejecutados desde **WSL (Ubuntu)**.

---

## ?? Estructura del Proyecto

\\\
proyecto/
¦
+-- Vagrantfile
+-- ansible/
¦   +-- inventory
¦   +-- nginx.yml
¦   +-- monitor.yml
¦
+-- README.md
\\\

---

## ?? 1. Preparación del Entorno

### ?? Instalar WSL + Ubuntu (en Windows)

En PowerShell:

\\\
wsl --install -d Ubuntu
\\\

Luego en Ubuntu:

\\\
sudo apt update && sudo apt upgrade -y
sudo apt install ansible -y
ansible --version
\\\

---

## ?? 2. Máquinas Virtuales con Vagrant

### ?? Levantar ambas VMs

\\\
cd C:\proyecto
vagrant up
\\\

Para levantarlas individualmente:

\\\
vagrant up proyecto
vagrant up monitor
\\\

Ver estado:

\\\
vagrant status
\\\

Entrar a cada máquina:

\\\
vagrant ssh proyecto
vagrant ssh monitor
\\\

---

## ??? 3. Configuración Automática con Ansible

### ?? Archivo de inventario (\inventory\)

\\\ini
[web]
proyecto ansible_host=192.168.50.10 ansible_user=vagrant ansible_ssh_private_key_file=../.vagrant/machines/proyecto/virtualbox/private_key

[monitor]
monitor ansible_host=192.168.50.11 ansible_user=vagrant ansible_ssh_private_key_file=/home/oscar_prada/.ssh/monitor_key
\\\

---

## ?? Playbook: Nginx (VM proyecto)

Archivo: \
ginx.yml\

\\\yaml
---
- name: Configurar servidor web Nginx
  hosts: web
  become: yes

  tasks:
    - name: Actualizar repos
      apt:
        update_cache: yes

    - name: Instalar Nginx
      apt:
        name: nginx
        state: present

    - name: Habilitar y arrancar servicio Nginx
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Crear página HTML personalizada
      copy:
        dest: /var/www/html/index.html
        content: "<h1>Servidor Nginx configurado con Ansible</h1>"
\\\

Ejecutarlo:

\\\
ansible-playbook -i inventory nginx.yml
\\\

Validación:

\\\
http://localhost:8080
\\\

---

## ?? Playbook: Prometheus + Grafana (VM monitor)

Archivo: \monitor.yml\

\\\yaml
---
- name: Configurar servidor de monitoreo Prometheus y Grafana
  hosts: monitor
  become: yes

  tasks:
    - name: Actualizar repos
      apt:
        update_cache: yes

    - name: Instalar Prometheus
      apt:
        name:
          - prometheus
        state: present

    - name: Agregar clave GPG de Grafana
      apt_key:
        url: https://packages.grafana.com/gpg.key
        state: present

    - name: Agregar repositorio de Grafana
      apt_repository:
        repo: "deb https://packages.grafana.com/oss/deb stable main"
        state: present

    - name: Actualizar repos después de agregar Grafana
      apt:
        update_cache: yes

    - name: Instalar Grafana
      apt:
        name: grafana
        state: present

    - name: Habilitar y arrancar servicio Prometheus
      service:
        name: prometheus
        state: started
        enabled: yes

    - name: Habilitar y arrancar servicio Grafana
      service:
        name: grafana-server
        state: started
        enabled: yes
\\\

Ejecutarlo:

\\\
ansible-playbook -i inventory monitor.yml
\\\

Validación:

- Prometheus ? http://localhost:9090  
- Grafana ? http://localhost:3000  

---

## ?? Validación Interna

\\\
systemctl status nginx
systemctl status prometheus
systemctl status grafana-server
\\\

---

## ?? Evidencias Requeridas

- Capturas de \agrant up\
- Ejecución de Ansible
- Web corriendo en localhost:8080
- Prometheus en 9090
- Grafana en 3000
- Estados de servicios systemctl

---

## ?? Cumplimiento de la Rúbrica

- Infraestructura como código  
- Automatización 100% funcional  
- Servicios desplegados correctamente  
- Documentación profesional  
- Seguridad (gitignore)

