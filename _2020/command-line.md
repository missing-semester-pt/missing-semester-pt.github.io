---
layout: lecture
title: "Interface de Linha de Comandos"
date: 2019-01-21
ready: true
video:
  aspect: 56.25
  id: e8BO_dYxk5c
---

Nessa aula vamos ver várias maneiras para melhorar o seu ambiente de trabalho ao usar o _shell_. Já estamos trabalhando com o _shell_ há um tempo, mas focamos principalmente em executar diferentes comandos. Agora vamos ver como executar diferentes processos ao mesmo tempo enquanto observamos o seu funcionamento, como parar ou pausar um processo específico e como fazer um processo funcionar em segundo plano.

Também vamos aprender diferentes maneiras para melhorar o seu _shell_ e outras ferramentas, ao definir apelidos e configurá-las utilizando _dotfiles_. Ambas ambordagens podem lhe ajudar a poupar tempo, ao por exemplo utilizar a mesma configuração em todas as suas máquinas sem ter que digitar longos comandos. Vamos ver como trabalhar em máquinas remotas utilizando SSH.


# Controle de processos

Em alguns casos você precisará interromper um processo enquanto ele está executando, como por exemplo quando um comando está demorando muito para finalizar a sua execução (como um `find` que precisará percorrer uma estrutura de diretórios muito grande).
Na maioria dos casos, você pode pressionar `Ctrl-C` e o comando será interrompido.
Mas como isso funciona de fato e por que às vezes isso não é suficiente para parar o processo?

## Matando um processo

O seu _shell_ está utilzando um mecanismo de comunicação do UNIX chamado _sinal_ para passar informações ao processo. Quando um processo recebe um sinal ele para a sua execução, interpreta o sinal e potencialmente modifica o seu fluxo de execução baseado na informação passada pelo sinal. Por essa razão, sinais são _interruptores de software_.

No seu caso, quando `Ctrl-C` é pressionado, isso indica ao _shell_ para passar um sinal `SIGINT` para o processo.

Aqui está um exemplo mínimo de um programa Python que capture um sinal `SIGINT` e o ignora, não mais interrompendo a sua execução ao recebê-lo. Para matar esse programa nos podemos utilizar o sinal `SIGQUIT`, em contrapartida, ao digitar `Ctrl-\`.

```python
#!/usr/bin/env python
import signal, time

def handler(signum, time):
    print("\nI got a SIGINT, but I am not stopping")

signal.signal(signal.SIGINT, handler)
i = 0
while True:
    time.sleep(.1)
    print("\r{}".format(i), end="")
    i += 1
```

Isso que acontece se mandarmos um sinal `SIGINT` duas vezes para esse programa, seguidos de um `SIGQUIT`. Note que `^` é como `Ctrl` é mostrado quando digitado no terminal.

```
$ python sigint.py
24^C
I got a SIGINT, but I am not stopping
26^C
I got a SIGINT, but I am not stopping
30^\[1]    39913 quit       python sigint.py
```

Enquanto ambos `SIGINT` e `SIGQUIT` são normalmente associados com requisições relacionadas ao terminal, um sinal mais genérico para demandar o fim de um processo é o sinal `SIGTERM`.
Para mandar esse sinal podemos utilizar o comando [`kill`](https://www.man7.org/linux/man-pages/man1/kill.1.html), com a sintaxe `kill -TERM <PID>`.

## Pausando e colocando processos em segundo plano

Sinais podem fazer outras coisas além de matar processos. Por exemplo, `SIGSTOP` pausa um processo. No terminal, pressionar `Ctrl-Z` irá requisitar ao _shell_ para enviar um sinal `SIGTSTP`, que é uma abreaviação para _Terminal Stop_ (a versão do terminal para `SIGSTOP`).

O processo pausado pode ser então continuado em primeiro ou segundo plano utilizando [`fg`](https://www.man7.org/linux/man-pages/man1/fg.1p.html) ou [`bg`](http://man7.org/linux/man-pages/man1/bg.1p.html), respectivamente.

O comando [`jobs`](https://www.man7.org/linux/man-pages/man1/jobs.1p.html) lista os processos não finalizados associados com a sessão atual do terminal.
Você pode se referir a esses processos utilizando o pid deles (você pode utilizar o comando [`pgrep`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) para descobrí-los).
De maneira mais intuitiva, você também pode referir a um processo utilizando o símbolo de porcentagem seguido do seu número identificador (indicado por `jobs`). Para se referir ao último processo que foi posto em segundo plano você pode utilizar o parâmetro especial `$!`.

Outra coisa importante é que utilizar o sufixo `&` em um comando vai executá-lo em segundo plano, lhe dando o controle do terminal de volta. No entanto, esse comando ainda vai utilizar a saída padrão (STDOUT) do _shell_, o que pode incomodar (você pode utilizar redirecionamentos do _shell_ nesse caso).

Para colocar em segundo plano um processo que já está rodando você pode pressionar `Ctrl-Z`, e executar o comando `bg` em seguida.
Note que processos em segundo plano ainda são processos filhos do seu terminal e vão morrer se você fechá-lo (isso enviará um outro sinal, `SIGHUP`).
Para evitar que isso aconteça você pode executar o programa com [`nohup`](https://www.man7.org/linux/man-pages/man1/nohup.1.html) (um _wrapper_ para ignorar `SIGHUP`), ou utilizar `disown` se o processo já foi iniciado.
Como outra alternativa, você pode utilizar um multiplexador de terminais, como veremos na próxima seção.

Abaixo é demonstrada uma sessão de exemplo para ilustrar alguns desses conceitos.

```
$ sleep 1000
^Z
[1]  + 18653 suspended  sleep 1000

