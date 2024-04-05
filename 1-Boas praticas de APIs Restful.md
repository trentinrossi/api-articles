# APIs Restful - Boas práticas de implementação

Estudos e pesquisas recentes realizadas pela Mulesoft e pelo Stackoverflow, indicam que um desenvoledor usa em média de 18 a 29 APIs diferentes por semana e cerca de 83% dos desenvolvedores utilizam alguma API de terceiros para melhorar a funcionalidade de seus aplicativos. Todo mes, surgem mais de 2.500 novas APIs.

O mercado de APIs está crescendo rapidamente. Em 2019, o mercado global de APIs foi avaliado em quase US$ 2 bilhões e espera-se que atinja mais de US$ 10 bilhões até 2024.

## Entendendo as requisições HTTP

## Endpoint

É o recurso que está sendo acessado, também chamado de rota, resource ou path. Por exemplo

```bash
http://api.escola.com.br/v1/alunos
```

Neste exemplo, o endpoint é `/alunos`, ou seja, estamos acessando o recurso de alunos.

- Use substantivos no plural para os endpoints, ou seja, `/alunos` em vez de `/aluno`.
- Use letras minúsculas e separar as palavras com hífen, ou seja, `/alunos` em vez de `/Alunos`.
- Use nomes descritivos para os endpoints, ou seja, `/alunos` em vez de `/a`.
- Use sub-recursos para representar relacionamentos, ou seja, `/alunos/123/matriculas` em vez de `/matriculas?aluno=123`.
- Para endpoints com nomes compostos use o padrão Kebab Case, ou seja, `/alunos-matriculados`.

### Base URL

É o domínio da API, também conhecido como Base URL, é o início da URL da requisição, aqui você basicamente vai usar a informação de domínio que se repete em qualquer requisição. Por exemplo

```bash
http://api.escola.com.br
```

No backend, você pode usar a base URL para configurar a conexão com o servidor, ou seja, você pode definir a base URL como uma variável de ambiente e usar essa variável para fazer as requisições. Por exemplo

```javascript
const baseURL = process.env.BASE_URL || "http://api.escola.com.br";
```

Esse domínio também deverá ser devidamente configurado e estar acessível para os clientes que irão consumir a API.

- Use HTTPS em vez de HTTP.
- Use um domínio descritivo, ou seja, use um domínio que descreva o que a API faz, como por exemplo `api.escola.com.br` em vez de `api123.com.br`.
- Use um domínio de nível superior, ou seja, use um domínio que seja de propriedade da empresa, como por exemplo `escola.com.br` em vez de `escola.herokuapp.com`.

### Métodos HTTP

Os métodos HTTP que podem ser usados para acessar o recurso. Por exemplo

- GET: para buscar informações (read)
- POST: para criar um novo recurso (create)
- PUT: para atualizar um recurso (update)
- PUSH: para atualizar um recurso parcialmente (update)
- DELETE: para deletar um recurso (delete)

- O uso correto dos métodos HTTP é uma das práticas mais importantes, pois além de fazer o uso correto dos métodos, onde por si só já estão documentando o que a requisição está fazendo, também ajuda a manter a API organizada e fácil de entender.

## Versionamento da API

O versionamento da API é uma prática comum para garantir a compatibilidade entre as versões da API, ou seja, se você fizer uma alteração na API, você pode criar uma nova versão da API para garantir que os clientes que estão usando a versão antiga da API não sejam afetados, evitando assim as `breaking-changes`. Exemplo:

```bash
http://api.escola.com.br/v1
```

Neste exemplo, a versão da API é `v1`, ou seja, estamos acessando a primeira versão da API.

- A melhor prática é usar a versão da API na URL, ou seja, `/v1` ou `/v1.0`. Desta forma o versionamento fica mais verboso e fácil de identificar.
- Evite usar a versão da API no header da requisição ou no query parameters, pois dificulta a identificação da versão da API.

### Query parameters

São usados para filtrar, ordenar ou paginar os resultados. Por exemplo

```bash
http://api.escola.com.br/v1/alunos?nome=joao&idade=18
```

Como podem ver, a URL é composta por várias partes, separando os parametros por `?` e caso queira adicionar mais de um parametro, basta separar por `&`.

