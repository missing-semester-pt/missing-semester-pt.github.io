---
layout: lecture
title: "Visão geral do curso + o shell"
date: 13-01-2019
ready: true
video:
  aspect: 56.25
  id: Z56Jmr9Z34Q
---

# Motivação

Como cientistas da computação, nós sabemos que computadores são ótimos
para ajudar a resolver tarefas repetitivas. Entretanto, frequentemente,
nós esquecemos que isso se aplica tanto ao nosso _uso_ do computador 
quanto aos processos que queremos que nossos programas realizem. Nós
temos uma vasta gama de ferramentas de fácil acesso disponíveis que nos
permitem ser mais produtivos e resolver problemas complexos quando estamos 
trabalhando em algo relacionado à um problema computacional. Porém, 
muitos de nós utilizamos uma pequena fração destas ferramentas; nós apenas
sabemos encantamentos mágicos suficientes para sobreviver e cegamente copiamos 
e colamos comandos da Internet quando ficamos travados em algum problema.

Este curso é uma tentativa de resolver isso.

Queremos te ensinar a aproveitar ao máximo as ferramentas que você conhece, 
te mostrar novas ferramentas e, com sorte, instigar você a explorar (e 
talvez construir) mais ferramentas por conta própria. Isso é o que 
acreditamos ser o semestre que falta na maioria dos cursos de Ciência da 
Computação.

# Estrutura do Curso

Este curso consiste em 11 aulas de 1 hora, cada uma focada em um
[tópico](/2020/) em particular. As aulas são independentes entre si,
porém, a medida que o semestre for passando, vamos presumir que você
está familiarizado com o assunto das aulas mais recentes. Nós temos
disponibilizado notas de aula online, mas terá muito conteúdo que foi
abordado nas aulas que não apareceram nelas (por exemplo, no formulário 
de demos). Contudo, nós iremos gravar as aulas e postá-las online.

Nós estamos tentando cobrir muito conteúdo em apenas 11 aulas de 1 hora,
então as aulas são bastante densas. Para que você tenha tempo para se 
familiarizar com o conteúdo no seu próprio rítmo, cada aula incluirá um
conjunto de exercícios que guiará você até os pontos chaves da aula. Depois
de cada aula, nós disponibilizaremos um horário e local em que estaremos 
presentes para ajudar a esclarecer quaisquer dúvidas que você possa ter.
Se você estiver fazendo este curso online, você pode nos enviar dúvidas 
em inglês para [missing-semester@mit.edu](mailto:missing-semester@mit.edu).

Devido ao tempo limitado, nós não seremos capazes de abordar todas as 
ferramentas no mesmo nível de detalhes do que um curso de longa duração 
pode. Sempre que possível, tentaremos indicar recursos para se aprofundar 
em uma ferramenta ou tópico, mas caso algo te interesse, não hesite em nos
contactar e pedir sugestões de conteúdo!

# Tópico 1: O Shell

## O que é o shell?

Hoje, os computadores possuem uma variedade de interfaces para executar 
comandos; interfaces gráficas estilosas, interfaces de voz e até mesmo
a realidade aumentada e a realidade virtual estão em todos os lugares.
E eles são ótimos para 80% dos casos, mas, frequentemente, restringem
fundamentalmente o que você pode fazer - você não pode pressionar um 
botão que não existe ou dar um comando de voz que não foi programado. 
Para tirar vantagem total das ferramentas que seu computador oferece,
nós teremos que ir à moda antiga e descer para uma interface textual:
O Shell.

Quase todas as plataformas que você usa tem o shell nela de uma forma
ou outra e muitas delas tem muitos shells para você escolher. Enquanto
talvez eles variem em detalhes, no seu núcleo eles são basicamente o
mesmo: eles permitem que você execute seus programas, insira entradas
e inspecione sua estrutura de uma forma semi-estruturada.

Nesta aula, nós iremos focar no Bourne Again SHell, o "bash". Este é
um dos shells mais amplamente usado e  sua sintaxe é similar ao que 
você verá em muitos outros shells. Para abrir um _prompt_ de shell
(local onde você digita os comandos), primeiramente você precisará
de um terminal. Seu aparelho provavelmente veio com um instalado,
ou você poderá instalar um facilmente.

