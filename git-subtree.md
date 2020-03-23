---
date: "2020-03-22"
title: "Git Subtree"
desc: "Como unir repositórios sem criar submodules."
tags: [ "code" ]
---
É a segunda vez que uso subtrees no Git. Não é algo que me acostumei usar por rotina, mas é uma técnica que eu recomendo que todo programador conheça para unir repositórios que não dependa dos pesadelos de configurar submodules.

Há vários tutoriais na internet sobre seu uso (como o da [Atlasian](https://www.atlassian.com/git/tutorials/git-subtree)), além do próprio manual do Git e sua ajuda. Só quero enfatizar neste post que ele existe, é fácil de usar, e pode resolver alguns problemas de gerenciamento de projeto:

 - Unir repositórios que foram separados em algum momento ou que nasceram separados.
 - Unir dependências que não estão online, mas que precisam estar caminhando em paralelo.
 - Compor árvores de histórico distintas e não se preocupar muito de onde elas vieram (exceto quando for necessário juntar de novo, e nesse caso o commit que as une possui algumas informações).