- Use query parameters para filtrar, ordenar ou paginar os resultados, exemplo: `/alunos?nome=joao&idade=18`.
- Não use query parameters para enviar dados sensíveis, como por exemplo o token de autenticação, pois eles podem ser facilmente interceptados.
- Use nomes descritivos para os query parameters, ou seja, `/alunos?nome=joao&idade=18` em vez de `/alunos?n=joao&i=18`.
- Uma boa prática é o uso de Kebab Case para os query parameters, ou seja, `/alunos?nome-completo=joao&idade=18`.
- Caso a quantidade de parametros seja muito grande, é recomendado o uso de um objeto JSON no body da requisição, ao invés de passar todos os parametros na URL. Exemplo: `/alunos` com um body `{"nome": "joao", "idade": 18}`. Neste caso o método da requisição seria `POST` ou `PUT`.

No backend, você pode por exemplo usar os dados capturados para aplicar filtros, ordenação e paginação já na consulta ao banco de dados. Exemplo:

```javascript
const nome = req.query.nome;
const idade = req.query.idade;

const alunos = await Aluno.find({ nome, idade });
```

### Headers

São usados para enviar informações adicionais na requisição, como por exemplo o token de autenticação. Por exemplo

```bash
curl -X GET "http://api.escola.com.br/v1/alunos" -H "Authorization: Bearer 123456"
```

Uma lista completa dos headers pode ser encontrada [aqui](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers).

- Use headers para enviar informações adicionais na requisição, como por exemplo o token de autenticação.
- Use nomes descritivos para os headers, ou seja, `Authorization` em vez de `Auth`.
- Use o header `Content-Type` para especificar o tipo de conteúdo que está sendo enviado no body da requisição, como por exemplo `application/json`.
- Use o header `Accept` para especificar o tipo de conteúdo que é aceito na resposta, como por exemplo `application/json`.

No backend, você pode usar os headers para verificar se o usuário está autenticado, ou seja, você pode verificar se o header `Authorization` está presente na requisição e se o token de autenticação é válido. Isso pode ser feito implementando um middleware que verifica o token de autenticação antes de processar a requisição. Exemplo:

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

É usado para enviar informações adicionais na requisição, como por exemplo os dados de um novo aluno que será criado. Por exemplo

```bash
curl -X POST "http://api.escola.com.br/v1/alunos" -d '{"nome": "João", "idade": 18}'
```

Ele é utilizado somente nos métodos de POST, PUT, PATCH, ou seja, ele contém o dado a ser processado pela API, e por isso ele não é necessário em métodos de leitura de dados.

### Status code

Para facilitar o entendimento das respostas, a API retorna um código de status HTTP, estes códigos são divididos em 5 classes:

- 1xx: Informacional - a requisição foi recebida e está sendo processada pelo servidor (ex: 100 - Continue)
- 2xx: Sucesso - a requisição foi recebida, compreendida e aceita com sucesso (ex: 200 - OK)
- 3xx: Redirecionamento - indica que o cliente precisa realizar mais ações para completar a requisição (ex: 301 - Moved Permanently)
- 4xx: Erro do cliente - indica que o cliente fez uma requisição inválida (ex: 404 - Not Found)
- 5xx: Erro do servidor - indica que o servidor falhou ao atender uma requisição aparentemente válida (ex: 500 - Internal Server Error)

### Autenticação

Existem várias formas de autenticação, as mais comuns são:

- Basic: é o método mais simples de autenticação, onde o usuário e senha são enviados no header da requisição e são codificados em base64.
- Bearer: é um método de autenticação mais seguro, onde o token é enviado no header da requisição e é utilizado para acessar os recursos da API, como por exemplo o token JWT.
- OAuth: é um protocolo de autorização que permite que aplicativos de terceiros acessem os recursos de um usuário sem que ele compartilhe suas credenciais. Ele é muito utilizado em APIs de redes sociais, como por exemplo o Facebook, Twitter, etc.
- API Key: é um método de autenticação onde o usuário envia uma chave de acesso no header da requisição. Este método é muito utilizado em APIs públicas, onde o usuário precisa se cadastrar para obter a chave de acesso.

## Métodos e códigos de status de protocolo HTTP

