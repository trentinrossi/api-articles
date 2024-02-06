# APIs Restful - Boas práticas de implementação

## Algumas estatísticas importantes

Um desenvoledor usa em média 18 APIs diferentes por semana e 60% dos desenvolvedores usam APIs de terceiros para melhorar a funcionalidade de seus aplicativos. Todo ano, surgem mais de 15.000 novas APIs.

O mercado de APIs está crescendo rapidamente. Em 2019, o mercado global de APIs foi avaliado em US$ 5,2 bilhões e espera-se que atinja US$ 6,2 bilhões até 2024.

## O endpoint

Um endpoint é um ponto de acesso a um serviço web. Ele é definido por um URL e pode ser acessado por meio de um método HTTP, como GET, POST, PUT, DELETE, etc. Porem é importante ressaltar que ele segue a seguinte estrutura:

### Base URL

É o domínio da API, também conhecido como Base URL, é o início da URL da requisição, aqui você basicamente vai usar a informação de domínio que se repete em qualquer requisição. Por exemplo

```bash
http://api.escola.com.br
```

### Versão da API

É uma boa prática incluir a versão da API na URL. Isso permite que você faça alterações na API sem quebrar as aplicações que já estão usando a versão anterior. Por exemplo

```bash
http://api.escola.com.br/v1
```

Também é possível usar a versão da API como um header ou como query parameter na requisição, mas é uma prática menos comum.

### Endpoint

É o recurso que está sendo acessado, também chamado de rota, resource ou path. Por exemplo

```bash
http://api.escola.com.br/v1/alunos
```

### Métodos HTTP

Os métodos HTTP que podem ser usados para acessar o recurso. Por exemplo

- GET: para buscar informações
- POST: para criar um novo recurso
- PUT: para atualizar um recurso
- PUSH: para atualizar um recurso parcialmente
- DELETE: para deletar um recurso

### Query parameters

São usados para filtrar, ordenar ou paginar os resultados. Por exemplo

```bash
http://api.escola.com.br/v1/alunos?nome=joao&idade=18
```

Como podem ver, a URL é composta por várias partes, separando os parametros por `?` e caso queira adicionar mais de um parametro, basta separar por `&`.

### Headers

São usados para enviar informações adicionais na requisição, como por exemplo o token de autenticação. Por exemplo

```bash
curl -X GET "http://api.escola.com.br/v1/alunos" -H "Authorization: Bearer 123456"
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

### Autenticações

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