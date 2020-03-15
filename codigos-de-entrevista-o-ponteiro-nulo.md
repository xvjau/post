---
date: "2008-02-25"
title: Códigos de entrevista - o ponteiro nulo
tags: [ "code" ]
---
<blockquote>_Bom, parece que o "mother-fucker" wordpress ferrou com meu artigo sobre o Houaiss. Enquanto eu choro as pitangas aqui vai um outro artigo um pouco mais simples, mas igualmente interessante._</blockquote>

<blockquote>_"Wanderley, tenho umas sugestões para teu blog.
A primeira:
Que tal analisar o código abaixo e dizer se compila ou não. Se não compilar, explicar porquê não compila. Se compilar, o que acontecerá e por quê."_</blockquote>

O código é o que veremos abaixo:

```
#include <stdio.h>
#include <stdlib.h>

void func()
{
	*(int *)0 = 0;
	return 0;
}

int main(int argc, char **argv)
{
	func();
	return 0;
} 

```

Bem, para testar a compilação basta compilar. Porém, se estivermos em uma entrevista, geralmente não existe nenhum compilador em um raio de uma sala de reunião senão seu próprio cérebro.

E é nessas horas que os entrevistadores testam se você tem um bom cérebro ou um bom currículo.

Por isso, vamos analisar passo a passo cada bloco de código e entender o que pode estar errado. Se não encontrarmos, iremos supor que está tudo certo.

    
    #include <stdio.h>
    #include <stdlib.h>

Dois includes padrões, ultranormal, nada de errado aqui.

```
void func()
{
  *(int *)0 = 0;return 0;
}

```

Duas ressalvas aqui: a primeira quanto ao retorno da função é **void**, porém a função retorna um inteiro. Na linguagem C, isso funciona, no máximo um _warning_ do compilador. Em C++, isso é erro brabo de tipagem.

A segunda ressalva diz respeito à linha obscura, sintaticamente correta, mas cuja semântica iremos guardar para o final, já que ainda falta o main para analisar.a

    
    int main(int argc, char **argv)
    {
        func();
        return 0;
    }

A clássica função inicial, nada de mais aqui. Retorna um int, e de fato retorn. Chama a função **func**, definida acima.

#### A linha obscura

A linha que guardamos para analisar contém uma operação de _casting_, atribuição e deferência, sendo o _casting _executado primeiro, operador unário que é, seguido pelo segundo operador unário, a deferência. Como sempre, a atribuição é uma das últimas. Descomprimida a expressão dessa linha, ficamos com algo parecido com as duas linhas abaixo:

    
    int* p = (int*) 0;
    *p = 0;

Não tem nada de errado em atribuir o valor 0 a um ponteiro, que é equivalente ao define NULL da biblioteca C (e C++). De acordo com a [referência GNU](http://www.gnu.org/software/libc/manual/html_node/Null-Pointer-Constant.html#Null-Pointer-Constant), é recomendado o uso do define, mas nada impede utilizar o 0 "hardcoded".

Porém, estamos escrevendo em um ponteiro nulo, o que com certeza é um comportamento não-definido de conseqüências provavelmente funestas. O ponteiro nulo é um ponteiro inválido que serve apenas para marcar um ponteiro como inválido. Se escrevermos em um endereço inválido, bem, não é preciso ler o padrão para saber o que vai acontecer =)

#### Atualização

<blockquote>_Alguns amigos me avisaram sobre algo muito pertinente: dizer que acessar um ponteiro nulo, portanto **inválido**, é **errado** e **nunca deve ser feito**. Como um ponteiro nulo aponta para um **endereço de memória inválido**, acessá-lo irá **gerar uma exceção no seu sistema operacional** e **fazer seu programa capotar**. Um ponteiro nulo é uma maneira padrão e confiável de marcar o ponteiro como **inválido**, e testar isso facilmente através de um if. Mais uma vez: ponteiros nulos apontando para um endereço de memória **inválido** (o endereço 0) **nunca devem ser acessados**, apenas atribuído a ponteiros._

Em código. Isso pode:

>     
>     int* p = 0; // atribuindo nulo a um ponteiro
>     int* p2 = p; // isso também pode
> 
Isso **não pode**:

>     
>     *p = 15; // <strong>NUNCA</strong> acessar ponteiros nulos
>     int x = *p; // isso também <strong>não pode</strong>, ler de um ponteiro nulo
> 
Dito isso, me sinto melhor =)</blockquote>
