---
date: "2020-04-18"
title: "Mbconf@Home2020: minha palestra sobre Windbg"
tags: [ "blog", "mbconf", "event", "windbg", "debug", "kernel", "vm" ]
desc: "Falei sobre windbg.exe, kd.exe, ntsd.exe, softice e debug da BIOS #sqn."
---
A [MBConf@Home2020](https://conf.mentebinaria.com.br/) foi um sucesso. Parabéns aos organizadores, palestrantes e apoiadores. Eu nunca fui em um evento de tecnologia em que tudo funcionou do começo ao fim. Simplesmente fantástico o nível de qualidade da organização. Fora que trezentas pessoas ficaram em casa e participaram conosco dessa troca de conhecimento =).

Minhas palestra foi a seguinte: dei uma pincelada no que é o WinDbg para os que ainda não conhecem e realizei algumas manobras pouco usuais de depuração, tentando fugir um pouco da rotina do programador e me enfiando no que seriam minhas sessões antigas de hacking ou cracking da época que analisava trojans ou depurava serviços que saíam depois que meu depurador remoto já tinha ido embora. Segue mais ou menos o roteiro e os pontos levantados.

### Instalação e configuração do WinDbg

Hoje em dia o caminho mais fácil é pelo Visual Studio Community, que instala por padrão um Windows SDK. Nessa instalação é possível modificar os itens checando o "Debugging Tools for Windows", que é o pacote que contém o ecossistema do WinDbg.

### Símbolos (PDBs)

Pulei essa parte. Tempo curto e me enrolei um pouco. E não era o caso de ficar focado na rotina de programador.

### Depuração user mode vs Visual Studio

Não fui eu que escrevi o MessageBox... juro. E nesse caso não ter o código-fonte é a rotina do crackudo, que vai ter que explorar no assembly o funcionamento de um programa. Depuramos um que chama MessageBox alterando a mensagem exibida (em 32 bits). Foi legal essa diferença entre Ansi e Unicode que me perdi no começo, pois serviu para exemplificar questões de API que precisam ser conhecidas.

### Depuração kernel mode vs Nothing®

Abordamos o boot do Windows com nt, o uso do kd.exe por baixo dos panos do WinDbg (o DarkMode do WinDbg) e configuramos o cabo. Cabo?

### Cabo serial... cabo??

Cabo virtual, sargento. Usamos a VMWare, pré-configurada após [alguns pesadelos de impressora se metendo no meio do caminho](http://driverentry.com.br/blog/?p=943). Configuramos a porta serial, que é a melhor ever. E apontamos como named pipe para o WinDbg "de fora" conectar. Ou o kd.exe. As linhas abaixo são equivalentes.

    windbg.exe -b -k com:pipe,port=\\.\pipe\com_1,resets=0
    kd.exe -b -k com:pipe,port=\\.\pipe\com_1,resets=0

### Depuração meio calabresa meio Ring0

Para exemplificar a depuração de um serviço bem no início (ou fim) ou o load de processos antes dele existir checamos uma flag na **gflags.exe** da máquina depurada para que quando o notepad.exe subisse o ntsd fosse depurá-lo e passasse o controle para o debug do sistema. E com isso fechamos o círculo sagrado da depuração holística.

### Perguntas

#### Dá pra depurar a BIOS com o WinDbg? E BIOS remota (pela rede)?

Não. Para depurar a BIOS local há o caminho do debug.com (um depurador bem simples da época do Windows 95) ou o [Softice DOS](/debug-da-bios-com-o-softice-16-bits), embora eu me lembre que tive umas dores de cabeça com ele por causa dos conflitos entre interrupções e programas residentes. A depuração estática acaba ganhando nesse quesito, que é basicamente abrir o assembly, papel e caneta. E imaginação.

Já para debug de BIOS em rede. Bem... esse é um nível hackudo. Sei que a Intel tem desenvolvido chips para diagnóstico e obtenção de dados de hardware pela rede antes mesmo do SO estar ligado, mas não cheguei a pesquisar a fundo.

#### Dá para depurar uma DLL?

Sim. Como o Mercês me ajudou a lembrar, existe um rundll32.exe, um executável que já vem no Windows e que pode carregar a DLL para você. Daí tudo que você precisa fazer é colocar o breakpoint das funções exportadas que deseja chamar. Dá para especificar essas funções pelo rundll32.exe também:

    //project.cpp
    void chama_eu()
    {
    	MessageBox(NULL, "Welcome to MBConf@Home2020", "MBConf2020", 0);
    }
    
    ;project.def
    EXPORTS
    chama_eu

    >rundll32.exe project.dll,chama_eu


### Referências

Recomendo sempre o [WinDbg.info](http://windbg.info/doc/1-common-cmds.html) como cheat sheet e docs.microsoft.com em seus artigos "Getting Started with WinDbg (User-Mode)" e "Getting Started with WinDbg (Kernel-Mode)" (sorry, m$, vcs mudam os links demais para eu colocar aqui).

