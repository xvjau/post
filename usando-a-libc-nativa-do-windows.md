---
date: "2007-11-21"
title: Usando a LIBC nativa do Windows
tags: [ "code" ]
---
Por padrão, todo projeto no Visual Studio depende da LIBC. Isso quer dizer que, mesmo que você não use nem um mísero printf em todos os projetos criados, está atrelado a essa dependência. Em tempos onde fazer um "Hello World" pode custar 56 KB em _Release_ - Visual Studio 2005, configuração padrão sem "_buffer security check_" - vale a pena economizar alguns KBytes que não se vão usar. Principalmente se essa possibilidade existe desde o cavernoso Windows 95.

#### Como fazer um projeto de Hello World

    
  1. Crie um novo projeto console Win32 vazio (_File, New, Project_, blá blá blá)

    
  2. Crie um arquivo CPP no projeto (Project, Add New Item, etc)

    
  3. Crie um código parecido com o código abaixo (parecido == usando apenas LIBC básica)

    
  4. Troque a configuração para _Release_, pois _Debug_ não tem graça (Build, Configuration Manager, Release)

    
  5. Mude a runtime para estática (Project, Properties, C/C++, Code Generation, Multi-threaded)

    
  6. Compile e _link_ (Build, Build Solution)

```
#define _CRT_SECURE_NO_DEPRECATE
#include <stdio.h>
#include <string.h>

int main()
{
	printf("oi, mundo!");
} 

```

Pronto, após esses passos temos um projeto ordinário que compila um executável console ordinário que não depende de runtimes novas com exatos (pelo menos aqui) 57.344 bytes.

Agora a parte divertida =).

#### Como gerar um .LIB de uma DLL de terceiros

Desde o Windows 95, existe uma DLL com a maioria das funções da LIBC disponíveis para link dinâmico. Só que, com o uso padrão do Visual C++, é usada sempre a biblioteca que vem junto com o ambiente, com suas trocentas funções (e conseqüentes _bytes_ enche-lingüiça). Porém, é possível utilizar diretamente a **msvcrt.dll** distribuída no diretório do sistema se criarmos uma LIB de importação para ela.

    
  1. Copie a msvcrt.dll diretamente de um Windows 95 (diretório System) para evitar funções que não existam desde a primeira distribuição

    
  2. Utilizando essa versão da DLL e o _prompt_ de comando do Visual Studio, execute:

    
    REM
    REM Isso gera um arquivo def em estado bruto
    REM
    DUMPBIN /EXPORTS msvcrt.dll > msvcrt.def

    
  3. Utilizando o editor do seu coração, retire as linhas desnecessárias (aquelas do início do comando)

    
  4. Retire as colunas desnecessárias (todas menos a com o nome das funções)

    
  5. Retire os nomes bizarros que você não vai usar (todos os primeiros e que começam com '?')

    
  6. Ainda no ambiente console do VC execute o seguinte comando:

    
    REM
    REM Isso gera um arquivo .lib para importar as funções da DLL
    REM
    LIB /DEF:msvcrt.def

#### Nada nessa mão, nem nessa. Mas no system32...

Ótimo. Geramos a LIB que precisávamos e agora só falta integrar com o projeto. Para isso, mais alguns passos:

    
  1. Copie o **msvcrt.lib** para o diretório do projeto.

    
  2. No projeto, coloque o arquivo na lista de LIBs a serem incluídas (Properties, Linker, Input, Additional Dependencies).

    
  3. Ignore o resto das LIBs colocadas por padrão no projeto (Linker, Input, Ignore All Default Libraries).

    
  4. Ignore as firulas de checagem (C/C++, Code Generation, Buffer Security Check, e Basic Runtime Checks em Debug).

    
  5. Explicite o entry-point para a função main (Linker, Advanced, Entry Point).

    
  6. Compile e linke!

    
    ------ Rebuild All started: Project: NativeC, Configuration: Release Win32 ------
    Deleting intermediate and output files for project 'NativeC', configuration 'Release|Win32'
    Compiling...
    NativeC.cpp
    Linking...
    Generating code
    Finished generating code
    Embedding manifest...
    Build Time 0:00
    Build log was saved at "file://c:ProjectsTempNativeCReleaseBuildLog.htm"
    NativeC - 0 error(s), 0 warning(s)
    ========== Rebuild All: 1 succeeded, 0 failed, 0 skipped ==========
    Size: 2.944 bytes.

E agora o tamanho final de nosso executável passou para espantosos 2KB! Isso a princípio parece ótimo e dá vontade de usar em todos os projetos, mas existe um porém ainda não resolvido: as limitações da falta de um _runtime_. Para isso que existe a próxima seção.

#### _Disclaimer_

_Essa é uma solução bem bobinha que não tem nada a ver com uma solução profissional 100% garantida e com suporte técnico 24 horas. Algumas coisas não vão funcionar, como inicialização de variáveis estáticas, exceções, redirecionamento de entrada/saída, etc. Contudo, para projetos simples e pequenos, isso não deverá ser um problema. No entanto, eu não garanto qualquer coisa que advier de compilações inspiradas neste artigo. _

#### Para quem é viciado por economia de espaço

    
  * [Reduce EXE and DLL Size with LIBCTINY.LIB](http://msdn.microsoft.com/msdnmag/issues/01/01/hood/) - Matt Pietrek

    
  * [Creating the smallest possible PE executable](http://www.phreedom.org/solar/code/tinype/)

