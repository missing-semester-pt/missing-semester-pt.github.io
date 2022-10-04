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

Também vamos aprender diferentes maneiras para melhorar o seu _shell_ e outras ferramentas, ao definir apelidos e configurá-las utilizando _dotfiles_. Ambas ambordagens podem lhe ajudar a poupar tempo, ao por exemplo utilizar a mesma configuração em todas as suas máquinas sem ter que digitar longos comandos. Tambpém vamos ver como trabalhar em máquinas remotas utilizando SSH.


# Controle de processos

Em alguns casos você precisará interromper um processo enquanto ele está executando, por exemplo: quando um comando está demorando muito para finalizar a sua execução (como um `find` que precisará percorrer uma estrutura de diretórios muito grande).
Na maioria dos casos, você pode pressionar `Ctrl-C` e o comando será interrompido.
Mas como isso funciona de fato e por que às vezes isso não é suficiente para parar o processo?

## Matando um processo

O seu _shell_ está utilzando um mecanismo de comunicação do UNIX chamado _sinal_ para passar informações ao processo. Quando um processo recebe um sinal, ele para a sua execução, interpreta o sinal e potencialmente modifica o seu fluxo de execução baseado na informação passada pelo sinal. Por essa razão, sinais são _interruptores de software_.

Nesse caso, quando `Ctrl-C` é pressionado, isso indica ao _shell_ para passar um sinal `SIGINT` para o processo.

Aqui está um exemplo mínimo de um programa Python que capture um sinal `SIGINT` e o ignora, não mais interrompendo a sua execução ao recebê-lo. Para matar esse programa nós podemos utilizar o sinal `SIGQUIT` ao digitar `Ctrl-\`.

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

Multiplexadores de terminais como o [`tmux`](https://www.man7.org/linux/man-pages/man1/tmux.1.html) lhe permitem mutiplexar janelas de terminais utilizando painéis e abas de maneira que você possa interagir com diferentes _shells_ em diferentes sessões.
Ademais, os multiplexadores de terminais permitem que você desmonte uma sessão atual e a monte novamente em algum momento futuro.
Isso pode melhorar muito o seu fluxo de trabalho ao trabalhar com máquinas remotas, pelo fato de evitar a necessidade de utilizar o comando `nohup` e outros truques semelhantes.

O multiplexador de terminais mais popular hoje em dia é o [`tmux`](https://www.man7.org/linux/man-pages/man1/tmux.1.html). O `tmux` é altamente configurável e ao utilizar os seus atalhos do teclado você pode criar múltiplas abas e painéis e navegar rapidamente entre eles.

O `tmux` espera que você conheça os seus atalhos do teclado. Todos eles tem o formato `<C-b> x`, que isso significa (1) pressionar `Ctrl+b`, (2) soltar `Ctrl+b`, e então (3) pressionar `x`. O `tmux` tem a seguinte hierarquia de objetos:
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
[aqui](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) está um tutorial rápido do `tmux` e [aqui](http://linuxcommand.org/lc3_adv_termmux.php) você pode ver uma explicação mais detalhada que cobre o comando original `screen`. Talvez você também queira se familiarizar ao [`screen`](https://www.man7.org/linux/man-pages/man1/screen.1.html), já que ele vem instalado na maioria dos sistemas UNIX.

# Apelidos

Escrever longos comandos que envolvem muitos parâmetros e opções prolixas pode ser cansativo.
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
alias mv="mv -i"           # -i perguntar antes de sobreescrever
alias mkdir="mkdir -p"     # -p cria diretórios pais conforme necessário
alias df="df -h"           # -h imprime em formáto legível para humanos

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

## Portabilidade

Um problema comum com _dotfiles_ é que as suas configurações podemo não funcionar quando se trabalha em diferentes máquinas, como por exemplo se essas máquinas tiverem diferentes sistemas operacionais ou _shells_. Às vezes você também pode querer que determinada configuração seja aplicada somente a uma determinada máquina.

Existem alguns truques para que isso seja mais fácil de se realizar.
Se o arquivo de configuração dá suporte a isso, use o equivalentea expressões if-else para aplicar customizações para máquinas especificas. Por exemplo, o seu _shell_ pode ter algo como o seguinte: 

```bash
if [[ "$(uname)" == "Linux" ]]; then {faca_algo}; fi

