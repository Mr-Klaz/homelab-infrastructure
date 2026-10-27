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

| Elemento | Registro público |
|---|---|
| Rede de administração | LAN privada; endereços omitidos |
| Acesso externo | Não previsto para exposição direta dos serviços |

O gerenciamento do Proxmox é realizado pela interface web na porta TCP `8006`, apenas pela rede privada. Não encaminhar essa porta à Internet. O levantamento inicial foi registrado em [02-network](../02-network/levantamento-rede-inicial.md); a causa do problema de DNS continua pendente e sua investigação está pausada até a etapa de backup.

---

## 🔒 Acesso

Este é um servidor pessoal e privado. Serviços e painéis permanecem limitados à LAN ou a um meio privado autorizado; nenhuma porta deve ser publicada diretamente na Internet.

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
- [Nextcloud no LXC 110](../04-servicos/nextcloud-homelab.md)

---

## 📚 Próximas etapas

- [x] Instalação e acesso inicial ao Proxmox VE
- [x] Inventário inicial do host e do armazenamento
- [x] Levantamento inicial da rede (causa do DNS ainda pendente)
- [x] Criar a VM Linux Mint; mantê-la desligada até definir a próxima etapa
- [x] Criar o LXC 110 para Nextcloud; revisar/documentar a configuração
- [ ] Escolher um destino independente e testar a restauração antes de confiar backups
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
