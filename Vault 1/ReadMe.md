
# Guia de Sincronização do Obsidian com o GitHub

Passo a passo para inicializar o controlo de versões de um cofre local do Obsidian, associá-lo a um repositório privado no GitHub e automatizar cópias de segurança sem recorrer ao terminal no dia a dia.

---

## 1. Pré-requisitos
* Conta criada no [GitHub](https://github.com).
* [Git for Windows](https://git-scm.com/) instalado no computador.
* Aplicação [Obsidian](https://obsidian.md/) instalada com o cofre de notas criado.

---

## 2. Criação do Repositório Remoto
1. Aceder ao GitHub e criar um novo repositório (**New repository**).
2. Definir o nome do repositório (ex.: `meu-obsidian-vault`).
3. Marcar a visibilidade como **Private**.
4. **Não adicionar** ficheiro README, nem `.gitignore`, nem licença (o repositório deve iniciar 100% vazio).
5. Copiar o URL HTTPS fornecido (formato: `https://github.com/utilizador/repositorio.git`).

---

## 3. Inicialização e Primeiro Envio via PowerShell
1. No Explorador de Ficheiros do Windows, navegar até à pasta do cofre do Obsidian.
2. Clicar na barra de endereços no topo, escrever `powershell` e premir `Enter`.
3. Executar a sequência de comandos para inicializar o repositório e ignorar ficheiros temporários da interface:
   ```powershell
   # Inicializar a base de dados do Git
   git init

   # Criar o .gitignore para ignorar ficheiros de espaço de trabalho voláteis
   Add-Content -Path .gitignore -Value ".obsidian/workspace.json"
   Add-Content -Path .gitignore -Value ".obsidian/workspace-mobile.json"

   # Registar todos os ficheiros no histórico local
   git add .
   git commit -m "Commit inicial do vault"

   # Definir o ramo principal, ligar ao GitHub e enviar
   git branch -M main
   git remote add origin [https://github.com/o-teu-utilizador/o-teu-repositorio.git](https://github.com/o-teu-utilizador/o-teu-repositorio.git)
   git push -u origin main
   
  ```
  ````

  ```
´´´

4. Se surgir a janela do gestor de credenciais do Git, autenticar através do browser (_Sign in with your browser_).
    

## 4. Automação no Obsidian (Plugin Obsidian Git)

Para sincronizar as notas automaticamente sem recorrer à linha de comandos:

1. No Obsidian, abrir **Definições** (`Ctrl + ,`) $\rightarrow$ **Módulos da comunidade** (_Community plugins_).
    
2. Desativar o _Modo restrito_ (_Restricted mode_) se estiver ligado.
    
3. Clicar em **Explorar** (_Browse_), pesquisar por `Git` (autor: _Vinzent_), **Instalar** e **Ativar**.
    
4. Aceder às opções do plugin **Git** no menu lateral das definições:
    
    - **Vault backup interval (minutes):** definir `10` ou `15` para envio automático periódico.
        
    - **Auto backup after file change:** opcional (guarda após alterações contínuas).
        
    - **Pull updates on startup:** ativar (garante a transferência de alterações remotas ao abrir a aplicação).
        

## 5. Comandos Rápidos e Teste

- **Testar envio manual:** premir `Ctrl + P`, escrever `Obsidian Git: Backup and sync` e premir `Enter`. Uma notificação no canto superior direito confirmará o envio com sucesso.
    
- **Forçar descarregamento manual:** premir `Ctrl + P`, escrever `Obsidian Git: Pull` e premir `Enter`.
  