# Check before using shell-specific features
if [[ "$SHELL" == "zsh" ]]; then {faca_algo}; fi

# You can also make it machine-specific
if [[ "$(hostname)" == "meuServidor" ]]; then {faca_algo}; fi
```

Se o arquivo de configuração der suporte, você pode utilizar _includes_. Por exemplo, um arquivo `~/.gitconfig` pode ter a seguinte configuração:

```
[include]
    path = ~/.gitconfig_local
```

Então em cada máquina, o arquivo `~/.gitconfig_local` pode conter configurações especificas de cada máquina. Você pode até gerenciar esses arquivos em um repositório separado para configurações específicas.

Essa ideia também é útil caso você queira que diferentes programas compartilhem algumas configurações. Por exemplo, se você quiser que ambos os _shells_ `zsh` e `bash` compartilhem o mesmo conjunto de apelidos você pode escrevê-los em um arquivo `.aliases` e ter o seguinte bloco em ambos:

```bash
# Testa se ~/.aliases existe e o carrega
if [ -f ~/.aliases ]; then
    source ~/.aliases
fi
```

# Máquinas Remotas

É cada vez mais comum para programadores utilizar servidores remoto no dia-a-dia. Se você precisa utilzar servidores remotos para realizar a implantação de um _software backend_ ou se você de um servidor com um maior poder computacional, você provavelmente utilizará _Secure Shell_ (SSH). Assim como a maioria das ferramentas aqui demonstradas, SSH é altamente configurável, então vale a pena aprender mais sobre isso.

Para entrar com `ssh` em um servidor você precisa executar o seguinte comando

```bash
ssh foo@bar.mit.edu
```

Aqui estamos tentando entrar com SSH utilizando o usuário `foo` no servidor `bar.mit.edu`.
O servidor pode ser especificado com uma URL (como `bar.mit.edu`) ou um IP (algo como `foobar@192.168.1.42`). Depois veremos como modificar o arquivo de configuração do SSH para acessar um servidor apenas digitando algo como `ssh bar`.

## Executando comandos

Uma funcionalidade do `ssh` frequentemente deixada de lado é a possibilidade de se executar comandos diretamente.
`ssh foobar@server ls` executar `ls` na pasta raíz de foobar.
Isso funciona com _pipes_, então `ssh foobar@server ls | grep PATTERN` irá executar o comando `grep` localmente sobre a saída remota de `ls` e `ls | ssh foobar@server grep PATTERN` rodará `grep` remotamente para a saída local de `ls`.

## Chaves SSH

Autenticação baseada em chaves utiliza criptografia de chaves privadas para provar ao servidor que o cliente possui a chave privada secreta sem revelar a chave.
Dessa maneira, você não precisa reintroduzir a sua senha sempre. De qualquer jeito, a chave privada (frequentemente `~/.ssh/id_rsa` e mais recentemente `~/.ssh/id_ed25519`) é efetivamente a sua senha, então trate-a como tal.

### Geração de chaves

Para gerar um pair de chaves você pode executar [`ssh-keygen`](https://www.man7.org/linux/man-pages/man1/ssh-keygen.1.html).
```bash
ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519
```

Você precisa escolher uma senha, para evitar que alguém que obtenha a sua chave privada consiga acessar servidores autorizados. Utilize [`ssh-agent`](https://www.man7.org/linux/man-pages/man1/ssh-agent.1.html) ou [`gpg-agent`](https://linux.die.net/man/1/gpg-agent) para que você não precise digitar a sua senha sempre.

Se alguma vez você configurou a atualização de repositórios do GitHub utilizando chaves SSH, então você provavelmente seguiu os passos listados aqui [aqui](https://help.github.com/articles/connecting-to-github-with-ssh/) e já tem um par de chaves. Para checar se voc6e tem uma senha e validá-la você pode executar `ssh-keygen -y -f /caminho/para/chave`.

### Autenticação baseada em chaves

`ssh` checará `.ssh/authorized_keys` para determinar a que clientes deverá permitir o acesso. Para copiar para esse caminho uma chave pública você pode utilizar:
`ssh` will look into `.ssh/authorized_keys` to determine which clients it should let in. To copy a public key over you can use:

```bash
cat .ssh/id_ed25519.pub | ssh foobar@remote 'cat >> ~/.ssh/authorized_keys'
```

Uma solução mais simples pode ser alcançada com `ssh-copy-id`, quando disponível:

```bash
ssh-copy-id -i .ssh/id_ed25519.pub foobar@remote
```

## Copiando arquivos por SSH

Existem diversas maneiras para copiar arquivos por SSH:

- `ssh+tee`, o mais simples é utilizar o comando `ssh` e a entrada STDIN ao executar `cat arquivolocal | ssh servidor_remoto tee arquivoremoto`. Lembre-se que o comando [`tee`](https://www.man7.org/linux/man-pages/man1/tee.1.html) escreve a saída de STDIN em um arquivo.
- [`scp`](https://www.man7.org/linux/man-pages/man1/scp.1.html), ao copiar grandes conjuntos de arquivos/diretórios o comando de cópia segura `scp` é mais conveniente já que pode passar por diretórios recursivamente. A sintaxe é `scp caminho/para/arquivo_local servidor_remoto:camiho/para/arquivo_remoto`.
- [`rsync`](https://www.man7.org/linux/man-pages/man1/rsync.1.html) melhora a funcionalidade de `scp` ao detectar arquivos idênticos tanto local quanto remotamente, e prevenindo que eles sejam copiados novamente. Ele também possui um controle mais direcionado sobre links simbólicos, permissões e tem funcionalidades extra como a _flag_ `--partial`, que pode resumir uma cópia interrompida previamente. `rsync` tem uma sintaxe parecida com `scp`.

## Redirecionamento de portas

Em muitos cenários estarão presentes _softwares_ que funcionam em uma determinada porta da máquina. Quando isso acontece na sua máquina local você pode digitar `localhost:PORTA` ou `127.0.0.1:PORTA`, mas o que você faz quando um servidor remoto não tem suas portas diretamente disponíveis pela rede/internet?

Isso é chamado de _redirecionamento de portas_ e isso pode ser realizado de duas maneiras: Redirecionamento Local de Portas e Redirecionamento Remoto de Portas (veja as figuras em mais detalhes, créditos para as figuras [desse post do StackOverflow](https://unix.stackexchange.com/questions/115897/whats-ssh-port-forwarding-and-whats-the-difference-between-ssh-local-and-remot)).

**Redirecionamento Local de Portas**
![Redirecionamento Local de Portas](https://i.stack.imgur.com/a28N8.png  "Redirecionamento Local de Portas") 
**Redirecionamento Remoto de Portas**
![Redirecionamento Remoto de Portas](https://i.stack.imgur.com/4iK3b.png  "Redirecionamento Remoto de Portas")

O cenário mais comum é o redirecionamento local de portas, em que um serviço na máquina remota executa em uma porta e você quer vincular uma porta na sua máquina local para redirecionar para a porta remota. Por exemplo, considere o caso em que executamos `jupyter notebook` no servidor remoto na porta `8888`. Então, para redirecionar para a porta local `9999`, poderíamos executar `ssh -L 9999:localhost:8888 foobar@servidor_remoto` e então navegar para `localhost:9999` na nossa máquina local.


## Configuração SSH

Aqui cobrimos muitos argumentos que pode sem passados para o `ssh`. Uma alternatvia tentadora é criar apelidos do _shell_, algo parecido com:
```bash
alias meu_servidor="ssh -i ~/.id_ed25519 --port 2222 -L 9999:localhost:8888 foobar@servidor_remoto
```

No entanto, existe uma alternativa melhor utilizando `~/.ssh/config`.

```bash
Host vm
    User foobar
    HostName 172.16.174.141
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    LocalForward 9999 localhost:8888

