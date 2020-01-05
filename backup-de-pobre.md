---
date: "2008-03-28"
title: Backup de pobre
tags: [ "blog" ]
---
O _backup_ - ato de fazer cópia(s) de segurança de dados considerados importantes -, como tudo na vida, para se tornar efetivo e transformador deve antes se tornar um hábito.

Hábitos, por definição, ao serem realizados repetidamente muitas vezes, podem se tornar poderosos catalisadores de tarefas, sejam elas cozinhar um bolo, compilar um programa ou fazer _backups_. Por isso é muito importante que o _backup_, antes de ser 100% seguro, seja 100% previsível e habitual.

#### É o hábito que faz o _backup_

Minhas restrições para que algo vire um hábito em minha vida, quando tarefas, são que a tarefa seja, antes de tudo,:

	
  * Simples de fazer. Quero conseguir efetuar a tafefa sem ter que toda vez preparar um ritual em noite de lua cheia, sacrificar uma virgem para os deuses pagãos e lembrar de todas as palavras proparoxítonas que terminam com x e rimam com fênix.

	
  * Fácil de executar. É um complemento do primeiro item. Com isso eu quero dizer que, além de simples, eu não precise despender grande força e energia diariamente para efetuar a tarefa. Limpar uma pasta de arquivos temporários pode ser simples; mas é fácil?

	
  * Fácil de lembrar. Se eu tenho que fazer um esforço mental diário tão grande para lembrar do que fazer então muito provavelmente será difícil transformá-lo em um hábito.

Passado por esse _checklist_, podemos montar um esquema tão simples que qualquer bobo que tem um blogue (por exemplo, eu) conseguirá executar diariamente, ou pelo menos quando tiver vontade. A freqüência dependerá se isso irá se transformar em um hábito ou não.

#### O mais poderoso copiador do mundo: xcopy

Ele pode não parecer, mas é beeem mais antigo do que parece. Nós, veteranos, que possuímos mais anos de vida em frente ao monitor que gostaríamos de admitir (_copyright_  => [DQ](http://www.cbrasil.org/wiki/index.php?title=DQuadros)), usávamos o xcopy para copiar pastas e disquetes inteiros no MS-DOS, um sistema operacional predecessor do Windows Vista que vinha em preto e branco e sem [User Account Control](http://technet2.microsoft.com/WindowsVista/en/library/0d75f774-8514-4c9e-ac08-4c21f5c6c2d91033.mspx).

No entanto, esse pequeno grande aplicativo sobreviveu todos esses anos, atingiu a maioridade, e hoje permite a nós, programadores de _mouse_, fazer nossos _backups_ com um simples arquivo de _batch_ e um pouco de imaginação.

<blockquote>Aos mocinho e mocinhas presentes: os arquivos de _batch_, de extensão .bat ou .cmd, são, assim como o MS-DOS, coisas de veteranos do velho oeste.  São arquivos de _script_ que contém um conjunto de comandos que pode-se digitar manualmente na tela preta. Seu objetivo principal é otimizar e facilitar a digitação de tarefas complexas e torná-las mais simples, fáceis de executar e de lembrar.</blockquote>

O uso do programa pode ser aprendido dando-se uma olhada em sua ajuda (xcopy /?)

    
    xcopy origem [destino] [opções]

Algumas opções bem úteis para efetuar cópias de segurança de arquivos modificados:

**/M - copia somente arquivos com atributo de arquivamento**; após a cópia, desmarca atributo. Ao escrever novamente em um arquivo copiado com esse método, o arquivo volta a ter o atributo de arquivamento, e irá ser copiado novamente se especificada essa opção. Se nunca mais for mexido, não será mais copiado.

**/D - copia arquivos mais novos na origem**. Não costumo usar pelos problemas que podem ocorrer em sistemas com horas diferentes, mas, dependendo da ocasião, pode ser útil. Também é possível especificar uma data de início da comparação.

**/EXCLUDE - permite excluir arquivo(s) de uma cópia coletiva**. Isso pode ser muito útil se você não deseja gastar tempo copiando arquivo que são inúteis dentro de pastas que contém arquivos importantes. É possível especificar um arquivo que irá conter uma lista de nomes-curinga, um por linha, que irá servir como filtro da cópia. Teremos um exemplo logo abaixo.

**/E - copia pastas e subpastas, mesmo que vazias**. Essa opção é básica, e ao mesmo tempo essencial. Não se esqueça dela quando for criar seu _script_ de _backup_!

**/C - continua copiando, mesmo com erros**. Se é mais importante copiar o máximo que puder do que parar no primeiro errinho de acesso negado, essa opção deve ser usada. É possível redirecionar a saída para um arquivo de _log, _que poderá ser usado para procurar por erros que ocorreram durante a operação.

**/Q - não exibe nome de arquivos ao copiar**. Às vezes imprimir o nome de cada arquivo na saída do prompt de comando acaba sendo mais custoso que copiar o próprio arquivo. Quando a cópia envolve muitos arquivos pequenos, é recomendável usar esta opção.

**/Y - suprime perguntas para o usuário**. Muito útil em arquivos _batch, _já que o usuário geralmente não estará lá para apertar _enter_ quando o programa pedir.

**/Z - copia arquivos da rede em modo reiniciável**. Muito importante quando estiver fazendo _backup _pela rede. Às vezes ela pode falhar, e essa opção permite continuar após pequenas quedas de desempenho.

#### Estudo de caso: código-fonte

Para a cópia do patrimônio mais valioso de um programador, os fontes, podemos usar um conjunto bem bolado das 0pções acima, além de generalizar um _script _para ser usado em outras situações. Inicialmente vamos definir que queremos um _backup _que altere o atributo de arquivamento, sobrescreva cópias antigas e que possa ser copiado pela rede sem maiores problemas. Além disso, não iremos copiar as pastas _Debug _e _Release _existentes geradas pela saída de algum compilador (ex: saída do Visual Studio), nem arquivos temporários muito grandes (ex: arquivos de navegação de símbolos).

O resultado é o que vemos abaixo:

    
    xcopy <pasta-origem> <pasta-destino> /M /E /C /Y /Z /EXCLUDE:sources.flt

O conteúdo de **sources.flt** (extensão escolhida arbitrariamente) pode ser o seguinte:

    
    .obj
    .res
    .pch
    .pdb
    .tlb
    .idb
    .ilk
    .opt
    .ncb
    .sbr
    .sup
    .bsc
    \Debug\
    \Release\

Só isso já basta para um _backup _simples, pequeno e fácil de executar. Só precisamos copiar a chamada ao xcopy em um arquivo de extensão .bat ou .cmd e executarmos sempre que acharmos interessante termos um backup quentinho em folha. Por exemplo, podemos manter os fontes do projeto atual em um _pen drive_ e, ao acessarmos uma máquina confiável, rodar um _backup _que copia os arquvos-fonte para um ambiente mais seguro e estável.

Note que esse procedimento não anula a necessidade de termos um [sistema de versionamento e controle de fontes](http://www.caloni.com.br/guia-basico-de-controle-de-codigo-source-safe). O _backup _é para aquelas projetos que demoram um tempinho para efetuar _commit_, projetos temporários ou então sistemas de controle de fonte distribuído, em que podemos ter inúmeras pastas com diversos _branchs _locais.
