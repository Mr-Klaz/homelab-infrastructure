# Armazenamento do Proxmox — verificação complementar

- **Data das capturas:** 2026-10-19
- **Nó:** `local`
- **Escopo:** telas do Proxmox VE compartilhadas durante a revisão do inventário.
- **Nota:** os valores representam o estado observado nas capturas e podem mudar.

## Disco físico

A tela **Nó → Discos** identificou um disco:

| Item | Valor observado |
|---|---|
| Dispositivo | `/dev/sda` |
| Modelo | WD Purple, `WDC_WD22PURZ-85B4ZY0` |
| Capacidade exibida | 2,00 TB |
| Tabela de partição | GPT |
| S.M.A.R.T. | `PASSED` na interface |

A tela exibia as seguintes partições:

| Partição | Uso exibido | Tamanho exibido |
|---|---|---:|
| `/dev/sda1` | BIOS boot | 1,03 MB |
| `/dev/sda2` | EFI | 1,07 GB |
| `/dev/sda3` | LVM | 2,00 TB |

O proprietário confirmou que este é o único disco instalado no homelab. Portanto, os storages abaixo não são dispositivos físicos independentes.

## Storages configurados

A tela **Datacenter → Armazenamento** mostrou dois storages habilitados e não compartilhados:

| ID | Tipo | Caminho/grupo | Conteúdos aceitos |
|---|---|---|---|
| `local` | Diretório | `/var/lib/vz` | Backup, Import, imagem ISO e modelo de contêiner |
| `local-lvm` | LVM-Thin | Grupo LVM `pve` | Imagem de disco e volumes de contêiner |

### `local`

A tela de resumo mostrou aproximadamente **100,86 GB** no total e **7,56 GB usados** (**7,50%**). Uma captura anterior mostrava a ISO `linuxmint-22.3-xfce-64bit.iso`, de 2,83 GiB. A lista de backups compartilhada anteriormente estava vazia.

### `local-lvm`

A tela de resumo mostrou aproximadamente **1,84 TB** e **0 B usados** para os dados do thin pool. A lista de discos de VM e a lista de volumes de contêiner estavam vazias nas capturas anteriores.

A tela LVM mostrou o grupo `pve` com três volumes lógicos, aproximadamente **2,00 TB** de tamanho e **17,44 GB livres** no grupo. O indicador de 99% atribuído a volumes lógicos descreve a alocação do grupo LVM; não significa que 99% dos dados do thin pool estejam ocupados. O resumo do thin pool, na captura, indicava 0 B de dados usados.

## Consequência para backups

O storage `local` permite armazenar arquivos de backup, mas reside no mesmo disco físico que o Proxmox e o `local-lvm`. Uma cópia mantida apenas nele não protege contra falha desse disco.

Nas capturas anteriores, a lista de backups de `local` e a lista de tarefas de backup do Datacenter estavam vazias. Não foi configurado nem executado backup durante este levantamento. Um destino independente ainda precisa ser escolhido antes de planejar a proteção de arquivos pessoais ou de máquinas virtuais.

## Mapa simplificado

```text
WD Purple /dev/sda (2 TB)
├── partição BIOS boot
├── partição EFI
└── partição LVM / grupo pve
    ├── pve-root → sistema do Proxmox e storage local (/var/lib/vz)
    └── pve-data → thin pool usado por local-lvm
```

Este mapa é simplificado e não pretende enumerar cada volume lógico interno do grupo.