O HTTP (Hypertext Transfer Protocol) é um protocolo de comunicação utilizado para transferir dados pela internet. Ele é baseado em um modelo cliente-servidor, onde o cliente faz uma requisição e o servidor retorna uma resposta. O HTTP é um protocolo sem estado, ou seja, ele não mantém informações entre requisições. Cada requisição é independente e não tem relação com as requisições anteriores.

### GET

O método GET é usado para buscar informações. Ele é seguro, ou seja, ele não modifica os dados do servidor. Ele é idempotente, ou seja, ele pode ser chamado várias vezes sem alterar o estado do servidor. Ele é cacheable, ou seja, ele pode ser armazenado em cache.

Imagine que você quer buscar as informações de todos os alunos entre as idades de 18 e 25 anos. Você pode fazer uma requisição GET para a URL `http://api.escola.com.br/v1/alunos?idade=18-25`. O servidor irá retornar um código de status 200 e um JSON com as informações dos alunos.

Lembrando que a API teria que fornecer suporte para a filtragem por idade, ou seja, a API teria que ter um endpoint `/alunos` que aceitasse um query parameter `idade` para filtrar os alunos por idade.

Digamos que eu já tenha feito a requisição anterior e agora preciso buscar as informaçoes de um aluno específico. Eu posso fazer uma requisição GET para a URL `http://api.escola.com.br/v1/alunos/123`. O servidor irá retornar um código de status 200 e um JSON com as informações do aluno.

### POST

O método POST é usado para criar um novo recurso. Ele não é seguro, ou seja, ele modifica os dados do servidor. Ele não é idempotente, ou seja, ele não pode ser chamado várias vezes sem alterar o estado do servidor. Ele não é cacheable, ou seja, ele não pode ser armazenado em cache.

Imagine que você quer criar um novo aluno. Você pode fazer uma requisição POST para a URL `http://api.escola.com.br/v1/alunos` com um JSON contendo as informações do aluno. O servidor irá retornar um código de status 201 e um JSON com as informações do aluno. Exemplo;

Body:

```bash
{
  "nome": "João",
  "idade": 18,
  "email": "joao@mail.com",
  "telefone": "123456789"
}
```

### PUT e PATCH

Os métodos PUT e PATCH são usados para atualizar um recurso. Eles não são seguros, ou seja, eles modificam os dados do servidor. Eles são idempotentes, ou seja, eles podem ser chamados várias vezes sem alterar o estado do servidor. Eles não são cacheable, ou seja, eles não podem ser armazenados em cache.

A diferença entre eles é que o método PUT é usado para atualizar um recurso inteiro, ou seja, você precisa enviar todas as informações do recurso no body, enquanto o método PATCH é usado para atualizar um recurso parcialmente, ou seja, você só precisa enviar as informações que serão atualizadas. Exemplo;

PUT:

```bash
{
  "nome": "João da Silva",
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

O método DELETE é usado para deletar um recurso. Ele não é seguro, ou seja, ele modifica os dados do servidor. Ele é idempotente, ou seja, ele pode ser chamado várias vezes sem alterar o estado do servidor. Ele não é cacheable, ou seja, ele não pode ser armazenado em cache.

Imagine que você quer deletar um aluno. Você pode fazer uma requisição DELETE para a URL `http://api.escola.com.br/v1/alunos/123`. O servidor irá retornar um código de status 204 e um JSON vazio.

Lembrando que isso seria uma recomendação do protocolo e que tudo depende da implementação da API.

## Segurança em APIs RESTful

A segurança é um dos aspectos mais importantes de uma API. Sem segurança, a API pode ser vulnerável a ataques, como por exemplo, ataques de injeção de SQL, ataques de cross-site scripting, ataques de negação de serviço, etc.

### Autenticação e autorização

A autenticação é o processo de verificar a identidade do usuário, ou seja, verificar se o usuário é quem ele diz ser. Já a autorização é o processo de verificar se o usuário tem permissão para acessar um recurso, ou seja, o que você pode fazer depois de estar autenticado.

### Basic Authentication

A autenticação básica é o método mais simples de autenticação, onde o usuário e senha são enviados no header da requisição e são codificados em base64. Exemplo:

```bash
curl -X GET "http://api.escola.com.br/v1/alunos" -H "Authorization: Basic dXNlcjpwYXNzd29yZA=="
```

