
# 📘 Desafio de Redes ITEP  
**Projeto:** Configuração de Switch Camada 3 com VLANs, ACLs, DHCP e DNS

---

## 🧰 Equipamentos Utilizados

- **Switch Cisco 3650-24PS**  
  - Switch de Camada 3 (Layer 3);
  - 24 portas Ethernet 10/100/1000 com PoE+;
  - 4 portas SFP para uplink (1 Gbps);
  - Suporte a PoE+ (até 30W por porta, total de 390W);
  - Capacidade de empilhamento (StackWise-160) até 9 switches;
  - Capacidade de comutação: 88 Gbps;
  - Controlador wireless integrado (até 50 APs e 1000 clientes);
  - Fonte de alimentação modular e redundante;
  - Memória RAM: 4 GB | Flash: 2 GB;
  - Tamanho: 1U rack | Peso: ~17,5 kg;
 
- **Datacenter - Servidor Web**
  - CPU Intel Xeon ou AMD EPYC (mínimo 4 núcleos, ideal 8+);
  - 32GB (Mínimo) de Memória RAM DDR5;
  - SSD NVMe de 01TB ou superior;
  - Placa de rede 10 Gbps;
  - Fonte redundante;
  - Sistema operacional Linux (Ubuntu Server, Debian ou CentOS)
  
- **Notebooks TI**  
  - CPU Intel Core i5 de 13a Geração ou superior;
  - 16 GB de memória RAM DDR5;
  - SSD NVMe de 480GB ou superior;
  - USB 3.2 Gen 1 ou superior, USB tipo C + Display Port 2.1;
  - Rede Fast Ethernet Gigabite 10/100/1000;
  - Teclado ABNT2 com parte númerica;
  - Sistema Operacional Windows 11 PRO.

- **PCS ADM**  
  - CPU Intel i3 de 13a Geração ou superior;
  - 08 GB de memória RAM DDR5;
  - SSD NVMe de 480GB ou superior;
  - Placa mãe com no mínimo 04 portas USB 3.0;
  - Rede integrada Fast Ethernet Gigabite 10/100/1000;
  - Fonte de alimentação de 400W ou superior com PFC ativo e proteção contra sub e sobre Tensão;
  - Sistema Operacional Windows 11 PRO.

- **PCS LABORATÓRIO DE PESQUISA**  
  - CPU Intel i5 de 13a Geração ou superior;
  - 16 GB de memória RAM DDR5;
  - SSD NVMe de 01TB;
  - Placa mãe com no mínimo 04 portas USB 3.0;
  - Rede integrada Fast Ethernet Gigabite 10/100/1000;
  - Fonte de alimentação de 400W ou superior com PFC ativo e proteção contra sub e sobre Tensão;
  - Sistema Operacional Windows 11 PRO.

---

## 🔐 Configurações Iniciais de Segurança

### 🔧 Nomeação do Switch
```bash
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname ITEP_SW01
ITEP_SW01(config)#
```

### 🔒 Senhas para acesso ao modo privilegiado, console e VTY (limitando acessos simultâneos) criptografadas
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
ITEP_SW01(config-vlan)#vlan 20
ITEP_SW01(config-vlan)#name ADM
ITEP_SW01(config-vlan)#vlan 30
ITEP_SW01(config-vlan)#name PESQ
ITEP_SW01(config-vlan)#vlan 100
ITEP_SW01(config-vlan)#name DATACENTER
```

### 🖧 Interfaces VLAN
```bash
ITEP_SW01(config-vlan)#interface vlan 10
ITEP_SW01(config-if)#
%LINK-5-CHANGED: Interface Vlan10, changed state to up

ITEP_SW01(config-if)#ip address 192.168.10.1 255.255.255.0
ITEP_SW01(config-if)#no shutdown
ITEP_SW01(config-if)#interface vlan 20
ITEP_SW01(config-if)#
%LINK-5-CHANGED: Interface Vlan20, changed state to up

ITEP_SW01(config-if)#ip address 192.168.20.1 255.255.255.0
ITEP_SW01(config-if)#no shutdown
ITEP_SW01(config-if)#interface vlan 30
ITEP_SW01(config-if)#
%LINK-5-CHANGED: Interface Vlan30, changed state to up

ITEP_SW01(config-if)#ip address 192.168.30.1 255.255.255.0
ITEP_SW01(config-if)#no shutdown
ITEP_SW01(config-if)#interface vlan 100
ITEP_SW01(config-if)#
%LINK-5-CHANGED: Interface Vlan100, changed state to up

ITEP_SW01(config-if)#ip address 192.168.100.1 255.255.255.0
ITEP_SW01(config-if)#no shutdown
ITEP_SW01(config-if)#end
ITEP_SW01#
%SYS-5-CONFIG_I: Configured from console by console

