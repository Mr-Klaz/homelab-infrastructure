# Nextcloud pessoal no homelab

**Estado documental:** proposta para revisão; baseada nas notas locais e capturas registradas em 26/10/2026.  
**Escopo:** fotografia histórica, não verificação do servidor atual.  
**Execução:** nenhuma mudança foi feita no host, VM ou LXC durante esta documentação.

## Princípio de uso e acesso

O Homelab é um servidor pessoal, para uso exclusivo do responsável. O Nextcloud e os demais serviços devem permanecer privados, acessíveis apenas pela rede local ou por um meio privado autorizado. Não expor portas ou serviços diretamente à Internet, nem configurar publicação externa como parte de correções ou melhorias. A existência de HTTP na LAN continua sendo um ponto de segurança a avaliar; isso não autoriza habilitar acesso público.

> “Confirmado” significa que aparece nas notas/capturas disponíveis ou foi informado diretamente pelo responsável. “Não confirmado” indica lacuna documental e não deve ser interpretado como ausência do recurso. Revalidar antes de qualquer ação.

## Visão geral

| Componente | Estado conhecido |
|---|---|
| Host | Proxmox VE 9.2.2 em Debian 13, conforme baseline existente; 8 GB físicos e um disco WD Purple de 2 TB |
| VM Linux Mint | Existe e está desligada, conforme contexto do responsável; finalidade e recursos da VM não registrados nas evidências disponíveis |
| Serviço | Nextcloud Hub 26 Summer, versão 35.0.2 na captura de 26/10/2026 |
| Contêiner | LXC 110; recursos, sistema operacional e modo privilegiado não registrados |
| Acesso | Instância observada em endereço privado da LAN; endereço omitido nesta proposta para o repositório público |
| Banco | MariaDB/tuning mencionados como tópico a documentar; versão, valores e validações não disponíveis nas evidências acessíveis |
| Tarefas em segundo plano | Configuração Cron não confirmada nas capturas/notas acessíveis |
| Backup | Destino independente e restauração não documentados; o host tem somente um disco físico |

## Host Proxmox, VM e armazenamento

O baseline do repositório registra Proxmox VE 9.2.2, Debian GNU/Linux 13, Intel Xeon E5-2650 v4 (12 núcleos/24 threads), 8 GB de RAM e um único disco WD Purple de 2 TB. O storage `local` e o `local-lvm` ficam no mesmo disco físico, portanto snapshots ou cópias no próprio host não protegem contra falha desse disco.

A VM Linux Mint já existe e permanece desligada. Não há informação suficiente para afirmar seu ID, vCPU, RAM, tamanho de disco, endereço, finalidade ou se contém dados a preservar. Confirmar esses dados antes de iniciar, alterar ou reaproveitar a VM.

O LXC 110 hospeda o Nextcloud. O storage Proxmox contém volumes de contêiner, mas as evidências não associam um volume específico ao LXC 110. Permanecem não documentados: storage e volume raiz do container, mounts adicionais, caminho `datadirectory`, localização do banco/configuração, capacidade livre e política de expansão.

## Rede e exposição

A captura de 26/10/2026 mostra acesso pela LAN, via HTTP. O endereço foi deliberadamente omitido por esta proposta, pois o repositório é público. O endereço permanente, máscara, gateway, DHCP/reserva, firewall, DNS e eventuais regras de encaminhamento não foram verificados.

As evidências disponíveis não demonstram publicação externa, proxy reverso, domínio público ou TLS configurado. A interface indicava HTTP inseguro e ausência do cabeçalho HSTS. HSTS só deve ser avaliado depois de HTTPS estar funcional para o nome de acesso correto. Não habilitar acesso remoto incidentalmente ao corrigir avisos.

## Nextcloud, PHP e Apache

Na captura de 26/10/2026, a interface informava Nextcloud Hub 26 Summer (35.0.2) e versão atualizada naquele momento. Isso não comprova a versão atual.