A basic authentication é um método simples de autenticação, onde o usuário e senha são enviados no header da requisição e são codificados em base64. No entanto, é importante ressaltar que esse método não é recomendado como única medida de segurança. É recomendado utilizar outros complementos de segurança, como o certificado HTTPS, para garantir a proteção adequada dos dados. Em alguns casos, é possível substituir o uso de usuário e senha por tokens, mas essa prática é exceção e não é recomendada pela especificação.

### API Keys

API Keys é um método de autenticação que tem como objetivo solucionar algumas limitações do modelo de Basic Authentication. A proposta do método de API Keys é simples: o servidor gera uma chave de acesso exclusiva para o cliente, e o cliente envia essa chave em cada requisição para utilizar a API.

Esse método é fácil e rápido de implementar, e por muito tempo foi amplamente utilizado pelos desenvolvedores. No entanto, ele possui algumas limitações. O principal problema é que esse método é utilizado apenas para autenticação e não para autorização. Em outras palavras, um usuário com uma chave válida tem acesso a todas as operações disponíveis na API.

Além disso, as requisições utilizando API Keys são transmitidas por meio do protocolo HTTP, o que significa que se houver algum ponto vulnerável na rede, a chave pode ser interceptada e utilizada para acessos não autorizados à API.

Normalmente, a chave de acesso é incluída no header da requisição. Por exemplo, suponha que você tenha recebido a seguinte chave do servidor: 'user123456'. Para autenticar-se na API, você deve realizar uma requisição com o seguinte header:

```bash
curl -X GET "http://api.escola.com.br/v1/alunos" -H "Api-Key: user123456"
```

### OAuth

O OAuth encontra-se na sua edição 2.0, não se limitando a ser apenas um meio de autenticação; trata-se, de fato, de um protocolo abrangente com inúmeras diretrizes de segurança. Sua utilidade é notável no contexto de autenticação e autorização, tornando-se, assim, a abordagem preconizada para o ambiente de APIs. Vamos explorar alguns princípios fundamentais do OAuth 2.

- Resource Owner: entidade detentora do recurso, isto é, capaz de gerenciar o acesso a um determinado recurso. Geralmente, corresponde ao próprio usuário.
- Resource Server: plataforma que detém os recursos e, por conseguinte, processa as solicitações.
- Authorization Server: sistema responsável por gerar tokens de acesso, viabilizando que o cliente acesse os recursos autorizados pelo proprietário de recursos.
- Client: aplicação que solicita acesso a um recurso protegido em nome do proprietário do recurso.

Para aprofundar a compreensão, suponhamos que você tenha desenvolvido uma aplicação que utiliza informações do usuário do Facebook. Vamos simular um fluxo básico de autenticação via OAuth 2.0:

1. O usuário acessa seu site ou aplicativo, onde encontra um botão "Integrar ao Facebook". Seu site ou aplicativo atua como o cliente (client).
2. Ao clicar no botão, o usuário é redirecionado para a tela de login do Facebook (servidor de autorização).
3. Após o usuário fornecer suas credenciais, o Facebook fornece um código de autorização para o cliente.
4. Em seguida, o cliente solicita a autorização dos recursos (endpoints da API do Facebook) ao proprietário de recursos (que é o próprio usuário), enviando o código de autorização recebido anteriormente.
5. O servidor de autorização verifica a validade do código de autorização e, se positivo, gera um token de acesso para retornar ao cliente.
6. Por fim, com o token de acesso e a devida autorização para acessar os recursos, o cliente, a cada requisição, envia solicitações ao servidor de recursos (API do Facebook), que responde com os dados protegidos.

Além dos tópicos mencionados sobre a especificação do OAuth, existem outros aspectos relevantes para implementação:

**OpenID Connect:** Esta é uma extensão da funcionalidade de autenticação do OAuth. Enquanto o OAuth foi concebido para autorização, o OpenID Connect é um complemento valioso para reforçar a segurança das APIs. Ele desempenha um papel fundamental ao verificar a verdadeira identidade do usuário.

**JWT (JSON Web Token):** Trata-se de um formato de token seguro que utiliza JSON como base, oferecendo uma maneira eficiente e segura de transmitir informações entre partes.

