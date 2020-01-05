---
date: "2008-04-29"
title: 'Bazaar e Fedora 8: a saga'
tags: [ "blog" ]
---
Seria bom se as coisas simples da vida fossem simples, não é mesmo?

Ontem, sexta passada e quinta passada, no meio de outras tarefas "urgentes", tentava desesperadamente conseguir instalar o [Bazaar](http://bazaar-vcs.org/) na minha [VM](http://en.wikipedia.org/wiki/Virtual_machine) de desenvolvimento, um **Fedora 8** todinho configurado.

Para azar da minha pessoa, o [guia simples e rápido](http://bazaar-vcs.org/Download) de instalação do Bazaar não funcionava para minha distribuição Linux. Na verdade, funciona. Porém, é instalada uma versão tão antiga (**0.91**!) que o formato do banco de dados já se tornou incompatível.

    
    #yum info bzr

    
    ...

    
    Available Packages
    Name     : bzr
    Arch     : i386
    Version  : <font color="#ff0000">0.91</font>
    ...

O pior, no entanto, foi tentar encontrar uma solução para o problema. Fiz mil e uma pesquisas com palavras-chave que nem imaginava que seria capaz de formular. E nada. A princípio minha idéia era apenas atualizar a lista de pacotes do repositório gerenciado pelo yum, o gerenciador de pacotes oficial do Fedora. Entre minhas buscas, encontrei os seguintes itens:

	
  * Um [FAQ do Fedora](http://www.fedorafaq.org/) (que não conseguiu responder à minha pergunta)

	
  * O sítio do [projeto do yum](http://linux.duke.edu/projects/yum/), gerenciador de pacotes (cujo [FAQ](http://wiki.linux.duke.edu/YumFaq) não conseguiu responder o mínimo)

	
  * Uma [lista enorme de sítios](http://www.google.com.br/search?q=yum+update+repository) explicando como criar seu próprio repositório (sem comentários)

Enfim, a coisa não estava saindo do lugar. E o cronograma apertando até o dia final. Até que decidi usar o caminho mais rápido e pentelho: [perguntar para quem entende do assunto](http://marcioandreyoliveira.blogspot.com/). A resposta foi simples e direta:

<blockquote>_- Por que você não instala direto dos fontes?_</blockquote>

Uia! E não é que é mais simples, mesmo?

    
    #wget https://launchpad.net/bzr/1.3/1.3.1/+download/bzr-1.3.1.tar.gz
    #tar -zxvf bzr-1.3.1.tar.gz /* ele teve que me explicar esse comando singelo */
    #cd bzr-1.3.1
    #cat INSTALL

    
    Installation
    ------------
    
    When upgrading using setup.py, it is recommended that you first delete the
    bzrlib directory from the install target.
    
    To install bzr as a user, run
    
      python setup.py install --home ~
    
    To install system-wide, run (as root)
    
      python setup.py install

    
    #python setup.py install

E foi isso! É a segunda vez que tento fazer algo simples no Linux e me dou mal. Com certeza os dias futuros serão melhores. Mas me bate aquela sensação que as coisas poderiam já estar em um nível mais fácil de se mexer. Opinião pessoal.
