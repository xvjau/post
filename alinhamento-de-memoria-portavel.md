---
date: "2008-06-18"
title: Alinhamento de memória portável
categories: [ "code" ]
---
Como vimos durante o [seminário CCPP](http://www.caloni.com.br/resultado-do-seminario-ccpp), o alinhamento de memória pode ser problemático durante momentos críticos, como migração de plataforma (16 para 32 bits) e de ambiente (compilador novo). A forma como a memória é alinhada influi diretamente em algoritmos de criptografia, por exemplo, fazendo com que o que funcionava antes não funcione mais sem mexer uma única linha de código. Eu já vi isso. E isso não é bom.

A raiz do problema é que, dependendo do alinhamento usado pelo compilador, o sizeof de uma variável pode mudar de valor, mesmo que o tamanho útil não mude. Por exemplo, vamos supor que temos uma dada estrutura que iremos encriptar:

    
    struct EstruturaQueIremosEncriptar    /* 4 + 31 = 35 bytes */
    {
        int size;        /* 4 bytes  */
        char name[31];   /* 31 bytes */
    };

Se usarmos a construção "sizeof(EstruturaQueIremosEncriptar)", podemos obter o valor 35 caso o alinhamento seja feito em 1 byte, ou podemos obter o valor 40 se o alinhamento estiver configurado em 8 bytes. E é aí que começa o problema.

#### Solução simples

Já pensando nesse problema, os projetistas de vários compiladores suportam uma extensão não-padrão que permite definir, para um dado conjunto de estruturas e variáveis, o alinhamento que deve ser seguido. Isso de cara já resolve o problema, SE sua solução usar apenas compiladores que suportem essa idéia. No Visual C++ essa idéia é traduzida por uma diretiva pragma:

    
    #pragma pack( push, 8 )

    
    struct EstruturaQueIremosEncriptar    /* 4 + 31 = 35 bytes */
    {
        int size;        /* 4 bytes  */
        char name[31];   /* 31 bytes */
    };

    
    #pragma pack( pop )

<blockquote>_A diretiva pragma está definida no padrão C (6.8.6) e C++ (16.6) e seu uso não torna um programa não-padrão. No entanto, o que vai depois da diretiva é dependente da implementação e não é garantido que irá funcionar._</blockquote>

Pronto, nossa estrutura sempre terá 40 bytes ocupados na memória, pois o alinhamento foi forçado em 8 bytes. Resolvido.

#### Solução portável

Existem aqueles compiladores que não suportam essa idéia da mesma forma, ou não suportam de jeito nenhum. Para esses casos, alguns desvios de comportamento são necessários. A grande pergunta é se isso é possível de ser feito de forma 100% padrão.

Pelo que eu pude constatar, existe, sim.

```c
#include <stdio.h>

union TesteAlign
{

	struct Teste
	{
		char buf[13];
		int x;
	}
	t;

	unsigned char align[ (sizeof(struct Teste) % 8) ?
		(sizeof(struct Teste) / 8 + 1) * 8
		: (sizeof(struct Teste))
		];

};

int main()
{
	union TesteAlign tst;

	printf("Alinhamento. Union: %d; Teste: %d; Align: %d.\n",
			sizeof(union TesteAlign), 
			sizeof(struct Teste), 
			sizeof(tst.align) );

	printf("t: %p, align: %p\n", &tst.t, tst.align);

	return 0;
}

 

```

O código acima usa o conceito de união de estruturas (union) para fazer valer um alinhamento na marra (no caso, 8). Para os que não conhecem unions, é necessário uma breve explicação do conceito.

#### O que são uniões

Uma estrutura, como todos sabem, amontoa os seus membros um após o outro na memória. Dessa forma podemos tratar um bloco de memória com um leiaute que definimos com outros tipos:

![struct.gif](/images/ZOSbCwW.gif)

Em uma união, os membros não são amontoados um após o outro. Todos eles começam no mesmo ponto da memória. Eles se sobrescrevem. O tamanho da união sempre é o tamanho do seu maior membro, e não a soma de todos. É um tanto difícil de descrever esse leiaute, mas imagine que você tenha apenas uma posição na memória e queira chamá-la de vários nomes e tamanhos diferentes. Essa é a união.

![union.gif](/images/o2u9ozy.gif)

Como deve ser fácil de imaginar, uma união não tem tanto uso quanto uma estrutura, mas ainda assim faz parte da linguagem. Ela possibilita enxergar a mesma região de memória sob vários ângulos. Podemos descobrir a organização de um inteiro na memória, por exemplo, byte a byte:

    
    int main()
    {
        union {
            int i;
            unsigned char uc[ sizeof(int) ];
        }
        myUnion;

    
       myUnion.i = 0x12345678;

    
       printf("Int: %d.\n", myUnion.i);
        printf("Bytes: %02X %02X %02X %02X.\n", uc[0], uc[1], uc[2], uc[3]);

    
    }

Dependendo se a plataforma onde o programa acima é compilado, a exibição do último printf pode mudar. [Eis o motivo](http://en.wikipedia.org/wiki/Endianness).

#### De volta para a solução

Agora que sabemos o que são uniões fica fácil entender o esquema da solução portável. Eu simplesmente uso a segunda estrutura como uma auxiliar de alinhamento. Com ela do tamanho múltiplo do alinhamento desejado forçamos a união inteira a ter esse alinhamento, independente do tamanho da estrutura útil, a que iremos usar para armazenar dados.

![testealign.gif](/images/ecTWkWD.gif)

Tudo que temos que saber para fazer o alinhamento é o tamanho normal de nosso tipo útil (o Teste). A partir desse valor deduzimos o próximo número que seja múltiplo de 8, através da seguinte construção:

    
    ( sizeof(struct Teste) % 8 ) ?
          (sizeof(struct Teste) / 8 + 1) * 8
        : (sizeof(struct Teste))

Ou seja, se já for múltiplo de 8, é o próprio valor. Se não for, então dividimos por 8 e multiplicamos pelo mesmo valor adicionado de um, o que nos retorna o próximo múltiplo.

#### Tornando as coisas genéricas

É lógico que, como se trata de uma construção onde temos completo domínio dos tipos e valores envolvidos, transformar isso em um _template _é "[pedaço de torta](http://en.wikipedia.org/wiki/Piece_of_cake)".

```cpp
#include <stdio.h>

template<typename Teste, int Align>
union TesteAlignTemplate
{

	Teste t;

	unsigned char align[ (sizeof(Teste) % Align) ?
		(sizeof(Teste) / Align + 1) * Align
		: (sizeof(Teste))
		];

};

struct Teste
{
	char buf[13];
	int x;
};

int main()
{
	typedef TesteAlignTemplate<Teste, 8> TesteAlign;
	TesteAlign tst;

	printf("Alinhamento. Union: %d; Teste: %d; Align: %d.\n",
			sizeof(TesteAlign), 
			sizeof(Teste), 
			sizeof(tst.align) );

	printf("t: %p, align: %p\n", &tst.t, tst.align);

	return 0;
}

 

```

E essa é a melhor parte de descobrir um padrão em um tipo: o _template _nasce quase que naturalmente. A beleza da linguagem floresce.