**SAML (Security Assertion Markup Language):** O SAML é um padrão aberto que possibilita que provedores de identidade (IDP) transmitam credenciais de autorização para provedores de serviços (SP). Em outras palavras, os usuários podem utilizar as mesmas credenciais para acessar diferentes aplicações. O SAML é especialmente útil na implementação de Single Sign-On (SSO), proporcionando aos usuários a conveniência de fazer login uma única vez e navegar por todas as aplicações compatíveis.

Embora o SAML ofereça benefícios no contexto do SSO, é importante notar que seu uso pode ser mais limitado em cenários atuais de desenvolvimento web devido à comunicação baseada em XML, considerada por alguns como uma abordagem ultrapassada para as práticas atuais. Portanto, sua aplicabilidade pode ser mais relevante em situações em que o SSO é uma exigência primordial.

## Boas práticas de segurança

- Use HTTPS em vez de HTTP, pois o HTTPS é mais seguro e protege os dados transmitidos entre o cliente e o servidor.
- Mantenha simples a sua implementação e pense na experiência do desenvolvedor, quanto mais complexo for o processo de autenticação, mais difícil será para os desenvolvedores utilizarem a sua API, perdendo o engajaento da comunidade.
- Não exponha informações sensíveis na URL, como por exemplo o token de autenticação, pois elas podem ser facilmente interceptadas.
- Considere o uso do OAuth 2.0 para autenticação e autorização, pois ele é um protocolo abrangente com inúmeras diretrizes de segurança.
- Utilize rate limiting para limitar o número de requisições que um cliente pode fazer em um determinado período de tempo, evitando assim ataques de negação de serviço.
- Para situações mais críticas, considere o uso de timestamps customizados no header das requisições, onde o server pode verificar se a requisição está próxima a um período de tempo esperado, evitando assim ataques básicos de replay e força bruta.
- Valide o parâmetros da requisição, sempre valide os parâmetros da requisição antes de entrar na lógica de negócio, caso possua algum parâmetro indesejado, retorne imediatamente um erro do tipo 400 por exemplo, fornecendo detalhes do motivo da rejeição, pois pode ser um idicio de ataque malicioso.
- Utilize uma ferramenta de API Gateway, como por exemplo o Amazon API Gateway, que fornece recursos de segurança, monitoramento e gerenciamento de tráfego para as APIs.

## Paginação, ordenação e filtragem

A paginação, ordenação e filtragem são práticas comuns em APIs RESTful para lidar com grandes volumes de dados. Elas permitem que os clientes solicitem apenas os dados necessários, evitando assim o envio de grandes volumes de dados pela rede.
Devem ser levadas em consideração a experiência do desenvolvedor bem como o aproveitamento inteligente da estrutura dos dados, isso ajuda muito na escalabilidade horizontal e vertical de uma API.

### Paginação

- Cursor-based: é um método de paginação que utiliza um cursor para marcar a posição do último registro retornado. O cursor é um ponteiro para um registro específico no conjunto de dados. O cliente envia o cursor na requisição e o servidor retorna os registros após o cursor. Este método é útil para conjuntos de dados que são atualizados frequentemente, pois ele garante que os registros não sejam duplicados ou perdidos.

```bash
http://api.escola.com.br/v1/alunos?matricula_from=123&matricula_to=456
```

Note que os parâmetros `matricula_from` e `matricula_to` são os cursores que delimitam o intervalo de registros que serão retornados. Claro que o nome dos parametros podem variar de acordo com a necessidade e da implementação da API.

- Offset-based: é um método de paginação que utiliza um offset e um limite para marcar a posição do primeiro registro retornado e o número de registros que serão retornados. O cliente envia o offset e o limite na requisição e o servidor retorna os registros a partir do offset e com o limite especificado. Este método é útil para conjuntos de dados que não são atualizados frequentemente, pois ele pode retornar registros duplicados ou perdidos.

```bash
http://api.escola.com.br/v1/alunos?offset=10&limit=10
```

- Page-based: é um método de paginação que utiliza um número de página e um tamanho de página para marcar a posição da página e o número de registros que serão retornados. O cliente envia o número da página e o tamanho da página na requisição e o servidor retorna os registros da página especificada com o tamanho especificado. Este método é útil para conjuntos de dados que são atualizados frequentemente, pois ele garante que os registros não sejam duplicados ou perdidos.

```bash
http://api.escola.com.br/v1/alunos?page=2&size=10
```

### Ordenação

