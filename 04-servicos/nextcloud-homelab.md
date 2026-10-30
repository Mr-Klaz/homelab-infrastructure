# Nextcloud pessoal no homelab

**Estado documental:** atualizado até 30/10/2026.  
**Escopo:** fotografia histórica, não verificação do servidor atual.  
**Atualização:** ações de Tailscale e Nextcloud foram executadas pelo responsável com aprovação. Nenhuma porta foi aberta no roteador; esta atualização não altera o homelab.

## Princípio de uso e acesso

O Homelab é um servidor pessoal, para uso exclusivo do responsável. O Nextcloud e os demais serviços devem permanecer privados, acessíveis apenas pela rede local ou por um meio privado autorizado. Não expor portas ou serviços diretamente à Internet, nem configurar publicação externa como parte de correções ou melhorias. A existência de HTTP na LAN continua sendo um ponto de segurança a avaliar; isso não autoriza habilitar acesso público.

> “Confirmado” significa que aparece nas notas/capturas disponíveis ou foi informado diretamente pelo responsável. “Não confirmado” indica lacuna documental e não deve ser interpretado como ausência do recurso. Revalidar antes de qualquer ação.

## Visão geral

| Componente | Estado conhecido |
|---|---|
| Host | Proxmox VE 9.2.2 em Debian 13, conforme baseline existente; 8 GB físicos e um disco WD Purple de 2 TB |
| VM Linux Mint | Existe e está desligada, conforme contexto do responsável; finalidade e recursos da VM não registrados nas evidências disponíveis |
| Serviço | Nextcloud Hub 26 Summer, versão 35.0.2 na captura de 26/10/2026 |
| Contêiner | LXC 110 `nextcloud`, Debian 13.7, não privilegiado, 2 vCPUs, 2 GiB RAM, 512 MiB swap; inicialização automática desativada |
| Acesso | HTTP na LAN e Tailscale privado configurado no host e no LXC; endereços e identificadores omitidos por privacidade |
| Banco | MariaDB/tuning mencionados como tópico a documentar; versão, valores e validações não disponíveis nas evidências acessíveis |
| Tarefas em segundo plano | Configuração Cron não confirmada nas capturas/notas acessíveis |
| Backup | Sem destino independente nem restauração testada; host com um único disco físico. Os originais de Destian Wars permanecem no PC pessoal |

## Host Proxmox, VM e armazenamento

O baseline registra Proxmox VE 9.2.2 em Debian 13, 8 GB de RAM e um único disco físico WD Purple de 2 TB. Os storages `local` e `local-lvm`, assim como os volumes das VMs e containers, residem nesse disco; cópias no mesmo disco não protegem contra falha física.

A VM 100 Linux Mint está desligada; o inventário compartilhado registrou 4 GiB de RAM e disco de boot de 32 GiB.

O LXC 110 `nextcloud` usa Debian 13.7, é não privilegiado e tem 2 vCPUs, 2 GiB de RAM e 512 MiB de swap. A configuração observada mantém inicialização automática desativada.

A raiz do LXC é um volume de 16 GiB em `local-lvm`. Um volume adicional de 300 GB nominais está montado em `/mnt/nextcloud-data`; o sistema de arquivos ext4 apresentou 295 GB, com cerca de 280 GB livres em 29/10/2026. O `datadirectory` informado pelo Nextcloud é `/mnt/nextcloud-data/data`. Ambos os volumes ficam no disco físico único do host: o volume de dados é dedicado, mas não constitui backup independente.

A interface do LXC recebe endereço por DHCP. Endereços privados e MACs foram omitidos.

## Rede e exposição

O acesso LAN ao Nextcloud foi observado por HTTP. O endereço LAN do LXC é dinâmico por DHCP; não foi criada reserva no roteador. O responsável não conseguiu autenticar na interface administrativa do roteador, e nenhuma configuração foi alterada.

Tailscale 1.104.4 foi instalado no host Proxmox e no LXC 110; o Android entrou na mesma tailnet. O LXC é não privilegiado: foi necessário liberar `/dev/net/tun` por passthrough em Recursos do container e reiniciar o LXC. Depois do reinício, TUN foi confirmado disponível.

O endereço Tailscale do LXC foi acrescentado como segunda entrada em `trusted_domains`, preservando a entrada LAN. Com Wi‑Fi doméstico ligado, o ping entre LXC e Android funcionou, e a URL Tailscale do Nextcloud exibiu a tela de login no navegador do celular. Isso valida o caminho privado via Wi‑Fi, não o acesso externo por dados móveis.

Com apenas dados móveis, o Android exibiu aviso de relay indisponível, os pings expiraram e as páginas Tailscale não carregaram. O responsável identificou que o pacote de dados estava esgotado e pausou os testes. A causa móvel permanece sem diagnóstico; não concluir que a operadora bloqueia a Tailscale.

