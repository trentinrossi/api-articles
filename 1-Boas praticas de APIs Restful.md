# APIs Restful - Boas pr�ticas de implementa��o

## Algumas estat�sticas importantes

Um desenvoledor usa em m�dia 18 APIs diferentes por semana e 60% dos desenvolvedores usam APIs de terceiros para melhorar a funcionalidade de seus aplicativos. Todo ano, surgem mais de 15.000 novas APIs.

O mercado de APIs est� crescendo rapidamente. Em 2019, o mercado global de APIs foi avaliado em US$ 5,2 bilh�es e espera-se que atinja US$ 6,2 bilh�es at� 2024.

## O endpoint

Um endpoint � um ponto de acesso a um servi�o web. Ele � definido por um URL e pode ser acessado por meio de um m�todo HTTP, como GET, POST, PUT, DELETE, etc. Porem � importante ressaltar que ele segue a seguinte estrutura:

### Base URL

� o dom�nio da API, tamb�m conhecido como Base URL, � o in�cio da URL da requisi��o, aqui voc� basicamente vai usar a informa��o de dom�nio que se repete em qualquer requisi��o. Por exemplo

```bash
http://api.escola.com.br
```

### Vers�o da API

� uma boa pr�tica incluir a vers�o da API na URL. Isso permite que voc� fa�a altera��es na API sem quebrar as aplica��es que j� est�o usando a vers�o anterior. Por exemplo

```bash
http://api.escola.com.br/v1
```

Tamb�m � poss�vel usar a vers�o da API como um header ou como query parameter na requisi��o, mas � uma pr�tica menos comum.

### Endpoint

� o recurso que est� sendo acessado, tamb�m chamado de rota, resource ou path. Por exemplo

```bash
http://api.escola.com.br/v1/alunos
```

### M�todos HTTP

Os m�todos HTTP que podem ser usados para acessar o recurso. Por exemplo

- GET: para buscar informa��es
- POST: para criar um novo recurso
- PUT: para atualizar um recurso
- PUSH: para atualizar um recurso parcialmente
- DELETE: para deletar um recurso

### Query parameters

S�o usados para filtrar, ordenar ou paginar os resultados. Por exemplo

```bash
http://api.escola.com.br/v1/alunos?nome=joao&idade=18
```

Como podem ver, a URL � composta por v�rias partes, separando os parametros por `?` e caso queira adicionar mais de um parametro, basta separar por `&`.

### Headers

S�o usados para enviar informa��es adicionais na requisi��o, como por exemplo o token de autentica��o. Por exemplo

```bash
curl -X GET "http://api.escola.com.br/v1/alunos" -H "Authorization: Bearer 123456"
```

### Body

� usado para enviar informa��es adicionais na requisi��o, como por exemplo os dados de um novo aluno que ser� criado. Por exemplo

```bash
curl -X POST "http://api.escola.com.br/v1/alunos" -d '{"nome": "Jo�o", "idade": 18}'
```

Ele � utilizado somente nos m�todos de POST, PUT, PATCH, ou seja, ele cont�m o dado a ser processado pela API, e por isso ele n�o � necess�rio em m�todos de leitura de dados.

### Status code

Para facilitar o entendimento das respostas, a API retorna um c�digo de status HTTP, estes c�digos s�o divididos em 5 classes:

- 1xx: Informacional - a requisi��o foi recebida e est� sendo processada pelo servidor (ex: 100 - Continue)
- 2xx: Sucesso - a requisi��o foi recebida, compreendida e aceita com sucesso (ex: 200 - OK)
- 3xx: Redirecionamento - indica que o cliente precisa realizar mais a��es para completar a requisi��o (ex: 301 - Moved Permanently)
- 4xx: Erro do cliente - indica que o cliente fez uma requisi��o inv�lida (ex: 404 - Not Found)
- 5xx: Erro do servidor - indica que o servidor falhou ao atender uma requisi��o aparentemente v�lida (ex: 500 - Internal Server Error)

### Autentica��es

Existem v�rias formas de autentica��o, as mais comuns s�o:

- Basic: � o m�todo mais simples de autentica��o, onde o usu�rio e senha s�o enviados no header da requisi��o e s�o codificados em base64.
- Bearer: � um m�todo de autentica��o mais seguro, onde o token � enviado no header da requisi��o e � utilizado para acessar os recursos da API, como por exemplo o token JWT.
- OAuth: � um protocolo de autoriza��o que permite que aplicativos de terceiros acessem os recursos de um usu�rio sem que ele compartilhe suas credenciais. Ele � muito utilizado em APIs de redes sociais, como por exemplo o Facebook, Twitter, etc.
- API Key: � um m�todo de autentica��o onde o usu�rio envia uma chave de acesso no header da requisi��o. Este m�todo � muito utilizado em APIs p�blicas, onde o usu�rio precisa se cadastrar para obter a chave de acesso.

