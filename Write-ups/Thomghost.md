# Writeup – Tomghost (TryHackMe)

## Nmap
Primeiro realizei um scan de portas com o `nmap` para identificar os serviços expostos na máquina:

![nmap scan](images/nmapscan.png)

O scan retornou as portas:
- **22/ssh**
- **53/tcp**
- **8009/tcp (ajp13)**
- **8080/tcp (http-proxy)**

---

## Vulnerabilidade AJP13 (Ghostcat)
Pesquisando sobre o serviço **ajp13**, encontrei a vulnerabilidade **Ghostcat (CVE-2020-1938)**, que afeta versões do Apache Tomcat abaixo da 9.31.  
Com o `metasploit` verifiquei que havia módulo para exploração:

![metasploit search](images/msfconsole.png)

Consegui explorar a vulnerabilidade e extrair o arquivo `WEB-INF/web.xml_471249.txt`, que continha credenciais.

---

## Acesso via SSH
No arquivo extraído, estavam as credenciais:  

skyfuck:8730281lkjlkjdqlksalks


Testei o login via SSH e obtive acesso:

![ssh login](images/ssh_login.png)

Encontrei o usuário **merlin** e consegui a **primeira flag (user.txt)**:


---

## Escalada de privilégios
Copiei os arquivos do usuário para minha máquina Kali usando `scp`, incluindo um arquivo `tryhackme.asc`.  

![scp transfer](images/scp.png)

Utilizando `gpg2john` + `john` consegui quebrar a senha da chave GPG.  
Após isso, descriptografei o arquivo e recuperei novas credenciais para o usuário **merlin**.

![john crack](images/gpg2john.png)

---
![john](images/john.png)

![john crack](images/gpg--decrypt.png)
## Root
Ao verificar permissões de `sudo`, vi que o usuário **merlin** podia rodar o `zip` como root sem senha.  

![sudo -l](images/merlin_sudo-l.png)

Usei o binário do `zip` para escalar privilégios e abrir um shell root:
![sudo](images/zip.png)

E com isso, consegui a **flag root** 🎉