As notas registram:

- `/etc/php/8.4/apache2/php.ini` tinha `memory_limit = 128M`; foi feita cópia `.bak` e o valor foi alterado para `512M`.
- `apache2ctl configtest` retornou `Syntax OK`, com aviso `AH00558` sobre `ServerName`.
- Apache foi recarregado; `systemctl is-active apache2` retornou `active` naquele momento.
- `maintenance_window_start` foi definido para `3` via `occ`. O fuso efetivo não foi registrado; não assumir que isso corresponde a 03:00 no horário local.
- O painel listou OPcache com `opcache.interned_strings_buffer` quase cheio (recomendação exibida: acima de `8`). Nenhum ajuste subsequente está confirmado.

## MariaDB e tuning

MariaDB é citado como parte da configuração que precisa constar no histórico, mas os materiais disponíveis não mostram de forma verificável versão, parâmetros alterados, valores anterior/novo, arquivo de configuração, motivo, reinício/reload ou métricas posteriores. Portanto, este registro **não atribui valores de tuning nem declara validação**.

Antes de completar esta seção, registrar apenas após confirmação documental ou leitura autorizada em outra etapa: versão; parâmetro e valor final; arquivo de origem (sem conteúdo de credenciais); motivo; data; serviço afetado; resultado da validação; plano de reversão. Não incluir senha, hash, string de conexão completa ou conteúdo de `config.php`.

A captura da administração indicava uma verificação do banco falhando, sem o detalhe expandido. Tratar como pendência de diagnóstico; não executar reparo/migração sem entender a mensagem e confirmar backup recuperável.

## Cron e janela de manutenção

O valor `maintenance_window_start = 3` foi observado após uma alteração. Isso configura a janela de manutenção, mas não prova que o modo de tarefas de fundo esteja definido como Cron.

Não há evidência acessível da tela de tarefas em modo Cron, da entrada do agendador dentro do LXC 110, da frequência ou de uma execução recente de `cron.php`. O serviço `cron` ativo no host Proxmox não comprova que o agendamento do Nextcloud esteja presente no container.

Na próxima revisão documental, registrar o modo mostrado pelo Nextcloud, agendamento dentro do container (sem dados sensíveis), usuário de execução, frequência, horário/fuso e evidência de execução bem-sucedida. Fazer isso como coleta de estado, sem editar o sistema.

## Validações registradas

| Verificação | Resultado histórico | Limite da evidência |
|---|---|---|
| Versão do Nextcloud | 35.0.2; painel dizia atualizada | Captura de 26/10/2026 |
| `memory_limit` do PHP Apache | `512M` depois da alteração | Leitura registrada; estado atual não verificado |
| Sintaxe Apache | `Syntax OK` | Aviso `AH00558` permaneceu |
| Estado Apache | `active` após reload | Resultado pontual |
| Janela de manutenção | Valor `3` | Fuso desconhecido |
| Banco de dados | Uma checagem falhando no painel | Diagnóstico detalhado não capturado |
| Cron do Nextcloud | Não confirmado | Host cron não serve como evidência do container |

## Avisos observados no painel

Na captura de 26/10/2026: dois erros no log (desde data/hora indicada pelo painel; conteúdo não capturado); checagem do banco com falha; HTTP inseguro e HSTS ausente; migração de mimetype pendente; aviso de OPcache; ausência de memcache; locking transacional baseado no banco; 2FA disponível mas não obrigatório; e-mail não configurado/verificado; região de telefone ausente; AppAPI/Deploy Daemon padrão não definido; suporte SVG do Imagick ausente; ID de servidor de configuração não definido.

Esses itens são observações históricas e precisam ser rechecados antes de priorização. Itens de AppAPI, e-mail, telefone, memcache e Imagick podem depender do uso pretendido.

## Riscos registrados