O host Proxmox também permanece conectado à tailnet; não foi testado se sua interface de administração está acessível por esse caminho. Nenhum port forwarding, Funnel ou rota da rede doméstica foi configurado durante estas etapas.

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

O valor `maintenance_window_start = 3` foi observado após uma alteração. Isso define a janela de manutenção diária.

Na coleta de 27/10, o modo de tarefas em segundo plano foi confirmado como `cron`, e `/etc/cron.d/nextcloud` agenda `cron.php` a cada cinco minutos como `www-data`. Ainda falta validar pela visão geral ou por registros do Nextcloud que as execuções recentes estão ocorrendo sem erro.

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

1. **Falha do único disco:** host, VM, LXC e dados compartilham um único disco físico. Os originais de Destian Wars permanecem no PC, mas não há backup independente do servidor nem teste de restauração documentado.
2. **Tráfego HTTP:** a interface LAN e a URL Tailscale foram observadas via HTTP; o acesso privado Tailscale funcionou no Wi‑Fi, mas HTTPS da aplicação ainda precisa ser avaliado.
3. **Saúde do banco e logs:** o painel mostrou falha em verificação de banco e erros de log; o diagnóstico atual não foi feito.
4. **Tarefas de fundo:** Cron está configurado, mas uma execução recente bem-sucedida ainda não foi confirmada.
5. **Acesso privado ao host:** o Proxmox está conectado à tailnet; não foi verificado se a interface administrativa é alcançável por outros dispositivos dessa rede.
6. **Acesso móvel externo:** os testes por dados móveis foram pausados quando se identificou pacote de dados esgotado; não há conclusão sobre a causa.
7. **DNS do host:** o baseline de 14/10 registrou falha de resolução; estado atual desconhecido.

## Próximas etapas — estado em 30/10/2026

1. Quando houver dados móveis, retomar a validação do Tailscale no Android e o acesso remoto ao Nextcloud. O app Nextcloud do Android ainda não foi validado usando o endereço Tailscale; manter a conta LAN até o acesso remoto funcionar.
2. Revisar se o Tailscale no host Proxmox deve permanecer conectado; se não for necessário, desconectá-lo para deixar somente o LXC Nextcloud na tailnet.
3. Planejar backup consistente de arquivos, banco e configuração. Até adquirir outro disco, manter os originais de Destian Wars no PC; cópias no disco do homelab não protegem contra falha física.
4. Confirmar uma execução recente bem-sucedida do Cron.
5. Revalidar avisos de banco, logs e HTTPS antes de qualquer reparo, migração ou tuning.

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

## Visão geral do Nextcloud — capturas de 27/10/2026

As capturas da página Administração → Visão geral mostram Nextcloud Hub 26 Summer (`35.0.2`), canal Estável, com mensagem de versão atualizada na data da captura.

### Erros e avisos que exigem revisão

- Acesso identificado como HTTP inseguro; não há evidência de HTTPS configurado.
- Dois erros no log desde `20/10/2026 22:31:15`; o conteúdo não foi expandido.
- A visão geral inicialmente reportou três verificações do banco falhando. Na captura expandida posterior, a seção InnoDB mostrava uma falha detalhada em `innodb_log_file_size`; não se deve inferir que todos os grupos de verificações foram expandidos.
- Migrações de mimetype disponíveis. A própria interface sugere uma ação de reparação cara, mas ela não foi executada; avaliar apenas depois de entender os erros e ter backup restaurável.
- Aviso de HSTS ausente. Não configurar HSTS até que HTTPS funcione para o acesso privado escolhido.
- OPcache informa o buffer de strings internas quase cheio e recomenda valor superior a `8`; ajuste não realizado.

### Informativos mostrados

A interface também indica ausência de memcache, locking transacional usando o banco, 2FA disponível mas não obrigatório, região de telefone e e-mail não configurados, ID de servidor ausente, Deploy Daemon do AppAPI não definido e Imagick sem suporte a SVG. A necessidade desses itens depende do uso e não é motivo para instalar componentes automaticamente.

As capturas não confirmam a última execução bem-sucedida do Cron nem mostram a área Arquivos. Os avisos de log e banco permanecem pendentes de diagnóstico. Nenhuma mudança foi feita no servidor.

### Detalhe da verificação InnoDB — captura expandida de 27/10/2026

