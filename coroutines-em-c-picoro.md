---
date: 2019-05-08T22:15:50-03:00
title: "Coroutines Em C: Picoro"
tags: [ "code" ]
desc: "Como corotinas podem ser implementadas em C de maneira portável e minimalista."
---
Tantas linguagens hoje em dia tentando implementar a abstração de corrotinas e inserindo mais camadas de abstração (fibras e cereais)... há duas implementações já no Boost, ambas dependendo de uma biblioteca de contexto de stack que é dependente de arquitetura (programada em Assembly).

E aqui está a linguagem C com sua elegância, minimalismo e a filosofia "just works", por mais ou menos 50 anos.

Estava pesquisando sobre bibliotecas de corrotinas em C e encontrei a [Picoro](http://dotat.at/cgi/~fanf/dotat/~fanf/dotat/git/picoro.git), de Tony Finch. O repositório pode ser baixado [por este link](https://www.chiark.greenend.org.uk/ucgi/~fanf/git/picoro.git/). Três coisas me encantaram nela: 

 1. portabilidade (fácil de testar em qualquer arquitetura).
 1. simplificade (um header e um .c com menos de 200 linhas, e a maioria são comentários).
 1. manutenção (o último commit é de 2010, ou seja, ninguém mais mexeu nela por nove anos).
 
Ela é uma biblioteca feita para resolver o problema mais básico de toda corrotina: troca de contexto. Isso é feito de maneira descentralizada, embora ela inicie com uma corrotina principal: a primeira que constrói uma corrotina. A partir dessa é possível criar outras e dar resume em qualquer uma delas que não tenha terminado.

A linguagem C já implementa troca de contexto através das funções padrão `setjmp` e `longjmp`. Há um tipo dependente de arquitetura, `jmp_buf`, que é usado para guardar o contexto. O salto é feito no estilo da função `fork` do Unix, ou seja, não há inclusão de mais nenhuma sintaxe diferente do usual: é um if que retorna 0 (contexto principal) ou não-0 (estamos em outro contexto).

O picoro organiza tudo isso em torno de uma lista ligada. Aliás, de duas listas ligadas: `running` e `idle`, onde o head de cada uma delas é usado para verificar se há corrotinas paradas ou em execução. Há algumas regras básicas para que tudo funcione. Por exemplo, uma corrotina que já foi executada até o final ou que está bloqueada pela chamada de `resume` não pode ser posta para rodar.

Vamos começar com um exemplo simples: apenas um corrotina que recebe um inteiro e incrementa três vezes. A cada vez que ele incrementa ele devolve o controle de execução via yield. O `main` cria três dessas corrotinas e dá resume em cada uma delas três vezes, finalizando a execução de todas. Ao final, o counter final é de 9.

```c++
#include "..\picoro\picoro.h"
#include <stdio.h>

void* mycoroutine(void* arg)
{
	int* counter = (int*) arg;
	(*counter) += 1;
	yield(arg);
	(*counter) += 1;
	yield(arg);
	(*counter) += 1;
	return arg;
}

int main()
{
    int counter = 0;
	int i;
	coro coroutines[3];
	int maxi = sizeof(coroutines) / sizeof(coro);

	for (i = 0; i < maxi; ++i)
		coroutines[i] = coroutine(mycoroutine);

	for (i = 0; i < maxi; ++i)
		resume(coroutines[i], &counter);
	for (i = 0; i < maxi; ++i)
		resume(coroutines[i], &counter);
	for (i = 0; i < maxi; ++i)
		resume(coroutines[i], &counter);

    printf("final counter: %d\n", counter);
	return 0;
}
```

É importante observar que o uso de troca de contexto pode facilmente consumir a pilha, pois ela está sendo compartilhada com muitas funções em paralelo. Para reservar espaço a `coroutine_start` aloca um array de 16 KB (fixo). Esses detalhes de implementação podem ser alterados, pois a biblioteca é tão mínima e simples de entender que construir qualquer coisa em cima dela é trivial.

