---
date: "2007-08-03"
title: História do Windows - parte 3.0
tags: [ "code" ]
---
**Windows 3.0**

Em 22 de maio de 1990 a versão 3.0 do Windows foi lançada. Foi melhorado o gerenciador de programas e o sistema de ícones, além de um novo gerenciador de arquivos e suporte a 16 cores. Entre as mudanças internas podemos citar a velocidade e a confiabilidade. Como a partir dessa versão apareceram muitos desenvolvedores que passaram a suportar a plataforma, o número de programas disponíveis aumentou, o que conseqüentemente fez com que as vendas alavancassem. Três milhões de cópias foram vendidas apenas no primeiro ano, e assim o Windows se tornou padrão nos computadores domésticos. Quando a versão 3.1 foi lançada, em 6 de abril de 1992, mais três milhões de cópias foram vendidos em apenas dois meses.

[![Windows 3.0 Desktop](/images/Windows_3.0_workspace.png)](/images/Windows_3.0_workspace.png)

**Novas tecnologias**

As fontes [TrueType](http://en.wikipedia.org/wiki/Truetype) foram adicionadas, junto de novas capacidades multimídia. Outro grande avanço foi na área de comunicação entre aplicativos com a implementação da tecnologia [OLE](http://support.microsoft.com/kb/86008) (_Object Linking and Embedding_), que permitiu documentos de diferentes fabricantes serem intercambiados.

Em novembro de 1993 foi lançada a primeira versão que integrou o Windows e a rede de trabalho, o **Windows for Workgroups 3.1**. O suporte a compartilhamento de arquivos e impressoras apareceu a partir daí. Duas aplicações novas também surgiram: Microsoft Mail, cliente de mail para uso em redes, e Schedule+, uma agenda de trabalho.

E, finalmente, agora já é hora de conversarmos sobre...

**Como o sistema de janelas funciona no Windows 3.x**

[![Charles Petzold, em foto do seu site](/images/phBymih.gif)](http://www.charlespetzold.com/)Quem começou a programar para Windows naquela época com certeza deve ter ouvido falar do livro clássico de [Charles Petzold](http://www.charlespetzold.com/), uma das poucas referências naquela época sem internet: [Programming Windows 3.1](http://www.amazon.com/Programming-Windows-3-1-Charles-Petzold/dp/1556152647/ref=sr_11_1/105-0066727-6712432?ie=UTF8&qid=1185296183&sr=11-1). É um livro consideravelmente completo se considerarmos a época em que foi escrito. Vários exemplos estão disponíveis em suas páginas, mas para os que não viveram essa época (como eu) existe a versão eletrônica disponível para [_download_](ftp://ftp.charlespetzold.com/ProgWin30/). Você deve estar se perguntando se todo esse código-fonte serve para alguma coisa hoje em dia. Por incrível que pareça, serve sim. E para demonstrar o conceito de compatibilidade retroativa da Microsoft, iremos utilizar os mesmos exemplos deste livro, sem por nem tirar uma linha de código. Com o devido _copyright_ e respeito merecidos ao autor, é claro =).

Programar interfaces naquela época não era bem o "clicar e arrastar" de hoje em dia. Eram necessários profundos conhecimentos sobre como o sistema operacional se relacionava com o seu programa e vice-versa. Hoje em dia é possível ainda programar como antigamente, já que toda a estrutura continua a mesma. Porém, é algo extremamente contraprodutivo de se fazer com as IDEs modernas que existem e suas barras de controles pré-fabricados e código automático. Faremos da forma mais rústica para entender como as coisas funcionam por baixo dos panos, o que por si só será extremamente produtivo para o nosso conhecimento.

**Classe de janela, janela, função de janela, mensagens e _loop_ de mensagens**

Antes de ser criada uma janela, é necessário **registrar uma classe de janela no sistema**, cuja relação com uma janela é mais ou menos a mesma entre classe e objeto no paradigma de orientação a objetos. Você primeiro define uma classe para sua janela e posteriormente pode criar inúmeras janelas a partir da mesma classe.

    
    WNDCLASS wndclass; //Dados sobre a classe de janela.
    wndclass.style = CS_HREDRAW | CS_VREDRAW;
    wndclass.lpfnWndProc = WndProc; // Função de janela (isso é importante!)
    ...
    wndclass.lpszClassName = szAppName;
    RegisterClass (&wndclass) ; // Registra a classe de janela.

Quando você define uma classe e a registra está dizendo para o sistema qual será sua **função de janela**, i. e., qual será a função responsável por receber as mensagens das janelas criadas.

    
    wndclass.lpfnWndProc = WndProc ; // Função de janela.
    ...
    long FAR PASCAL WndProc (HWND hwnd, WORD message, WORD wParam, LONG lParam)
    {
    switch( message ) // Manipulando as mensagens.
    ...
    }

Uma mensagem é um evento que ocorre relativo à sua janela ou o que está acontecendo ao redor dela no mundo Windows. Por exemplo, as janelas recebem eventos a respeito dos cliques do usuário, redesenho da janela, etc. Quem envia essas mensagens é o próprio Windows, e ele espera uma resposta da sua função de janela. Agora a parte esquisita: quem envia essas mensagens para o Windows é o seu próprio aplicativo!

[![Função de janela](/images/LwBAko9.gif)](/images/LwBAko9.gif)

O aplicativo fica aguardando por eventos em um _loop_ conhecido como **_loop_ de mensagens**. A função do _loop_ basicamente é chamar a função [GetMessage](http://msdn2.microsoft.com/en-us/library/ms644936.aspx) e redirecionar as mensagens obtidas para as respectivas funções de janela.

    
    while( GetMessage (&msg, NULL, 0, 0) )
    {
       TranslateMessage (&msg);
       DispatchMessage (&msg); // Despacha a mensagem para a função de janela.
    }

E aqui está o código completo:

```c
/*--------------------------------------------------------
   HELLOWIN.C -- Displays "Hello, Windows" in client area
                 (c) Charles Petzold, 1990
  --------------------------------------------------------*/

#include <windows.h>

long FAR PASCAL WndProc (HWND, WORD, WORD, LONG) ;

int PASCAL WinMain (HANDLE hInstance, HANDLE hPrevInstance,
                    LPSTR lpszCmdParam, int nCmdShow)
     {
     static char szAppName[] = "HelloWin" ;
     HWND        hwnd ;
     MSG         msg ;
     WNDCLASS    wndclass ;

     if (!hPrevInstance)
          {
          wndclass.style         = CS_HREDRAW | CS_VREDRAW ;
          wndclass.lpfnWndProc   = WndProc ;
          wndclass.cbClsExtra    = 0 ;
          wndclass.cbWndExtra    = 0 ;
          wndclass.hInstance     = hInstance ;
          wndclass.hIcon         = LoadIcon (NULL, IDI_APPLICATION) ;
          wndclass.hCursor       = LoadCursor (NULL, IDC_ARROW) ;
          wndclass.hbrBackground = GetStockObject (WHITE_BRUSH) ;
          wndclass.lpszMenuName  = NULL ;
          wndclass.lpszClassName = szAppName ;

          RegisterClass (&wndclass) ;
	  }

     hwnd = CreateWindow (szAppName,         // window class name
		    "The Hello Program",     // window caption
                    WS_OVERLAPPEDWINDOW,     // window style
                    CW_USEDEFAULT,           // initial x position
                    CW_USEDEFAULT,           // initial y position
                    CW_USEDEFAULT,           // initial x size
                    CW_USEDEFAULT,           // initial y size
                    NULL,                    // parent window handle
                    NULL,                    // window menu handle
                    hInstance,               // program instance handle
		    NULL) ;		     // creation parameters

     ShowWindow (hwnd, nCmdShow) ;
     UpdateWindow (hwnd) ;

     while (GetMessage (&msg, NULL, 0, 0))
          {
          TranslateMessage (&msg) ;
          DispatchMessage (&msg) ;
          }
     return msg.wParam ;
     }

long FAR PASCAL WndProc (HWND hwnd, WORD message, WORD wParam, LONG lParam)
     {
     HDC         hdc ;
     PAINTSTRUCT ps ;
     RECT	 rect ;

     switch (message)
          {
          case WM_PAINT:
	       hdc = BeginPaint (hwnd, &ps) ;

               GetClientRect (hwnd, &rect) ;

	       DrawText (hdc, "Hello, Windows!", -1, &rect,
			 DT_SINGLELINE | DT_CENTER | DT_VCENTER) ;

	       EndPaint (hwnd, &ps) ;
               return 0 ;

          case WM_DESTROY:
               PostQuitMessage (0) ;
               return 0 ;
          }

     return DefWindowProc (hwnd, message, wParam, lParam) ;
     } 

```

Esse exemplo é bem velho, mas compila e funciona até hoje, depois de passados 17 anos:

    
    cl /c hellowin.c
    link hellowin.obj user32.lib gdi32.li
    hellowin.exe

**Travamentos no Windows 16 bits**

O Windows 3.x tinha uma particularidade nefasta: qualquer aplicativo poderia travar o sistema como um todo. Se lembrarmos que o Windows antigamente era multitarefa e não-preemptivo, podemos deduzir que enquanto é executada a função de janela de um aplicativo o sistema aguarda por esse aplicativo indefinidamente. Se o aplicativo trava, ele nunca retorna. Se ele nunca retorna, o sistema fica eternamente esperando pelo retorno da função de janela. Alguns travamentos conseguiam ser resolvidos por interrupção, mas a maioria não. [No próximo capítulo da série](http://www.caloni.com.br/historia-do-windows-parte-40) veremos como os sistemas de 32 bits resolveram esse pequeno problema.

**Problemas para resolver**

O que o resto do código do Petzold faz? Dê uma olhada na documentação do [MSDN](http://msdn.microsoft.com). Ela ainda está disponível, já que todos os aplicativos precisam utilizar essas funções, seja diretamente ou através de imensos [_frameworks_](http://msdn2.microsoft.com/en-us/netframework/default.aspx) de interface com o usuário. E existem pessoas que precisam suportar código-fonte legado.

**Ferramentas para se divertir**

Já que agora você sabe o que são funções de janela, mensagens e afins, por que não ver tudo isso funcionando? O Microsoft Visual Studio possui uma ferramenta muito útil para isso chamada **Spy++ (spyxx.exe)**. Existem também [aplicativos equivalentes](http://www.google.com.br/search?q=spy%2B%2B+site%3Acodeproject.com) (com fonte). Outra ferramenta muito útil, principalmente na hora de desenvolver janelas com controles comuns do Windows, é o [Control Spy](http://msdn2.microsoft.com/en-us/library/ms649774.aspx).

#### Para saber mais

    
  * [Sítio de Charles Petzold](http://www.charlespetzold.com/)

    
  * [Outros artigos sobre a história do windows](http://www.caloni.com.br/search/historia%20do%20windows%20-%20parte)

