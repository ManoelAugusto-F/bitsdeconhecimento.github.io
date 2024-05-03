---
title: "IpFix"
date: 2024-05-03T11:28:07-03:00
author: Manoel A.
tags: [Tutorial,Linux,Debian]
categories: [Tutorial]
draft: false
description: "Configuração de Ip Estatico Debian."
image: ""
---



# Configurando IP estático no Debian.

Neste tutorial, aprenderemos como configurar uma interface de rede para ter um IP estático.

## Quando é necessário ter um IP estático?

Um IP estático é necessário em casos como servidores Web, bancos de dados, serviços de NAS e qualquer serviço que precise se conectar sempre pelo mesmo IP. Se o host estiver configurado para DHCP, o IP pode mudar sempre que for reiniciado ou quando a validade do IP expirar.

## Configuração

Para começar, precisamos saber o nome da nossa interface de rede. Podemos usar o comando:

```bash
ip addr show
```

A saída será algo como:

```bash
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:e0:4c:36:59:04 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.100/24 brd 192.168.1.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::20e:4cff:fe36:5904/64 scope link
       valid_lft forever preferred_lft forever

```

No nosso caso, o nome da interface é `eth0`, mas isso pode e certamente vai mudar dependendo do sistema. Preste atenção nisso.

Agora, vamos acessar o arquivo de configuração das interfaces com o comando:

```bash
nano /etc/network/interfaces
```

Por padrão, o conteúdo do arquivo será semelhante a isto:

```
# Este arquivo descreve as interfaces de rede disponíveis no seu sistema
# e como ativá-las. Para mais informações, consulte interfaces(5).

# A interface de loopback
auto lo
iface lo inet loopback

# A interface de rede primária
allow-hotplug eth0
iface eth0 inet dhcp
```

Vamos começar alterando, comentando as linhas 9 e 10, e adicionar as seguintes linhas:

```bash
# Endereço IP estático
auto eth0
iface eth0 inet static
        address 192.168.1.100
        netmask 255.255.255.0
        network 192.168.1.0
        broadcast 192.168.1.255
        gateway 192.168.1.1

```
Lembre-se de que `eth0` foi mencionado aqui porque é o nome da nossa interface.

Depois de fazer as alterações e salvar o arquivo, vamos reiniciar o serviço:

```bash
systemctl restart network.service
```
E então, podemos verificar se o serviço está em execução:

```bash
systemctl status network.service
```

A saída será algo como:

```bash
● network.service - LSB: Bring up/down networking
   Loaded: loaded (/etc/init.d/network; generated)
   Active: active (exited) since Thu 2024-04-25 12:00:00 UTC; 5min ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 32768)
   Memory: 0B
   CGroup: /system.slice/network.service

Apr 25 12:00:00 debian systemd[1]: Starting LSB: Bring up/down networking...
Apr 25 12:00:00 debian network[1234]: Bringing up loopback interface:  [  OK  ]
Apr 25 12:00:00 debian network[1234]: Bringing up interface eth0:  [  OK  ]
Apr 25 12:00:00 debian systemd[1]: Started LSB: Bring up/down networking.

```

Lembre-se de que essas instruções são válidas para servidores Linux baseados em Debian e para desktops pessoais.
