# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # VM 1: Servidor web (Nginx)
  config.vm.define "proyecto" do |proyecto|
    proyecto.vm.box = "bento/ubuntu-22.04"
    proyecto.vm.hostname = "proyecto"

    # Red interna
    proyecto.vm.network "private_network", ip: "192.168.50.10"

    # Acceso HTTP desde el host -> Nginx (puerto 80 dentro de la VM)
    proyecto.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true

    proyecto.vm.provider "virtualbox" do |vb|
      vb.name = "proyecto"
      vb.memory = 2048
      vb.cpus = 2
    end
  end

  # VM 2: Monitor (Prometheus + Grafana)
  config.vm.define "monitor" do |monitor|
    monitor.vm.box = "bento/ubuntu-22.04"
    monitor.vm.hostname = "monitor"

    # Red interna
    monitor.vm.network "private_network", ip: "192.168.50.11"

    # Puertos pensados para Grafana y Prometheus
    monitor.vm.network "forwarded_port", guest: 3000, host: 3000, auto_correct: true
    monitor.vm.network "forwarded_port", guest: 9090, host: 9090, auto_correct: true

    monitor.vm.provider "virtualbox" do |vb|
      vb.name = "proyecto-monitor"
      vb.memory = 2048
      vb.cpus = 2
    end
  end
end