$ nohup sleep 2000 &
[2] 18745
appending output to nohup.out

$ jobs
[1]  + suspended  sleep 1000
[2]  - running    nohup sleep 2000

$ bg %1
[1]  - 18653 continued  sleep 1000

$ jobs
[1]  - running    sleep 1000
[2]  + running    nohup sleep 2000

$ kill -STOP %1
[1]  + 18653 suspended (signal)  sleep 1000

$ jobs
[1]  + suspended (signal)  sleep 1000
[2]  - running    nohup sleep 2000

$ kill -SIGHUP %1
[1]  + 18653 hangup     sleep 1000

$ jobs
[2]  + running    nohup sleep 2000

$ kill -SIGHUP %2

$ jobs
[2]  + running    nohup sleep 2000

$ kill %2
[2]  + 18745 terminated  nohup sleep 2000

$ jobs

```

Um sinal especial é o `SIGKILL`, porque ele não pode ser capturado pelo processo e sempre o terminará imediatamente. No entanto, isso pode ter efeitos colaterais indesejados, como originar processos órfãos.

Você pode aprender mais sobre esses e outros sinais [aqui](https://en.wikipedia.org/wiki/Signal_(IPC)), executando o comando [`man signal`](https://www.man7.org/linux/man-pages/man7/signal.7.html), ou `kill -t`.

# Multiplexadores de terminais

Ao usar a interface de linha de comando você frequentemente vai querer executar mais de uma coisa ao mesmo tempo.
Por exemplo, você pode querer executar um editor e um programa lado a lado.
Apesar de isso poder ser feito ao abrir novas janelas de terminal, utilizar um multiplexador de terminais é uma solução mais versátil.

Multiplexadores de terminais como o [`tmux`](https://www.man7.org/linux/man-pages/man1/tmux.1.html) lhe ermitem mutiplexar janelas de terminais utilizando painéis e abas de maneira que você possa interagir com diferentes _shells_ em diferentes sessões.
Ademais, os multiplexadores de terminais permitem que você desmonte uma sessão atual e a monte novamente em algum momento futuro.
Isso pode fazer o seu fluxo de trabalho muito melhor ao trabalhar com máquinas remotas, pelo fato de evitar a necessidade de utilizar o comando `nohup` e outros truques semelhantes.

O multiplexador de terminais mais popular hoje em dia é o [`tmux`](https://www.man7.org/linux/man-pages/man1/tmux.1.html). O `tmux` é altamente configurável e ao utilizar os seus atalhos do teclado você pode criar múltiplas abas e painéis e navegar rapidamente entre eles.

O `tmux` espera que você conheça os seus atalhos do teclado. Todos eles tem o formato `<C-b> x`, em que isso significa (1) pressionar `Ctrl+b`, (2) soltar `Ctrl+b`, e então (3) pressionar `x`. O `tmux` tem a seguinte hierarquia de objetos:
- **Sessões$** - uma sessão é um espaço de trabalho independente com uma ou mais janelas
    + O comando `tmux` inicia uma nova sessão
    + `tmux new -s NOME` a inicia com o nome especificado
    + `tmux ls` lista as sessões abertas
    + Dentro do `tmux`, pressionar `<C-b> d` desmonta a sessão atual
    + `tmux a` monta a última sessão. Você pode utilizar o parâmetro `-t` para especificar qual

- **Janelas** - Equivalente a abas em editores ou navegadores, elas são partes visualmente separadas dentro de uma mesma sessão
    + `<C-b> c` Cria uma nova janela. Para fechá-la basta finalizar o _shell_ pressionando `<C-d>`
    + `<C-b> N` Vai para a _N_-ésima janela. Note que elas são numeradas
    + `<C-b> p` Vai para a janela anterior
    + `<C-b> n` Vai para a próxima janela
    + `<C-b> ,` Renomeia a janela atual
    + `<C-b> w` Lista as janelas existentes

- **Painéis** - Assim como _splits_ no vim, painéis permitem que você tenha múltiplos _shells_ dividindo o espaço visual do seu terminal.
    + `<C-b> "` Divide o painel atual na direção horizontal
    + `<C-b> %` Divide o painel atual na vertical
    + `<C-b> <direção>` Mover para o painel na _direção_ especificada. A direção é indicada pelas teclas de setas
    + `<C-b> z` Ativa o _zoom_ no painel atual
    + `<C-b> [` Inicia o _scrollback_. Você pode pressionar `<espaço>` para iniciar uma seleção e `<enter>` para copiar essa seleção.
    + `<C-b> <space>` Passar por diferentes disposições dos painéis

