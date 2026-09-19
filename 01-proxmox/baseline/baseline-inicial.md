# Baseline Inicial — Proxmox VE

## 1. Identificação do Sistema

| Item | Informação |
|---|---|
| Hostname | `local` |
| Sistema operacional | Debian GNU/Linux 13 (trixie) |
| Proxmox VE | 9.2.2 |
| Kernel | Linux 7.0.2-6-pve |
| Arquitetura | x86-64 |
| Fabricante | MACHINIST |
| Modelo da placa-mãe | X99 PR9 |
| Firmware | 5.11 |

---

## 2. Processador

| Item | Informação |
|---|---|
| CPU | Intel Xeon E5-2650 v4 @ 2.20 GHz |
| Socket(s) | 1 |
| Núcleos físicos | 12 |
| Threads | 24 |
| Threads por núcleo | 2 |
| Arquitetura | x86-64 |
| Virtualização | VT-x |
| Cache L1 | 384 KiB |
| Cache L2 | 3 MiB |
| Cache L3 | 30 MiB |
| NUMA | 1 |

---

## 3. Memória RAM

Estado observado durante o baseline:

| Item | Valor |
|---|---:|
| Memória total | 7,6 GiB |
| Memória utilizada | 1,6 GiB |
| Memória livre | 5,6 GiB |
| Memória disponível | 5,9 GiB |
| Swap total | 7,6 GiB |
| Swap utilizada | 0 B |

### Observação

O hardware possui 8 GB de memória DDR4. A diferença entre a capacidade nominal e o valor apresentado pelo sistema é representada pela conversão entre GB e GiB e pela memória reservada pelo sistema.

---

## 4. Armazenamento

### Hardware

- Disco físico: WD Purple
- Capacidade nominal: 2 TB
- Quantidade de discos físicos atualmente disponíveis: 1

### Estado da arquitetura

Neste momento o Homelab possui apenas um disco físico.

**RAID não faz parte da arquitetura atual.**

A estratégia de armazenamento será revisada futuramente quando um segundo disco estiver disponível.

### LVM

As informações de:

- `pvs`
- `vgs`
- `lvs`

foram coletadas durante o baseline e devem ser consideradas parte do diagnóstico inicial da estrutura de armazenamento do Proxmox.

### Storages configurados no Proxmox

O Proxmox possui atualmente dois storages configurados:

| ID | Tipo | Conteúdo | Caminho/Alvo |
|---|---|---|---|
| `local` | Directory | Backup, imagens ISO, templates de containers | `/var/lib/vz` |
| `local-lvm` | LVM-Thin | Imagens de disco de VMs e Containers | VG `pve`, Thin Pool `data` |

#### `local`

Storage baseado em diretório utilizado para:

- Backups
- Imagens ISO
- Templates de containers

Caminho:

`/var/lib/vz`

#### `local-lvm`

Storage baseado em LVM-Thin utilizado para:

- Discos de máquinas virtuais
- Volumes de containers

Configuração observada:

- Volume Group: `pve`
- Thin Pool: `data`
- Status: habilitado

A arquitetura observada pode ser representada como:

```text
Disco físico
    ↓
Partição /dev/sda3
    ↓
LVM Volume Group: pve
    ↓
LVM-Thin Pool: data
    ↓
Proxmox Storage: local-lvm
    ↓
Discos de VMs / Containers

```


---

## 5. Rede

### Configuração atual conhecida

| Item | Informação |
|---|---|
| Interface bridge principal | `vmbr0` |
| Endereço IP do Proxmox | `192.168.0.146/24` |
| Gateway | `192.168.0.1` |
| Roteador | `192.168.0.1` |
| Acesso Web | `https://192.168.0.146:8006` |

### Observações

O acesso ao painel Web do Proxmox foi validado na rede local.

O acesso pelo dispositivo móvel também foi testado com sucesso dentro da rede local.

A porta `8006` não deve ser exposta diretamente à Internet.

O acesso remoto será tratado posteriormente através de uma solução de acesso seguro, com VPN prevista na arquitetura futura.

---

## 6. Estado Inicial do Projeto

Este documento representa o estado inicial observado do servidor Proxmox antes da implementação das próximas etapas do Homelab.

O objetivo do baseline é registrar as condições iniciais de:

- Hardware
- CPU
- Memória
- Armazenamento
- Rede
- Sistema operacional
- Proxmox VE

Essas informações servirão como referência para futuras alterações, troubleshooting e documentação da evolução da infraestrutura.

---

## 7. Próximas Etapas

As próximas etapas do Homelab serão executadas somente após a revisão e aprovação deste baseline.

Possíveis áreas futuras:

- Configuração e documentação de rede
- Máquinas virtuais
- Containers
- Linux
- Windows Server
- Armazenamento
- Segurança
- Monitoramento
- Backup
- Automação
- Troubleshooting

Cada alteração relevante deverá ser documentada para manter o histórico técnico do projeto.

---

## 8. Status da Documentação

**Status:** Aprovado

Este documento representa o baseline inicial aprovado do servidor Proxmox.

O baseline foi revisado e aprovado pelo responsável pelo projeto antes do início das próximas etapas da infraestrutura.