1. **Falha do único disco:** host, volumes de VM/container e storage `local` compartilham o mesmo disco físico; não há backup independente documentado.
2. **Recuperação incerta:** não foram identificados um conjunto consistente de backup de banco + arquivos + configuração e um teste de restauração.
3. **Tráfego HTTP:** a captura indicava HTTP sem TLS, mesmo sendo acesso LAN.
4. **Integridade/saúde do banco:** uma verificação falhava e o detalhe não foi capturado.
5. **Tarefas assíncronas:** modo Cron e execução dentro do LXC não comprovados.
6. **Recursos limitados:** host com 8 GB de RAM; limites e consumo do LXC não documentados. `memory_limit=512M` é limite PHP, não memória total do container.
7. **DNS:** o baseline anterior registrou falha de resolução no host; causa e estado atual seguem desconhecidos.
8. **VM desligada sem inventário:** não iniciar ou reutilizar a VM Linux Mint antes de confirmar discos e dados que devem ser preservados.

## Próximas etapas documentais

Ordem proposta, respeitando a preferência de uma etapa por vez e documentação antes de mudanças:

1. Confirmar inventário do LXC 110 e da VM Linux Mint, incluindo estados e recursos; não alterar serviços.
2. Registrar mounts, storage, `datadirectory`, localização do banco e capacidade, sem copiar segredos.
3. Obter evidência dos parâmetros MariaDB citados como tuning e da validação; se não houver registro, marcar como desconhecido em vez de reconstruir por suposição.
4. Confirmar modo Cron e agendamento no container, frequência e execução recente; apenas observação.
5. Revisar estado de backup independente e existência de restauração testada.
6. Só após atualização documental e decisão do responsável, priorizar diagnóstico do banco/log e demais avisos, um item de cada vez.

Nenhuma das etapas acima deve ser executada durante esta revisão. Nenhuma mudança foi feita no homelab.



## Inventário confirmado do host e do LXC — 27/10/2026

Coleta somente de leitura executada no Proxmox e no LXC 110. Endereços IP, MAC e outros identificadores da rede foram omitidos.

### Host e máquinas

- Proxmox VE: gerenciador `9.2.2`, kernel `7.0.2-6-pve` (o pacote `proxmox-ve` reportou `9.2.0`).
- VM 100 — Linux Mint: desligada; 4 GiB de memória e disco de boot de 32 GiB.
- LXC 110 — `nextcloud`: em execução, Debian GNU/Linux 13.7 (Trixie), não privilegiado, 2 vCPUs, 2 GiB de memória e 512 MiB de swap. Inicialização automática desabilitada.

### Volumes e armazenamento

- Raiz do LXC: volume `local-lvm:vm-110-disk-0`, provisionado com 16 GiB; no momento da coleta, 2,4 GiB usados e 13 GiB disponíveis.
- Volume adicional: `local-lvm:vm-110-disk-1`, provisionado com 300 GiB e montado no container em `/mnt/nextcloud-data`; o sistema de arquivos apresentava 295 GiB, com 75 MiB usados e 280 GiB disponíveis.
- A montagem foi confirmada como ext4 e gravável. Ainda falta confirmar se o `datadirectory` do Nextcloud aponta para esse caminho.
- O host apresentou um disco físico de 1,8 TiB. Os dois volumes do LXC residem em `local-lvm`, portanto o volume adicional não constitui backup independente.

### Serviços e rede do container

- Apache, MariaDB e Cron estavam ativos e habilitados no systemd do LXC.
- A interface do container usa DHCP na bridge `vmbr0`; o endereço e MAC foram omitidos.
- O estado ativo do serviço Cron não confirma que o Nextcloud esteja em modo Cron nem que exista um agendamento de `cron.php`. Isso será verificado separadamente.
- A versão e variáveis atuais do MariaDB, o diretório de dados e o modo Cron foram confirmados na coleta de 27/10/2026 e estão detalhados na seção de estado atual. Ainda não foi determinado se os valores do MariaDB foram ajustados deliberadamente nem onde está o agendamento efetivo do Nextcloud.

