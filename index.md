---
layout: page
title: O semestre que falta na sua faculdade em ciência da computação
---

As aulas vão te ensinar tudo sobre tópicos avançados de Ciência da Computação,
de sistemas operacionais à machine learning. Mas há uma um assunto de extrema
importancia que raramente é abordado e é deixado para que os alunos descubram:
como ser produtivo com suas ferramentas. Nós vamos lhe ensinar como dominar as
linhas de comando, usar um editor de texto poderoso, usar recursos sofisticados
de sistemas de controle de versão (Version Control Systems - VCS) e muito mais!

Os estudantes dedicam horas e horas usando essas ferramentas durante o curso
(e milhares de horas durante a carreira), então faz sentido fazer a experiência
o mais fluida possível. Dominar estas ferramentas fará com que você gaste menos
tempo descobrindo como lidar com as ferramentas e permitirá que você resolva 
problemas que antes pareciam muito complexos e impossíveis.

Leia sobre a motivação por trás deste curso [aqui](/about/).

{% comment %}
# Registration

Sign up for the IAP 2020 class by filling out this [registration form](https://forms.gle/TD1KnwCSV52qexVt9).
{% endcomment %}

# Calendário

{% comment %}
**Lecture**: 35-225, 2pm--3pm<br>
**Office hours**: 32-G9 lounge, 3pm--4pm (every day, right after lecture)
{% endcomment %}

<ul>
{% assign lectures = site['2020'] | sort: 'date' %}
{% for lecture in lectures %}
    {% if lecture.phony != true %}
        <li>
        <strong>{{ lecture.date | date: '%-m/%d' }}</strong>:
        {% if lecture.ready %}
            <a href="{{ lecture.url }}">{{ lecture.title }}</a>
        {% else %}
            {{ lecture.title }} {% if lecture.noclass %}[no class]{% endif %}
        {% endif %}
        </li>
    {% endif %}
{% endfor %}
</ul>

Aulas gravadas disponíveis [no YouTube](https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J).

# Sobre as aulas

**Equipe**: Essa aula foi lecionada por [Anish](https://www.anishathalye.com/), [Jon](https://thesquareplanet.com/) e [Jose](http://josejg.com/).
**Dúvidas**: Nos envie um e-mail para [missing-semester@mit.edu](mailto:missing-semester@mit.edu).

# Além do MIT

Compartilhamos essa aula para além do MIT com a esperança de que outros
possam se beneficiar do material. Você pode encontrar as publicações e
discussões em:

 - [Hacker News](https://news.ycombinator.com/item?id=22226380)
 - [Lobsters](https://lobste.rs/s/ti1k98/missing_semester_your_cs_education_mit)
 - [/r/learnprogramming](https://www.reddit.com/r/learnprogramming/comments/eyagda/the_missing_semester_of_your_cs_education_mit/)
 - [/r/programming](https://www.reddit.com/r/programming/comments/eyagcd/the_missing_semester_of_your_cs_education_mit/)
 - [Twitter](https://twitter.com/jonhoo/status/1224383452591509507)
 - [YouTube](https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J)

# Translations

- [Chinese (Simplified)](https://missing-semester-cn.github.io/)
- [Chinese (Traditional)](https://missing-semester-zh-hant.github.io/)
- [Korean](https://missing-semester-kr.github.io/)
- [Serbian](https://netboxify.com/missing-semester/)
- [Spanish](https://missing-semester-esp.github.io/)
- [Turkish](https://missing-semester-tr.github.io/)
- [Vietnamese](https://missing-semester-vn.github.io/)
- [Original version - English](https://missing.csail.mit.edu/)

Note: these are external links to community translations. We have not vetted
them.

Have you created a translation of the course notes from this class? Submit a
[pull request](https://github.com/missing-semester/missing-semester/pulls) so
we can add it to the list!

## Agradecimentos

Nós agradecemos à Elaine Mello, Jim Cain, e ao [MIT Open
Learning](https://openlearning.mit.edu/) por tornar possível a gravação dos
vídeos; ao Anthony Zolnik e o [MIT
AeroAstro](https://aeroastro.mit.edu/) pelos equipamentos de audio e video;
e à Brandi Adams e o [MIT EECS](https://www.eecs.mit.edu/) por apoiar este 
curso.

---

<div class="small center">
<p><a href="https://github.com/missing-semester/missing-semester">Source code</a>.</p>
<p>Licensed under CC BY-NC-SA.</p>
<p>See <a href="/license/">here</a> for contribution &amp; translation guidelines.</p>
</div>
