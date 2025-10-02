
# 📘 Desafio de Redes ITEP  
**Projeto:** Configuração de Switch Camada 3 com VLANs, ACLs, DHCP e DNS

---

## 🧰 Equipamentos Utilizados

- **Switch Cisco 3650-24PS**  
  - Camada 3 (Layer 3)  
  - Compartimento extra de fonte para redundância de alimentação

- **NB01**  
  - Conectado ao switch via cabo console USB  
  - Acesso via terminal com drivers Cisco USB instalados

---

## 🔐 Configurações Iniciais de Segurança

### 🔧 Nomeação do Switch
```bash
Switch>en
Switch#conf t
Switch(config)#hostname ITEP_SW01
```

### 🔒 Senhas para acesso ao modo privilegiado, console e VTY (limitando acessos simultâneos) criptografadas:
```bash
ITEP_SW01(config)#service password-encryption
ITEP_SW01(config)#enable secret It3P,9
ITEP_SW01(config)#line con 0
ITEP_SW01(config-line)#password CoN0_eLO$
ITEP_SW01(config-line)#login
ITEP_SW01(config-line)#exit
ITEP_SW01(config)#line vty 0 4
ITEP_SW01(config-line)#password v1R_Ty16!
ITEP_SW01(config-line)#login
ITEP_SW01(config-line)#^Z
ITEP_SW01#
%SYS-5-CONFIG_I: Configured from console by console

ITEP_SW01#wr
Building configuration...
Compressed configuration from 7383 bytes to 3601 bytes[OK]
[OK]
ITEP_SW01#
```

---

## 🌐 Configuração de VLANs e Interfaces

```bash
ITEP_SW01(config)#ip routing
ITEP_SW01(config)#vlan 10
ITEP_SW01(config-vlan)#name TI
ITEP_SW01(config)#vlan 20
ITEP_SW01(config-vlan)#name ADM
ITEP_SW01(config)#vlan 30
ITEP_SW01(config-vlan)#name PESQ
ITEP_SW01(config)#vlan 100
ITEP_SW01(config-vlan)#name DATACENTER
```

### 🖧 Interfaces VLAN
```bash
interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface vlan 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown

interface vlan 30
 ip address 192.168.30.1 255.255.255.0
 no shutdown

interface vlan 100
 ip address 192.168.100.1 255.255.255.0
 no shutdown
```

---

## 🔌 Atribuição de Portas às VLANs

```bash
interface range gigabitEthernet1/0/1-3
 switchport mode access
 switchport access vlan 10

interface range gigabitEthernet1/0/4-5
 switchport mode access
 switchport access vlan 20

interface range gigabitEthernet1/0/6-7
 switchport mode access
 switchport access vlan 30

interface gigabitEthernet1/0/8
 switchport mode access
 switchport access vlan 100
```

---

## 🚫 Configuração de ACLs

```bash
ip access-list extended ITEP-ACL
 permit ip 192.168.10.0 0.0.0.255 192.168.100.0 0.0.0.255
 deny ip 192.168.20.0 0.0.0.255 192.168.100.0 0.0.0.255
 deny ip 192.168.30.0 0.0.0.255 192.168.100.0 0.0.0.255
 deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255
 deny ip 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255
 permit ip any any
```

### 🔗 Aplicação da ACL
```bash
interface vlan 20
 ip access-group ITEP-ACL in

interface vlan 30
 ip access-group ITEP-ACL in

interface vlan 100
 ip access-group ITEP-ACL in
```

---

## 🔍 Comandos de Verificação

```bash
show vlan brief
show ip interface brief
show ip route
show access-lists
```

---

## 📢 Banner de Aviso (MOTD)

```bash
banner motd #
**************************
ITEP - ACESSO RESTRITO!!!
Somente Pessoal Autorizado
Ambiente Monitorado
**************************
#
```

---

## 🧭 Configuração de DHCP e DNS

### 🔒 Exclusão de IPs Reservados
```bash
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10
```

### 📦 Pools DHCP por VLAN
```bash
ip dhcp pool TI
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 192.168.100.2

ip dhcp pool ADM
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 192.168.100.10

ip dhcp pool PESQ
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 192.168.100.2
```

> **No Web_Server:** habilitar serviço DNS e adicionar o registro `srv.itep.local` apontando para o IP do servidor.

---

## 🛠️ Melhorias Adicionais

### 🚫 Desativar resolução automática de nomes inválidos
```bash
no ip domain-lookup
```

