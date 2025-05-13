# Guia Detalhado de Configuração Git e GitHub com SSH

Este guia apresenta o passo a passo completo para configurar o Git e GitHub utilizando SSH como método de autenticação, garantindo uma conexão segura entre seus repositórios locais e remotos.

## Sumário

1. [Instalação dos binários](#1-instalação-dos-binários)
2. [Configuração inicial do Git](#2-configuração-inicial-do-git)
3. [Geração e configuração de chaves SSH](#3-geração-e-configuração-de-chaves-ssh)
4. [Autenticação no GitHub](#4-autenticação-no-github)
5. [Inicializando projetos com Git](#5-inicializando-projetos-com-git)
6. [Conectando com repositórios remotos](#6-conectando-com-repositórios-remotos)
7. [Comandos essenciais para trabalho diário](#7-comandos-essenciais-para-trabalho-diário)
8. [Solução de problemas comuns](#8-solução-de-problemas-comuns)

## 1. Instalação dos binários

### Instalação do Git

Verifique se o Git já está instalado:

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
  sudo apt update
  sudo apt install git
  ```

- **Windows**:
  Baixe o instalador em [git-scm.com](https://git-scm.com/download/win)

### Instalação do GitHub CLI (gh)

O GitHub CLI permite gerenciar o GitHub diretamente pelo terminal:

- **macOS**:
  ```bash
  brew install gh
  ```

- **Ubuntu/Debian**:
  ```bash
  curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
  echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
  sudo apt update
  sudo apt install gh
  ```

- **Windows** (via Chocolatey):
  ```bash
  choco install gh
  ```

## 2. Configuração inicial do Git

### Configurando seu nome e email

Estas informações aparecerão em seus commits:

```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu-email@example.com"
```

### Verificando a configuração

Para confirmar que as configurações foram salvas corretamente:

```bash
git config --list
```

### Definindo branch padrão como "main"

```bash
git config --global init.defaultBranch main
```

## 3. Geração e configuração de chaves SSH

SSH (Secure Shell) é o método recomendado para autenticação com GitHub, pois é mais seguro e evita inserir senhas a cada operação.

### Verificando chaves existentes

Antes de criar uma nova chave, verifique se já existe alguma:

```bash
ls -la ~/.ssh
```

### Gerando uma nova chave SSH

```bash
ssh-keygen -t ed25519 -C "seu-email@example.com"
```

Durante o processo:
- Você pode pressionar ENTER para salvar a chave no local padrão (`~/.ssh/id_ed25519`)
- Recomenda-se adicionar uma senha para sua chave SSH (passphrase)

> **Nota**: Se seu sistema não suporta o algoritmo Ed25519, use:
> ```bash
> ssh-keygen -t rsa -b 4096 -C "seu-email@example.com"
> ```

### Iniciando o agente SSH

O agente SSH gerencia suas chaves e lembra sua senha:

```bash
eval "$(ssh-agent -s)"
```

### Adicionando sua chave ao agente SSH

```bash
ssh-add ~/.ssh/id_ed25519
```

> **Para macOS**: Use `ssh-add --apple-use-keychain ~/.ssh/id_ed25519` para adicionar a chave ao keychain do macOS.

### Copiando a chave pública

Para adicionar ao GitHub, primeiro copie o conteúdo da chave pública:

```bash
# Linux/macOS
cat ~/.ssh/id_ed25519.pub | pbcopy

# Alternativamente, você pode exibir para copiar manualmente
cat ~/.ssh/id_ed25519.pub
```

## 4. Autenticação no GitHub

### Adicionando a chave SSH ao GitHub usando gh CLI

Autentique-se no GitHub CLI (fará login via navegador):

```bash
gh auth login
```

Selecione:
1. GitHub.com
2. SSH como protocolo preferido
3. Sim para fazer upload da chave SSH
4. Escolha sua chave ou forneça o caminho
5. Título para a chave (ex. "Notebook Pessoal")
6. Login via navegador

Alternativamente, adicione a chave manualmente:

```bash
gh ssh-key add ~/.ssh/id_ed25519.pub -t "Descrição da chave"
```

> **Nota**: Se houver erro de permissão, execute:
> ```bash
> gh auth refresh -h github.com -s admin:public_key
> ```

### Adicionando chave manualmente via website

1. Acesse [GitHub Settings > SSH keys](https://github.com/settings/keys)
2. Clique em "New SSH key"
3. Insira um título descritivo
4. Cole sua chave pública
5. Clique em "Add SSH key"

### Testando a conexão SSH

```bash
ssh -T git@github.com
```

Você deve receber uma mensagem de boas-vindas do GitHub.

## 5. Inicializando projetos com Git

### Para um novo projeto

```bash
# Crie e entre no diretório do projeto
mkdir meu-projeto
cd meu-projeto

# Inicialize o repositório Git
git init

# Crie um arquivo README inicial
echo "# Meu Projeto" > README.md

# Configure o .gitignore
touch .gitignore
```

Exemplo básico de `.gitignore`:
```
# Dependências
/node_modules
/.pnp
.pnp.js

# Arquivos de ambiente
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Logs
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Arquivos do sistema
.DS_Store
Thumbs.db
```

### Para um projeto existente

```bash
cd caminho/para/seu-projeto
git init
```

## 6. Conectando com repositórios remotos

### Criando um novo repositório no GitHub com gh CLI

```bash
gh repo create nome-do-repositorio --private --source=. --remote=origin
```

> Opções:
> - `--public` ou `--private`: Visibilidade do repositório
> - `--source=.`: Usa o diretório atual como fonte
> - `--remote=origin`: Define o nome do remoto como "origin"

### Conectando a um repositório existente

```bash
# Usando SSH (recomendado)
git remote add origin git@github.com:seu-usuario/seu-repositorio.git

# Renomeie a branch principal para 'main' (se necessário)
git branch -M main
```

### Verificando conexões remotas

```bash
git remote -v
```

## 7. Comandos essenciais para trabalho diário

### Fluxo básico de trabalho

```bash
# Verifique o status do seu repositório
git status

# Adicione arquivos para commit
git add . 
# Ou específicos: git add arquivo.txt pasta/

# Crie um commit
git commit -m "Mensagem clara descrevendo as alterações"

# Envie commits para o repositório remoto
git push -u origin main

# Após o primeiro push, pode usar apenas:
git push
```

### Trabalhando com branches

```bash
# Criar e mudar para nova branch
git checkout -b nome-da-feature

# Listar branches
git branch

# Mudar de branch
git checkout main

# Mesclar branch de feature na main
git checkout main
git merge nome-da-feature

# Enviar branch para repositório remoto
git push -u origin nome-da-feature

# Excluir branch local após mescla
git branch -d nome-da-feature

# Excluir branch remota
git push origin --delete nome-da-feature
```

### Atualizando repositório local

```bash
# Baixar atualizações do repositório remoto
git fetch

# Baixar e mesclar atualizações
git pull
```

## 8. Solução de problemas comuns

### Problemas de autenticação SSH

Se receber o erro "Permission denied":

1. Verifique se a chave SSH está adicionada ao agente:
   ```bash
   ssh-add -l
   ```

2. Se não estiver, adicione-a:
   ```bash
   ssh-add ~/.ssh/id_ed25519
   ```

3. Verifique se a chave pública está corretamente adicionada ao GitHub:
   ```bash
   gh ssh-key list
   ```

### Problemas com múltiplas contas GitHub

Para gerenciar várias contas GitHub, configure o arquivo `~/.ssh/config`:

```
# Conta principal
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519

# Segunda conta
Host github-segunda-conta
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_segunda_conta
```

Para usar a segunda conta, altere o URL do remoto:
```bash
git remote add origin git@github-segunda-conta:usuario-segunda-conta/repositorio.git
```

### Erro ao fazer push

Se receber o erro "Updates were rejected":

```bash
# Puxe as mudanças primeiro
git pull --rebase origin main

# Então tente push novamente
git push origin main
```

### Remover credenciais armazenadas incorretamente

```bash
git config --global --unset credential.helper
```

---

Este guia cobre os aspectos essenciais da configuração e uso do Git com GitHub através de SSH. Para casos específicos ou problemas não abordados, consulte a [documentação oficial do Git](https://git-scm.com/doc) ou a [documentação do GitHub](https://docs.github.com/en). 