# Redu JavaScript Client

Esta biblioteca é um cliente open-source para a API REST do
[Redu](http://www.redu.com.br). Sua intenção é facilitar a criação de aplicações
integradas ao Redu e que funcionam dentro do navegador.

Como todo o acesso a dados do usuário depende de uma autorização explícita do mesmo,
antes de qualquer coisa é necessário chamar a função ``init`` informando o ID
da sua aplicação.

## Configuração

```js
  var config = { client_id : 'XXXXX' };
  var client = redu.client.init(config); // Bloqueia até a autorizar

  client.me(function(me){
    console.log("Quem eu sou?");
    console.log(me);
  });
```

A função ``init`` irá redirecionar o navegador para a página de autorização do
Redu e redireciona-lo de volta para a página da aplicação. O segundo redirecionamento
é chamado de *callback* e conterá um *token* que será enviado pelo redu-js
nas requisições posteriores. Este token serve para identificar tanto o
usuário quanto a aplicação que está realizando uma determinada requisição.

Caso o usuário já tenha autorizado a aplicação, o token será mantido o navegador
e será utilizado pelo redu-js posteriormente.

## Timeline

Para acessar a timeline do usuário basta chamar a função timeline.
```js
  client.me.timeline(function(logs){
    for(var i = 0; i < logs.length; i++)
      console.log(logs[i]);
  });
```

### Filtragem e paginação

A função timeline aceita um objeto de opções. É possível, por exemplo, utilizar
os filtros por tipo de posts (``{ type:'Log' }``) bem como a
paginação (``{ page : 2 }``). Para visualizar os posts do usuário é necessário
fazer o seguinte:

```js
  // Todos os Logs da página 2
  client.me.timeline({ type : 'Log', page : 2 },function(logs){
    for(var i = 0; i < logs.length; i++)
      console.log(logs[i]);
  });
```

Lembre-se que para cada página são retornados 25 posts. Caso seja necessário
varrer todos os posts do usuário incremente o valor da página:

```js
  var pageIdx = 1;

  client.me.timeline({ type : 'Log', page : pageIdx }, function(logs){
    console.log(logs);

    if(logs.length !== 0) {
      pageIdx += 1;
      client.me.timeline( { type : 'Log', page : pageIdx });
    }
  });
```

Para mais informações sobre as opções aceitas pela função veja a [documentação](http://developers.redu.com.br)
da API REST.

## Criação de Post

Para criar um post do tipo *Activity* em numa disciplina, basta fazer o seguinte:

```js
  client.me.space({ id : '23' }, function(space){
    space.statuses('post', { text : 'Lorem lipsum' }, function(post){
      console.log("Você criou o post:");
      console.log(post);
    });
  });
```

Perceba que é necessário informar um parâmetro adicional informando o método
HTTP utilizado (neste caso ``POST``).

### Resposta

```js
  client.me.status({ id : 2233 }, function(status){
    status.answers('post', { text : 'Lorem answer' }, function(answer){
      console.log("Você criou a resposta:");
      console.log(answer);
    })
  });
```

## Remoção de Post

```js
  client.me.status('delete' { id : 2233 }, function(status){
    consle.log("O post " + status['text'] + " foi removido" );
  });
```

## Mais informações
Consulte a [documentação](http://developers.redu.com.br) da API REST. Ou entre
em contato através do e-mail contato [at] redu.com.br.