ITEP_SW01#wr
Building configuration...
Compressed configuration from 7383 bytes to 3601 bytes[OK]
[OK]
ITEP_SW01#
```

---

## 🔌 Atribuição de Portas às VLANs

```bash
ITEP_SW01#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
ITEP_SW01(config)#interface range gigabitEthernet1/0/1-3
ITEP_SW01(config-if-range)#switchport mode access
ITEP_SW01(config-if-range)#switchport access vlan 10
ITEP_SW01(config-if-range)#interface range gigabitethernet1/0/4-5
ITEP_SW01(config-if-range)#switchport mode access
ITEP_SW01(config-if-range)#switchport access vlan 20
ITEP_SW01(config-if-range)#interface range gigabitethernet1/0/6-7
ITEP_SW01(config-if-range)#switchport mode access
ITEP_SW01(config-if-range)#switchport access vlan 30
ITEP_SW01(config-if-range)#interface range gigabitethernet1/0/8
ITEP_SW01(config-if-range)#switchport mode access
ITEP_SW01(config-if-range)#switchport access vlan 100
ITEP_SW01(config-if-range)#
ITEP_SW01#
%SYS-5-CONFIG_I: Configured from console by console

ITEP_SW01#wr
Building configuration...
Compressed configuration from 7383 bytes to 3601 bytes[OK]
[OK]
ITEP_SW01#
```

---

## 🚫 Configuração e Aplicação de ACLs

```bash
ITEP_SW01#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
ITEP_SW01(config)#ip access-list extended ITEP-ACL
ITEP_SW01(config-ext-nacl)#! Permitir TI acessar o Datacenter
ITEP_SW01(config-ext-nacl)#permit ip 192.168.10.0 0.0.0.255 192.168.100.0 0.0.0.255
ITEP_SW01(config-ext-nacl)#! Bloquear Administrativo e Pesquisa de acessar o Datacenter
ITEP_SW01(config-ext-nacl)#deny ip 192.168.20.0 0.0.0.255 192.168.100.0 0.0.0.255
ITEP_SW01(config-ext-nacl)#deny ip 192.168.30.0 0.0.0.255 192.168.100.0 0.0.0.255
ITEP_SW01(config-ext-nacl)#! Bloquear comunicao entre Administrativo e Pesquisa
ITEP_SW01(config-ext-nacl)#deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255
ITEP_SW01(config-ext-nacl)#deny ip 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255
ITEP_SW01(config-ext-nacl)#! Permitir todo o restante (inclusive comunicao interna nas VLANs)
ITEP_SW01(config-ext-nacl)#permit ip any any
ITEP_SW01(config-ext-nacl)#exit
ITEP_SW01(config)#interface vlan 20
ITEP_SW01(config-if)#ip access-group ITEP-ACL in
ITEP_SW01(config-if)#interface vlan 30
ITEP_SW01(config-if)#ip access-group ITEP-ACL in
ITEP_SW01(config-if)#interface vlan 100
ITEP_SW01(config-if)#ip access-group ITEP-ACL in
ITEP_SW01(config-if)#^Z
ITEP_SW01#
%SYS-5-CONFIG_I: Configured from console by console

ITEP_SW01#wr
Building configuration...
Compressed configuration from 7383 bytes to 3601 bytes[OK]
[OK]
ITEP_SW01#
```

---

## 📢 Banner de Aviso antes do login (MOTD)

```bash
ITEP_SW01#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
ITEP_SW01(config)#banner motd #
Enter TEXT message.  End with the character '#'.
**************************
ITEP - ACESSO RESTRITO!!!
Somente Pessoal Autorizado
Ambiente Monitorado
**************************
#

ITEP_SW01(config)#^Z
ITEP_SW01#
%SYS-5-CONFIG_I: Configured from console by console

ITEP_SW01#wr
Building configuration...
Compressed configuration from 7383 bytes to 3601 bytes[OK]
[OK]
#
```

---

## 🧭 Configuração de DHCP e DNS

### 🔒 Exclusão de IPs Reservados
```bash
ITEP_SW01#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
ITEP_SW01(config)#ip dhcp excluded-address 192.168.10.1 192.168.10.10
ITEP_SW01(config)#ip dhcp excluded-address 192.168.20.1 192.168.20.10
ITEP_SW01(config)#ip dhcp excluded-address 192.168.30.1 192.168.30.10
```

### 📦 Pools DHCP por VLAN
```bash
ITEP_SW01(config)#! Pool para VLAN 10 - TI
ITEP_SW01(config)#ip dhcp pool TI
ITEP_SW01(dhcp-config)#network 192.168.10.0 255.255.255.0
ITEP_SW01(dhcp-config)#default-router 192.168.10.1
ITEP_SW01(dhcp-config)#dns-server 192.168.100.2
ITEP_SW01(dhcp-config)#! Pool para VLAN 20 - Administrativo
ITEP_SW01(dhcp-config)#ip dhcp pool ADM
ITEP_SW01(dhcp-config)#network 192.168.20.0 255.255.255.0
ITEP_SW01(dhcp-config)#default-router 192.168.20.1
ITEP_SW01(dhcp-config)#dns-server 192.168.100.10
ITEP_SW01(dhcp-config)#! Pool para VLAN 30  Pesquisa
ITEP_SW01(dhcp-config)#ip dhcp pool PESQ
ITEP_SW01(dhcp-config)#network 192.168.30.0 255.255.255.0
ITEP_SW01(dhcp-config)#default-router 192.168.30.1
ITEP_SW01(dhcp-config)#dns-server 192.168.100.2
ITEP_SW01(dhcp-config)#exit
ITEP_SW01(config)#^Z
ITEP_SW01#
%SYS-5-CONFIG_I: Configured from console by console

