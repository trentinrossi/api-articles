# APIs Restful - Boas pr�ticas de implementa��o

Estudos e pesquisas recentes realizadas pela Mulesoft e pelo Stackoverflow, indicam que um desenvoledor usa em m�dia de 18 a 29 APIs diferentes por semana e cerca de 83% dos desenvolvedores utilizam alguma API de terceiros para melhorar a funcionalidade de seus aplicativos. Todo mes, surgem mais de 2.500 novas APIs.

O mercado de APIs est� crescendo rapidamente. Em 2019, o mercado global de APIs foi avaliado em quase US$ 2 bilh�es e espera-se que atinja mais de US$ 10 bilh�es at� 2024.

## Entendendo as requisi��es HTTP

## Endpoint

� o recurso que est� sendo acessado, tamb�m chamado de rota, resource ou path. Por exemplo

```bash
http://api.escola.com.br/v1/alunos
```

Neste exemplo, o endpoint � `/alunos`, ou seja, estamos acessando o recurso de alunos.

- Use substantivos no plural para os endpoints, ou seja, `/alunos` em vez de `/aluno`.
- Use letras min�sculas e separar as palavras com h�fen, ou seja, `/alunos` em vez de `/Alunos`.
- Use nomes descritivos para os endpoints, ou seja, `/alunos` em vez de `/a`.
- Use sub-recursos para representar relacionamentos, ou seja, `/alunos/123/matriculas` em vez de `/matriculas?aluno=123`.
- Para endpoints com nomes compostos use o padr�o Kebab Case, ou seja, `/alunos-matriculados`.

### Base URL

� o dom�nio da API, tamb�m conhecido como Base URL, � o in�cio da URL da requisi��o, aqui voc� basicamente vai usar a informa��o de dom�nio que se repete em qualquer requisi��o. Por exemplo

```bash
http://api.escola.com.br
```

No backend, voc� pode usar a base URL para configurar a conex�o com o servidor, ou seja, voc� pode definir a base URL como uma vari�vel de ambiente e usar essa vari�vel para fazer as requisi��es. Por exemplo

```javascript
const baseURL = process.env.BASE_URL || "http://api.escola.com.br";
```

Esse dom�nio tamb�m dever� ser devidamente configurado e estar acess�vel para os clientes que ir�o consumir a API.

- Use HTTPS em vez de HTTP.
- Use um dom�nio descritivo, ou seja, use um dom�nio que descreva o que a API faz, como por exemplo `api.escola.com.br` em vez de `api123.com.br`.
- Use um dom�nio de n�vel superior, ou seja, use um dom�nio que seja de propriedade da empresa, como por exemplo `escola.com.br` em vez de `escola.herokuapp.com`.

### M�todos HTTP

Os m�todos HTTP que podem ser usados para acessar o recurso. Por exemplo

- GET: para buscar informa��es (read)
- POST: para criar um novo recurso (create)
- PUT: para atualizar um recurso (update)
- PUSH: para atualizar um recurso parcialmente (update)
- DELETE: para deletar um recurso (delete)

- O uso correto dos m�todos HTTP � uma das pr�ticas mais importantes, pois al�m de fazer o uso correto dos m�todos, onde por si s� j� est�o documentando o que a requisi��o est� fazendo, tamb�m ajuda a manter a API organizada e f�cil de entender.

## Versionamento da API

O versionamento da API � uma pr�tica comum para garantir a compatibilidade entre as vers�es da API, ou seja, se voc� fizer uma altera��o na API, voc� pode criar uma nova vers�o da API para garantir que os clientes que est�o usando a vers�o antiga da API n�o sejam afetados, evitando assim as `breaking-changes`. Exemplo:

```bash
http://api.escola.com.br/v1
```

Neste exemplo, a vers�o da API � `v1`, ou seja, estamos acessando a primeira vers�o da API.

