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

O gerenciamento do Proxmox é realizado pela interface web na porta TCP `8006`. O levantamento inicial foi registrado em [02-network](../02-network/levantamento-rede-inicial.md); a causa do problema de DNS continua pendente e sua investigação está pausada até a etapa de backup.

---

## 💾 Armazenamento

O nó possui um único disco físico identificado como WD Purple de 2 TB. Os storages `local` e `local-lvm` estão nesse mesmo disco, portanto não constituem destinos independentes de backup.

- [Verificação complementar do armazenamento](../03-linux/baseline/armazenamento-proxmox-2026-10-19.md)

---

## 🖥️ Virtualização

O Proxmox será utilizado para hospedar máquinas virtuais e contêineres destinados aos projetos do Homelab. As cargas serão introduzidas gradualmente devido à disponibilidade de 8 GB de RAM.

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

- [x] Instalação e acesso inicial ao Proxmox VE
- [x] Inventário inicial do host e do armazenamento
- [x] Levantamento inicial da rede (causa do DNS ainda pendente)
- [ ] Definir recursos e objetivo da primeira VM de aprendizagem
- [ ] Criar e instalar a primeira VM
- [ ] Planejar o primeiro servidor Linux e serviços
- [ ] Escolher um destino independente antes de configurar backups
- [ ] Planejar monitoramento, segurança e automação

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
