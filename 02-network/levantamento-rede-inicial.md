# Levantamento Inicial da Rede — Proxmox

## 1. Objetivo

Registrar o estado inicial da configuração de rede do servidor Proxmox antes da realização de alterações na infraestrutura.

Este documento representa um levantamento técnico da configuração existente e serve como referência para futuras alterações, troubleshooting e evolução da infraestrutura.

---

## 2. Identificação do ambiente

- Hostname: `local`
- Proxmox VE: `9.2.2`
- Sistema operacional: Debian GNU/Linux 13 (trixie)
- Node: `local`

---

## 3. Interface física

A interface física identificada no Proxmox é:

- Interface: `nic0`
- Estado: ativa
- Função: porta física associada à bridge `vmbr0`

---

## 4. Linux Bridge

A bridge principal identificada é:

- Nome: `vmbr0`
- Tipo: Linux Bridge
- Estado: ativo
- Inicialização automática: habilitada
- Porta da bridge: `nic0`
- VLAN-aware: desabilitado

### Endereçamento

- IPv4/CIDR: `[IP LAN do Proxmox]/[CIDR omitido]`
- Gateway IPv4: `[IP LAN do gateway/DNS omitido]`
- IPv6: não configurado
- Gateway IPv6: não configurado

---

## 5. DNS

A configuração de DNS observada no Proxmox apresenta:

- Servidor DNS: `[IP LAN do gateway/DNS omitido]`
- Domínio de pesquisa: `proxmox`

O endereço `[IP LAN do gateway/DNS omitido]` exerce, no ambiente atual, a função de gateway e servidor DNS configurado para o Proxmox.

---

## 6. Hosts

A configuração local de hosts apresenta:

`127.0.0.1 localhost.localdomain localhost`

`[IP LAN do Proxmox omitido] [hostname interno omitido]`

A entrada `[IP LAN do Proxmox omitido] [hostname interno omitido]` associa o endereço IPv4 do Proxmox ao hostname `local` e ao nome `local.proxmox`.

---

## 7. Arquitetura observada

A estrutura de rede observada é:

**Roteador `[IP LAN do gateway/DNS omitido]` → `nic0` → `vmbr0` → Proxmox `[IP LAN do Proxmox]/[CIDR omitido]`**

### Fluxo de rede

**Rede física → nic0 → vmbr0 → Proxmox**

A `vmbr0` utiliza `nic0` como porta física e possui o endereço IP utilizado para administração do Proxmox.

---

## 8. Realocação física do Homelab

Após o levantamento inicial da configuração de rede, o servidor Homelab foi fisicamente realocado para um local mais adequado, seguro e protegido.

Para realizar a realocação, foi utilizado um cabo Ethernet de aproximadamente 10 metros.

A máquina permaneceu conectada ao mesmo roteador/modem utilizado anteriormente. Não foram realizadas alterações na configuração lógica da rede durante a mudança de local.

### Configuração preservada

- IP do Proxmox: `[IP LAN do Proxmox]/[CIDR omitido]`
- Gateway: `[IP LAN do gateway/DNS omitido]`
- Servidor DNS: `[IP LAN do gateway/DNS omitido]`
- Bridge: `vmbr0`
- Interface física: `nic0`

---

## 9. Validação após a realocação

Após a realocação física, o Proxmox foi iniciado novamente e o acesso ao ambiente foi validado.

### Resultado

- Configuração lógica de rede permaneceu inalterada.
- Endereço IP permaneceu `[IP LAN do Proxmox]/[CIDR omitido]`.
- Gateway permaneceu `[IP LAN do gateway/DNS omitido]`.
- A bridge `vmbr0` permaneceu operacional.
- O acesso ao painel Web do Proxmox foi restabelecido com sucesso.
- Dois usuários conseguiram acessar o ambiente após a realocação.
- Nenhuma alteração de configuração de rede foi necessária.

### Conclusão

A realocação física do Homelab foi concluída com sucesso utilizando um cabo Ethernet de aproximadamente 10 metros.

A mudança de localização não exigiu alterações na configuração lógica da rede.

---

## 10. Cuidados operacionais

Durante a realocação física do Homelab, foram observados os seguintes cuidados:

- O servidor foi desligado de forma controlada antes da movimentação.
- A máquina foi transportada somente após o desligamento completo.
- Foi utilizado um cabo Ethernet de aproximadamente 10 metros.
- O servidor permaneceu conectado ao mesmo roteador/modem.
- A nova localização foi escolhida visando maior segurança e proteção física.
- Após a realocação, o servidor foi ligado novamente.
- A conectividade foi validada através do acesso ao painel Web do Proxmox.
- Nenhuma alteração na configuração lógica da rede foi necessária.

### Resultado operacional

A realocação física foi concluída sem alteração da configuração lógica e sem perda de acesso ao ambiente após a validação.

---

## 11. Estado atual

Nenhuma alteração de configuração lógica de rede foi realizada durante o levantamento ou durante a realocação física.

O ambiente permanece utilizando a configuração de rede registrada neste documento.

### 11.1 Validação operacional da rede

Em 22/09/2026 foi realizada uma validação operacional da configuração de rede do Proxmox, sem aplicação de alterações na configuração.

#### Estado das interfaces

A interface física `nic0` encontra-se operacional (`UP`) e está vinculada à bridge `vmbr0`.

A bridge `vmbr0` encontra-se operacional (`UP`) e possui o endereço `[IP LAN do Proxmox]/[CIDR omitido]`.

A interface física `nic0` não possui endereço IP próprio. O endereço IPv4 do host está associado à `vmbr0`.

#### Roteamento

A tabela de rotas confirmou:

- Rede local: `[sub-rede LAN omitida]` através de `vmbr0`
- Endereço de origem: `[IP LAN do Proxmox omitido]`
- Rota padrão: `[IP LAN do gateway/DNS omitido]` através de `vmbr0`

#### Teste de conectividade com o gateway

Foi executado o comando `ping -c 4 [IP LAN do gateway/DNS omitido]`.

Resultado:

- 4 pacotes enviados
- 4 pacotes recebidos
- 0% de perda
- Latência média de aproximadamente 0,92 ms

O gateway apresentou conectividade normal com o Proxmox.

#### Teste de conectividade externa

Foi executado o comando `ping -c 4 1.1.1.1`.

Resultado:

- 4 pacotes enviados
- 4 pacotes recebidos
- 0% de perda
- Latência média de aproximadamente 34,17 ms

A conectividade IP externa foi confirmada.

#### Validação do DNS

A configuração observada no Proxmox utiliza `[IP LAN do gateway/DNS omitido]` como servidor DNS.

O arquivo `/etc/resolv.conf` também apresenta `nameserver [IP LAN do gateway/DNS omitido]`.

Entretanto, a consulta `nslookup debian.org [IP LAN do gateway/DNS omitido]` retornou erro de conexão recusada na porta DNS `53`.

Como teste comparativo, foi realizada a consulta `nslookup debian.org 1.1.1.1`.

A resolução ocorreu normalmente.

#### Diagnóstico

A validação indica que a conectividade de rede e o roteamento do Proxmox estão operacionais.

A inconsistência identificada está relacionada ao servidor DNS atualmente configurado (`[IP LAN do gateway/DNS omitido]`), que não respondeu à consulta DNS realizada na porta `53`.

Nenhuma alteração de configuração foi aplicada durante essa investigação.

A correção do DNS permanece pendente de análise e deverá ser realizada de forma controlada, seguida de nova validação.

#### Validação adicional do serviço DNS

Foi realizado um teste adicional de conectividade TCP com a porta DNS `53` do gateway `[IP LAN do gateway/DNS omitido]`.

Foi executado o comando `nc -zv [IP LAN do gateway/DNS omitido] 53`.

### Resultado

O teste retornou:

`Connection refused`

A conexão TCP com a porta `53` do endereço `[IP LAN do gateway/DNS omitido]` foi recusada.

Este resultado reforça a evidência de que o serviço DNS do endereço `[IP LAN do gateway/DNS omitido]` não está aceitando conexões TCP na porta `53`.

> Observação: este teste verificou somente TCP/53. Ainda não foi realizado um teste específico de UDP/53.

---

## 12. Próximas etapas

O estado atual da rede está documentado e validado.

A próxima etapa será investigar de forma controlada o funcionamento do DNS no gateway `[IP LAN do gateway/DNS omitido]`.

Antes de qualquer alteração de configuração, serão verificados:

- funcionamento do serviço DNS no roteador/modem;
- disponibilidade de DNS para os dispositivos da rede;
- possíveis regras de firewall ou segurança;
- possibilidade de utilizar um servidor DNS externo;
- impacto de qualquer alteração no funcionamento do Proxmox.