## Usando o shell

Quando você iniciar o terminal, provavelmente você verá um _prompt_
que se parecerá com isso:

```console
missing:~$ 
```

Esta é a interface textual principal para o shell. Te diz que você
está na máquina `missing` e que você está na sua "área de trabalho
atual", ou, onde você realmente está: `~` (abreviação para "home"). 
O `$` te diz que você não é um usuário root (veremos mais sobre 
isso depois). Neste prompt você pode digitar um _comando_, que vai
ser interpretado pelo shell. O comando mais básico para executar um
programa é:

```console
missing:~$ date
Fri 10 Jan 2020 11:49:31 AM EST
missing:~$ 
```

Aqui, nós executamos o programa `date`, o qual (surpreendentemente)
imprime a data e hora atual. O shell então nos pergunta por outro
comando a se executar. Nós também podemos executar um comando com
_argumentos_:

```console
missing:~$ echo hello
hello
```

Neste exemplo, nós dizemos ao shell para executar o programa `echo`
com o argumento `hello`. O `echo` simplesmente imprime na tela o
argumento. O shell analisa o comando dividindo-o pelos espaços 
brancos e então executa o programa indicado pela primeira fornecendo
cada palavra subsequente como um argumento que o programa pode acessar.
Se você quer fornecer um argumento que contem espaços em branco ou
outro caractere especial (por exemplo: uma pasta chamada "Minhas fotos"),
você pode ou escrever o argumento entre aspas `'` (`'Minhas fotos'`),
ou usar aspas duplas `"` (`"Minhas fotos"`), ou digitar `\` antes de 
caracteres relevantes (`My\ Photos`).

Mas como o shell sabe como encontrar os programas `date` ou `echo`? Bom,
o shell é um ambiente de programação, assim como Python ou Ruby, então
ele tem variáveis, condicionais, loops e funções (proxíma aula!). Quando
você executa um comando no shell, você está escrevendo um pequeno pedaço
de código que seu shell interpreta. Se você digita um comando que o shell
não reconhece, ele consulta uma _variavel de ambiente_ chamada $PATH
que lista quais diretórios o shell pode procurar por programas quando é lhe dado algum comando:

```console
missing:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
missing:~$ which echo
/bin/echo
missing:~$ /bin/echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

Quando rodamos o comando _echo_, o shell percebe que deveria executar o programa _echo_, e então procura através
`:`-da lista separada de diretórios na variável de ambiente `$PATH` por um arquivo que tenha este nome. Quando encontra, 
ele roda o arquivo (assumindo que seja _executável_; explicaremos mais sobre isso adiante). Nós podemos descobrir qual arquivo
é executado por um determinado programa utilizando o `which`. Nós também podemos contornar o `$PATH` colocando
_path_ no arquivo que queremos executar.


## Navegando no shell

Um caminho no shell é uma lista delimitada de diretórios; separada por `/` 
no Linux e no macOS e `\` no Windows. No Linux e macOS, o caminho `/` é a raiz do sistema, 
na qual abaixo ficam todos os diretórios e arquivos, enquanto no Windows existe uma raiz para 
cada partição do disco (`C:\`). Nós geralmente iremos assumir que você está usando um sistema Linux
para essa classe. Um caminho que começa com `/` é chamado de caminho _absoluto_. Qualquer outro caminho
é um caminho _relativo_. Caminhos relativos são relativos ao atual diretório, no qual podemos ver com o comando`pwd`
e mudar utilizando o comando `cd`. Em um caminho, `.` se refere ao diretório atual, e `..` ao seu diretório pai:

```console
missing:~$ pwd
/home/missing
missing:~$ cd /home
missing:/home$ pwd
/home
missing:/home$ cd ..
missing:/$ pwd
/
missing:/$ cd ./home
missing:/home$ pwd
/home
missing:/home$ cd missing
missing:~$ pwd
/home/missing
missing:~$ ../../bin/echo hello
hello
```

Note que nosso shell sempre nos mantém informado sobre qual era nosso diretório atual. 
Você pode configurar o terminal para mostrar todo o tipo de informação útil, iremos falar sobre isso mais adiante.

Geralmente, iremos rodar um programa, e ele irá operar no diretório atual a não ser 
que indiquemos outro diretório. Por exemplo, geralmente irá procurar por arquivos
no diretório atual, e criará arquivos ali se for necessário.

Para ver o que existe no atual diretório, usamos o comando `ls`:

```console
missing:~$ ls
missing:~$ cd ..
missing:/home$ ls
missing
missing:/home$ cd ..
missing:/$ ls
bin
boot
dev
etc
home
...
```

A não ser que um diretório seja passado como primeiro argumento, `ls` irá imrpimir na tela
os conteúdos do atual diretório. A maioria dos comandos aceitam flags e opções (flags com valores) que comecem com `-` para modificar 
o seu comportamento. Geralmente rodar um programa com `-h` ou `--help` irá trazer algum texto destinado a ajudar o usuário e explicar 
quais flags e opções estão disponíveis. Por exemplo, `ls --help` nos diz:

```
  -l                         use a long listing format
