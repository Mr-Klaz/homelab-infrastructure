# Inventário inicial do host Linux

- **Data do levantamento:** 2026-10-14
- **Host:** servidor Proxmox
- **Escopo:** informações observadas nos comandos e telas compartilhados durante o levantamento
- **Observação:** este documento registra o estado observado naquele momento; não implica que a configuração permaneça igual.

## Plataforma

| Item | Valor observado |
|---|---|
| Hipervisor | Proxmox VE 9.2.2 |
| Sistema operacional | Debian GNU/Linux 13 (Trixie) |
| Versão Debian reportada | `DEBIAN_VERSION_FULL=13.7` |
| Kernel | `7.0.2-6-pve` |
| Arquitetura | x86_64 |

## Processador

| Item | Valor observado |
|---|---|
| Modelo | Intel Xeon E5-2650 v4 |
| CPUs lógicas | 24 |
| Núcleos | 12 |
| Sockets | 1 |
| Nós NUMA | 1 |
| Virtualização | VT-x identificada |

## Memória e swap

Saída observada de `free -h`:

| Recurso | Total | Usado | Livre | Outros valores reportados |
|---|---:|---:|---:|---|
| RAM | 7.6 GiB | 1.7 GiB | 5.1 GiB | shared: 49 MiB; buff/cache: 1.1 GiB; available: 5.9 GiB |
| Swap | 7.6 GiB | 0 B | — | — |

Os valores são uma fotografia do uso no momento da coleta.

## Armazenamento

O disco identificado foi `/dev/sda`, com aproximadamente 1.8 TB. As partições observadas foram:

| Dispositivo | Tamanho aproximado | Observação |
|---|---:|---|
| `/dev/sda1` | 1007 KB | Partição pequena; finalidade não confirmada neste levantamento |
| `/dev/sda2` | 1 GB | Montada em `/boot/efi` |
| `/dev/sda3` | 1.8 TB | Contém a estrutura LVM |

A estrutura LVM reportada inclui `pve-swap` (7.6 GB), `pve-root` (96 GB), `pve-data_tmeta` (15.9 GB) e `pve-data_tdata` (1.7 TB), associados ao thin pool `pve-data`.

Uso observado em `df -h`:

| Sistema de arquivos | Tamanho | Usado | Disponível | Uso |
|---|---:|---:|---:|---:|
| `/dev/mapper/pve-root` | 94 GB | 7.1 GB | 83 GB | 8% |
| `/dev/sda2` | 1022 MB | 9.1 MB | 1.0 GB | 1% |

Armazenamentos reportados por `pvesm status`:

| Armazenamento | Tipo | Estado | Total | Usado | Disponível | Uso |
|---|---|---|---:|---:|---:|---:|
| `local` | `dir` | active | 98,497,780 KiB | 7,382,188 KiB | 86,066,044 KiB | 7.49% |
| `local-lvm` | `lvmthin` | active | 1,793,507,328 KiB | 0 KiB | 1,793,507,328 KiB | 0% |

Os números são os valores reportados pelos comandos durante o levantamento; podem variar com o uso.

## Rede observada

Saída observada de `ip -br addr`:

| Interface | Estado | Endereços |
|---|---|---|
| `lo` | UNKNOWN | `127.0.0.1/8`, `::1/128` |
| `nic0` | UP | Nenhum endereço IP reportado |
| `vmbr0` | UP | `192.168.0.146/24`, `fe80::2e0:1fff:fe9b:476a/64` |

O arquivo `/etc/resolv.conf` indicava `nameserver 192.168.0.1`.

### DNS

O comportamento observado foi misto: o gateway e um IP externo respondiam a ping; a consulta DNS ao roteador era recusada; a consulta direcionada a `1.1.1.1` funcionava; e a resolução via `getent` falhava.

A causa raiz não foi confirmada. O levantamento correspondente está em [`02-network/levantamento-rede-inicial.md`](../../02-network/levantamento-rede-inicial.md). Não há evidência de que o Cloudflare One Client esteja instalado no host Proxmox; ele foi identificado no computador Windows.

## Estado do sistema e sessão

| Item | Valor observado |
|---|---|
| Usuário da sessão | `root` |
| Identidade reportada | `uid=0(root) gid=0(root) groups=0(root)` |
| Uptime reportado | 23 dias, 4 horas e 43 minutos |
| Usuários reportados no uptime | 1 |
| Load average reportado | `0.02, 0.03, 0.00` |

O uptime foi copiado de uma saída que também mostrava o horário `20:28:36`. O horário foi mantido apenas como parte da saída observada; não foi usado para inferir a data de inicialização.

## Serviços em execução

A saída de `systemctl --type=service --state=running` mostrou 33 unidades carregadas. Entre os serviços listados estavam:

- `chrony`, `cron`, `dbus` e `ssh`
- `pve-cluster`, `pvedaemon`, `pveproxy`, `pvescheduler` e `pvestatd`
- `pve-firewall`, `proxmox-firewall`, `pve-ha-crm` e `pve-ha-lrm`
- `lxc-monitord`, `lxcfs` e `pve-lxc-syscalld`
- `smartmontools`, `spiceproxy`, `postfix`, `rpcbind` e `zfs-zed`
- `watchdog-mux.service`

Esta é uma seleção da saída observada, não uma lista de serviços necessários ou recomendados. A presença de um serviço em execução, por si só, não indica problema.

### Sobre `watchdog-mux.service`

`watchdog-mux` é um serviço do Proxmox que faz a mediação do watchdog do host para que componentes do Proxmox possam usar o mecanismo de watchdog de forma coordenada. Sua presença na lista de serviços em execução é compatível com um host Proxmox; a saída compartilhada não indicou falha nesse serviço.

## Limitações do levantamento

- Não foram confirmadas neste levantamento a finalidade de todas as partições, a topologia física completa da rede nem a causa do problema de DNS.
- O estado de serviços, endereços, armazenamento e recursos muda com o tempo.
- Este inventário não contém senhas, tokens ou outros segredos.