- A seção InnoDB indicava **1 falha**.
- `innodb_log_file_size`: valor em execução mostrado como `100663296` bytes (96 MiB); a verificação recomenda ao menos 128 MiB.
- A tela também mostra `innodb_log_file_size=256M` como configuração que requer reinicialização. Isso sugere que o valor de 256 MiB está definido para entrar em vigor após reiniciar MariaDB, enquanto o processo em execução ainda reporta 96 MiB. A origem exata da configuração e o estado do serviço devem ser confirmados antes de qualquer reinicialização.
- A verificação de taxa de acerto do buffer pool foi ignorada porque o servidor tinha apenas 1,6 hora de atividade; a tela indica que precisa de pelo menos 24 horas para estatísticas confiáveis.
- As verificações de razão de páginas sujas e modo de flush do log foram marcadas como sucesso; a de tamanho de log muito grande também apareceu como sucesso. A verificação de tamanho de log relativo ao buffer pool foi ignorada por falta de uma métrica no grupo.

Não foi clicado o botão de reinício nem feita qualquer alteração. O resultado precisa ser reavaliado após confirmar a origem da configuração e planejar uma recuperação segura.

### Trechos sugeridos para log de consultas lentas — 28/10/2026

A página Administração → Visão geral classificou os dois itens como **Dica**, sem marcá-los como falhas:

| Verificação | Valor observado | Sugestão exibida |
|---|---:|---|
| `slow_query_log` | `0` (desativado) | `slow_query_log = ON` |
| `long_query_time` | 10 segundos | `long_query_time = 2` (ou menos) |

Os trechos mostrados para adicionar em `my.cnf`, seção `[mysqld]`, são recomendações para registrar consultas lentas e reduzir o limite de registro. Não são evidência de que esses valores estejam definidos nos arquivos de configuração. Nenhuma alteração ou reinicialização foi feita. Esses itens são sugestões de diagnóstico/desempenho e não corrigem a falha separada de `innodb_log_file_size` (96 MiB em execução; 256 MiB aparece pendente de reinicialização). Caso sejam avaliados depois, confirmar a origem dos parâmetros e planejar retenção/permissões do log.

### Consulta dos arquivos MariaDB — 28/10/2026

Uma busca somente de leitura em `/etc/mysql` e `/etc/my.cnf` por atribuições explícitas a `innodb_log_file_size`, `slow_query_log` e `long_query_time` não retornou linhas. Isso indica que esses parâmetros não aparecem explicitamente nesses caminhos consultados; não exclui outras fontes de configuração fora deles. Os valores em execução previamente observados continuam sendo a referência para o estado ativo. Nenhuma edição ou reinicialização foi feita.

## Atualização consolidada — 29–30/10/2026

Esta seção registra fatos confirmados após as coletas históricas acima e prevalece sobre lacunas anteriores quando houver conflito.

### Arquivos Destian Wars

O responsável confirmou que os itens iniciais do Nextcloud eram arquivos de demonstração. Depois, conectou o cliente de desktop no PC e criou sincronização clássica da pasta local Destian Wars para uma nova pasta remota `/Destian Wars`, sem arquivos virtuais. O cliente exibiu estado sem erro e a interface web passou a mostrar as pastas e imagens. A conta mostrava cerca de 168,7 MB usados; o número inclui outros arquivos de demonstração e não representa apenas Destian Wars. Os originais permanecem no PC.

O app Nextcloud no Android também foi conectado, e o responsável abriu Destian Wars pelo Wi‑Fi doméstico. Isso valida acesso local, não constitui backup independente: os dados do servidor continuam no único disco físico do homelab.

### Tailscale e acesso privado

- Tailscale 1.104.4 foi instalado no host Proxmox e no LXC 110. O Android entrou na mesma tailnet.
- Para o LXC não privilegiado, `/dev/net/tun` foi liberado por passthrough nos Recursos do container. O LXC foi reiniciado e a presença de TUN foi confirmada dentro dele.
- O endereço Tailscale do LXC foi adicionado como segunda entrada em `trusted_domains`, preservando o endereço LAN. Endereços Tailscale e LAN foram omitidos.
- Com Wi‑Fi doméstico ligado, o ping entre LXC e Android funcionou e a URL Tailscale do Nextcloud exibiu a tela de login no navegador do celular.
- O teste com apenas dados móveis falhou: o app mostrou aviso de relay indisponível, o ping expirou e as páginas Tailscale não carregaram. O responsável identificou depois que o pacote de dados estava esgotado e pausou os testes. A causa móvel permanece sem diagnóstico; não concluir que a operadora bloqueia a Tailscale.
- O host Proxmox permanece conectado à tailnet. O acesso à interface de administração do Proxmox por esse caminho não foi testado; avaliar se é necessário manter o host conectado.

Não foram abertas portas no roteador, configurado port forwarding, anunciadas rotas da LAN ou habilitado Tailscale Funnel durante estas etapas. Nenhuma configuração do roteador foi alterada.
