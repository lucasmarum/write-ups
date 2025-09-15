# 🚀 TryHackMe - Startup \[Write-up]

Este write-up documenta a exploração completa da máquina Startup no TryHackMe. A abordagem envolveu enumeração de serviços, exploração via FTP e HTTP, execução de uma shell reversa, escalonamento de privilégios e captura das flags de usuário e root.

Iniciei com um scan TCP SYN usando o Nmap para identificar portas abertas:

![](images/Pasted%20image%2020250911072520.png)

Host ativo com 0.27s de latência

Portas abertas:

21/tcp – FTP

22/tcp – SSH

80/tcp – HTTP

Conectei ao serviço FTP e consegui listar arquivos sem autenticação especial:

![](images/Pasted%20image%2020250911072702.png)

Enviei uma web shell PHP via FTP para o diretório acessível via HTTP:

![](images/Pasted%20image%2020250911073741.png)

Logo apos isso verifiquei se tinha python no servidor (e tinha) e consgui obter uma shell reversa para minha maquina local

Naveguei até o diretório /incidents e encontrei o arquivo suspicious.pcapng.

![](images/Pasted%20image%2020250911075314.png)

Identifiquei tentativas de uso de sudo, acesso negado a diretórios como /home-data, e comandos como ls retornando “Permission denied”. Isso sugere atividade suspeita ou tentativa de escalonamento de privilégios.

![](images/Pasted%20image%2020250911081228.png)

com a senha tentei entrar como o usuario Lennie e obtive acesso via ssh e obtendo a primeira flag!

![](images/Pasted%20image%2020250911081452.png)

Agora com acesso como usuário, o próximo passo era escalar privilégios para obter acesso root.

Comecei verificando permissões de sudo:

```
sudo -l
```

Nenhuma permissão relevante foi encontrada. Em seguida, listei os arquivos e diretórios com:

```
ls -la
```

Naveguei até o diretório scripts e encontrei o arquivo planner.sh:

```
cd scripts
ls
cat planner.sh
```

O conteúdo revelou que planner.sh executa o script print.sh, que temos permissão para editar. Isso abriu uma oportunidade clara para exploração.

Utilizei um gerador online de payloads para criar uma shell reversa e editei o print.sh com o seguinte conteúdo:

![](images/Pasted%20image%2020250911082310.png)

Antes de salvar, iniciei um listener com Netcat:

![](images/Pasted%20image%2020250911082322.png)

e com isso consegui logar como root e pegar a ultima flag

![](images/Pasted%20image%2020250911082341.png)

---