ITEP_SW01#wr
Building configuration...
Compressed configuration from 7383 bytes to 3601 bytes[OK]
[OK]
ITEP_SW01#
```

> **No Web_Server:** habilitar serviço DNS e adicionar o registro `srv.itep.local` apontando para o IP do servidor.

---

## 📡 Esquema de Endereçamento IP

---

## 🧭 Estrutura de VLANs e Sub-redes

| VLAN | Nome         | Finalidade              | Sub-rede             | Gateway (SVI)     | Faixa DHCP Dinâmica     | IPs Reservados        |
|------|--------------|--------------------------|----------------------|-------------------|--------------------------|------------------------|
| 10   | TI           | Suporte técnico e TI     | 192.168.10.0/24      | 192.168.10.1      | 192.168.10.11 – 192.168.10.254 | 192.168.10.1 – 192.168.10.10 |
| 20   | ADM          | Administrativo           | 192.168.20.0/24      | 192.168.20.1      | 192.168.20.11 – 192.168.20.254 | 192.168.20.1 – 192.168.20.10 |
| 30   | PESQ         | Pesquisa e desenvolvimento | 192.168.30.0/24      | 192.168.30.1      | 192.168.30.11 – 192.168.30.254 | 192.168.30.1 – 192.168.30.10 |
| 100  | DATACENTER   | Servidores e serviços     | 192.168.100.0/24     | 192.168.100.1     | Não utiliza DHCP         | IPs fixos para servidores |

---

## 🧠 Observações Técnicas

- **Acesso ao Switch**: Cabo console USB, conexão via terminal (Drivers CISCO USB) no Notebook TI01.
- **Exclusão de IPs no DHCP**: IPs de `.1` a `.10` foram excluídos para evitar conflitos com gateways e permitir reservas manuais.
- **Servidores DNS**:
  - Interno: `192.168.100.2` ou `192.168.100.10`
  - Externo: `8.8.8.8` (Google DNS)
- **Servidor Web/DNS**: Localizado na VLAN 100 com IP fixo (`192.168.100.2`), configurado para responder por `srv.itep.local`.
- **Comandos de Verificação**:
```bash
show vlan brief
show ip interface brief
show ip route
show access-lists
```
- **OBS**: Todas as configurações atendendo as melhores práticas de segurança (mínimo privilégio, senhas fortes e documentação clara) atendendo demanda escalável.

---

## 🛠️ Melhorias Adicionais/Futuras

### 🚫 Desativar resolução automática de nomes inválidos
```bash
no ip domain-lookup
```
Útil para evitar que o switch tente resolver comandos digitados incorretamente como nomes de host via DNS, o que pode causar lentidão ou travamento na linha de comando.

## ☁️ Alternativa Futura: Uso de Computação em Nuvem

Objetivo: Realizar cotação e análise de viabilidade para substituir o servidor local por uma solução em nuvem.

- Provedores recomendados: AWS, Microsoft Azure, Google Cloud
- Tipo de instância sugerida: VM com 2–4 vCPUs, 8–16 GB RAM, SSD 100 GB
- Sistema operacional: Linux Server (Ubuntu/Debian) ou Windows Server
- Serviços: HTTP, DNS, opcionalmente FTP/HTTPS
- IP público fixo ou DNS dinâmico (ex: srv.itep.cloud)
- Segurança: firewall em nível de instância + regras de acesso restrito
- Backup: snapshots automáticos ou armazenamento em bucket (S3, Blob)
- Monitoramento: uso de métricas, alertas e logs integrados
- Custo estimado: entre R$ 150 e R$ 400/mês (dependendo do provedor e recursos)

**OBS**: A substituição do servidor físico por uma instância em nuvem pode reduzir custos com energia, manutenção e espaço físico, além de aumentar a escalabilidade e disponibilidade do ambiente.

---



