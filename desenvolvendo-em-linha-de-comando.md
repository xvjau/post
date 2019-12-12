---
date: "2007-11-01"
title: Desenvolvendo em linha de comando
categories: [ "blog" ]
---
Desde uns tempos para cá o Visual Studio tem se tornado uma das ferramentas mais pesadas de desenvolvimento já criadas. Como se não bastasse, a compilação de pequenos trechos de código é algo desnecessariamente complicado no ambiente. Por esse motivo estou ganhando o costume de usar a linha de comando para esse tipo de tarefa. Afinal de contas,  na maioria das  vezes a única coisa que eu preciso fazer é abrir o atalho "Visual Studio Command Prompt" e digitar uma linha:

    
    cl meu-codigo-fonte-do-coracao.cpp

O problema é ter que "andar" do diretório padrão de início até a pasta onde está o código-fonte que desejo compilar. Porém, isso é facilmente resolvido com uma linha (no registro):

[![Shell Folder Command](/images/slXwEFK.png)](/images/shel-folder-command.png)

A partir daí, o comando "Console" existe no menu de contexto de qualquer pasta que clicarmos no Windows Explorer.

[![Shell Context Menu](/images/rKZdguV.png)](/images/shell-context-menu.png)

Note que é possível criar outros comandos, como é o meu caso, onde preciso de vez em quando compilar utilizando o Visual Studio 2005 (o comando Console) e o Visual Studio 2003 (o comando VS2003). Ao escolher a opção, um _prompt_ de comando é aberto com o ambiente de compilação montado e (adivinhe) com a pasta padrão sendo a que foi clicada no explorer.

#### Projetos mais complexos

Nossos projetos aqui na empresa costumam ser divididos em inúmeras soluções do Visual Studio para evitar a bagunça que seria (foi) ter que abrir uma solução de 10^24324 projetos. O problema é que, se abrir um Visual Studio já pesa, imagine abrir cinco de uma vez.

Por isso mesmo que, aproveitando que agora tenho uma linha de comando personalizada com o ambiente de compilação, faço uso da compilação de soluções em modo _console_ que o **devenv** (a IDE do Visual Studio) oferece:

    
    devenv meu-solution-do-coracao.sln /build Debug
    devenv meu-project-do-coracao.vcproj /build Release

<blockquote>

> 
> #### Dica para programadores profissionais
> 
_Além de ser rápido, pode ser usado em __builds automatizados, coisa que já fazemos. O que quer dizer que podemos matar os itens 2 e 3 do [teste do Joel](http://brazil.joelonsoftware.com/Articles/TheJoelTest.html), nos deixando um passo mais próximo do purgatório._</blockquote>

#### Depuração

<blockquote>_Tudo bem, mas eu preciso depurar o código! Você não quer que eu use o [NTSD](http://www.codeproject.com/debug/cdbntsd.asp), ou quer?_</blockquote>

Sabe que não é uma má idéia?

Porém, se você prefere algo mais amigável, mais ainda que o [WinDbg](http://www.codeproject.com/debug/windbg_part1.asp), você pode iniciar o depurador do Visual Studio por linha de comando:

    
    vsjitdebugger notepad.exe
    vsjitdebugger -p meu-pid-do-coracao

Daí não tem jeito: você economiza no _start_, mas o Visual Studio vai acabar subindo. Ou um ou outro.

[![VSJitDebugger](/images/d2AlZoy.png)](/images/vsjitdebugger.png)

Por isso eu recomendo aprender a usar o WinDbg ou até o NTSD. Quer dizer, é muito melhor do que esperar por uma versão mais _light_ do Visual Studio no próximo ano.
