# 🖥️ Homelab Infrastructure

Laboratório prático de infraestrutura de TI criado para desenvolver conhecimentos em administração de sistemas, virtualização, redes, segurança, monitoramento e automação.

O ambiente utiliza hardware físico próprio e Proxmox VE como plataforma de virtualização. Este é um servidor pessoal, destinado ao uso exclusivo do responsável pelo Homelab. Os serviços devem permanecer privados, acessíveis somente pela rede local ou por um meio privado autorizado; não publicar painéis, aplicações ou portas diretamente na Internet.

---

## 🎯 Objetivos

- Desenvolver experiência prática em infraestrutura de TI.
- Aprender administração de servidores Linux e Windows.
- Praticar virtualização e gerenciamento de máquinas virtuais.
- Desenvolver conhecimentos de redes e troubleshooting.
- Implementar monitoramento, backup e segurança.
- Praticar automação de infraestrutura.
- Documentar problemas, soluções e procedimentos técnicos.
- Manter documentação técnica organizada para aprendizado e manutenção do ambiente pessoal.

---

## 🖥️ Hardware atual

| Componente | Especificação |
|---|---|
| Placa-mãe | X99 PR9 |
| Processador | Intel Xeon E5-2650 v4 |
| CPU | 12 núcleos / 24 threads |
| Memória RAM | 8 GB DDR4 |
| Armazenamento interno | 1 WD Purple de 2 TB |

---

## ⚙️ Tecnologias e áreas de estudo

- **Virtualização:** Proxmox VE
- **Sistemas:** Linux e Windows Server
- **Redes:** TCP/IP, DNS, DHCP, VLAN, firewall, VPN e troubleshooting
- **Serviços:** Docker, Nginx e bancos de dados
- **Monitoramento:** Zabbix, Grafana e Prometheus
- **Automação:** Git, GitHub e Ansible

---

## 🛠️ Mapa e status do projeto

| Etapa/Projeto | Situação |
|---|---|
| Instalação do Proxmox VE | ✅ Concluída |
| Configuração inicial de rede | ✅ Registrada; DNS segue pendente |
| Troubleshooting de acesso ao Proxmox | ✅ Concluído e documentado |
| Inventário inicial do host e armazenamento | ✅ Documentado; capturas complementares incorporadas |
| VM Linux Mint | ⏸️ Criada e atualmente desligada; finalidade e retomada pendentes |
| LXC 110 — Nextcloud | 🟡 Instância instalada e acessível na LAN; revisão documental registrada |
| Backup independente e teste de restauração | ⏳ Pendente; o host atualmente tem somente um disco |
| Windows Server / Active Directory | ⏳ Planejado |
| Docker | ⏳ Planejado |
| Monitoramento | ⏳ Planejado |
| Segurança | ⏳ Planejado |
| Automação com Ansible | ⏳ Planejado |

### Possíveis extensões

Estas são ideias para avaliar ao longo do projeto, sem compromisso de instalar todas:

- Armazenamento e compartilhamento de arquivos pessoais
- Casa inteligente com Home Assistant
- Acesso remoto privado por VPN, somente se necessário e sem exposição direta dos serviços
- Serviços para músicas, filmes e séries
- OpenMediaVault para administrar armazenamento e compartilhamento, após definir discos e estratégia de backup

A disponibilidade atual é de 8 GB de RAM e um único disco físico. Antes de colocar dados pessoais ou serviços importantes no homelab, será necessário definir como manter cópias em um destino independente.

---

## 🔎 Troubleshooting

Os problemas encontrados durante a construção do laboratório serão documentados com:

- Sintomas
- Ambiente afetado
- Hipóteses
- Testes realizados
- Evidências coletadas
- Causa identificada
- Solução aplicada
- Resultado

### Casos documentados

- [Acesso ao Proxmox bloqueado por VPN](./01-proxmox/troubleshooting/acesso-proxmox-vpn.md)
- [Levantamento inicial de rede e DNS](./02-network/levantamento-rede-inicial.md)
- [Nextcloud no LXC 110](./04-servicos/nextcloud-homelab.md)

---

## 📚 Documentação

A documentação está organizada por área:

```text
01-proxmox/
02-network/
03-linux/
04-servicos/
05-windows-server/
06-docker/
07-monitoring/
08-backup/
09-security/
10-automation/
```
