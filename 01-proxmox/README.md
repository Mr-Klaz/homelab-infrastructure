# ⚙️ Projeto 01 — Proxmox VE

## 📌 Objetivo

Documentar a implantação, configuração e administração do Proxmox VE utilizado como hypervisor principal do Homelab.

O projeto tem como objetivo desenvolver conhecimentos práticos em virtualização, administração de servidores, armazenamento, redes e troubleshooting.

---

## 🖥️ Hardware

| Componente | Especificação |
|---|---|
| Placa-mãe | X99 PR9 |
| Processador | Intel Xeon E5-2650 v4 |
| CPU | 12 núcleos / 24 threads |
| Memória RAM | 8 GB DDR4 |
| Armazenamento | WD Purple 2 TB |

---

## ⚙️ Software

| Componente | Versão |
|---|---|
| Hypervisor | Proxmox VE 9.2.2 |
| Nó | local |

---

## 🌐 Rede

Configuração atual do laboratório:

| Dispositivo | Endereço |
|---|---|
| Roteador/Gateway | 192.168.0.1 |
| Proxmox | 192.168.0.146 |
| Estação de administração | 192.168.0.172 |

### Interface Web

O gerenciamento do Proxmox é realizado através da interface web utilizando a porta TCP `8006`.

---

## 💾 Armazenamento

O ambiente possui atualmente os seguintes storages configurados:

- `local`
- `local-lvm`

O armazenamento físico principal do laboratório é um HDD WD Purple de 2 TB.

---

## 🖥️ Virtualização

O Proxmox será utilizado para hospedar máquinas virtuais e containers destinados aos projetos do Homelab.

A criação de novas cargas será realizada de forma gradual devido à disponibilidade atual de 8 GB de RAM.

---

## 🔎 Troubleshooting

Os problemas encontrados durante a implantação e operação do ambiente serão documentados seguindo um processo de diagnóstico baseado em evidências.

Cada ocorrência deverá registrar:

1. Sintoma
2. Ambiente afetado
3. Hipóteses
4. Testes realizados
5. Evidências coletadas
6. Causa identificada
7. Solução aplicada
8. Resultado

### Casos

- [Acesso ao Proxmox bloqueado por VPN](./troubleshooting/acesso-proxmox-vpn.md)

---

## 📚 Próximas etapas

- [ ] Inventário completo do Proxmox
- [ ] Documentação da configuração de rede
- [ ] Documentação dos storages
- [ ] Criação do primeiro servidor Linux
- [ ] Implementação de serviços
- [ ] Backup
- [ ] Monitoramento
- [ ] Segurança
- [ ] Automação

---

## 🎯 Competências desenvolvidas

Este projeto busca desenvolver competências práticas relacionadas a:

- Virtualização
- Administração de servidores
- Linux
- Redes TCP/IP
- Armazenamento
- Troubleshooting
- Backup
- Segurança
- Monitoramento
- Automação
