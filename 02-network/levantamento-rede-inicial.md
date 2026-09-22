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

O endereço `192.168.0.1` exerce, no ambiente atual, a função de gateway e servidor DNS.

---

## 6. Hosts

A configuração local de hosts apresenta:

```text
127.0.0.1 localhost.localdomain localhost
192.168.0.146 local.proxmox local
```

A entrada `192.168.0.146 local.proxmox local` associa o endereço IPv4 do Proxmox ao hostname `local` e ao nome `local.proxmox`.

---

## 7. Arquitetura observada

```text
                    Roteador
                  192.168.0.1
                       │
                     nic0
                       │
                    vmbr0
                 Linux Bridge
                       │
              192.168.0.146/24
                       │
                    Proxmox
                     local
```

### Fluxo de rede

```text
Rede física
    ↓
nic0
    ↓
vmbr0
    ↓
Proxmox
```

A `vmbr0` utiliza `nic0` como porta da bridge e possui o endereço IP utilizado para administração do Proxmox.

---

## 8. Realocação física do Homelab

Após o levantamento inicial da configuração de rede, o servidor Homelab foi fisicamente realocado para um local mais adequado, seguro e protegido.

Para realizar a realocação, foi utilizado um cabo Ethernet de aproximadamente **10 metros**.

A máquina permaneceu conectada ao **mesmo roteador/modem** utilizado anteriormente. Não foram realizadas alterações na configuração lógica da rede durante a mudança de local.

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

A realocação física do Homelab foi concluída com sucesso utilizando um cabo Ethernet de aproximadamente **10 metros**.

A mudança de localização não exigiu alterações na configuração lógica da rede, mantendo a conectividade do servidor através do mesmo roteador/modem.

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

---

## 12. Próximas etapas

A configuração documentada neste arquivo será utilizada como referência para as próximas etapas de evolução da rede do Homelab.

Alterações futuras deverão ser realizadas de forma controlada, testadas e documentadas quando necessário.

---

**Status:** Em revisão

**Responsável:** Mr. Klaz

**Data do levantamento:** 20/09/2026

**Data da validação após realocação:** 22/09/2026