Estes valores são uma fotografia da coleta em 27/10/2026; podem mudar. Nenhum serviço ou configuração foi alterado.

## Estado atual de Nextcloud, MariaDB e Cron — 27/10/2026

Coleta somente de leitura. Nenhuma configuração foi alterada.

### Nextcloud e dados

- `occ status`: instalação ativa; Nextcloud `35.0.2.2` (versão exibida `35.0.2`); modo de manutenção desativado; nenhuma atualização de banco pendente.
- Tipo de banco: `mysql`; host reportado: `localhost`. Nome de banco, usuário e credenciais não foram coletados.
- Diretório de dados reportado pelo Nextcloud: `/mnt/nextcloud-data/data`. Esse caminho fica dentro do volume adicional montado em `/mnt/nextcloud-data`.
- Na coleta anterior, o sistema de arquivos montado tinha 75 MiB usados e cerca de 280 GiB disponíveis. Esse número é uma fotografia do uso do volume, não uma validação da integridade nem da completude dos arquivos.

### MariaDB

- Versão reportada: `11.8.6-MariaDB-0+deb13u1`.
- Variáveis em execução observadas:

| Variável | Valor observado |
|---|---:|
| `innodb_buffer_pool_size` | 128 MiB |
| `innodb_flush_log_at_trx_commit` | 1 |
| `innodb_log_file_size` | 96 MiB |
| `max_connections` | 151 |
| `max_heap_table_size` | 32 MiB |
| `tmp_table_size` | 32 MiB |

Esses são valores em execução. Não foi confirmado se são padrões da distribuição ou tuning manual, nem foram coletados os arquivos de origem, histórico de mudanças, métricas de carga ou testes de desempenho. Não alterar até avaliar recursos do LXC e necessidade do serviço.

### Tarefas em segundo plano

- O valor de `core.backgroundjobs_mode` reportado pelo Nextcloud é `cron`.
- O serviço Cron do LXC estava ativo e habilitado na coleta de 27/10.
- O arquivo `/etc/cron.d/nextcloud` contém `*/5 * * * * www-data php -f /var/www/nextcloud/cron.php`, configurando execução a cada cinco minutos como `www-data`.
- Não há crontab pessoal para `www-data` nem para `root`; os comandos não retornaram timer systemd correspondente. Isso é compatível com o agendamento em `/etc/cron.d`.
- A configuração do agendamento está confirmada. Ainda falta confirmar pela visão geral do Nextcloud ou por registro apropriado que as execuções estão ocorrendo sem erros.

### Validações e lacunas

A saída de `occ status` confirma que a instalação está ativa e sem atualização de banco pendente naquele momento. Não foi executado teste de integridade do banco, não foram examinados logs e não foi comprovada uma execução recente do Cron.

## Diretório de dados e agendamento confirmados — 27/10/2026

- O diretório configurado pelo Nextcloud é `/mnt/nextcloud-data/data`, dentro do volume adicional do LXC 110.
- O ponto de montagem estava em ext4, com 295 GiB apresentados, 75 MiB usados e aproximadamente 280 GiB disponíveis.
- O diretório ocupava 73 MiB e tinha proprietário/grupo `www-data:www-data`, com permissões `drwxr-x---` (750).
- A configuração de modo do Nextcloud é `cron`. O arquivo `/etc/cron.d/nextcloud` agenda `php -f /var/www/nextcloud/cron.php` a cada cinco minutos como `www-data`.
- Não há crontab pessoal para `www-data` ou para `root`; a consulta de timers systemd não listou item relacionado. O agendamento no Cron tradicional é suficiente como configuração, mas ainda não constitui evidência de execução recente bem-sucedida.

O tamanho observado é uma fotografia; não indica por si só que todos os arquivos pessoais esperados já foram carregados. Nenhuma alteração foi feita.