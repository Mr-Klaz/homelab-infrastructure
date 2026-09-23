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

- IPv4/CIDR: `192.168.0.146/24`
- Gateway IPv4: `192.168.0.1`
- IPv6: não configurado
- Gateway IPv6: não configurado

---

## 5. DNS

A configuração de DNS observada no Proxmox apresenta:

- Servidor DNS: `192.168.0.1`
- Domínio de pesquisa: `proxmox`

O endereço `192.168.0.1` exerce, no ambiente atual, a função de gateway e servidor DNS configurado para o Proxmox.

---

## 6. Hosts

A configuração local de hosts apresenta:

`127.0.0.1 localhost.localdomain localhost`

`192.168.0.146 local.proxmox local`

A entrada `192.168.0.146 local.proxmox local` associa o endereço IPv4 do Proxmox ao hostname `local` e ao nome `local.proxmox`.

---

## 7. Arquitetura observada

A estrutura de rede observada é:

**Roteador `192.168.0.1` → `nic0` → `vmbr0` → Proxmox `192.168.0.146/24`**

### Fluxo de rede

**Rede física → nic0 → vmbr0 → Proxmox**

A `vmbr0` utiliza `nic0` como porta física e possui o endereço IP utilizado para administração do Proxmox.

---

## 8. Realocação física do Homelab

Após o levantamento inicial da configuração de rede, o servidor Homelab foi fisicamente realocado para um local mais adequado, seguro e protegido.

Para realizar a realocação, foi utilizado um cabo Ethernet de aproximadamente 10 metros.

A máquina permaneceu conectada ao mesmo roteador/modem utilizado anteriormente. Não foram realizadas alterações na configuração lógica da rede durante a mudança de local.

### Configuração preservada

- IP do Proxmox: `192.168.0.146/24`
- Gateway: `192.168.0.1`
- Servidor DNS: `192.168.0.1`
- Bridge: `vmbr0`
- Interface física: `nic0`

---

## 9. Validação após a realocação

Após a realocação física, o Proxmox foi iniciado novamente e o acesso ao ambiente foi validado.

### Resultado

- Configuração lógica de rede permaneceu inalterada.
- Endereço IP permaneceu `192.168.0.146/24`.
- Gateway permaneceu `192.168.0.1`.
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

A bridge `vmbr0` encontra-se operacional (`UP`) e possui o endereço `192.168.0.146/24`.

A interface física `nic0` não possui endereço IP próprio. O endereço IPv4 do host está associado à `vmbr0`.

#### Roteamento

A tabela de rotas confirmou:

- Rede local: `192.168.0.0/24` através de `vmbr0`
- Endereço de origem: `192.168.0.146`
- Rota padrão: `192.168.0.1` através de `vmbr0`

#### Teste de conectividade com o gateway

Foi executado o comando `ping -c 4 192.168.0.1`.

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

A configuração observada no Proxmox utiliza `192.168.0.1` como servidor DNS.

O arquivo `/etc/resolv.conf` também apresenta `nameserver 192.168.0.1`.

Entretanto, a consulta `nslookup debian.org 192.168.0.1` retornou erro de conexão recusada na porta DNS `53`.

Como teste comparativo, foi realizada a consulta `nslookup debian.org 1.1.1.1`.

A resolução ocorreu normalmente.

#### Diagnóstico

A validação indica que a conectividade de rede e o roteamento do Proxmox estão operacionais.

A inconsistência identificada está relacionada ao servidor DNS atualmente configurado (`192.168.0.1`), que não respondeu à consulta DNS realizada na porta `53`.

Nenhuma alteração de configuração foi aplicada durante essa investigação.

A correção do DNS permanece pendente de análise e deverá ser realizada de forma controlada, seguida de nova validação.

#### Validação adicional do serviço DNS

Foi realizado um teste adicional de conectividade TCP com a porta DNS `53` do gateway `192.168.0.1`.

Foi executado o comando `nc -zv 192.168.0.1 53`.

### Resultado

O teste retornou:

`Connection refused`

A conexão TCP com a porta `53` do endereço `192.168.0.1` foi recusada.

Este resultado reforça a evidência de que o serviço DNS do endereço `192.168.0.1` não está aceitando conexões TCP na porta `53`.

> Observação: este teste verificou somente TCP/53. Ainda não foi realizado um teste específico de UDP/53.

---

## 12. Próximas etapas

O estado atual da rede está documentado e validado.

A próxima etapa será investigar de forma controlada o funcionamento do DNS no gateway `192.168.0.1`.

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
