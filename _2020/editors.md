---
layout: lecture
title: "Editores de Texto (Vim)"
date: 2023-08-16
ready: true
video:
  aspect: 56.25
  id: a6Q8Na575qc
---

Escrever palavras em português e escrever código são atividades bem diferentes.
Quando estamos programando, você passa a maior parte do tempo pulando de arquivo
em arquivo, lendo, navegando e editando código ao invés de escrever um long e
contínuo texto. Faz sentido que existem diferentes programas para escrever
palavras e escrever código (Ex: Microsoft Word vs Visual Studio Code).

Como programadores, nós passamos a maior parte do nosso tempo editando código,
então vale a pena investir tempo em nós tornar mestre em um editor de texto que
se encaixe nas suas necessidades. Aqui você vai aprender um novo editor:

- Comece com um tutorial (Ex: essa aula mais o material que nós indicaremos)
- Continue usando o editor para todas as suas edições de texto (mesmo que
diminua sua produtividade inicialmente)
- Aprenda coisas enquanto você vai utilzando: se por acaso, parece que tem uma
maneira melhor de fazer alguma coisa, provavelmente tem.

Se você seguir o método acima, se comprometendo totalmente em usar o novo
programa para todas as suas edições de texto, o tempo para aprender um editor
sofisticado vai parecer algo como isso. Em uma hora ou duas, você vai aprender
as funcionalidades básicas do editor, como abrir e editar arquivos, salvar e
sair, navegar pelos buffers. Quando você estiver com 20 horas de investidas,
você provavelmente estará tão rápido quanto você era no seu editor antigo.
Após isso, os benefícios começam: você vai ter conhecimento e memória muscular
suficiente para que usar esse novo editor te economize tempo. Editores de texto
moderno são bonitas e poderosas ferramentas, então a aprendizagem nunca para:
você vai ficar mais rápido cada vez que você aprende mais

# Qual editor escolher?

