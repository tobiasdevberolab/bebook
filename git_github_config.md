# Guia Completo de Configuração do Git e GitHub

Este guia apresenta os passos necessários para configurar corretamente o Git e GitHub em sua máquina, incluindo configurações de usuário, autenticação e boas práticas de segurança.

## Sumário

1. [Verificação e instalação do Git](#1-verificação-e-instalação-do-git)
2. [Configuração do usuário Git](#2-configuração-do-usuário-git)
3. [Instalação do GitHub CLI](#3-instalação-do-github-cli)
4. [Autenticação no GitHub](#4-autenticação-no-github)
5. [Configuração de chaves SSH](#5-configuração-de-chaves-ssh)
6. [Conectando ao repositório remoto](#6-conectando-ao-repositório-remoto)
7. [Criando e enviando commits](#7-criando-e-enviando-commits)
8. [Lidando com múltiplas contas GitHub](#8-lidando-com-múltiplas-contas-github)

## 1. Verificação e instalação do Git

Primeiro, verifique se o Git já está instalado:

```bash
git --version
```

Se não estiver instalado:

- **macOS**: 
  ```bash
  brew install git
  ```
- **Ubuntu/Debian**:
  ```bash
  sudo apt-get update
  sudo apt-get install git
  ```
- **Windows**: Baixe o instalador em [git-scm.com](https://git-scm.com/download/win)

## 2. Configuração do usuário Git

Configure seu nome de usuário e email para os commits:

```bash
git config --global user.name "seu-nome-de-usuario"
git config --global user.email "seu-email@example.com"
```

Verifique suas configurações:

```bash
git config --list
```

## 3. Instalação do GitHub CLI

O GitHub CLI (`gh`) facilita a interação com o GitHub pela linha de comando:

- **macOS**:
  ```bash
  brew install gh
  ```
- **Ubuntu/Debian**:
  ```bash
  sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key C99B11DEB97541F0
  sudo apt-add-repository https://cli.github.com/packages
  sudo apt update
  sudo apt install gh
  ```
- **Windows** (via Chocolatey):
  ```bash
  choco install gh
  ```

## 4. Autenticação no GitHub

A maneira mais simples de se autenticar usando o GitHub CLI:

```bash
gh auth login
```

Siga as instruções para:
1. Escolher GitHub.com
2. Selecionar SSH como método preferido
3. Selecionar seu navegador para autenticação

Isso abrirá seu navegador e solicitará autorização para o GitHub CLI.

## 5. Configuração de chaves SSH

Gerar uma nova chave SSH:

```bash
ssh-keygen -t ed25519 -C "seu-email@example.com"
```

Inicie o agente SSH e adicione sua chave:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

Adicione sua chave pública ao GitHub:

```bash
gh ssh-key add ~/.ssh/id_ed25519.pub -t "Descrição da chave"
```

> **Observação**: Se o comando acima retornar um erro de permissão, execute:
> ```bash
> gh auth refresh -h github.com -s admin:public_key
> ```
> E tente adicionar a chave novamente.

## 6. Conectando ao repositório remoto

**Criar um novo repositório no GitHub e conectar a um diretório local**:

```bash
# Inicializa um novo repositório Git local
git init

# Conecta ao repositório remoto
git remote add origin git@github.com:seu-usuario/seu-repositorio.git

# Renomeia a branch principal para 'main'
git branch -M main
```

**Clonar um repositório existente**:

```bash
git clone git@github.com:seu-usuario/seu-repositorio.git
```

## 7. Criando e enviando commits

Prepare-se antes de enviar código sensível:

1. Crie um arquivo `.gitignore` para excluir arquivos sensíveis:

```bash
touch .gitignore
```

Adicione entradas para arquivos que não devem ser versionados:

```
# dependencies
/node_modules

# local env files
.env*.local
.env

# chaves privadas e arquivos sensíveis
*.pem
id_rsa*
id_ed25519*
```

Depois de configurar o `.gitignore`:

```bash
# Adicionar arquivos
git add .

# Fazer commit
git commit -m "Mensagem descritiva do commit"

# Enviar para o repositório remoto
git push -u origin main
```

## 8. Lidando com múltiplas contas GitHub

Se você precisa trabalhar com múltiplas contas GitHub na mesma máquina:

1. Crie chaves SSH separadas para cada conta:

```bash
ssh-keygen -t ed25519 -C "email-da-segunda-conta@example.com" -f ~/.ssh/id_ed25519_conta2
```

2. Configure o arquivo `~/.ssh/config`:

```
# Conta padrão
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519

# Segunda conta
Host github-conta2
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_conta2
```

3. Para usar a segunda conta, especifique o host ao adicionar o remoto:

```bash
git remote add origin git@github-conta2:usuario-conta2/repositorio.git
```

## Dicas importantes

1. **Nunca comite credenciais**: Certifique-se de que arquivos como `.env`, chaves privadas e tokens nunca sejam commitados.

2. **Tokens de autenticação**: Se estiver tendo problemas com autenticação SSH, você pode usar tokens:

```bash
git remote set-url origin https://seu-usuario:ghp_SeuTokenAqui@github.com/seu-usuario/seu-repositorio.git
```

3. **Status da autenticação**: Para verificar qual conta está ativa no GitHub CLI:

```bash
gh auth status
```

4. **Solução de problemas comuns**:
   - Erro "Permission denied": Verifique se sua chave SSH está configurada corretamente
   - Erro "Repository not found": Verifique se você tem acesso ao repositório
   - Erro "Fatal: refusing to merge unrelated histories": Use `git pull origin main --allow-unrelated-histories`

---

Este guia foi criado para facilitar a configuração do Git e GitHub, evitando problemas comuns e seguindo boas práticas de segurança. 