Para referência, 
[aqui](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) está um tutorial rápido do `tmux` e [aqui](http://linuxcommand.org/lc3_adv_termmux.php) você pode ver uma explicação mais detalhada que cobre o comando original `screen`. Talvez você também queira se conhecer [`screen`](https://www.man7.org/linux/man-pages/man1/screen.1.html), já que ele vem instalado na maioria dos sistemas UNIX.

# Apelidos

Escrever longos comandos que envolvem muitos parâmetros e opções verbosas pode ser cansativo.
Por essa razão, muitos _shells_ permitem a criação de _apelidos_.
Um apelido do shell é uma representação mais curta para outro comando que o _shell_ irá substituir automaticamente para você.
Por exemplo, um apelido no _bash_ tem a seguinte estrutura:

```bash
alias nome_do_apelido="comando_abreviado arg1 arg2"
```

Observe que não há espaços em volta do sinal de igual `=`, porque [`alias`](https://www.man7.org/linux/man-pages/man1/alias.1p.html) é um comando que recebe um único argumento.

Apelidos tem várias funcionalidades convenientes:

```bash
# Fazer atalhos para parâmetros comuns
alias ll="ls -lh"

# Digitar menos em comandos comuns
alias gs="git status"
alias gc="git commit"
alias v="vim"

# Driblar problemas causados por erros de digitação
alias sl=ls

# Sobrescrever comandos existents para um melhor comportamento padrão
alias mv="mv -i"           # -i prompts before overwrite
alias mkdir="mkdir -p"     # -p make parent dirs as needed
alias df="df -h"           # -h prints human readable format

# Apelidos podem ser compostos
alias la="ls -A"
alias lla="la -l"

# Para ignorar um apelido execute o comando prefixado por um \
\ls
# Ou desative o apelido com o comando `unalias`
unalias la

# Para obter a definição de um apelido chame-o com o comando `alias`
alias ll
# Imprimirá ll='ls -lh'
```

Note também que apelidos não persistem por sessões do _shell_ por padrão.
Para fazer que um apelido seja persistente, você precisa incluí-lo em arquivos de inicialização do _shell_, como `.bashrc` ou `.zshrc`, que vão ser introduzidos na próxima seção.


# Dotfiles

Muitos programas são configurados utilizando arquivos de texto simples conhecidos como _dotfiles_. 
(porque os nomes desses arquivos começam com um `.`, como por exemplo `~/.vimrc`, ficando ocultos na listagem de diretórios do `ls` por padrão.)

_Shells_ são exemplos de programas configurados com esses arquivos. Na sua inicialização, o seu _shell_ irá ler vários arquivos para carregar a sua configuração.
Dependendo do _shell_, seja iniciando um login e/ou uma sessão interativa o processo de inicialização pode ser razoavelmente complexo.
Depending on the shell, whether you are starting a login and/or interactive the entire process can be quite complex.
[Aqui](https://blog.flowblok.id.au/2013-02/shell-startup-scripts.html) está uma excelente referência nesse tópico.

No caso do `bash`, editar o seu `.bashrc` ou `.bash_profile` funcionará na maioria dos sistemas.
Aqui você pode incluir comandos que você deseja que rodem na inicialização, como os apelidos descritos na seção anterior ou modificações à variável de ambiente `PATH`.
Na verdade, vários programas pedirão que você inclua uma linha como `export PATH="$PATH:/caminho/para/programa/bin"` na configuração do seu _shell_ para que os seus arquivos binários possam ser encontrados.

Some other examples of tools that can be configured through dotfiles are:
Outros exemplos de ferramentas que podem ser configuradas por meio de _dotfiles_ são:

- `bash` - `~/.bashrc`, `~/.bash_profile`
- `git` - `~/.gitconfig`
- `vim` - `~/.vimrc` e a pasta `~/.vim`
- `ssh` - `~/.ssh/config`
- `tmux` - `~/.tmux.conf`

Como _dotfiles_ devem ser organizados? Eles devem estar na sua própria pasta, sob um sistema de controle de versionamento e com **links simbólicos** criados adequadamente utilizando um script. Isso tem os seguintes benefícios:

- **Instalação fácil**: se você fizer login em uma nova máquina, aplicar as suas customizações levará pouco tempo.
- **Portabilidade**: as suas ferramentas funcionam da mesma maneira em qualquer lugar.
- **Sincronização**: você pode atualizar os seus _dotfiles_ em qualquer lugar e mantê-los sincronizados.
- **Rastreamento de mudanças**: provavelmente você vai manter os seus _dotfiles_ por toda a sua carreira, e é bom ter histórico de versões por projetos de longo prazo.

O que deve ser colocado nos seus _dotfiles_?
Você pode aprender mais sobre as configurações de uma ferramenta específica ao ler a sua documentação online ou as [páginas do man](https://en.wikipedia.org/wiki/Man_page). Outra ótima alternativa é pesquisar na internet por posts em blogs sobre programas específicos, em que autores expõe as suas customizações preferidas. Outra maneira para aprender sobre essas customizações é olhar os _dotfiles_ de outras pessoas: você pode encontrar diversos [repositórios de _dotfiles_](https://github.com/search?o=desc&q=dotfiles&s=stars&type=Repositories) no GitHub --- veja o mais popular [aqui](https://github.com/mathiasbynens/dotfiles) (no entanto, recomenda-se que você não copie configurações cegamente. Tente adaptar às suas preferências).
[Aqui](https://dotfiles.github.io/) você pode encontrar outra boa referência nesse tópico.

Todos os instrutores das aulas tem os seus _dotfiles_ disponibilizados no GitHub: [Anish](https://github.com/anishathalye/dotfiles),
[Jon](https://github.com/jonhoo/configs),
[Jose](https://github.com/jjgo/dotfiles).

## Portability

A common pain with dotfiles is that the configurations might not work when working with several machines, e.g. if they have different operating systems or shells. Sometimes you also want some configuration to be applied only in a given machine.

There are some tricks for making this easier.
If the configuration file supports it, use the equivalent of if-statements to
apply machine specific customizations. For example, your shell could have something
like:

```bash
if [[ "$(uname)" == "Linux" ]]; then {do_something}; fi

# Check before using shell-specific features
if [[ "$SHELL" == "zsh" ]]; then {do_something}; fi

# You can also make it machine-specific
if [[ "$(hostname)" == "myServer" ]]; then {do_something}; fi
```

If the configuration file supports it, make use of includes. For example,
a `~/.gitconfig` can have a setting:

```
[include]
    path = ~/.gitconfig_local
```

And then on each machine, `~/.gitconfig_local` can contain machine-specific
settings. You could even track these in a separate repository for
machine-specific settings.

This idea is also useful if you want different programs to share some configurations. For instance, if you want both `bash` and `zsh` to share the same set of aliases you can write them under `.aliases` and have the following block in both:

```bash
# Test if ~/.aliases exists and source it
if [ -f ~/.aliases ]; then
    source ~/.aliases
fi
```

# Remote Machines

It has become more and more common for programmers to use remote servers in their everyday work. If you need to use remote servers in order to deploy backend software or you need a server with higher computational capabilities, you will end up using a Secure Shell (SSH). As with most tools covered, SSH is highly configurable so it is worth learning about it.

To `ssh` into a server you execute a command as follows

```bash
ssh foo@bar.mit.edu
```

Here we are trying to ssh as user `foo` in server `bar.mit.edu`.
The server can be specified with a URL (like `bar.mit.edu`) or an IP (something like `foobar@192.168.1.42`). Later we will see that if we modify ssh config file you can access just using something like `ssh bar`.

## Executing commands

An often overlooked feature of `ssh` is the ability to run commands directly.
`ssh foobar@server ls` will execute `ls` in the home folder of foobar.
It works with pipes, so `ssh foobar@server ls | grep PATTERN` will grep locally the remote output of `ls` and `ls | ssh foobar@server grep PATTERN` will grep remotely the local output of `ls`.


## SSH Keys

Key-based authentication exploits public-key cryptography to prove to the server that the client owns the secret private key without revealing the key. This way you do not need to reenter your password every time. Nevertheless, the private key (often `~/.ssh/id_rsa` and more recently `~/.ssh/id_ed25519`) is effectively your password, so treat it like so.

### Key generation

To generate a pair you can run [`ssh-keygen`](https://www.man7.org/linux/man-pages/man1/ssh-keygen.1.html).
```bash
ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519
```
You should choose a passphrase, to avoid someone who gets hold of your private key to access authorized servers. Use [`ssh-agent`](https://www.man7.org/linux/man-pages/man1/ssh-agent.1.html) or [`gpg-agent`](https://linux.die.net/man/1/gpg-agent) so you do not have to type your passphrase every time.

If you have ever configured pushing to GitHub using SSH keys, then you have probably done the steps outlined [here](https://help.github.com/articles/connecting-to-github-with-ssh/) and have a valid key pair already. To check if you have a passphrase and validate it you can run `ssh-keygen -y -f /path/to/key`.

### Key based authentication

`ssh` will look into `.ssh/authorized_keys` to determine which clients it should let in. To copy a public key over you can use:

```bash
cat .ssh/id_ed25519.pub | ssh foobar@remote 'cat >> ~/.ssh/authorized_keys'
```

A simpler solution can be achieved with `ssh-copy-id` where available:

```bash
ssh-copy-id -i .ssh/id_ed25519.pub foobar@remote
```

## Copying files over SSH

There are many ways to copy files over ssh:

- `ssh+tee`, the simplest is to use `ssh` command execution and STDIN input by doing `cat localfile | ssh remote_server tee serverfile`. Recall that [`tee`](https://www.man7.org/linux/man-pages/man1/tee.1.html) writes the output from STDIN into a file.
- [`scp`](https://www.man7.org/linux/man-pages/man1/scp.1.html) when copying large amounts of files/directories, the secure copy `scp` command is more convenient since it can easily recurse over paths. The syntax is `scp path/to/local_file remote_host:path/to/remote_file`
- [`rsync`](https://www.man7.org/linux/man-pages/man1/rsync.1.html) improves upon `scp` by detecting identical files in local and remote, and preventing copying them again. It also provides more fine grained control over symlinks, permissions and has extra features like the `--partial` flag that can resume from a previously interrupted copy. `rsync` has a similar syntax to `scp`.

## Port Forwarding

In many scenarios you will run into software that listens to specific ports in the machine. When this happens in your local machine you can type `localhost:PORT` or `127.0.0.1:PORT`, but what do you do with a remote server that does not have its ports directly available through the network/internet?.

This is called _port forwarding_ and it
comes in two flavors: Local Port Forwarding and Remote Port Forwarding (see the pictures for more details, credit of the pictures from [this StackOverflow post](https://unix.stackexchange.com/questions/115897/whats-ssh-port-forwarding-and-whats-the-difference-between-ssh-local-and-remot)).

**Local Port Forwarding**
![Local Port Forwarding](https://i.stack.imgur.com/a28N8.png  "Local Port Forwarding")

**Remote Port Forwarding**
![Remote Port Forwarding](https://i.stack.imgur.com/4iK3b.png  "Remote Port Forwarding")

The most common scenario is local port forwarding, where a service in the remote machine listens in a port and you want to link a port in your local machine to forward to the remote port. For example, if we execute  `jupyter notebook` in the remote server that listens to the port `8888`. Thus, to forward that to the local port `9999`, we would do `ssh -L 9999:localhost:8888 foobar@remote_server` and then navigate to `locahost:9999` in our local machine.


## SSH Configuration

We have covered many many arguments that we can pass. A tempting alternative is to create shell aliases that look like
```bash
alias my_server="ssh -i ~/.id_ed25519 --port 2222 -L 9999:localhost:8888 foobar@remote_server
```

However, there is a better alternative using `~/.ssh/config`.

```bash
Host vm
    User foobar
    HostName 172.16.174.141
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    LocalForward 9999 localhost:8888

# Configs can also take wildcards
Host *.mit.edu
    User foobaz
```

An additional advantage of using the `~/.ssh/config` file over aliases  is that other programs like `scp`, `rsync`, `mosh`, &c are able to read it as well and convert the settings into the corresponding flags.


Note that the `~/.ssh/config` file can be considered a dotfile, and in general it is fine for it to be included with the rest of your dotfiles. However, if you make it public, think about the information that you are potentially providing strangers on the internet: addresses of your servers, users, open ports, &c. This may facilitate some types of attacks so be thoughtful about sharing your SSH configuration.

Server side configuration is usually specified in `/etc/ssh/sshd_config`. Here you can make changes like disabling password authentication, changing ssh ports, enabling X11 forwarding, &c. You can specify config settings on a per user basis.

## Miscellaneous

A common pain when connecting to a remote server are disconnections due to shutting down/sleeping your computer or changing a network. Moreover if one has a connection with significant lag using ssh can become quite frustrating. [Mosh](https://mosh.org/), the mobile shell, improves upon ssh, allowing roaming connections, intermittent connectivity and providing intelligent local echo.

Sometimes it is convenient to mount a remote folder. [sshfs](https://github.com/libfuse/sshfs) can mount a folder on a remote server
locally, and then you can use a local editor.


# Shells & Frameworks

During shell tool and scripting we covered the `bash` shell because it is by far the most ubiquitous shell and most systems have it as the default option. Nevertheless, it is not the only option.

For example, the `zsh` shell is a superset of `bash` and provides many convenient features out of the box such as:

- Smarter globbing, `**`
- Inline globbing/wildcard expansion
- Spelling correction
- Better tab completion/selection
- Path expansion (`cd /u/lo/b` will expand as `/usr/local/bin`)

**Frameworks** can improve your shell as well. Some popular general frameworks are [prezto](https://github.com/sorin-ionescu/prezto) or [oh-my-zsh](https://ohmyz.sh/), and smaller ones that focus on specific features such as [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) or [zsh-history-substring-search](https://github.com/zsh-users/zsh-history-substring-search). Shells like [fish](https://fishshell.com/) include many of these user-friendly features by default. Some of these features include:

- Right prompt
- Command syntax highlighting
- History substring search
- manpage based flag completions
- Smarter autocompletion
- Prompt themes

One thing to note when using these frameworks is that they may slow down your shell, especially if the code they run is not properly optimized or it is too much code. You can always profile it and disable the features that you do not use often or value over speed.

# Terminal Emulators

Along with customizing your shell, it is worth spending some time figuring out your choice of **terminal emulator** and its settings. There are many many terminal emulators out there (here is a [comparison](https://anarc.at/blog/2018-04-12-terminal-emulators-1/)).

Since you might be spending hundreds to thousands of hours in your terminal it pays off to look into its settings. Some of the aspects that you may want to modify in your terminal include:

- Font choice
- Color Scheme
- Keyboard shortcuts
- Tab/Pane support
- Scrollback configuration
- Performance (some newer terminals like [Alacritty](https://github.com/jwilm/alacritty) or [kitty](https://sw.kovidgoyal.net/kitty/) offer GPU acceleration).

# Exercises

## Job control

1. From what we have seen, we can use some `ps aux | grep` commands to get our jobs' pids and then kill them, but there are better ways to do it. Start a `sleep 10000` job in a terminal, background it with `Ctrl-Z` and continue its execution with `bg`. Now use [`pgrep`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) to find its pid and [`pkill`](http://man7.org/linux/man-pages/man1/pgrep.1.html) to kill it without ever typing the pid itself. (Hint: use the `-af` flags).

1. Say you don't want to start a process until another completes, how you would go about it? In this exercise our limiting process will always be `sleep 60 &`.
One way to achieve this is to use the [`wait`](https://www.man7.org/linux/man-pages/man1/wait.1p.html) command. Try launching the sleep command and having an `ls` wait until the background process finishes.

    However, this strategy will fail if we start in a different bash session, since `wait` only works for child processes. One feature we did not discuss in the notes is that the `kill` command's exit status will be zero on success and nonzero otherwise. `kill -0` does not send a signal but will give a nonzero exit status if the process does not exist.
    Write a bash function called `pidwait` that takes a pid and waits until the given process completes. You should use `sleep` to avoid wasting CPU unnecessarily.

## Terminal multiplexer

1. Follow this `tmux` [tutorial](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) and then learn how to do some basic customizations following [these steps](https://www.hamvocke.com/blog/a-guide-to-customizing-your-tmux-conf/).

## Aliases

1. Create an alias `dc` that resolves to `cd` for when you type it wrongly.

1.  Run `history | awk '{$1="";print substr($0,2)}' | sort | uniq -c | sort -n | tail -n 10`  to get your top 10 most used commands and consider writing shorter aliases for them. Note: this works for Bash; if you're using ZSH, use `history 1` instead of just `history`.


## Dotfiles

Let's get you up to speed with dotfiles.
1. Create a folder for your dotfiles and set up version
   control.
1. Add a configuration for at least one program, e.g. your shell, with some
   customization (to start off, it can be something as simple as customizing your shell prompt by setting `$PS1`).
1. Set up a method to install your dotfiles quickly (and without manual effort) on a new machine. This can be as simple as a shell script that calls `ln -s` for each file, or you could use a [specialized
   utility](https://dotfiles.github.io/utilities/).
1. Test your installation script on a fresh virtual machine.
1. Migrate all of your current tool configurations to your dotfiles repository.
1. Publish your dotfiles on GitHub.

## Remote Machines

Install a Linux virtual machine (or use an already existing one) for this exercise. If you are not familiar with virtual machines check out [this](https://hibbard.eu/install-ubuntu-virtual-box/) tutorial for installing one.

1. Go to `~/.ssh/` and check if you have a pair of SSH keys there. If not, generate them with `ssh-keygen -o -a 100 -t ed25519`. It is recommended that you use a password and use `ssh-agent` , more info [here](https://www.ssh.com/ssh/agent).
1. Edit `.ssh/config` to have an entry as follows

```bash
Host vm
    User username_goes_here
    HostName ip_goes_here
    IdentityFile ~/.ssh/id_ed25519
    LocalForward 9999 localhost:8888
```
1. Use `ssh-copy-id vm` to copy your ssh key to the server.
1. Start a webserver in your VM by executing `python -m http.server 8888`. Access the VM webserver by navigating to `http://localhost:9999` in your machine.
1. Edit your SSH server config by doing  `sudo vim /etc/ssh/sshd_config` and disable password authentication by editing the value of `PasswordAuthentication`. Disable root login by editing the value of `PermitRootLogin`. Restart the `ssh` service with `sudo service sshd restart`. Try sshing in again.
1. (Challenge) Install [`mosh`](https://mosh.org/) in the VM and establish a connection. Then disconnect the network adapter of the server/VM. Can mosh properly recover from it?
1. (Challenge) Look into what the `-N` and `-f` flags do in `ssh` and figure out what a command to achieve background port forwarding.
