---
date: "2014-07-22"
title: Integrando BitBucket/GitHub com Trello
tags: [ "blog", "git" ]
---
Eu nem acredito que estou escrevendo sobre desenvolvimento web, mas como foi algo que me fez dedicar algumas horas do meu fim-de-semana, e não encontrei facilmente uma solução já feita, acredito que pode ser útil para mais alguém que usa Trello e GitHub (ou BitBucket).

Mas o que é [Trello](http://www.trello.com)? Basicamente é um TodoList feito da maneira mais inteligente possível: uma lista de listas de listas! Os espaços, ou desktops, onde você organiza suas tarefas são chamados de Boards. Em cada board vivem L listas, e em cada lista vivem C cards. Cada card pode conter comentários, histórico de mudanças, labels, checklists, due dates e todas as tranqueiras que geralmente existe em uma lista de tarefas. É um sistema online, desenvolvido pela empresa do Joel Spolsky (o mesmo do excelente blogue de programador [Joel on Software](http://www.joelonsoftware.com/) (ou em [português](http://brazil.joelonsoftware.com/), e que contém algo que eu adoro em sistemas web: atalhos!

[![Atalhos do Trello](/images/747UasT.png)](/images/747UasT.png)

A ideia que tive foi usar os webhooks dos saites de repositórios de fontes para permitir comentar dentro dos cards o commit que foi feito, sua mensagem e o linque para o commit. OK, mas por que não usar o sistema de issues dos já feitos pra isso GitHub e BitBucket? Ele já faz isso muito melhor. De fato. Porém, fica espalhado pelos repositórios, e não é sempre que uma tarefa envolve código (comprar pão, por exemplo). Além do mais, praticamente qualquer serviço desses oferece hooks para a integração de outros projetos/serviços, então se um dia nascer mais um sistema de controle de fonte ou mais um saite que organiza essas tralhas haverá um hook e consequentemente mais uma adaptação do meu código PHP.

E por que PHP? Bom, PHP é uma linguagem fácil de mexer (se parece com C, mas é um script) e praticamente qualquer servidor web do universo, mesmo o mais baratinho, vem com o pacote Apache + PHP (e geralmente uma base MySql). Dessa forma, é uma solução que pode ser implantada fácil e rapidamente.

## Comentando no Trello

Vamos começar pelo mais difícil que o resto vai fácil: comentar pela API do Trello. Sua [API é beta](https://trello.com/docs/), assim como sua documentação, então tive arrancar significado inexistente em seu help, mas acabou funcionando. Como qualquer API web, você precisa de uma chave, segredo e a permissão do usuário. Com essa permissão é possível comentar em todas as boards que esse usuário específico tem acesso.

Pelo menos a parte de [geração de chave/segredo é simples](https://trello.com/1/appKey/generate), tanto que se você clicou nesse linque, já conseguiu gerar uma =).

Depois disso, mesmo nessa página já é possível conseguir uma chave de acesso para o seu usuário.

[![Pedindo autorização para o Trello](/images/oQ97bDI.png)](/images/oQ97bDI.png)

Por fim, para fazer o código que irá comentar dentro de um card no Trello, basta usar dois ou três métodos que lidam com enviar coisas pela web (não me pergunte mais que isso):

```
<?php

$url = 'https://trello.com/1/cards/ID_DO_CARD/actions/comments';

$msg = 'Hello, World!';

$data = array(
        'key' => 'AQUI_VAI_SUA_CHAVE', 
        'token' => 'AQUI_VAI_SEU_TOKEN_DE_ACESSO',
        'text' => $msg
        );

$options = array(
        'http' => array(
            'header'  => "Content-type: application/x-www-form-urlencoded\r\n",
            'method'  => 'POST',
            'content' => http_build_query($data),
            ),
        );

$context  = stream_context_create($options);

$result = file_get_contents($url, false, $context);

?>

```

As informações _AQUI_VAI_SUA_CHAVE_ e _AQUI_VAI_SEU_TOKEN_DE_ACESSO_ você já obteve no linque de geração de key/secret. Já o _ID_DO_CARD_ é algo que depende de em qual lista seu card está, mas felizmente também existe um shortlink único e imutável para cada card no sistema:

[![ID único de um Card](/images/xONdnSw.png)](/images/xONdnSw.png)

Basta usar o ID em Base64-ou-o-que-o-valha no lugar de _ID_DO_CARD_ que já estamos OK. Depois que este código conseguir ser executado, basta ter acesso à internet que ele irá escrever "Hello, World" no cartão referenciado:

[![Hello, World!](/images/rbCigTV.png)](/images/rbCigTV.png)

Muito bem. Primeira parte da missão concluída.

## Terminando com GitHub

Como o [GitHub](http://www.github.com) é um dos serviços de repositório de fontes mais famoso, vamos torná-lo nosso caso de sucesso. Basicamente você deve ir no seu repositório do coração (essa é a parte ruim: se você tem mais de um coração, vai ter que repetir esse mesmo procedimento para todos os outros repositórios dos seus outros corações), Settings, Webhooks & Services.

[![Adicionando um WebHook ao GitHub](/images/4Lph9w6.png)](/images/4Lph9w6.png)

Lembre-se de colocar seu código PHP em um servidor visível na web. Lembre-se também de usar o método de envio urlencoded do payload para simplificar seu tratamento. Para simplificar ainda mais o processo, coloque qualquer coisa no segredo (não validaremos neste post, mas #ficadica de segurança se você não quer que outros acessem seu PHP inadvertidamente).

Pois bem. No código que irá receber o payload do GitHub precisamos de duas coisas: saber qual [a estrutura que vai ser recebida](https://developer.github.com/webhooks/) e _como localizar o id do card onde iremos enviar a informação_. Nesse caso, mais uma vez, para simplificar, vamos procurar pelo próprio linque permanente do cartão na mensagem do commit. Aliás, doS commitS (sendo um push, é provável que o evento seja gerado com diversos commits aninhados).

```
<?php

$pushData = json_decode($_POST['payload']);

foreach( $pushData->commits as $c )
{
    $msg = $c->message;
    $pattern = '#http[s]*://trello.com/c/([A-Za-z0-9]+)#';
    if( preg_match($pattern, $msg, $matches) == 0 )
        continue;

    $url = 'https://trello.com/1/cards/' . $matches[1] . '/actions/comments';
    $msg = $c->message . ' Commit: ' . $c->url;
    $data = array(
            'key' => 'AQUI_VAI_SUA_CHAVE', 
            'token' => 'AQUI_VAI_SEU_TOKEN_DE_ACESSO',
            'text' => $msg
            );

    $options = array(
            'http' => array(
                'header'  => "Content-type: application/x-www-form-urlencoded\r\n",
                'method'  => 'POST',
                'content' => http_build_query($data),
                ),
            );

    $context  = stream_context_create($options);

    $result = file_get_contents($url, false, $context);
}
?>

```

Agora é só testar. Posso pegar esse mesmo artigo e comitá-lo no [repositório do blogue](https://github.com/Caloni/Caloni.com.br) usando o linque único do card da tarefa de escrever este artigo. Ou seja, aqui é Inception na veia, mermão!

[![Comitando o artigo para gerar evento que irá comentar no Trello para continuar este artigo que estou comitando..](/images/NzQPB9o.png)](/images/NzQPB9o.png)

O que vai deixar você perplexo é entender como esse texto está sendo comitado antes mesmo de eu comitar este texto ;).

[![Resultado do meu commit](/images/ZIPNSpV.png)](/images/ZIPNSpV.png)

E o negócio é rápido, viu?

[![E o negócio é rápido, viu?](/images/vVmuKb7.png)](/images/vVmuKb7.png)

## _Adendo: BitBucket_

A única coisa que muda no caso do [BitBucket](http://www.bitbucket.org) é a tela onde deve ser inserido seu webhook (método POST, sempre) e a estrutura JSon que é enviada. De lambuja, eis o que deve ser feito com esse payload:

```
<?php

$bitData = json_decode($_POST["payload"]);

foreach( $bitData->commits as $c )
{
    $msg = $c->message;
    $pattern = '#http[s]*://trello.com/c/([A-Za-z0-9]+)#';
    if( preg_match($pattern, $msg, $matches) == 0 )
        continue;

    $url = 'https://trello.com/1/cards/' . $matches[1] . '/actions/comments';
    $msg = $c->message . ' Commit: ' . $bitData->canon_url . $bitData->repository->absolute_url . 'commits/' . $c->raw_node;
    $data = array(
            'key' => 'AQUI_VAI_SUA_CHAVE', 
            'token' => 'AQUI_VAI_SEU_TOKEN_DE_ACESSO',
            'text' => $msg
            );

    $options = array(
            'http' => array(
                'header'  => "Content-type: application/x-www-form-urlencoded\r\n",
                'method'  => 'POST',
                'content' => http_build_query($data),
                ),
            );

    $context  = stream_context_create($options);

    $result = file_get_contents($url, false, $context);
}
?>

```

