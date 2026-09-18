# Troubleshooting — Erro 401 no Console Shell do Proxmox

## 📋 Informações do incidente

- **Plataforma:** Proxmox VE
- **Versão:** 9.2.2
- **Nó:** local
- **Usuário:** root@pam
- **Serviço afetado:** Console Shell
- **Código do erro:** HTTP 401 — Authentication failed

---

## 🔴 Problema

Ao tentar abrir o Console Shell do nó `local` através da interface web do Proxmox, o console não carregava e apresentava:

```text
Connection failed (Error 401: Authentication failed!)
