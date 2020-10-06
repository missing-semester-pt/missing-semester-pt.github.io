---
layout: lecture
title: "Porque estamos oferecendo este curso"
---

Durante uma educação tradicional em Ciência da Computação, é muito provável
que você fará disciplinas que ensinam tópicos avançados de computação, desde
Sistemas Operacionais até Linguagens de Programação e Aprendizagem de Máquina.
Mas, em muitas instituições, existe um tópico essencial que raramente é coberto
e geralmente é deixado para que os estudantes aprendam por si só: a alfabetização
do ecosistema computacional.

Através dos anos, nós ajudamos a ensinar várias disciplinas no MIT e, vez após vez,
nós vimos que muitos estudantes tinham um conhecimento limitado das ferramentas disponíveis
à eles. Computadores foram construídos para automatizar tarefas manuais, mas os estudantes
frequentemente faziam tarefas repetitivas manualmente ou falhavam em utilizar todo o potencial
de ferramentas como sistemas de versionamento e editores de texto. No melhor cenário, isso
resulta em ineficiências e tempo perdido. No pior cenário, em problemas como perda de dados
ou inabilidade de completar certas tarefas.

Esses tópicos não são ensinados como parte do currículo universitário. Os estudantes
nunca são mostrados a como usar essas ferramentas, ou, pelo menos, a como usá-las
eficientemente, desperdiçando tempo e esforço em tarefas que _deveriam_ ser simples.
O currículo oficial de Ciência da Computação está devendo tópicos cruciais sobre o
ecossistema de computação que poderia tornar a vida de estudantes significativamente mais simples.

# O semestre que faltava na sua educação em Ciência da Computaçao

Para ajudar a resolver isso, nós estamos oferecendo uma disciplina que cobre todos os tópicos
que consideramos cruciais para um cientista da computação ou programador efetivo.
A disciplina é pragmática e prática, provendo uma introdução mãos-na-massa à ferramentas
e técnicas que você poderá imediatamente aplicar numa grande variedade de situações.
A disciplina está sendo oferecida durante o "Período de Atividades Independentes" do MIT,
em Janeiro de 2020 - um semestre de um mês onde são ofertadas disciplinas ministradas por
estudantes. Enquanto que as aulas estarão disponíveis apenas para alunos do MIT, nós vamos
divulgar todos os materiais, junto com gravações em vídeo ao público.

Se isso parece algo que você gostaria, aqui estão alguns exemplos concretos do que a
disciplina vai ensinar:

## Shell de Comandos

Como automatizar tarefas repetitivas com aliases, scripts e
sistemas de build. Nunca mais copiar e colar comandos de um
documento de texto. Nunca mais "rode esses 15 comandos um
após o outro". Nunca mais "você esqueceu de rodar isso aqui"
ou "você esqueceu de passar esse argumento".

Por exemplo, buscar no seu histórico rapidamente pode ser um grande economizador de tempo.
No exemplo abaixo, mostramos vários truques relacionados à navegar no histórico do seu
shell buscando por comandos `convert`.

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/history.mp4" type="video/mp4">
</video>

## Controle de versão

Como usar controle de versão _apropriadamente_, e utilizá-lo para
te salvar de um desastre, colaborar com outros e rapidamente encontrar e
isolar mudanças problemáticas. Nunca mais `rm -rf; git clone`. Nunca mais
grandes blocos de código comentado. Nunca mais se desesperar sobre como
achar o que quebrou o seu código. Nunca mais "ah não, nós deletamos código
que estava funcionando?!". Nós iremos até te ensinar como contribuir para
o código de outros com pull requests!

No exemplo abaixo, nós usamos `git bisect` para encontrar qual commit quebrou um teste 
unitário e, então, concertamos com `git revert`.

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/git.mp4" type="video/mp4">
</video>

## Edição de texto

How to efficiently edit files from the command-line, both locally and
remotely, and take advantage of advanced editor features. No more
copying files back and forth. No more repetitive file editing.

Como eficientemente editar arquivos da linha de comando, tanto localmente
quanto remotamente, e tirar vantagem de funcionalidades avançadas do editor.
Nunca mais copiar arquivos pra lá e pra cá. Nunca mais edição repetitiva de arquivos.

Os macros do Vim são uma das melhores funcionalidades dele. No exemplo abaixo, nós rapidamente convertemos uma tabela html para csv usando um macro vim.

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/vim.mp4" type="video/mp4">
</video>

## Máquinas remotas

Como se manter são quando estiver trabalhando com máquinas remotas
usando chaves SSH e multiplexing de terminal. Nunca mais manter
vários terminais abertos apenas para rodar dois comandos ao mesmo
tempo. Nunca mais digitar a sua senha toda vida que se conectar.
Nunca mais perder tudo porque a sua internet disconectou ou você
teve que reiniciar o seu notebook.

No exemplo abaixo, nós usamos `tmux` para manter as seções vivas em servidores remotos e `mosh` para fazer roaming de redes e desconexão.

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/ssh.mp4" type="video/mp4">
</video>

## Achando arquivos

Como achar rapidamente arquivos que você está procurando.
Nunca mais sair clicando em todos os arquivos no seu projeto
até você achar o que você está procurando.

No exemplo abaixo, nós procuramos por arquivos rapidamente com `fd` e por pedaços de código com `rg`.
Nós também usamos `cd` e `vim` em arquivos/pastas recentes/frequentes usando `fasd`.

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/find.mp4" type="video/mp4">
</video>

## Lidando com dados

Como rapidamente e facilmente modificar, ver, parsear, plotar e computar sobre
dados e arquivos direto da linha de comando. Nunca mais copiar e colar
de arquivos de log. Nunca mais manualmente calcular estatística sobre os dados.
Nunca mais plotar em planilhas.

## Máquinas virtuais

Como usar máquinas virtuais para experimentar novos sistemas operacionais,
isolar projetos não relacionados e manter sua máquina limpa e organizada.
Nunca mais acidentalmente corroper o seu computador enquanto faz um experimento
de segurança. Nunca mais milhões de pacotes aleatórios instalados com diferentes versões.

## Segurança

Como estar na internet sem revelar imediatamente todos os seus segredos
para o mundo. Nunca mais inventar você mesmo senhas que obedecem aos critérios
do site. Nunca mais redes WiFi abertas e inseguras. Nunca mais mensagens não-encriptadas.

# Conclusão

Isso, e mais, será coberto nas 12 aulas, cada uma incluindo um exercício
para te dar mais familiaridade com as ferramentas. Se você não consegue
mais esperar até Janeiro, você também pode olhar as aulas do
[Hacker Tools](https://hacker-tools.github.io/lectures/), que
oferecemos durante o IAP ano passado. É o precursor desta disciplina e cobre
muitos dos mesmos tópicos.

Tomara que nos vejamos em Janeiro, virtualmente ou pessoalmente!

Feliz hacking,<br>
Anish, Jose, and Jon