## M�todos e c�digos de status de protocolo HTTP

O HTTP (Hypertext Transfer Protocol) � um protocolo de comunica��o utilizado para transferir dados pela internet. Ele � baseado em um modelo cliente-servidor, onde o cliente faz uma requisi��o e o servidor retorna uma resposta. O HTTP � um protocolo sem estado, ou seja, ele n�o mant�m informa��es entre requisi��es. Cada requisi��o � independente e n�o tem rela��o com as requisi��es anteriores.

### GET

O m�todo GET � usado para buscar informa��es. Ele � seguro, ou seja, ele n�o modifica os dados do servidor. Ele � idempotente, ou seja, ele pode ser chamado v�rias vezes sem alterar o estado do servidor. Ele � cacheable, ou seja, ele pode ser armazenado em cache.

Imagine que voc� quer buscar as informa��es de todos os alunos entre as idades de 18 e 25 anos. Voc� pode fazer uma requisi��o GET para a URL `http://api.escola.com.br/v1/alunos?idade=18-25`. O servidor ir� retornar um c�digo de status 200 e um JSON com as informa��es dos alunos.

Lembrando que a API teria que fornecer suporte para a filtragem por idade, ou seja, a API teria que ter um endpoint `/alunos` que aceitasse um query parameter `idade` para filtrar os alunos por idade.

Digamos que eu j� tenha feito a requisi��o anterior e agora preciso buscar as informa�oes de um aluno espec�fico. Eu posso fazer uma requisi��o GET para a URL `http://api.escola.com.br/v1/alunos/123`. O servidor ir� retornar um c�digo de status 200 e um JSON com as informa��es do aluno.

### POST

O m�todo POST � usado para criar um novo recurso. Ele n�o � seguro, ou seja, ele modifica os dados do servidor. Ele n�o � idempotente, ou seja, ele n�o pode ser chamado v�rias vezes sem alterar o estado do servidor. Ele n�o � cacheable, ou seja, ele n�o pode ser armazenado em cache.

Imagine que voc� quer criar um novo aluno. Voc� pode fazer uma requisi��o POST para a URL `http://api.escola.com.br/v1/alunos` com um JSON contendo as informa��es do aluno. O servidor ir� retornar um c�digo de status 201 e um JSON com as informa��es do aluno. Exemplo;

Body:

```bash
{
  "nome": "Jo�o",
  "idade": 18,
  "email": "joao@mail.com",
  "telefone": "123456789"
}
```

### PUT e PATCH

Os m�todos PUT e PATCH s�o usados para atualizar um recurso. Eles n�o s�o seguros, ou seja, eles modificam os dados do servidor. Eles s�o idempotentes, ou seja, eles podem ser chamados v�rias vezes sem alterar o estado do servidor. Eles n�o s�o cacheable, ou seja, eles n�o podem ser armazenados em cache.

A diferen�a entre eles � que o m�todo PUT � usado para atualizar um recurso inteiro, ou seja, voc� precisa enviar todas as informa��es do recurso no body, enquanto o m�todo PATCH � usado para atualizar um recurso parcialmente, ou seja, voc� s� precisa enviar as informa��es que ser�o atualizadas. Exemplo;

PUT:

```bash
{
  "nome": "Jo�o da Silva",
  "idade": 19,
  "email": "joao@mail.com",
    "telefone": "123456789"
}
```

PATCH:

```bash
{
  "idade": 19
}
```

### DELETE

O m�todo DELETE � usado para deletar um recurso. Ele n�o � seguro, ou seja, ele modifica os dados do servidor. Ele � idempotente, ou seja, ele pode ser chamado v�rias vezes sem alterar o estado do servidor. Ele n�o � cacheable, ou seja, ele n�o pode ser armazenado em cache.

Imagine que voc� quer deletar um aluno. Voc� pode fazer uma requisi��o DELETE para a URL `http://api.escola.com.br/v1/alunos/123`. O servidor ir� retornar um c�digo de status 204 e um JSON vazio.

Lembrando que isso seria uma recomenda��o do protocolo e que tudo depende da implementa��o da API.

## Seguran�a em APIs RESTful

A seguran�a � um dos aspectos mais importantes de uma API. Sem seguran�a, a API pode ser vulner�vel a ataques, como por exemplo, ataques de inje��o de SQL, ataques de cross-site scripting, ataques de nega��o de servi�o, etc.