A ordenação é uma prática comum em APIs RESTful para ordenar os registros de acordo com um ou mais campos. Ela permite que os clientes solicitem os registros em uma ordem específica, como por exemplo, ordem alfabética, ordem numérica, etc.

```bash
http://api.escola.com.br/v1/alunos?sort=nome,idade
```

Outra forma de ordenação é utilizando o sinal de `-`, `asc` ou `desc` para controlar a forma de ordenação.

```bash
http://api.escola.com.br/v1/alunos?sort=nome:desc,idade
```

### Boas práticas

- Use offset-based ou page-based para paginação, pois eles são mais fáceis de entender e implementar do que cursor-based.
- Defina alguns valores default para o offset e o limit, além de limitar os valores caso nenhum seja informado, também protege a API de ataques de negação de serviço.
- Não exagere na quantidade de parametros na query, pois isso pode dificultar a leitura e entendimento da requisição.

## Políticas em APIs RESTful

As políticas em APIs RESTful são regras que definem como os clientes podem acessar os recursos da API. Elas são usadas para controlar o acesso aos recursos, limitar o número de requisições que um cliente pode fazer em um determinado período de tempo, autenticar e autorizar os clientes, etc.

### Throttling e Rate limiting

Embora ambos os termos sejam usados para limitar o número de requisições que um cliente pode fazer em um determinado período de tempo, eles têm significados diferentes.

- **Rate limiting**: técnica utilizada para limitar o número de requisições que um cliente pode fazer em um determinado período de tempo. Ela é usada para proteger a API de ataques de negação de serviço, como por exemplo, ataques de força bruta, ataques de scraping, etc.

Exemplo de implementação que limita o número de requisições para 1000 requisições por hora:

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

- **Throttling**: Usada para limitar o delay entre uma requisição e outra, ou seja, caso o Rate limit seja definido para no máximo 1000 requisições por hora, o que impede o cliente de fazer essas 1000 requisições dentro de 1 segundo? É ai que entra o Throttling.

Exemplo de implementação que limita o delay entre uma requisição e outra para 1 segundo:

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

Importante destacar que ambas as técnicas são complementares e podem ser utilizadas/parametrizadas por rotas, usuários, IPs, etc. Além disso, elas são indispensáveis para ajudar na escalabilidade e segurança da API.

### API Quota

Utilizada em uma estratégia mais comercial, onde você pode vender pacotes de requisições para os clientes, ou seja, você pode vender um pacote de 1000 requisições por mês para um cliente, e um pacote de 5000 requisições por mês para outro cliente em determinada API ou domínio.

Note que agora o período de tempo é maior, onde normalmente é mensal pois é mais comum em contratos comerciais, mas pode ser configurado para qualquer período de tempo, funciona como uma conta de celular onde você tem um pacote de dados para usar durante o mês, e caso você ultrapasse o limite, você pode ser cobrado por requisição adicional por exemplo.

É importante ressaltar que API Quota pode ser combinada com as técnicas de Rate limiting e Throttling, oferecendo diferentes opções de pacotes para os clientes conforme suas necessidades, tornando seu produto mais competitivo.

### API Burst

Utilizado quando você possui uma arquitetura elástica, onde você pode aumentar a capacidade de processamento da API conforme a demanda, ou seja, sua configuração de rate-limit é de 10 requisições por minuto, porém um cliente envia 20 requisições de uma vez, se sua API está com capacidade ociosa no
servidor, você processa todas as requisições em alta performance e retorna ao client.

Essa técnica é muito utilizada em APIs de streaming, onde o cliente pode solicitar um grande volume de dados de uma vez, e a API precisa ter a capacidade de processar essas requisições rapidamente.

Diversos framworks e plataformas oferecem suporte nativo para essas técnicas, como por exemplo o Amazon API Gateway, que oferece suporte para rate limiting, throttling, API quota, API burst, etc.

## Performance em APIs RESTful

A performance é um dos aspectos mais importantes de uma API. Sem performance, a API pode ser lenta e não responsiva, o que pode afetar a experiência do usuário e a eficiência da aplicação. Neste capítulo, iremos abordar algumas práticas para melhorar a performance de uma API.

### Cache