# Configurações podem também possuir "wildcards"
Host *.mit.edu
    User foobaz
```

Uma vantagem adicional de utilizar o arquivo `~/.ssh/config` ao invés de apelidos é que outros programas como `scp`, `rsync`, `mosh`, etc podem lê-lo assim como converter as configurações nas _flags_ correspondentes.


Note que o arquivo `~/.ssh/config` pode ser considerado um _dotfile_, e em geral não há problema de incluí-lo junto com o restante dos seus _dotfiles_. No entanto, se você deixá-lo disponível publicamente, pense sobre a informação que você está potencialmente disponibilizando a estranhos na internet: endereços dos seus servidores, usuários, portas abertas, etc. Isso pode facilitar tipos de ataques, então pense bem antes de compartilhar a sua configuração do SSH.

Configuracões do lado do servidor são geralmente especificadas em `/etc/ssh/sshd_config`. Aqui você pode fazer mudanças como desativar autenticação por senha, modificar portas ssh, ativar redirecionamento X11, etc. Você também pode especificar configurações para cada usuário.

## Diversos

Um problema comum em conexões a servidores remotos são desconexões em função do desligamento/modo de descanso do seu computador ou modificação da rede. Além disso, acesso ssh em uma conexão com latência significante pode ser bem frustrante. [Mosh](https://mosh.org/), o _shell_ móvel, trás melhoras sobre o ssh, ao permitir conexões em _roaming_, conectividade intermitente e disponibilizando eco local inteligente. 

Às vezes é conveniente montar um diretório remoto. [sshfs](https://github.com/libfuse/sshfs) pode montar localmente diretórios em um servidor remoto, fazendo com que você possa utilizá-lo com um editor local.
locally, and then you can use a local editor.


# Shells e Frameworks

Ao falar sobre ferramentas de _shell_ e de scripts tratamos o _shell_ `bash` porque esse é de longe o _shell_ mais onipresente e a maioria dos sistemas o tem como a opção padrão. No entanto, essa não é a única opção.

Por exemplo, o `zsh` é uma extensão do `bash` e assim fornece por padrão várias funcionalidades úteis como:

- _Globbing_ mais inteligente, `**`
- Expansão _inline_ de padrões com _globbing_ ou _wildcards_
- Correção ortográfica
- Melhor completamento/seleção com a tecla tab
- Expansão de caminhos (`cd /u/lo/b` é expandido como `/usr/local/bin`)


**Frameworks** podem melhorar o seu _shell_ também. Alguns _frameworks_ gerais são o [prezto](https://github.com/sorin-ionescu/prezto) e o [oh-my-zsh](https://ohmyz.sh/), e alguns menores, focados em funcionalidades específicas como o [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) e o [zsh-history-substring-search](https://github.com/zsh-users/zsh-history-substring-search).
_Shells_ como o [fish](https://fishshell.com/) incluem várias dessas funcionalidades amigáveis por padrão. Algumas delas são:

- Prompts à direita
- Destaque de sintaxe para comandos
- Busca de _substrings_ no histórico
- Completamento de _flags_ baseado nas manpages.
- Completamento automático mais inteligente
- Temas

Algo a se notar quando se utiliza _frameworks_ como esse é que eles podem deixar o seu _shell_ mais lento, especialmente se o código que eles rodam não é otimizado propriamente ou se é muito código em si. Você pode analisar isso e desabilitar funcionalidades que você não utiliza muito ou que estejam deixando o seu _shell_ mais lento.

# Emuladores de terminais

Além de customizar o seu _shell_, também pode ser útil avaliar o uso de diferentes **emuladores de terminais** e as suas diferentes configurações. Existem diferentes emuladores de terminais disponíveis para download e uso (aqui pode ser vista uma [comparação](https://anarc.at/blog/2018-04-12-terminal-emulators-1/), em inglês).

Já que você pode passar centenas ou até milhares de horas no seu terminal, pode ser que valha a pena explorar as suas configurações. Alguns aspectos que você pode querer modificar nele incluem:

- Escolha da fonte
- Esquema de cores
- Atalhos de teclado
- Uso de janelas/painéis
- Configuração de rolagem para trás
- Desempenho (terminais mais modernos como o [Alacritty](https://github.com/jwilm/alacritty) e [kitty](https://sw.kovidgoyal.net/kitty/) oferecem aceleração por GPU).

# Exercícios

## Controle de processos

1. Pelo que já vimos, podemos utilizar comandos do tipo `ps aux | grep` para obter os pids de processos e então encerrá-los, mas existem melhores maneiras de se fazer isso. Comece rodando o comando `sleep 10000` em um terminal, ponha ele em segundo plano pressionando `Ctrl-Z` e continue a sua execução com `bg`. Agora utilize [`pgrep`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) para achar o seu pid e [`pkill`](http://man7.org/linux/man-pages/man1/pgrep.1.html) para encerrá-lo sem precisar digitar o seu pid. (Dica: utilize as _flags_ `-af`).

1. Vamos supor que você não quer começar um processo até que outro termine. Como você faria isso? Nesse exercício o nosso processo limitador sempre será `sleep 60 &`.
Um jeito de fazer isso é utilizar o comando [`wait`](https://www.man7.org/linux/man-pages/man1/wait.1p.html). Tente rodar o comando `sleep 60 &` e fazer com que um comando `ls` espere o processo em segundo plano finalizar.
    No entanto, essa estratégia falhará se o iniciarmos em uma sessão de um _bash_ diferente, já que `wait` só considera processos filhos. Uma funcionalidade que não discutimos foi que o status de saída do comando `kill` é zero no caso de sucesso e um número diferente de zero caso contrário. `kill -0` não manda um sinal mas dará um status diferente de zero se o processo não existir.
    Escreva uma função _bash_ chamada `pidwait` que recebe um pid e espera até que o processo dado complete. Você deverá utilizar o comando `sleep` para evitar o gasto desnecessário de ciclos de CPU.

## Multiplexador de terminal

1. Siga este [tutorial](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) do `tmux` e aprenda como fazer algumas combinações básicas seguindo [esses passos] (https://www.hamvocke.com/blog/a-guide-to-customizing-your-tmux-conf/).

## Apelidos

1. Crie um apelido chamado `dc` que executa o comando `cd` quando você digitá-lo incorretamente.

1. Execute `history | awk '{$1="";print substr($0,2)}' | sort | uniq -c | sort -n | tail -n 10` para listar os seus 10 comandos mais executados e considere escrever apelidos mais curtos para eles. Nota: isso apenas funciona para o Bash; se você estiver utilizando ZSH, utilize o comando `history 1` ao invés de apenas `history`.


## Dotfiles

Vamos praticar o uso de _dotfiles_.
1. Crie uma pasta para os seus _dotfiles_ e configure seu sistema de controle de versionamento.
1. Adicione uma configuração para pelo menos um programa, como por exemplo o seu _shell_, com algumas customizações (por exemplo, pode ser algo simples como customizar o _prompt do seu_ ao atribuir um valor para `$PS1`).
1. Configure um método para instalar os seus _dotfiles_ rapidamente (e sem esforço manual) em uma nova máquina. Isso pode ser tão simples quando um script _shell_ que chama `ln -s` para cada arquivo, ou você pode utilizar um [utilitário especializado](https://dotfiles.github.io/utilities/).
1. Teste o seu script de instalação em uma máquina virtual recém instalada.
1. Migre todas as suas configurações atuais para o seu repositório de _dotfiles_.
1. Publique os seus _dotfiles_ no GitHub.

## Máquinas Remotas

Instale uma máquina virtual Linux (ou utilize uma existente) para este exercício. Se você não tem familiaridade com máquinas virtuais, veja [esse tutorial](https://hibbard.eu/install-ubuntu-virtual-box/) para aprender como instalar uma.

1. Navegue até a pasta `~/.ssh/` e verifique se existe um par de chaves SSH nela. Se não, gere um com o comando `ssh-keygen -o -a 100 -t ed25519`. É recomendado que você utilize uma senha e utilize `ssh-agent`, (mais informação [aqui](https://www.ssh.com/ssh/agent)).
1. Edite o arquivo `.ssh/config` para adicionar uma entrada como a seguinte:

```bash
Host vm
    User usuario_vai_aqui
    HostName ip_vai_aqui
    IdentityFile ~/.ssh/id_ed25519
    LocalForward 9999 localhost:8888
```
1. Utilize `ssh-copy-id maquina-virtual` para copiar a chave SSH para o servidor.
1. Inicie um servidor na sua máquina virtual executando `python -m http.server 8888`. Acesse esse servidor ao navegar ao endereço `http://localhost:9999` na sua máquina.
1. Edite a configuração do seu servidor SSH executando `sudo vim /etc/ssh/sshd_config` e desabilite a autenticação por senha ao editar o valor de `PasswordAuthentication`. Desabilite login do usuário root ao editar o valor de `PermitRootLogin`. Reinicie o serviço `ssh` executando `sudo service sshd restart`. Tente entrar no servidor com SSH novamente.
1. (Desafio) Instale o [`mosh`](https://mosh.org/) na máquina virtual e abra uma conexão com ele. Depois, desconecte o adaptador de rede do servidor/máquina virtual. O `mosh` consegue se recuperar dessa falha?
1. (Desafio) Procure saber o que as _flags_ `-N` e `-f` fazem no comando `ssh` e descubra qual comando conseguiria realizar o redirecionamento de portas em segundo plano.
