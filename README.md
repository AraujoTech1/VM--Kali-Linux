<p align="center">
  <a href="https://skillicons.dev">
    <img src="https://skillicons.dev/icons?i=azure,kali,linux,apache,git" width="220" />
  </a>
</p>

---

<h1 align="center">Laboratório: VM Ubuntu (Azure) & Desafio Kali + Medusa</h1>

**Resumo do repositório**  
Este repositório reúne duas atividades de laboratório: (1) criação e configuração de uma VM Ubuntu em Microsoft Azure (ambiente de estudos/servidor web Apache) e (2) um desafio prático de segurança ofensiva em ambiente controlado usando **Kali Linux** e a ferramenta **Medusa** para simular ataques de força bruta (FTP, Web/DVWA, SMB). O objetivo é documentar a montagem do ambiente, os testes realizados, as ferramentas/comandos usados e as recomendações de mitigação.

> ⚠️ **Aviso de segurança**: nunca armazene senhas, chaves privadas ou endereços IP públicos sensíveis neste repositório. Se já publicou credenciais, remova-as e **roteie/rotacione** as credenciais imediatamente.

---

## Índice
- [Visão Geral](#visão-geral)
- [Desafio — Kali + Medusa](#desafio---kali--medusa)
  - [Objetivo](#objetivo)
  - [Ambiente (recomendado)](#ambiente-recomendado)
  - [Cenários de Teste](#cenários-de-teste)
  - [Wordlists e Comandos](#wordlists-e-comandos)
  - [Validação e Evidências](#validação-e-evidências)
  - [Recomendações de Mitigação](#recomendações-de-mitigação)
- [VM Ubuntu no Azure (resumo)](#vm-ubuntu-no-azure-resumo)
- [Tecnologias](#tecnologias)
- [Pré-requisitos](#pré-requisitos)
- [Como usar / reproduzir](#como-usar--reproduzir)
- [Considerações éticas e legais](#considerações-éticas-e-legais)
- [Licença](#licença)

---

## Visão Geral
Este projeto documenta tanto a criação de um ambiente Linux acessível para testes (VM Ubuntu) quanto a execução de um desafio de segurança ofensiva controlada. A ideia é oferecer um portfólio técnico com evidências e explicações, ideal para aprendizado e apresentação em plataformas como a DIO.

---

## Desafio — Kali + Medusa

### Objetivo
Implementar, executar e documentar ataques de força bruta controlados utilizando **Medusa** em um ambiente de laboratório (Kali Linux -> serviços vulneráveis em Metasploitable2 / DVWA), e então propor medidas de mitigação.

### Ambiente (recomendado)
- Host: VirtualBox (ou VMware)
- 2 VMs:
  - **Kali Linux** (atacante)
  - **Metasploitable2** e/ou **DVWA** (alvo vulnerável)
- Rede: *Host-only* ou *Internal Network* (isolada da Internet)
- Ferramentas:
  - medusa
  - hydra (opcional)
  - nikto / dirb (opcional)
  - nmap (enumeração)
- Observação: mantenha o ambiente isolado e legal — só atacar máquinas que você controla.

### Cenários de Teste
1. **FTP (Metasploitable2)**  
   - Ataque de força bruta contra FTP usando Medusa com uma wordlist simples.
2. **Formulário web (DVWA)**  
   - Automação de tentativas contra formulário de login (se DVWA configurado para inseguro) — cuidado com limites e CSRF.
3. **SMB (password spraying + enumeração)**  
   - Enumeração de usuários (e.g., via enum4linux / smbclient) seguida de password-spraying (senhas comuns) por meio do Medusa.

### Wordlists e Comandos (exemplos)
Crie uma pasta `wordlists/` no repositório com wordlists pequenas para demonstração (ex.: `small-userlist.txt`, `small-passlist.txt`).

**Exemplo: nmap (enumeração)**  
```bash
# varrer portas e serviços básicos
nmap -sV -Pn -p 21,22,80,139,445 <IP_ALVO>
Medusa — FTP (exemplo)

# medusa utilizando lista de usuários e senhas
medusa -h <IP_ALVO> -u userlist.txt -P passlist.txt -M ftp


Medusa — SMB (password spraying)

# testar uma senha para vários usuários (spraying)
medusa -h <IP_ALVO> -U userlist.txt -p 'Password123' -M smbnt


Automação contra formulário web (conceito)
Para formulários HTML pode-se usar curl em um loop ou ferramentas específicas/robots — sempre com autorização:

# exemplo conceitual (não execute sem autorização)
curl -X POST -d "username=USER&password=PASS&submit=Login" http://<IP_DVWA>/vulnerabilities/brute/login.php


Obs: adapte os comandos ao alvo; os módulos do Medusa são: ftp, http, http-form-post, smbnt, etc. (confira medusa -h).

Validação e Evidências

Salve logs e outputs (em /artifacts/), por exemplo:

nmap-output.txt

medusa-ftp-results.txt

screenshots (ex.: imgs/ftp-success.png, imgs/dvwa-attempts.png)

Documente: qual usuário/senha funcionou (somente em ambiente de laboratório), número de tentativas, tempo, e impacto.

Recomendações de Mitigação

Habilitar bloqueio por tentativas falhas (fail2ban, account lockout).

Utilizar senhas fortes e políticas de complexidade.

Habilitar MFA onde possível.

Restringir acessos via firewall/segurança de rede.

Monitoramento e alertas (SIEM / logs).

Não permitir contas administrativas com senhas por padrão.

VM Ubuntu no Azure (resumo)

Esta seção resume a criação de uma VM Ubuntu 24.04 LTS no Azure para estudos (detalhes completos em docs/azure-vm-setup.md se quiser expandir).

Funcionalidades:

VM Ubuntu 24.04 LTS com Apache instalado

Acesso SSH (recomenda-se usar chave pública: não usar acesso por senha em produção)

UFW configurado para permitir Apache e SSH

Ferramentas instaladas: git, curl, wget, htop, net-tools, ufw

⚠️ Importante: Se o seu README anterior expõe IPs e senhas, remova essas informações imediatamente e rotacione as credenciais.

Tecnologias

Kali Linux

Metasploitable2 / DVWA

Medusa

VirtualBox

Ubuntu 24.04 LTS

Apache2

Git

Pré-requisitos

Conta/instância local para rodar VirtualBox

Imagens ISO/VMs de Kali e Metasploitable2/DVWA

Conhecimentos básicos de Linux e redes

Como usar / reproduzir

Clone o repositório:

git clone <URL_DO_REPOSITORIO>
cd <REPOSITORIO>


Monte a rede local no VirtualBox com duas VMs (Kali + Metasploitable2/DVWA) em Host-only.

Em Kali, atualize e instale medusa:

sudo apt update && sudo apt install medusa -y


Coloque suas wordlists em wordlists/.

Execute passos de enumeração (nmap) e depois execute medusa conforme os exemplos.

Guarde os resultados em artifacts/ e tire screenshots em imgs/.

Considerações éticas e legais

Este repositório é para uso em laboratório controlado. Nunca realize ataques sem autorização explícita do proprietário do sistema alvo. Atividades ofensivas sem consentimento são crime em muitos países.

Licença

Este projeto está disponível sob a licença MIT — adapte conforme preferir.


---

## Passos Git sugeridos (para atualizar seu repositório)
Se você já tem o repositório clonado localmente, rode:

```bash
# entrar na pasta do repo
cd /caminho/para/seu-repo

# criar branch de atualização (opcional mas recomendado)
git checkout -b update-readme-challenge

# substituir README.md pelo conteúdo acima (cole e salve)
# se estiver no Linux/WSL, por exemplo:
cat > README.md <<'EOF'
... (cole todo o conteúdo do README acima) ...
EOF

# adicionar e commitar
git add README.md
git commit -m "Atualiza README: documenta desafio Kali+Medusa e resumo da VM Azure"

# push para o GitHub
git push origin update-readme-challenge

# depois crie um Pull Request no GitHub e merge — ou, se quiser atualizar direto na main:
# git checkout main
# git merge update-readme-challenge
# git push origin main

Se já expôs senhas/IPs — passos rápidos e essenciais

Remova imediatamente as informações sensíveis do repositório (edite README e retire IP/senha).

Roteie/rotacione as credenciais expostas (troque a senha, renove chaves).

Se as credenciais estiverem em commits antigos, considere reescrever o histórico (ex.: git filter-branch, git filter-repo ou BFG) e forçar push — apenas se souber as implicações. Uma alternativa mais simples é deletar o repositório e recriá-lo caso a exposição seja crítica.

Notifique qualquer serviço afetado.

Se quiser, eu já faço as próximas etapas por você:

gerar o README.md final (copio exatamente o conteúdo em um arquivo pronto para você colar),

criar os arquivos auxiliares sugeridos (wordlists/README.md, docs/azure-vm-setup.md, artifacts/.gitkeep) e mostrar os comandos exatos git para adicionar tudo, ou

criar um PR (se você me der acesso via paste do git remote? — mas NÃO preciso de senha: apenas faça localmente com os comandos acima).