O cache é uma técnica utilizada para armazenar temporariamente os dados em um local de fácil acesso, como por exemplo a memória RAM, o disco rígido, etc. Se tratando de APIs, ele é usado para reduzir o tempo de resposta da API, evitando assim a necessidade de fazer requisições repetidas ao servidor.

A implementação de um cache não é simples, mas em contra partida, é uma das técnicas mais eficazes para melhorar a performance de uma API, por exemplo:

- Reduz a latência da rede, pois os dados são armazenados localmente.
- Reduz a carga do servidor, evitando a necessidade de fazer requisições repetidas ao servidor.
- Reduz o tempo de resposta da API.

Os caches podem ser do tipo `private` onde armazena as informações dos servidores para utilização de um único usuário ou `shared` onde os dados armazenados são compartilhados entre todos os clientes.

Podem ser implementados através dos browsers, servidores de proxy, CDNs, load balancer, etc.

O controle de cache se baseia nas especificações HTTP (RFC) e é feito através dos headers `Cache-Control`, `Expires`, `ETag`, `Last-Modified`, etc.

### Cache do lado do cliente

O header `Cache-Control` é o mais recomendado para controlar o cache do lado do cliente, pois ele permite que o servidor especifique como os dados devem ser armazenados em cache.

- _No caching_

Neste exemplo, você estará instruindo o navegador a não armazenar cache nem na requisição e nem na resposta.
Toda vez que uma requisicao for feita, o navegador irá buscar e fazer o download dos dados do servidor novamente.

```bash
Cache-Control: no-store
```

- _Cache sempre atualizado_

Com essa diretiva `no-cache`, o navegador sempre irá enviar a requisição ao servidor para verificar se o cache está atualizado, caso esteja, serão devolvidos os dados quem estão em cache.

```bash
Cache-Control: no-cache
```

- _Caches publicos e privados_

A diretiva `public` permite que o cache seja armazenado em cache tanto no servidor quanto no cliente, para que sejam compartilhados entre os usuários, enquanto a diretiva `private` permite que o cache seja armazenado apenas no cliente.

```bash
Cache-Control: public
```

```bash
Cache-Control: private
```

- _Expiração do cache_

A diretiva `max-age` especifica o tempo em segundos que o cache pode ser armazenado no cliente, ou seja, após o tempo especificado, o cache será considerado inválido e o navegador irá buscar os dados no servidor novamente.

```bash
Cache-Control: max-age=3600
```

- _Validação do cache_

A diretiva `must-revalidate` indica que o tempo de expiração, indicado por `max-age`, `s-maxage` ou por `Expires`, deve ser obrigatoriamente obedecido. Nesse caso, o conteúdo obsoleto obrigatoriamente não pode ser entregue ao cliente, e o navegador pode usar um conteúdo obsoleto somente se ocorrer uma falha na rede

```bash
Cache-Control: must-revalidate
```

Se você quiser exigir uma revalidação do cache a cada vez que o cliente fizer uma requisição, você pode usar a diretiva `no-cache` em conjunto com a diretiva `must-revalidate`.

```bash
Cache-Control: no-cache, must-revalidate
```

Neste exemplo, `no-cache` garante que o recurso não seja servido a partir do cache sem revalidação, e `must-revalidate` assegura que o cliente deve revalidar o recurso com o servidor antes de utilizá-lo após sua expiração.

Essas diretivas são úteis para garantir que o cliente sempre tenha a versão mais recente e válida do recurso, minimizando o uso de dados obsoletos armazenados em cache.

- _Entity Tag (ETag)_

O ETag é um identificador único que o servidor gera para cada recurso. Ele é usado para verificar se o recurso foi modificado desde a última requisição. O cliente envia o ETag na requisição e o servidor verifica se o ETag é o mesmo que o ETag do recurso. Se o ETag for o mesmo, o servidor retorna um código de status 304 e um JSON vazio, indicando que o recurso não foi modificado. Se o ETag for diferente, o servidor retorna o recurso com um código de status 200.

É um dos mais eficientes mecanismos de cache, pois ele permite que o servidor envie apenas o cabeçalho de resposta, sem o corpo da resposta, caso o recurso não tenha sido modificado.

```bash
ETag: "123456789"
```

### Cache do lado do servidor

Enquanto o cache do lado do cliente é usado para armazenar temporariamente os dados no navegador do cliente, o cache do lado do servidor é usado para armazenar temporariamente os dados no servidor da API.