Programadores têm uma [opinião](https://en.wikipedia.org/wiki/Editor_war) bem forte sobre seus editores de texto
about their text editors.

Quais editores são populares hoje? Veja esse [Stack Overflow
survey](https://insights.stackoverflow.com/survey/2019/#development-environments-and-tools)
(Pode ter um pouco de viés, pois os usuários do Stack Overflow talvez não
representem os programadores no geral). [Visual Studio
Code](https://code.visualstudio.com/) é o editor mais popular.
[Vim](https://www.vim.org/) é o mais popular editor de linha de comando

## Vim

Todos os instrutores dessa aula usa Vim como seu editor de texto. Vim tem uma
rica história; é originário do editor VI (1975), e ainda está em desenvolvimento
hoje. O Vim tem umas ideas bastante polidas por trás e, por conta disso, muitas
ferramentas suportam uma emulação de VIM ( por exemploo, 1.4 milhões de pessoas
tem instalada [Emule VIM no VS code](https://github.com/VSCodeVim/Vim)).
VIM provavelmente vale apena aprender mesmo que você acabe por trocar por outro
editor no fim das contas :)

Não é possivel ensinar todas as funcionalidades do VIM em 50 minutos, então nós
vamos nos focar em explicar a filosofia por trás do VIM, ensinar o básico,
mostrar umas funcionalidades avançadas e te dar os recursos para você se dominar
a ferramenta.

# Filosofia do Vim

Quando programa, você passa a maior parte do tempo lendo/editando e não
escrevendo. Por conta disso, VIM é um editor _modal_: ele tem modos
diferentes para inserir texto vs manipular texto. VIM é programável (com
Vimscript e outras linguagens como Python), e a interface do VIM por si só é
uma linguagem de programação:
as teclas (com nomes mnemônicos) são comandos, e esses comandos ser
compostos. VIM evita o uso do mouse, pois é muito lento; VIM evita até mesmo
usar as teclas de setas (navegação) pois precisa de muito movimento para
apertá-las.

O resultado final é um editor que te traz a velocidade do seu pensamento
na hora de editar seu código.

# Modal editing

O design do VIM é baseado na ideia de que muito tempo do programador é usado
para ler, navegar e fazer pequenas edições, ao contrário de uma grande edição
de texto. Por conta disso o VIM tem vários modos para trabalhar.

- **Normal**: para mover-se por um arquivo e fazer edições
- **Insert**: para inserir texto
- **Replace**: para trocar texto
- **Visual** (simples, linha, ou bloco): para selecionar blocos de texto
- **Command-line**: para rodar comandos

Teclas tem diferentes significados em diferentes modos de trabalho. Por exemplo,
a letra 'x' no modo Insert vai inserir um caractere literalmente 'x', mas no
modo Normal, a letra 'x' vai deletar o caractere embaixo do cursor e no modo
Visual ele vai deletar a seleção.

Em sua configuração padrão, VIM mostra o modo de trabalho na posição
inferior esquerda. O modo padrão/inicial é o modo Normal. Você vai passar
a maior parte do tempo entre os modos Normal e Insert.

Você muda de modo pressionando '<ESC>' (a tecla escape) para mudar de
qualquer modo de volta para o modo Normal. A partir do modo Normal,
entra-se no modo Insert apertando a tecla 'i', no modo Replace apertando
a teclar 'R', no visual com a tecla 'v', no visual de linha com a tecla 'V' , representado também por '^V') e no modo Commad-Line com ':'.
no visual de bloco com o conjunto de teclas '<C-v>' (Ctrl-V, algumas vezes
`:`.

Você usará a tecla `<ESC>` muito enquanto estiver usando VIM: pode-se
considerar alterar o CapsLock para Esc ([macOS instructions](https://vim.fandom.com/wiki/Map_caps_lock_to_escape_in_macOS)).

# Básico

## Inserindo texto

A partir do modo Normal, pressione `i` para entrar no modo de inserção.
Agora, o VIM se comportará como um editor de texto qualquer, até você
apertar `<ESC>` para retornar pro modo Normal. Isso juntamente com o básico
explicado acima é tudo que você precisa para começar editar arquivos usando
VIM (não necessariamente de forma eficiente se você está passando todo o tempo
editando seu arquivo no modo de Inserção)

## Buffers, abas, e janelas

O VIM mantém um conjunto de arquivos abertos, chamados de "buffers". Uma
sessão do VIM tem uma quantidade de abas, e cada aba tem uma quantidade de
janelas (painéis separados). Cada janela/painel mostra apenas um buffer.
Diferente de outros programas que você está familiarizado, como navegadores,
não existe uma correspondência de 1-para-1 entre buffers e janelas/paineis,
até mesmo dentro da mesma aba. Isso pode ser bem útil, por exemplo, para ver
duas partes diferentes de um arquivo ao mesmo tempo.

Por padrão, o VIM abre apenas uma aba que contém apenas uma janela/painel.

## Linha de comando

Podemos entrar no modo de comando apertando a tecla `:` no modo Normal. Seu
cursor vai pular para a linha de comando na parte inferior da tela assim que
apertar `:`. Esse modo tem muitas funcionalidades, incluindo abrir, salvar,
fechar e
[sair do VIM](https://twitter.com/iamdevloper/status/435555976687923200).

- `:q` sair (fechar janela)
- `:w` salvar ("escrever")
- `:wq` salvar e sair
- `:e {nome do arquivo}` abrir arquivo para editar
- `:ls` mostrar buffers abertos
- `:help {tópico}` abrir ajudar
    - `:help :w` abrir ajuda para o comando `:w`
    - `:help w` abrir ajuda para o movimento `w`

# A interface do VIM é uma própria linguagem de programação

A mais importante ideia no VIM é que sua interface é uma própria linguagem
de programação. Teclas (com nomes mnemônicos) são comandos e esses comandos
podem ser compostos. Isso permite edições e movimentações eficientes,
principalmente quando os comandos entram na sua memória muscular.

## Movimento

Você deve passar a maior parte do seu tempo no modo Normal, usando
commandos de movimento para navegar dentro do seu buffer. Movimentos no VIM
também são chamados de "substantivos", porque eles se referem a um conjunto de
texto

- Movimento básico: `hjkl` (esquerda, baixo, cima, direita)
- Palavras: `w` (próxima palavra), `b` (começo da palavra), `e` (fim da
palavra)
- Linhas: `0` (começo da linha), `^` (primeiro caractere não vazio), `$` (fim
da linha)
- Janela: `H` (topo da janela), `M` (meio da janela), `L` (fim da janela)
- Rolar: `Ctrl-u` (para cima), `Ctrl-d` (para baixo)
- Arquivo: `gg` (começo do arquivo), `G` (fim do arquivo)
- Número da Linha: `:{number}<CR>` ou `{number}G` (linha {número})
- Etc: `%` (item correspondente)
- Encontrar: `f{character}`, `t{character}`, `F{character}`, `T{character}`
    - encontrar/para frente/ao contrário {caractere} na mesma linha
    - `,` / `;` para navegar entre os resultados
- Procurar: `/{regex}`, `n` / `N` para navegar entre os resultados

## Seleção

Modos Visuais:

- Visual
- Visual Line
- Visual Block

Pode usar os movimentos para fazer seleções.

## Edição

Tudo que você está acostumado a fazer com o mouse, a partir de agora você irá
fazer com o teclado usando comandos de edição para compor seus comandos de
movimentos. É aqui que a interface do VIM começa a ficar parecido com uma
linguagem de programação. Os comandos de edição do VIM também são chamados
de "verbos", porque verbos agem em cima de substantivos.

- `i` entra no modo de Inserção
    - Para manipular/deletar texto, você deve usar alguma coisa a mais do que o backspace
- `o` / `O` insere linha abaixo / acima
- `d{motion}` deleta {movimento}
    - Ex: `dw` deleta palavra, `d$` deleta até o fim da linha, `d0` deleta
    até o começo da linha.
- `c{motion}` muda {movimento}
    - Ex: `cw` muda a palavra
    - Assim como `d{motion}` só que seguido de i `i`
- `x` deleta o caractere (igual a`dl`)
- `s` substitui o caractere (igual a `xi`)
- Modo visual + manipulação
    - seleciona o texto, `d` para deletá-lo ou `c` para mudá-lo
- `u` desfazer, `<C-r>` refazer
- `y` copiar / "yank" (alguns comandos como o `d` também copiam)
- `p` colar
- Muito mais para aprender: Ex: `~` muda o caso da letra (maiúscula,
minúscula)

## Contagem

Você pode combinar substantivos e verbos com contagem, e elas vão performar
uma determinada ação uma quantidade de vezes.

- `3w` move o cursor 3 palavras pra frente
- `5j` move o cursor 5 linhas para baixo
- `7dw` deleta 7 palavras

## Modificadores

Você pode usar modificadores para mudar o sentido de um substantivo. Alguns
modificadores são `i` que quer dizer "por dentro" ou "dentro" e `a`, que quer
dizer "ao redor"

- `ci(` muda o conteúdo dentro do par de parênteses.
- `ci[` muda o conteudo dentro do par de colchetes.
- `da'` deleta, incluido as aspas simples, todo o conteúdo dentro das aspas
simples

# Demonstração

Aqui está uma implementação quebrada [fizz buzz](https://en.wikipedia.org/wiki/Fizz_buzz)

```python
def fizz_buzz(limit):
    for i in range(limit):
        if i % 3 == 0:
            print('fizz')
        if i % 5 == 0:
            print('fizz')
        if i % 3 and i % 5:
            print(i)

def main():
    fizz_buzz(10)
```

Nós vamos ajeitar os seguintes problemas:

- Main nunca foi chamada
- Começa em 0 ao invés de 1
- Printa "fizz" e "buzz" em linhas separadas nos múltiplos de 15
- Printa "fizz" em múltiplos de 5
- Usa um argumento hard-coded de 10 ao invés de usar um argumento de linha
de commando

{% comment %}
- main nunca é chamada
  - `G` fim do arquivo
  - `o` abra uma nova linha abaixo
  - escreva o código "if __name__ ..."
- começar com 0 ao invés de 1
  - procure por `/range`
  - `ww` para mover duas palavras para frente
  - `i` para inserir o texto, "1, "
  - `ea` para inserir depois do limite, "+1"
- nova linha para "fizzbuzz"
  - `jj$i` para inserir o texto ao fim da linha
  - adicione ", end=''"
  - `jj.` para repetir o segundo print
  - `jjo` abir linha embaixo se
  - adicionar "else: print()"
- fizz fizz
  - `ci'` para mudar fizz
- argumento de linha de comando
  - `ggO` abrir linha acima
  - "import sys"
  - `/10`
  - `ci(` para "int(sys.argv[1])"
{% endcomment %}

See the lecture video for the demonstration. Compare how the above changes are
made using Vim to how you might make the same edits using another program.
Notice how very few keystrokes are required in Vim, allowing you to edit at the
speed you think.

# Customizando o VIM

O VIM é customizado por um arquivo de configuração de texto que fica em
`~/.vimrc` (contendo comandos Vimscript). Provavelmente existem muitas
opções básicas que você gostaria de ligar.

Nós estamos disponibilizando uma configuração bem documentada que você pode
usar como ponto de partida. Nós recomendamos usar isso porque ele ajeita
algumas configurações peculiares padrões do VIM **Baixe sua configuração [aqui](/2020/files/vimrc) e salve em `~/.vimrc`.**

O VIM é extremamente customizável, e vale a pena passar um tempo explorando
suas opções de customização. Você pode olhar para os dotfiles no Github de
algumas pessoas para ter como inspiração. Como base, aqui vai os dotfiles do
VIM dos instrutores
([Anish](https://github.com/anishathalye/dotfiles/blob/master/vimrc),
[Jon](https://github.com/jonhoo/configs/blob/master/editor/.config/nvim/init.vim) (Usa [neovim](https://neovim.io/)),
[Jose](https://github.com/JJGO/dotfiles/blob/master/vim/.vimrc)). Existem
muitos materiais desse tópico na internet. Tente não copiar e colar a
configuração inteira das pessoas, mas leia, entenda e pegue o que você
precisar.

# Extendendo VIM

Tem uma grande quantidade de plugins para aumentar as possibilidades do VIM.
Ao contrário de um conselho desatualizado que você pode encontrar na
internet, você não precisa usar um gerenciador de plugin para o VIM (desde
o Vim 8.0). Ao invés disso, você pode usar o gerenciador de pacote
integrado. Simplesmente crie um diretório `~/.vim/pack/vendor/start/`, e
coloque os plugins lá (Ex: via `git clone`).

Aqui estão alguns dos nossos plugins favoritos:

- [ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim): Buscador de Arquivo
Indistinto
- [ack.vim](https://github.com/mileszs/ack.vim): Procura de Código
- [nerdtree](https://github.com/scrooloose/nerdtree): Explorador de Arquivo
- [vim-easymotion](https://github.com/easymotion/vim-easymotion):
Movimentos mágicos

Nós estamos tentando evitar entregar uma longa lista de plugins aqui. Você
pode checar os arquivos de configuração dos instrutores se quiser.
([Anish](https://github.com/anishathalye/dotfiles),
[Jon](https://github.com/jonhoo/configs),
[Jose](https://github.com/JJGO/dotfiles)) para ver quais outros plugins nós
usamos
Dá uma olhada em [Vim Awesome](https://vimawesome.com/) para mais plugins do
VIM
Tem também uma grande quantidade de posts sobre esse assunto. Basta
procurar por "best Vim Plugins".

# Modo Vim em outros programas

Muitas ferramentas suportam a emulação do Vim. A qualidade varia de bom para
excelente; Dependendo da ferramenta pode ser que não suporte as
características mais avançadas, mas a maioria suporta o básico de forma bem
feita.

## Shell

Se você é um usuario de Bash, use `set -o vi`. Se é de Zsh, `bindkey -v`. para
Fish,
`fish_vi_key_bindings`. Adicionalmente, não importa qual shell você usa, você
pode `export EDITOR=vim`. Essa variável de ambiente é usada para decidir qual
editor é chamado quando o programa quer iniciar um editor. Por exemplo, `git`
vai usar esse editor para comitar mensagens.

## Readline

Muitos programas usam a [GNU
Readline](https://tiswww.case.edu/php/chet/readline/rltop.html) biblioteca
para sua interface de linha de comando. Readline suporta uma emulação
Básica do VIM também que pode ser adicionada utilizando a seguinte linha no
arquivo `~/.inputrc`:

```
set editing-mode vi
```

Com essa configuração, por exemplo, o REPL do Python vai suportar os
comandos do Vim

## Outros

Têm extensões para a internet dos atalhos do Vim
[browsers](http://vim.wikia.com/wiki/Vim_key_bindings_for_web_browsers) -
algumas populares
[Vimium](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb?hl=en)
para o Google Chrome [Tridactyl](https://github.com/tridactyl/tridactyl) para
Firefox. Você pode até conseguir os atalhos do Vim no [Jupyter
notebooks](https://github.com/lambdalisue/jupyter-vim-binding).

# Vim Avançado

Aqui estão alguns exemplos para mostrar você o poder do editor. Nós não
podemos ensinar a você todas essas coisas, mas você vai aprender elas
durante sua jornada. Uma boa heurística: quando você estiver usando o
editor e pensar "deve ter algo para fazer isso de maneira mais rápida e
prática", provavelmente irá existir: procure online.

## Procurar e substituir

`:s` (substituir) comando ([documentation](http://vim.wikia.com/wiki/Search_and_replace)).

- `%s/foo/bar/g`
    - substitua foo por bar globalmente no arquivo
- `%s/\[.*\](\(.*\))/\1/g`
    - substitua links nomeados de Markdown por simples URLs

## Multiplas Janelas

- `:sp` / `:vsp` para dividir as janelas
- Você pode ter multiplas visões do mesmo buffer.

## Macros

- `q{character}` para começar a gravar a macro no registrador `{character}`
- `q` para parar a gravação
- `@{character}` executa a macro
- Execução da macro para no erro
- `{number}@{character}` executa a macro {número} vezes
- Macros podem ser recursivas
    - primeiro limpe a macro com `q{character}q`
    - grave a macro, com `@{character}` para executar a macro
    recursivamente
    (vai ser uma no-op até que a gravação esteja completa)
- Exemplo: converter xml to json ([file](/2020/files/example-data.xml))
    - Array de objetos com as chaves "name" / "email"
    - Usar um programa Python?
    - Usar sed / regexes
        - `g/people/d`
        - `%s/<person>/{/g`
        - `%s/<name>\(.*\)<\/name>/"name": "\1",/g`
        - ...
    - Comandos Vim / macros
        - `Gdd`, `ggdd` Deleta a primeira e última linha
        - Macro para formatar apenas um elemento ( registro `e` )
            - Vai para linha com `<name>`
            - `qe^r"f>s": "<ESC>f<C"<ESC>q`
        - Macro para formatar uma person
            - Vai para a linha com `<person>`
            - `qpS{<ESC>j@eA,<ESC>j@ejS},<ESC>q`
        - Macro para formatar uma person e ir para a próxima person
            - Vai para a linha `<person>`
            - `qq@pjq`
        - Executar a macro até o fim do arquivo
            - `999@q`
        - Manualmente remover o último `,` e adicionar `[` e `]`
        delimitadores

# Recursos

- `vimtutor` é um tutorial que vem com o Vim instalado - se o Vim está
instalado, você pode executar o comando `vimtutor` do seu shell
- [Vim Adventures](https://vim-adventures.com/) um jogo para aprender Vim
- [Vim Tips Wiki](http://vim.wikia.com/wiki/Vim_Tips_Wiki)
- [Vim Advent Calendar](https://vimways.org/2019/) tem várias dicas Vim
- [Vim Golf](http://www.vimgolf.com/) é [code golf](https://en.wikipedia.org/wiki/Code_golf), mas onde a linguagem de programação é Vim's UI
- [Vi/Vim Stack Exchange](https://vi.stackexchange.com/)
- [Vim Screencasts](http://vimcasts.org/)
- [Practical Vim](https://pragprog.com/titles/dnvim2/) (livro)

# Exercícios

1. Complete `vimtutor`. Nota: ficar melhor em
   [80x24](https://en.wikipedia.org/wiki/VT100) (80 colunas por 24 linhas)
   janela do terminal.
1. Download nosso [basic vimrc](/2020/files/vimrc) e salve para `~/.vimrc`. Leia
   o bem documentado arquivo (using Vim!), e observe como o Vim é e se
   comporta com a nova configuração.
1. Instale e configure um plugin:
   [ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim).
   1. Crie um diretório de plugins com `mkdir -p ~/.vim/pack/vendor/start`
   1. Baixe o plugin: `cd ~/.vim/pack/vendor/start; git clone
      https://github.com/ctrlpvim/ctrlp.vim`
   1. Leia a
      [documentação](https://github.com/ctrlpvim/ctrlp.vim/blob/master/readme.md)
      para o plugin. Tente usar CtrlP para localizar o arquivo e navegar para
      o diretório do projeto, abrir o Vim e usar a linha de commando para
      começar
      `:CtrlP`.
    1. Customize CtrlP adicionando
       [configuração](https://github.com/ctrlpvim/ctrlp.vim/blob/master/readme.md#basic-options)
       em seu `~/.vimrc` para abrir o CtrlP pressionando Ctrl-P
1. Para praticar usando Vim, refaça o [Demo](#demo) da aula na sua própria
   máquina.
1. Use Vim para _todas_ as suas ediçdoes de texto no próximo mês. Sempre que
   alguma coisa parecer ineficiente, ou quando você pensar "Deve ter
   alguma outra maneira melhor", tente dar um Google, provavelmente tem. Se
   você ficar preso, venha até nosso escritório ou mande nos um email.
1. Configura suas outras ferramentas para usar os atalhos do Vim (veja as
   intruções acima).
1. Continue customizando seu `~/.vimrc` e instale mais plugins.
1. (Avançado) Converter XML para JSON ([example file](/2020/files/example-data.xml))
   usando Vim macros. Tente fazer você mesmo, mas você pode olhar em
   [macros](#macros) acima se você ficar preso.