- A melhor pr�tica � usar a vers�o da API na URL, ou seja, `/v1` ou `/v1.0`. Desta forma o versionamento fica mais verboso e f�cil de identificar.
- Evite usar a vers�o da API no header da requisi��o ou no query parameters, pois dificulta a identifica��o da vers�o da API.

### Query parameters

S�o usados para filtrar, ordenar ou paginar os resultados. Por exemplo

```bash
http://api.escola.com.br/v1/alunos?nome=joao&idade=18
```

Como podem ver, a URL � composta por v�rias partes, separando os parametros por `?` e caso queira adicionar mais de um parametro, basta separar por `&`.

- Use query parameters para filtrar, ordenar ou paginar os resultados, exemplo: `/alunos?nome=joao&idade=18`.
- N�o use query parameters para enviar dados sens�veis, como por exemplo o token de autentica��o, pois eles podem ser facilmente interceptados.
- Use nomes descritivos para os query parameters, ou seja, `/alunos?nome=joao&idade=18` em vez de `/alunos?n=joao&i=18`.
- Uma boa pr�tica � o uso de Kebab Case para os query parameters, ou seja, `/alunos?nome-completo=joao&idade=18`.
- Caso a quantidade de parametros seja muito grande, � recomendado o uso de um objeto JSON no body da requisi��o, ao inv�s de passar todos os parametros na URL. Exemplo: `/alunos` com um body `{"nome": "joao", "idade": 18}`. Neste caso o m�todo da requisi��o seria `POST` ou `PUT`.

No backend, voc� pode por exemplo usar os dados capturados para aplicar filtros, ordena��o e pagina��o j� na consulta ao banco de dados. Exemplo:

```javascript
const nome = req.query.nome;
const idade = req.query.idade;

const alunos = await Aluno.find({ nome, idade });
```

### Headers

S�o usados para enviar informa��es adicionais na requisi��o, como por exemplo o token de autentica��o. Por exemplo

```bash
curl -X GET "http://api.escola.com.br/v1/alunos" -H "Authorization: Bearer 123456"
```

Uma lista completa dos headers pode ser encontrada [aqui](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers).

- Use headers para enviar informa��es adicionais na requisi��o, como por exemplo o token de autentica��o.
- Use nomes descritivos para os headers, ou seja, `Authorization` em vez de `Auth`.
- Use o header `Content-Type` para especificar o tipo de conte�do que est� sendo enviado no body da requisi��o, como por exemplo `application/json`.
- Use o header `Accept` para especificar o tipo de conte�do que � aceito na resposta, como por exemplo `application/json`.

No backend, voc� pode usar os headers para verificar se o usu�rio est� autenticado, ou seja, voc� pode verificar se o header `Authorization` est� presente na requisi��o e se o token de autentica��o � v�lido. Isso pode ser feito implementando um middleware que verifica o token de autentica��o antes de processar a requisi��o. Exemplo:

```javascript
const jwt = require("jsonwebtoken");

const verifyToken = (req, res, next) => {
  const token = req.headers.authorization;

  if (!token) {
    return res.status(401).json({ message: "Token not found" });
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    return res.status(401).json({ message: "Invalid token" });
  }
};
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

### Autentica��o

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

### Autentica��o e autoriza��o

A autentica��o � o processo de verificar a identidade do usu�rio, ou seja, verificar se o usu�rio � quem ele diz ser. J� a autoriza��o � o processo de verificar se o usu�rio tem permiss�o para acessar um recurso, ou seja, o que voc� pode fazer depois de estar autenticado.

### Basic Authentication

A autentica��o b�sica � o m�todo mais simples de autentica��o, onde o usu�rio e senha s�o enviados no header da requisi��o e s�o codificados em base64. Exemplo:

```bash
curl -X GET "http://api.escola.com.br/v1/alunos" -H "Authorization: Basic dXNlcjpwYXNzd29yZA=="
```

A basic authentication � um m�todo simples de autentica��o, onde o usu�rio e senha s�o enviados no header da requisi��o e s�o codificados em base64. No entanto, � importante ressaltar que esse m�todo n�o � recomendado como �nica medida de seguran�a. � recomendado utilizar outros complementos de seguran�a, como o certificado HTTPS, para garantir a prote��o adequada dos dados. Em alguns casos, � poss�vel substituir o uso de usu�rio e senha por tokens, mas essa pr�tica � exce��o e n�o � recomendada pela especifica��o.

### API Keys

API Keys � um m�todo de autentica��o que tem como objetivo solucionar algumas limita��es do modelo de Basic Authentication. A proposta do m�todo de API Keys � simples: o servidor gera uma chave de acesso exclusiva para o cliente, e o cliente envia essa chave em cada requisi��o para utilizar a API.

Esse m�todo � f�cil e r�pido de implementar, e por muito tempo foi amplamente utilizado pelos desenvolvedores. No entanto, ele possui algumas limita��es. O principal problema � que esse m�todo � utilizado apenas para autentica��o e n�o para autoriza��o. Em outras palavras, um usu�rio com uma chave v�lida tem acesso a todas as opera��es dispon�veis na API.

Al�m disso, as requisi��es utilizando API Keys s�o transmitidas por meio do protocolo HTTP, o que significa que se houver algum ponto vulner�vel na rede, a chave pode ser interceptada e utilizada para acessos n�o autorizados � API.

Normalmente, a chave de acesso � inclu�da no header da requisi��o. Por exemplo, suponha que voc� tenha recebido a seguinte chave do servidor: 'user123456'. Para autenticar-se na API, voc� deve realizar uma requisi��o com o seguinte header:

```bash
curl -X GET "http://api.escola.com.br/v1/alunos" -H "Api-Key: user123456"
```

### OAuth

O OAuth encontra-se na sua edi��o 2.0, n�o se limitando a ser apenas um meio de autentica��o; trata-se, de fato, de um protocolo abrangente com in�meras diretrizes de seguran�a. Sua utilidade � not�vel no contexto de autentica��o e autoriza��o, tornando-se, assim, a abordagem preconizada para o ambiente de APIs. Vamos explorar alguns princ�pios fundamentais do OAuth 2.

- Resource Owner: entidade detentora do recurso, isto �, capaz de gerenciar o acesso a um determinado recurso. Geralmente, corresponde ao pr�prio usu�rio.
- Resource Server: plataforma que det�m os recursos e, por conseguinte, processa as solicita��es.
- Authorization Server: sistema respons�vel por gerar tokens de acesso, viabilizando que o cliente acesse os recursos autorizados pelo propriet�rio de recursos.
- Client: aplica��o que solicita acesso a um recurso protegido em nome do propriet�rio do recurso.

Para aprofundar a compreens�o, suponhamos que voc� tenha desenvolvido uma aplica��o que utiliza informa��es do usu�rio do Facebook. Vamos simular um fluxo b�sico de autentica��o via OAuth 2.0:

1. O usu�rio acessa seu site ou aplicativo, onde encontra um bot�o "Integrar ao Facebook". Seu site ou aplicativo atua como o cliente (client).
2. Ao clicar no bot�o, o usu�rio � redirecionado para a tela de login do Facebook (servidor de autoriza��o).
3. Ap�s o usu�rio fornecer suas credenciais, o Facebook fornece um c�digo de autoriza��o para o cliente.
4. Em seguida, o cliente solicita a autoriza��o dos recursos (endpoints da API do Facebook) ao propriet�rio de recursos (que � o pr�prio usu�rio), enviando o c�digo de autoriza��o recebido anteriormente.
5. O servidor de autoriza��o verifica a validade do c�digo de autoriza��o e, se positivo, gera um token de acesso para retornar ao cliente.
6. Por fim, com o token de acesso e a devida autoriza��o para acessar os recursos, o cliente, a cada requisi��o, envia solicita��es ao servidor de recursos (API do Facebook), que responde com os dados protegidos.

Al�m dos t�picos mencionados sobre a especifica��o do OAuth, existem outros aspectos relevantes para implementa��o:

**OpenID Connect:** Esta � uma extens�o da funcionalidade de autentica��o do OAuth. Enquanto o OAuth foi concebido para autoriza��o, o OpenID Connect � um complemento valioso para refor�ar a seguran�a das APIs. Ele desempenha um papel fundamental ao verificar a verdadeira identidade do usu�rio.

**JWT (JSON Web Token):** Trata-se de um formato de token seguro que utiliza JSON como base, oferecendo uma maneira eficiente e segura de transmitir informa��es entre partes.

**SAML (Security Assertion Markup Language):** O SAML � um padr�o aberto que possibilita que provedores de identidade (IDP) transmitam credenciais de autoriza��o para provedores de servi�os (SP). Em outras palavras, os usu�rios podem utilizar as mesmas credenciais para acessar diferentes aplica��es. O SAML � especialmente �til na implementa��o de Single Sign-On (SSO), proporcionando aos usu�rios a conveni�ncia de fazer login uma �nica vez e navegar por todas as aplica��es compat�veis.

Embora o SAML ofere�a benef�cios no contexto do SSO, � importante notar que seu uso pode ser mais limitado em cen�rios atuais de desenvolvimento web devido � comunica��o baseada em XML, considerada por alguns como uma abordagem ultrapassada para as pr�ticas atuais. Portanto, sua aplicabilidade pode ser mais relevante em situa��es em que o SSO � uma exig�ncia primordial.

## Boas pr�ticas de seguran�a

- Use HTTPS em vez de HTTP, pois o HTTPS � mais seguro e protege os dados transmitidos entre o cliente e o servidor.
- Mantenha simples a sua implementa��o e pense na experi�ncia do desenvolvedor, quanto mais complexo for o processo de autentica��o, mais dif�cil ser� para os desenvolvedores utilizarem a sua API, perdendo o engajaento da comunidade.
- N�o exponha informa��es sens�veis na URL, como por exemplo o token de autentica��o, pois elas podem ser facilmente interceptadas.
- Considere o uso do OAuth 2.0 para autentica��o e autoriza��o, pois ele � um protocolo abrangente com in�meras diretrizes de seguran�a.
- Utilize rate limiting para limitar o n�mero de requisi��es que um cliente pode fazer em um determinado per�odo de tempo, evitando assim ataques de nega��o de servi�o.
- Para situa��es mais cr�ticas, considere o uso de timestamps customizados no header das requisi��es, onde o server pode verificar se a requisi��o est� pr�xima a um per�odo de tempo esperado, evitando assim ataques b�sicos de replay e for�a bruta.
- Valide o par�metros da requisi��o, sempre valide os par�metros da requisi��o antes de entrar na l�gica de neg�cio, caso possua algum par�metro indesejado, retorne imediatamente um erro do tipo 400 por exemplo, fornecendo detalhes do motivo da rejei��o, pois pode ser um idicio de ataque malicioso.
- Utilize uma ferramenta de API Gateway, como por exemplo o Amazon API Gateway, que fornece recursos de seguran�a, monitoramento e gerenciamento de tr�fego para as APIs.

## Pagina��o, ordena��o e filtragem

A pagina��o, ordena��o e filtragem s�o pr�ticas comuns em APIs RESTful para lidar com grandes volumes de dados. Elas permitem que os clientes solicitem apenas os dados necess�rios, evitando assim o envio de grandes volumes de dados pela rede.
Devem ser levadas em considera��o a experi�ncia do desenvolvedor bem como o aproveitamento inteligente da estrutura dos dados, isso ajuda muito na escalabilidade horizontal e vertical de uma API.

### Pagina��o

- Cursor-based: � um m�todo de pagina��o que utiliza um cursor para marcar a posi��o do �ltimo registro retornado. O cursor � um ponteiro para um registro espec�fico no conjunto de dados. O cliente envia o cursor na requisi��o e o servidor retorna os registros ap�s o cursor. Este m�todo � �til para conjuntos de dados que s�o atualizados frequentemente, pois ele garante que os registros n�o sejam duplicados ou perdidos.

```bash
http://api.escola.com.br/v1/alunos?matricula_from=123&matricula_to=456
```

Note que os par�metros `matricula_from` e `matricula_to` s�o os cursores que delimitam o intervalo de registros que ser�o retornados. Claro que o nome dos parametros podem variar de acordo com a necessidade e da implementa��o da API.

- Offset-based: � um m�todo de pagina��o que utiliza um offset e um limite para marcar a posi��o do primeiro registro retornado e o n�mero de registros que ser�o retornados. O cliente envia o offset e o limite na requisi��o e o servidor retorna os registros a partir do offset e com o limite especificado. Este m�todo � �til para conjuntos de dados que n�o s�o atualizados frequentemente, pois ele pode retornar registros duplicados ou perdidos.

```bash
http://api.escola.com.br/v1/alunos?offset=10&limit=10
```

- Page-based: � um m�todo de pagina��o que utiliza um n�mero de p�gina e um tamanho de p�gina para marcar a posi��o da p�gina e o n�mero de registros que ser�o retornados. O cliente envia o n�mero da p�gina e o tamanho da p�gina na requisi��o e o servidor retorna os registros da p�gina especificada com o tamanho especificado. Este m�todo � �til para conjuntos de dados que s�o atualizados frequentemente, pois ele garante que os registros n�o sejam duplicados ou perdidos.

```bash
http://api.escola.com.br/v1/alunos?page=2&size=10
```

### Ordena��o

A ordena��o � uma pr�tica comum em APIs RESTful para ordenar os registros de acordo com um ou mais campos. Ela permite que os clientes solicitem os registros em uma ordem espec�fica, como por exemplo, ordem alfab�tica, ordem num�rica, etc.

```bash
http://api.escola.com.br/v1/alunos?sort=nome,idade
```

Outra forma de ordena��o � utilizando o sinal de `-`, `asc` ou `desc` para controlar a forma de ordena��o.

```bash
http://api.escola.com.br/v1/alunos?sort=nome:desc,idade
```

### Boas pr�ticas

- Use offset-based ou page-based para pagina��o, pois eles s�o mais f�ceis de entender e implementar do que cursor-based.
- Defina alguns valores default para o offset e o limit, al�m de limitar os valores caso nenhum seja informado, tamb�m protege a API de ataques de nega��o de servi�o.
- N�o exagere na quantidade de parametros na query, pois isso pode dificultar a leitura e entendimento da requisi��o.

## Pol�ticas em APIs RESTful

As pol�ticas em APIs RESTful s�o regras que definem como os clientes podem acessar os recursos da API. Elas s�o usadas para controlar o acesso aos recursos, limitar o n�mero de requisi��es que um cliente pode fazer em um determinado per�odo de tempo, autenticar e autorizar os clientes, etc.

### Throttling e Rate limiting

Embora ambos os termos sejam usados para limitar o n�mero de requisi��es que um cliente pode fazer em um determinado per�odo de tempo, eles t�m significados diferentes.

- **Rate limiting**: t�cnica utilizada para limitar o n�mero de requisi��es que um cliente pode fazer em um determinado per�odo de tempo. Ela � usada para proteger a API de ataques de nega��o de servi�o, como por exemplo, ataques de for�a bruta, ataques de scraping, etc.

Exemplo de implementa��o que limita o n�mero de requisi��es para 1000 requisi��es por hora:

```javascript
const rateLimit = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 1000, // limit each IP to 1000 requests per windowMs
  message: "Too many requests from this IP, please try again after an hour",
});