**Nenhuma alteração de configuração deverá ser realizada antes dessa análise.**

---

**Status:** Em revisão

**Responsável:** Mr. Klaz

**Data do levantamento:** 20/09/2026

**Data da validação após realocação:** 22/09/2026
---

## 13. Investigação complementar de DNS — 14/10/2026

### 13.1. Objetivo

Investigar o comportamento da resolução de nomes no Proxmox após a instalação do Cloudflare One Client no computador pessoal com Windows.

**Observação:** o Cloudflare One Client foi instalado no Windows, não diretamente no Proxmox. Não foi demonstrada relação causal entre esse cliente e a falha de DNS do servidor.

### 13.2. Estado da rede

Os testes confirmaram:

- Interface física `nic0` e bridge `vmbr0` em estado `UP`.
- Endereço IPv4 do Proxmox: `[IP LAN do Proxmox]/[CIDR omitido]`.
- Gateway padrão: `[IP LAN do gateway/DNS omitido]`, pela interface `vmbr0`.
- Serviço `networking` ativo.
- Arquivo `/etc/network/interfaces` coerente com o endereço, gateway e associação da bridge observados.
- Diretório `/etc/network/interfaces.d/` sem arquivos adicionais listados.

### 13.3. Configuração de resolução de nomes

O arquivo `/etc/resolv.conf` apresentou:

```text
search proxmox
nameserver [IP LAN do gateway/DNS omitido]
```

O arquivo é regular, não um link simbólico.

O arquivo `/etc/nsswitch.conf` contém:

```text
hosts: files dns
networks: files
```

O pacote `libc6` está instalado, assim como o módulo `/lib/x86_64-linux-gnu/libnss_dns.so.2`.

A resolução de `localhost` via `getent` funcionou. Entretanto, as consultas `getent hosts debian.org`, `getent ahostsv4 debian.org` e `getent ahosts deb.debian.org` não retornaram endereços nos testes realizados.

### 13.4. Resultados dos testes

| Teste | Resultado observado |
|---|---|
| Ping para `[IP LAN do gateway/DNS omitido]` | 4/4 respostas, 0% de perda |
| Ping para `1.1.1.1` | 4/4 respostas, 0% de perda |
| `nslookup debian.org [IP LAN do gateway/DNS omitido]` | `connection refused` na porta 53 |
| `nslookup debian.org 1.1.1.1` | Resolução bem-sucedida |
| `nslookup -vc debian.org 1.1.1.1` | Resolução bem-sucedida com TCP solicitado |
| `getent` para nomes externos | Sem endereços retornados |
| `getent hosts localhost` | Resolução local bem-sucedida |
| `curl -I https://deb.debian.org` | Falha de resolução do nome |
| `curl` com `--resolve` e IP explícito | Resposta HTTP/2 200 |

### 13.5. Diagnóstico provisório

Os testes demonstram que o gateway responde, que houve conectividade IP externa nos testes realizados e que consultas DNS diretas a `1.1.1.1` foram bem-sucedidas.

Em contrapartida, as consultas DNS direcionadas a `[IP LAN do gateway/DNS omitido]` foram recusadas, e a resolução de nomes externos pelo mecanismo padrão do sistema não retornou endereços.

**A causa raiz permanece não identificada.** Os resultados não permitem concluir que o roteador esteja defeituoso, nem demonstram relação causal com o Cloudflare One Client instalado no Windows.

### 13.6. Limitações e pendências

- O painel administrativo do roteador Claro ainda não foi inspecionado.
- Não foi identificado por que o DNS configurado no Proxmox não aceita as consultas testadas.
- Não foram realizadas alterações de DNS, firewall, rotas ou serviços durante esta investigação.
- A ausência de saída em comandos de consulta foi registrada como resultado observado, sem presumir uma causa não comprovada.

### 13.7. Próximas ações

1. Revisar e validar este registro.
2. Investigar o serviço DNS do roteador quando houver acesso ao painel administrativo.
3. Se necessário, realizar testes adicionais de DNS UDP/TCP e examinar a configuração efetiva de resolução.
4. Avaliar qualquer alteração somente após compreender as evidências e os riscos.
5. Validar a documentação antes do commit no GitHub.

**Status:** investigação complementar registrada para revisão.

**Responsável:** Mr. Klaz