```

```console
missing:~$ ls -l /home
drwxr-xr-x 1 missing  users  4096 Jun 15  2019 missing
```

Isso nos traz bastante informações sobre cada arquivo ou diretório presente. 
Primeiro, o `d` no início da linha nos diz que `missing` é um diretório. Então os 
três seguintes grupos de caracteres (`rwx`). Esses indicam quais as permissões o dono do diretório (`missing`), 
o grupo atual (`users`), e todo o resto respectivamente tem em relação ao item. Uma `-` indica que 
atualmente não há permissão para fazer tal ação. Acima, apenas o dono é que consegue alterar (`w`) o diretório `missing` (adicionar/remover arquivos). 
Para entrar em um diretório, o usuário precisa ter a permissão de "procurar" (representada por "execute":`x`) naquele diretório (e nos seus diretórios pais). 
Para listar os seus conteúdos, o usuário precisa ter a permissão de leitura (`r`) naquele diretório. Para arquivos, as permissões necessitam ser as mesmas. 
Note que praticamente todos os arquivos em `/bin` tem a permissão de `x` indicada para o último grupo, "todos os outros", para que todos possam executar estes programas.

Alguns outros programas muito úteis para se saber nesse ponto são o `mv` (para renomear/mover um arquivo), `cp` (para copiar um arquivo), e `mkdir` (para fazer um novo diretório).

Se você quiser _mais_ informação sobre os argumentos de um programa, entradas, saídas, ou como ele funciona no geral, dê uma chance para o programa `man`. Ele leva como argumento o nome do programa que deseja saber mais informações, e mostra o seu _manual_. Pressione `q` para sair.
```console
missing:~$ man ls
```

## Connecting programs

In the shell, programs have two primary "streams" associated with them:
their input stream and their output stream. When the program tries to
read input, it reads from the input stream, and when it prints
something, it prints to its output stream. Normally, a program's input
and output are both your terminal. That is, your keyboard as input and
your screen as output. However, we can also rewire those streams!

The simplest form of redirection is `< file` and `> file`. These let you
rewire the input and output streams of a program to a file respectively:

```console
missing:~$ echo hello > hello.txt
missing:~$ cat hello.txt
hello
missing:~$ cat < hello.txt
hello
missing:~$ cat < hello.txt > hello2.txt
missing:~$ cat hello2.txt
hello
```

You can also use `>>` to append to a file. Where this kind of
input/output redirection really shines is in the use of _pipes_. The `|`
operator lets you "chain" programs such that the output of one is the
input of another:

```console
missing:~$ ls -l / | tail -n1
drwxr-xr-x 1 root  root  4096 Jun 20  2019 var
missing:~$ curl --head --silent google.com | grep --ignore-case content-length | cut --delimiter=' ' -f2
219
```

We will go into a lot more detail about how to take advantage of pipes
in the lecture on data wrangling.

## A versatile and powerful tool

On most Unix-like systems, one user is special: the "root" user. You may
have seen it in the file listings above. The root user is above (almost)
all access restrictions, and can create, read, update, and delete any
file in the system. You will not usually log into your system as the
root user though, since it's too easy to accidentally break something.
Instead, you will be using the `sudo` command. As its name implies, it
lets you "do" something "as su" (short for "super user", or "root").
When you get permission denied errors, it is usually because you need to
do something as root. Though make sure you first double-check that you
really wanted to do it that way!

One thing you need to be root in order to do is writing to the `sysfs` file
system mounted under `/sys`. `sysfs` exposes a number of kernel parameters as
files, so that you can easily reconfigure the kernel on the fly without
specialized tools. **Note that sysfs does not exist on Windows or macOS.**

For example, the brightness of your laptop's screen is exposed through a file
called `brightness` under

```
/sys/class/backlight
```

By writing a value into that file, we can change the screen brightness.
Your first instinct might be to do something like:

```console
$ sudo find -L /sys/class/backlight -maxdepth 2 -name '*brightness*'
/sys/class/backlight/thinkpad_screen/brightness
$ cd /sys/class/backlight/thinkpad_screen
$ sudo echo 3 > brightness
An error occurred while redirecting file 'brightness'
open: Permission denied
```

This error may come as a surprise. After all, we ran the command with
`sudo`! This is an important thing to know about the shell. Operations
like `|`, `>`, and `<` are done _by the shell_, not by the individual
program. `echo` and friends do not "know" about `|`. They just read from
their input and write to their output, whatever it may be. In the case
above, the _shell_ (which is authenticated just as your user) tries to
open the brightness file for writing, before setting that as `sudo
echo`'s output, but is prevented from doing so since the shell does not
run as root. Using this knowledge, we can work around this:

```console
$ echo 3 | sudo tee brightness
```

Since the `tee` program is the one to open the `/sys` file for writing,
and _it_ is running as `root`, the permissions all work out. You can
control all sorts of fun and useful things through `/sys`, such as the
state of various system LEDs (your path might be different):

```console
$ echo 1 | sudo tee /sys/class/leds/input6::scrolllock/brightness
```

# Next steps

At this point you know your way around a shell enough to accomplish
basic tasks. You should be able to navigate around to find files of
interest and use the basic functionality of most programs. In the next
lecture, we will talk about how to perform and automate more complex
tasks using the shell and the many handy command-line programs out
there.

# Exercises

 1. For this course, you need to be using a Unix shell like Bash or ZSH. If you
    are on Linux or macOS, you don't have to do anything special. If you are on
    Windows, you need to make sure you are not running cmd.exe or PowerShell;
    you can use [Windows Subsystem for
    Linux](https://docs.microsoft.com/en-us/windows/wsl/) or a Linux virtual
    machine to use Unix-style command-line tools. To make sure you're running
    an appropriate shell, you can try the command `echo $SHELL`. If it says
    something like `/bin/bash` or `/usr/bin/zsh`, that means you're running the
    right program.
 1. Create a new directory called `missing` under `/tmp`.
 1. Look up the `touch` program. The `man` program is your friend.
 1. Use `touch` to create a new file called `semester` in `missing`.
 1. Write the following into that file, one line at a time:
    ```
    #!/bin/sh
    curl --head --silent https://missing.csail.mit.edu
    ```
    The first line might be tricky to get working. It's helpful to know that
    `#` starts a comment in Bash, and `!` has a special meaning even within
    double-quoted (`"`) strings. Bash treats single-quoted strings (`'`)
    differently: they will do the trick in this case. See the Bash
    [quoting](https://www.gnu.org/software/bash/manual/html_node/Quoting.html)
    manual page for more information.
 1. Try to execute the file, i.e. type the path to the script (`./semester`)
    into your shell and press enter. Understand why it doesn't work by
    consulting the output of `ls` (hint: look at the permission bits of the
    file).
 1. Run the command by explicitly starting the `sh` interpreter, and giving it
    the file `semester` as the first argument, i.e. `sh semester`. Why does
    this work, while `./semester` didn't?
 1. Look up the `chmod` program (e.g. use `man chmod`).
 1. Use `chmod` to make it possible to run the command `./semester` rather than
    having to type `sh semester`. How does your shell know that the file is
    supposed to be interpreted using `sh`? See this page on the
    [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) line for more
    information.
 1. Use `|` and `>` to write the "last modified" date output by
    `semester` into a file called `last-modified.txt` in your home
    directory.
 1. Write a command that reads out your laptop battery's power level or your
    desktop machine's CPU temperature from `/sys`. Note: if you're a macOS
    user, your OS doesn't have sysfs, so you can skip this exercise.