//  apply to all requests
app.use(limiter);
```

- **Throttling**: Usada para limitar o delay entre uma requisi��o e outra, ou seja, caso o Rate limit seja definido para no m�ximo 1000 requisi��es por hora, o que impede o cliente de fazer essas 1000 requisi��es dentro de 1 segundo? � ai que entra o Throttling.

Exemplo de implementa��o que limita o delay entre uma requisi��o e outra para 1 segundo:

```javascript
const slowDown = require("express-slow-down");

const speedLimiter = slowDown({
  windowMs: 60 * 60 * 1000, // 1 hour
  delayAfter: 1, // allow 1 request per windowMs
  delayMs: 1000, // 1 second delay
});

//  apply to all requests
app.use(speedLimiter);
```

Importante destacar que ambas as t�cnicas s�o complementares e podem ser utilizadas/parametrizadas por rotas, usu�rios, IPs, etc. Al�m disso, elas s�o indispens�veis para ajudar na escalabilidade e seguran�a da API.

### API Quota

Utilizada em uma estrat�gia mais comercial, onde voc� pode vender pacotes de requisi��es para os clientes, ou seja, voc� pode vender um pacote de 1000 requisi��es por m�s para um cliente, e um pacote de 5000 requisi��es por m�s para outro cliente em determinada API ou dom�nio.

Note que agora o per�odo de tempo � maior, onde normalmente � mensal pois � mais comum em contratos comerciais, mas pode ser configurado para qualquer per�odo de tempo, funciona como uma conta de celular onde voc� tem um pacote de dados para usar durante o m�s, e caso voc� ultrapasse o limite, voc� pode ser cobrado por requisi��o adicional por exemplo.

� importante ressaltar que API Quota pode ser combinada com as t�cnicas de Rate limiting e Throttling, oferecendo diferentes op��es de pacotes para os clientes conforme suas necessidades, tornando seu produto mais competitivo.

### API Burst

Utilizado quando voc� possui uma arquitetura el�stica, onde voc� pode aumentar a capacidade de processamento da API conforme a demanda, ou seja, sua configura��o de rate-limit � de 10 requisi��es por minuto, por�m um cliente envia 20 requisi��es de uma vez, se sua API est� com capacidade ociosa no
servidor, voc� processa todas as requisi��es em alta performance e retorna ao client.

Essa t�cnica � muito utilizada em APIs de streaming, onde o cliente pode solicitar um grande volume de dados de uma vez, e a API precisa ter a capacidade de processar essas requisi��es rapidamente.

Diversos framworks e plataformas oferecem suporte nativo para essas t�cnicas, como por exemplo o Amazon API Gateway, que oferece suporte para rate limiting, throttling, API quota, API burst, etc.

## Performance em APIs RESTful

A performance � um dos aspectos mais importantes de uma API. Sem performance, a API pode ser lenta e n�o responsiva, o que pode afetar a experi�ncia do usu�rio e a efici�ncia da aplica��o. Neste cap�tulo, iremos abordar algumas pr�ticas para melhorar a performance de uma API.

### Cache

O cache � uma t�cnica utilizada para armazenar temporariamente os dados em um local de f�cil acesso, como por exemplo a mem�ria RAM, o disco r�gido, etc. Se tratando de APIs, ele � usado para reduzir o tempo de resposta da API, evitando assim a necessidade de fazer requisi��es repetidas ao servidor.

A implementa��o de um cache n�o � simples, mas em contra partida, � uma das t�cnicas mais eficazes para melhorar a performance de uma API, por exemplo:

- Reduz a lat�ncia da rede, pois os dados s�o armazenados localmente.
- Reduz a carga do servidor, evitando a necessidade de fazer requisi��es repetidas ao servidor.
- Reduz o tempo de resposta da API.

Os caches podem ser do tipo `private` onde armazena as informa��es dos servidores para utiliza��o de um �nico usu�rio ou `shared` onde os dados armazenados s�o compartilhados entre todos os clientes.

Podem ser implementados atrav�s dos browsers, servidores de proxy, CDNs, load balancer, etc.

O controle de cache se baseia nas especifica��es HTTP (RFC) e � feito atrav�s dos headers `Cache-Control`, `Expires`, `ETag`, `Last-Modified`, etc.

### Cache do lado do cliente

O header `Cache-Control` � o mais recomendado para controlar o cache do lado do cliente, pois ele permite que o servidor especifique como os dados devem ser armazenados em cache.

- _No caching_

Neste exemplo, voc� estar� instruindo o navegador a n�o armazenar cache nem na requisi��o e nem na resposta.
Toda vez que uma requisicao for feita, o navegador ir� buscar e fazer o download dos dados do servidor novamente.

```bash
Cache-Control: no-store
```

- _Cache sempre atualizado_

Com essa diretiva `no-cache`, o navegador sempre ir� enviar a requisi��o ao servidor para verificar se o cache est� atualizado, caso esteja, ser�o devolvidos os dados quem est�o em cache.

```bash
Cache-Control: no-cache
```

- _Caches publicos e privados_

A diretiva `public` permite que o cache seja armazenado em cache tanto no servidor quanto no cliente, para que sejam compartilhados entre os usu�rios, enquanto a diretiva `private` permite que o cache seja armazenado apenas no cliente.

```bash
Cache-Control: public
```

```bash
Cache-Control: private
```

- _Expira��o do cache_

A diretiva `max-age` especifica o tempo em segundos que o cache pode ser armazenado no cliente, ou seja, ap�s o tempo especificado, o cache ser� considerado inv�lido e o navegador ir� buscar os dados no servidor novamente.

```bash
Cache-Control: max-age=3600
```

- _Valida��o do cache_

A diretiva `must-revalidate` indica que o tempo de expira��o, indicado por `max-age`, `s-maxage` ou por `Expires`, deve ser obrigatoriamente obedecido. Nesse caso, o conte�do obsoleto obrigatoriamente n�o pode ser entregue ao cliente, e o navegador pode usar um conte�do obsoleto somente se ocorrer uma falha na rede

```bash
Cache-Control: must-revalidate
```

Se voc� quiser exigir uma revalida��o do cache a cada vez que o cliente fizer uma requisi��o, voc� pode usar a diretiva `no-cache` em conjunto com a diretiva `must-revalidate`.

```bash
Cache-Control: no-cache, must-revalidate
```

Neste exemplo, `no-cache` garante que o recurso n�o seja servido a partir do cache sem revalida��o, e `must-revalidate` assegura que o cliente deve revalidar o recurso com o servidor antes de utiliz�-lo ap�s sua expira��o.

Essas diretivas s�o �teis para garantir que o cliente sempre tenha a vers�o mais recente e v�lida do recurso, minimizando o uso de dados obsoletos armazenados em cache.

- _Entity Tag (ETag)_

O ETag � um identificador �nico que o servidor gera para cada recurso. Ele � usado para verificar se o recurso foi modificado desde a �ltima requisi��o. O cliente envia o ETag na requisi��o e o servidor verifica se o ETag � o mesmo que o ETag do recurso. Se o ETag for o mesmo, o servidor retorna um c�digo de status 304 e um JSON vazio, indicando que o recurso n�o foi modificado. Se o ETag for diferente, o servidor retorna o recurso com um c�digo de status 200.

� um dos mais eficientes mecanismos de cache, pois ele permite que o servidor envie apenas o cabe�alho de resposta, sem o corpo da resposta, caso o recurso n�o tenha sido modificado.

```bash
ETag: "123456789"
```

### Cache do lado do servidor

Enquanto o cache do lado do cliente � usado para armazenar temporariamente os dados no navegador do cliente, o cache do lado do servidor � usado para armazenar temporariamente os dados no servidor da API.

- _Load balancer_

Em cen�rios e ambientes de alta demanda, � comum o uso de load balancer para distribuir a capacidade de processamento das requisi��es e tr�fego de rede, com isso voc� torna a sua API mais escal�vel, resiliente e com alta disponibilidade. Caso um dos servidores falhar, o load balancer redireciona as requisi��es para outro servidor dispon�vel. Por sua vez, o load balancing n�o tem uma funcionalidade padr�o de cache, mas pode ser configurado para fazer cache de requisi��es, por exemplo, o Amazon Elastic Load Balancing (ELB) oferece suporte para cache de requisi��es, ou voc� pode utilizar a funcionalidade de armazenamento da sess�o.

Como os cabe�alhos HTTP s�o stateless, ou seja, n�o mant�m estado, o load balancer pode ser configurado para armazenar temporariamente os dados das requisi��es, e por isso pode ser utilizado como um cache do lado do servidor.

Existem diversos algoritimos de balanceamento de carga, as principais implementa��es s�o feitas atrav�s de configura��es no proprio Web server, como por exemplo o Nginx, Apache, etc.

- _Proxy reverso_

� uma interface publica que fica entre o cliente e o servidor, funciona como um intermediador. A principais vantagens de se utilizar um proxy reverso s�o:

- Seguran�a: o proxy reverso pode ser configurado para proteger o servidor de ataques, como por exemplo, ataques de nega��o de servi�o, ataques de inje��o de SQL, ataques de cross-site scripting, etc.
- Cache: o proxy reverso pode ser configurado para armazenar temporariamente os dados das requisi��es, evitando assim a necessidade de fazer requisi��es repetidas ao servidor.
- Performance: Funcionalidades nativas de compress�o de dados trafegados em cache, como ele atua como intermediador de requisi��es, ele consegue tratar os dados de forma inteligente antes de retornar para o client.

- _Gateway_

Funciona como um proxy reverso, por�m com funcionalidades mais avan�adas, como por exemplo, autentica��o, autoriza��o, rate limiting, throttling, API quota, API burst, etc, al�m de ser tamb�m a interface de acesso das suas APIs para a internet.

� o padr�o de arquitetura para APIs modernas, onde voc� pode centralizar o controle de acesso, monitoramento, seguran�a, cache, em um �nico ponto de acesso. O Amazon API Gateway � um exemplo de gateway que oferece suporte para cache de requisi��es.

Tamb�m existem outros mecanismos de armazenamento de dados de alta peformance, como Redis e Memcached.

- _CDN_

O CDN (Content Delivery Network) � uma rede de servidores distribu�dos geograficamente que armazena temporariamente os dados das requisi��es. Ele � usado para reduzir a lat�ncia da rede e reduzir a carga do servidor, como esse armazenamento � geograficamente distribu�do, ele consegue redirecionar as requisi��es do cliente para um servidor mais pr�ximo, reduzindo assim o tempo de resposta da API.

Essa t�cninca pode ser utilizada para Web de forma geral, n�o limita somente para o mundo de APIs. Alguns players do mercado s�o Cloudflare, Akamai, Amazon CloudFront, etc.

### Compress�o de dados

O padr�o REST permite que fiversos formatos de dados sejam utilizados, como por exemplo, JSON, XML, HTML, etc. Em todos eles � poss�vel comprimir a mensagem (payload) antes de ser enviada, gerando uma melhoria de performance na API.

- _Accept-Encoding_

O header `Accept-Encoding` � usado para especificar os tipos de compress�o no lado do cliente. Ele pode ser usado para especificar a compress�o `gzip`, `deflate`, `br`, etc. Sendo o `gzip` o mais comum e amplamente suportado.

```bash
Accept-Encoding: gzip, deflate, br
```

- _Content-Encoding_

Nesse caso o servidor que ir� comprimir a mensagem antes de enviar para o cliente, o header `Content-Encoding` � usado para retornar ao cliente o indicativo de qual compress�o foi utilizada.

```bash
200 OK
Content-Type: application/json
Content-Encoding: gzip
```

Caso o `Accept-Encoding` informado pelo cliente n�o for reconhecido pelo servidor, ele pode retornar um c�digo de status 406 - Not Acceptable e um JSON vazio, indicando que a compress�o n�o � suportada.

Se for um formato conhecido, mas o servidor ainda n�o tiver implementado, ent�o ele dever� retornar um c�digo de status 415 - Unsupported Media Type.

> Voc� sabia? A maioria dos browsers automaticamente fazem a requisi��o utilizando essas t�cnicas de compress�o de dados.