- _Load balancer_

Em cenários e ambientes de alta demanda, é comum o uso de load balancer para distribuir a capacidade de processamento das requisições e tráfego de rede, com isso você torna a sua API mais escalável, resiliente e com alta disponibilidade. Caso um dos servidores falhar, o load balancer redireciona as requisições para outro servidor disponível. Por sua vez, o load balancing não tem uma funcionalidade padrão de cache, mas pode ser configurado para fazer cache de requisições, por exemplo, o Amazon Elastic Load Balancing (ELB) oferece suporte para cache de requisições, ou você pode utilizar a funcionalidade de armazenamento da sessão.

Como os cabeçalhos HTTP são stateless, ou seja, não mantém estado, o load balancer pode ser configurado para armazenar temporariamente os dados das requisições, e por isso pode ser utilizado como um cache do lado do servidor.

Existem diversos algoritimos de balanceamento de carga, as principais implementações são feitas através de configurações no proprio Web server, como por exemplo o Nginx, Apache, etc.

- _Proxy reverso_

É uma interface publica que fica entre o cliente e o servidor, funciona como um intermediador. A principais vantagens de se utilizar um proxy reverso são:

- Segurança: o proxy reverso pode ser configurado para proteger o servidor de ataques, como por exemplo, ataques de negação de serviço, ataques de injeção de SQL, ataques de cross-site scripting, etc.
- Cache: o proxy reverso pode ser configurado para armazenar temporariamente os dados das requisições, evitando assim a necessidade de fazer requisições repetidas ao servidor.
- Performance: Funcionalidades nativas de compressão de dados trafegados em cache, como ele atua como intermediador de requisições, ele consegue tratar os dados de forma inteligente antes de retornar para o client.

- _Gateway_

Funciona como um proxy reverso, porém com funcionalidades mais avançadas, como por exemplo, autenticação, autorização, rate limiting, throttling, API quota, API burst, etc, além de ser também a interface de acesso das suas APIs para a internet.

É o padrão de arquitetura para APIs modernas, onde você pode centralizar o controle de acesso, monitoramento, segurança, cache, em um único ponto de acesso. O Amazon API Gateway é um exemplo de gateway que oferece suporte para cache de requisições.

Também existem outros mecanismos de armazenamento de dados de alta peformance, como Redis e Memcached.

- _CDN_

O CDN (Content Delivery Network) é uma rede de servidores distribuídos geograficamente que armazena temporariamente os dados das requisições. Ele é usado para reduzir a latência da rede e reduzir a carga do servidor, como esse armazenamento é geograficamente distribuído, ele consegue redirecionar as requisições do cliente para um servidor mais próximo, reduzindo assim o tempo de resposta da API.

Essa técninca pode ser utilizada para Web de forma geral, não limita somente para o mundo de APIs. Alguns players do mercado são Cloudflare, Akamai, Amazon CloudFront, etc.

### Compressão de dados

O padrão REST permite que fiversos formatos de dados sejam utilizados, como por exemplo, JSON, XML, HTML, etc. Em todos eles é possível comprimir a mensagem (payload) antes de ser enviada, gerando uma melhoria de performance na API.

- _Accept-Encoding_

O header `Accept-Encoding` é usado para especificar os tipos de compressão no lado do cliente. Ele pode ser usado para especificar a compressão `gzip`, `deflate`, `br`, etc. Sendo o `gzip` o mais comum e amplamente suportado.

```bash
Accept-Encoding: gzip, deflate, br
```

- _Content-Encoding_

Nesse caso o servidor que irá comprimir a mensagem antes de enviar para o cliente, o header `Content-Encoding` é usado para retornar ao cliente o indicativo de qual compressão foi utilizada.

```bash
200 OK
Content-Type: application/json
Content-Encoding: gzip
```

Caso o `Accept-Encoding` informado pelo cliente não for reconhecido pelo servidor, ele pode retornar um código de status 406 - Not Acceptable e um JSON vazio, indicando que a compressão não é suportada.

Se for um formato conhecido, mas o servidor ainda não tiver implementado, então ele deverá retornar um código de status 415 - Unsupported Media Type.

> Você sabia? A maioria dos browsers automaticamente fazem a requisição utilizando essas técnicas de compressão de dados.

