# Definições de APIs


**_Menu:_**

* [Hosts no Gateway](#hosts-no-gateway)
* [Sobre a solução](#sobre-a-solução)
* [Como começar](#como-começar)
* [API Contatos](#gestão-de-contatos)
* [API Produtos](#gestão-de-produtos)


### Hosts no Gateway

| Host | Ambiente |
| -------- | -------- |
| url  | Produção   |
| url | Homologação   |
| url  | Desenvolvimento   |


A definição desta API está escrita na notação RAML. 
Para maiores informações sobre a notação [RAML](http://raml.org/)

# Sobre a solução

A nossa solução de documentação de api vai reinventar a experiência do desenvolvedor ao utilizar e implementar uma API XXXX, (*nessa área da documentação você irá informar um pouco sobre a sua solução, qual o objetivo dela, e se ela for muito complexa você pode adicionar uma imagem com o passo a passo de como ela funciona*). Com uma documentação robusta, exemplos de como implementar as API's, informações sobre os parâmetros e exemplos de request e response, entregaremos muito valor não só aos interessados em utilizar a API, mas a todo o ecosistemas de API's. Evoluindo a maneira como desenvolvemos API's.


# Como começar

O objetivo desta documentação é orientar o desenvolvedor sobre como integrar com a solução de um conjunto de API's Itaú. Apresentaremos no decorrer desse documento as informações necessárias para compreender o fluxo dos dados entre as APIs, dicas que auxiliarão no momento da implantação e exemplos.

## Autenticação
Nosso modelo de comunicação atual para o ambiente de desenvolvimento requer a autenticação do parceiro através do fluxo [OAuth2](https://oauth.net/2/). Essa etapa é composta por duas credenciais: o client_id que identifica a aplicação e será usado no nosso parâmetro x-empresa-apikey da APIs e o client_secret que é a senha que pode ser alterada sob demanda, caso necessário. 

## Informações necessárias
Para utilizar as nossas API's você precisará ter algumas informações:

### x-empresa-apikey
O x-empresa-apikey é um parâmetro que será requerido em todas as chamadas das APIs que contemplam essa solução. A informação que deve ser usada nesse campo é o código gerado através da autenticação OAuth 2.0 para o client_id. Para saber como gerar o client_id ou como recuperar as informações de códigos já gerados basta acessar a etapa de ‘autenticação’ desse tutorial.

### x-empresa-flowID
A implementação de um flowID permite identificar qual é a funcionalidade de negócio sendo executada na aplicação. Por exemplo, dentro de uma mesma aplicação, várias telas ou linhas de negócio diferentes podem acessar a mesma API. Quando é identificado que algumas chamadas a uma determinada API estão dando erro, através do flowID, podemos descobrir que as chamadas são de uma tela específica e com isso solucionar o problema mais rápido do que se não tivéssemos essa informação no log. Ter o flowID nos logs também permite extrair algum tipo de métrica através do log.

### x-empresa-correlationID
A implementação de um correlationID permite relacionar uma mesma chamada passando em diversas aplicações/sistemas diferentes, conseguindo fornecer um mapeamento de ponta-a-ponta. Por exemplo, uma aplicação client gera uma chamada à API, neste momento fornecendo o correlationID gerado pelo consumidor, o qual é repassado em todas as camadas/microserviços que a api precisa para realizar determinada funcionalidade. É importante lembrar que o header x-empresa-correlationID deve sempre ser diferente a cada requisição. Recomenda-se mandar um uuid version 4 nesse campo como: a1e64241-7fdb-4d05-a7f6-c44febcdd8d9

## Métodos
Requisições para a API devem seguir os padrões dos [Métodos HTTP](url) dos quais são atualizados pelo time de Governança de API, que segue os padrões de mercado de modelagem de API Rest.


## Desenho dos Fluxos

Os fluxos abaixo representa o fluxo da API de contatos, especificando, quais são as chamadas para cada uma de suas rotas.


<img align="left" src="/images/get-doc-api.png">

*Fluxo da rota de listar os contatos*

<br>
<img align="left" src="/images/patch-doc-api.png">

*Fluxo da rota de atualizar um contato*

<br>
<img align="left" src="/images/delete-doc-api.png">

*Fluxo da rota de deletar um contato*

# Gestão de Contatos

Essa api de exemplo, tem o objetivo de explicar como documentar uma api e suas rotas. É uma api de exemplo de gestão de contatos, onde os contatos (desenvolvedores e parceiros) podem ser listados, editados e excluídos.

### Lista Contatos
#### [GET] ../api_empresa/v1/contatos/listar
A chamada de consulta da lista de contatos, retornará uma lista com todos os contatos (desenvolvedores e parceiros) e todas as suas informações cadastrais, como é possível ver a seguir. 

Abaixo segue uma tabela com todos os parâmetros exigidos por essa rota e uma breve descrição de cada um. Caso você tenha dúvidas sobre os parâmetros x-empresa-apikey, x-empresa-correlationID, acesse o tópico ‘Informações necessárias’ para encontrar mais informações de como consegui-los.

Tabela de parâmetros:

| Parâmetro | Tipo Parâmetro | Tipo Dado | Obrigatório | Descrição |
|---|---|---|---|---|
| `x-empresa-apikey` | header | string | sim | client_id gerado pela autenticação Oauth utilizado para autorizar o consumo de uma aplicação requisitante. |
| `x-empresa-correlationID` | header | string | sim | identificador de correlação utilizado como um agrupar dentro da estrutura de audit trail e que permite relacionar uma mesma chamada passando em diversas aplicações/sistemas diferentes. |
| `status_code` | query | string | não | Campo utilizado para os testes dos casos de exceção. |

 Código de status:

| Código | Descrição |
|---|---|
| `200` | Requisição executada com sucesso (success).|
| `400` | Erros de validação ou os campos informados não existem no sistema.|
| `401` | Dados de acesso inválidos.|
| `404` | Registro pesquisado não encontrado (Not found).|
| `405` | Método não implementado.|
| `410` | Registro pesquisado foi apagado do sistema e não esta mais disponível.|
| `422` | Dados informados estão fora do escopo definido para o campo.|
| `429` | Número máximo de requisições atingido. (*aguarde alguns segundos e tente novamente*)|

+ Request (application/json)

    + Headers

            Authorization: Bearer [access_token]
            x-empresa-apikey:{clientId}
            x-empresa-correlationID:{correlationId}


+ Response 200 (application/json)

          {
              "total": 1,
              "data": [
                {
                  "codigo": "1",
                  "tipo": ["desenvolvedor", "parceiro"],
                  "nome": "Nome do contato",
                  "emails": [],
                  "fones": [],
                  "tags": [],
                  "cidade": "Nome da cidade",
                  "uf": "UF"
                }
              ]
          }

+ Response 401 (application/json)

          {
              "errCode": 401,
              "errMsg": "Não foi possível acessar o sistema. Verifique seu \"access_token\".",
              "errObs": "access_denied",
              "errFields": null,
              "errUrl": "/api_empresa/v1/contatos/listar"
          }


### Edição de informações do contato
#### [PUT] ../api_empresa/v1/contatos/atualizar/{id}
Essa chamada tem como objetivo atualizar qualquer informação de um contato (desenvolvedores e parceiros), é necessário informar o id do contato que deseja atualizar, e todas as suas informações cadastrais no body da requisição, como é possível ver a seguir. 

Abaixo segue uma tabela com todos os parâmetros exigidos por essa rota e uma breve descrição de cada um. Caso você tenha dúvidas sobre os parâmetros x-empresa-apikey, x-empresa-correlationID, acesse o tópico ‘Informações necessárias’ para encontrar mais informações de como consegui-los.

Tabela de parâmetros: 

| Parâmetro | Tipo Parâmetro | Tipo Dado | Obrigatório | Descrição |
|---|---|---|---|---|
| `x-empresa-apikey` | header | string | sim | client_id gerado pela autenticação Oauth utilizado para autorizar o consumo de uma aplicação requisitante. |
| `x-empresa-correlationID` | header | string | sim | identificador de correlação utilizado como um agrupar dentro da estrutura de audit trail e que permite relacionar uma mesma chamada passando em diversas aplicações/sistemas diferentes. |
| `id` | path | string | sim | Id do contato que será atualizado. |
| `status_code` | query | string | não | Campo utilizado para os testes dos casos de exceção. |

 Código de status:

| Código | Descrição |
|---|---|
| `200` | Requisição executada com sucesso (success).|
| `400` | Erros de validação ou os campos informados não existem no sistema.|
| `401` | Dados de acesso inválidos.|
| `404` | Registro pesquisado não encontrado (Not found).|
| `405` | Método não implementado.|
| `410` | Registro pesquisado foi apagado do sistema e não esta mais disponível.|
| `422` | Dados informados estão fora do escopo definido para o campo.|
| `429` | Número máximo de requisições atingido. (*aguarde alguns segundos e tente novamente*)|

+ Request (application/json)

    + Headers

            Authorization: Bearer [access_token]
            x-empresa-apikey:{clientId}
            x-empresa-correlationID:{correlationId}
   
    + Body

            {
              "nome": "Melissa Lobo",
              "cpfcnpj": "12332198712",
              "tipo": [
                "desenvolvedor"
              ],
              "dtNasc": "1993-08-23",
              "emails": [
                "exemplo@example.com.br"
              ],
              "fones": [],
              "cep": 01516101,
              "logradouro": "Avenida do Estado",
              "numero": "5533",
              "complemento": "",
              "bairro": "",
              "uf": "SP",
              "pais": "Brasil",
              "obs": "",
              "tags": []
            }


+ Response 200 (application/json)

    + Headers

            X-RateLimit-Limit: 60
            X-RateLimit-Remaining: 59
            
    + Body

            {
              "nome": "Melissa Lobo",
              "cpfcnpj": "12332198712",
              "tipo": [
                "desenvolvedor"
              ],
              "dtNasc": "1993-08-23",
              "emails": [
                "exemplo@example.com.br"
              ],
              "fones": [],
              "cep": 01516101,
              "logradouro": "Avenida do Estado",
              "numero": "5533",
              "complemento": "",
              "bairro": "",
              "uf": "SP",
              "pais": "Brasil",
              "obs": "",
              "tags": []
            }


+ Response 401 (application/json)

          {
              "errCode": 401,
              "errMsg": "Não foi possível acessar o sistema. Verifique seu \"access_token\".",
              "errObs": "access_denied",
              "errFields": null,
              "errUrl": "/api_empresa/v1/contatos/atualizar"
          }


### Exclusão de um contato
#### [DELETE] ../api_empresa/v1/contatos/deletar/{id}
Essa rota tem como objetivo excluir um contato. Onde para a realização da exclusão será necessário informar o id do mesmo, como é possível ver a seguir. 

Abaixo segue uma tabela com todos os parâmetros exigidos por essa rota e uma breve descrição de cada um. Caso você tenha dúvidas sobre os parâmetros x-empresa-apikey, x-empresa-correlationID, acesse o tópico ‘Informações necessárias’ para encontrar mais informações de como consegui-los.

Tabela de parâmetros:

| Parâmetro | Tipo Parâmetro | Tipo Dado | Obrigatório | Descrição |
|---|---|---|---|---|
| `x-empresa-apikey` | header | string | sim | client_id gerado pela autenticação Oauth utilizado para autorizar o consumo de uma aplicação requisitante. |
| `x-empresa-correlationID` | header | string | sim | identificador de correlação utilizado como um agrupar dentro da estrutura de audit trail e que permite relacionar uma mesma chamada passando em diversas aplicações/sistemas diferentes. |
| `id` | path | string | sim | Id do contato que será excluído. |
| `status_code` | query | string | não | Campo utilizado para os testes dos casos de exceção. |

 Código de status:

| Código | Descrição |
|---|---|
| `200` | Requisição executada com sucesso (success).|
| `400` | Erros de validação ou os campos informados não existem no sistema.|
| `401` | Dados de acesso inválidos.|
| `404` | Registro pesquisado não encontrado (Not found).|
| `405` | Método não implementado.|
| `410` | Registro pesquisado foi apagado do sistema e não esta mais disponível.|
| `422` | Dados informados estão fora do escopo definido para o campo.|
| `429` | Número máximo de requisições atingido. (*aguarde alguns segundos e tente novamente*)|

+ Request (application/json)

    + Headers

            Authorization: Bearer [access_token]
            x-empresa-apikey:{clientId}
            x-empresa-correlationID:{correlationId}


+ Response 200 (application/json)

    + Headers

            X-RateLimit-Limit: 60
            X-RateLimit-Remaining: 59

    + Body

            {
                "code": "200",
                "msg": "Contato com código 397 excluído com sucesso!",
                "obs": null,
                "fields": null
            }

+ Response 401 (application/json)

          {
              "errCode": 401,
              "errMsg": "Não foi possível acessar o sistema. Verifique seu \"access_token\".",
              "errObs": "access_denied",
              "errFields": null,
              "errUrl": "/api_empresa/v1/contatos/deletar"
          }

# Gestão de Produtos
Essa api de exemplo, tem o objetivo de explicar como documentar uma api e suas rotas. É uma api de exemplo de gestão de produtos, onde os produtos podem ser criados, editados e excluídos.

### Inclusão de um novo produto
#### [POST] ../api_empresa/v1/produtos
Essa chamada tem como objetivo criar um novo produto. Onde para a realização da inclusão será necessário informar todas as informações do novo produto no body da requisição, como é possível ver a seguir. 

Abaixo segue uma tabela com todos os parâmetros exigidos por essa rota e uma breve descrição de cada um. Caso você tenha dúvidas sobre os parâmetros x-empresa-apikey, x-empresa-correlationID, acesse o tópico ‘Informações necessárias’ para encontrar mais informações de como consegui-los.

Tabela de parâmetros: 

| Parâmetro | Tipo Parâmetro | Tipo Dado | Obrigatório | Descrição |
|---|---|---|---|---|
| `x-empresa-apikey` | header | string | sim | client_id gerado pela autenticação Oauth utilizado para autorizar o consumo de uma aplicação requisitante. |
| `x-empresa-correlationID` | header | string | sim | identificador de correlação utilizado como um agrupar dentro da estrutura de audit trail e que permite relacionar uma mesma chamada passando em diversas aplicações/sistemas diferentes. |
| `status_code` | query | string | não | Campo utilizado para os testes dos casos de exceção. |

 Código de status:

| Código | Descrição |
|---|---|
| `200` | Requisição executada com sucesso (success).|
| `400` | Erros de validação ou os campos informados não existem no sistema.|
| `401` | Dados de acesso inválidos.|
| `404` | Registro pesquisado não encontrado (Not found).|
| `405` | Método não implementado.|
| `410` | Registro pesquisado foi apagado do sistema e não esta mais disponível.|
| `422` | Dados informados estão fora do escopo definido para o campo.|
| `429` | Número máximo de requisições atingido. (*aguarde alguns segundos e tente novamente*)|


+ Request (application/json)

    + Headers

            Authorization: Bearer [access_token]
            x-empresa-apikey:{clientId}
            x-empresa-correlationID:{correlationId}

    + Body

                {
                  "descricao": "Novo produto",
                  "codigoProprio": "001",
                  "estoque": 100,
                  "estoqueMinimo": 0,
                  "controlarEstoque": true,
                  "precoCusto": 5.00,
                  "precoVenda": 10.37,
                  "tags": ['examplo', 'modelo']
                }

+ Response 200 (application/json)

    + Headers

            X-RateLimit-Limit: 60
            X-RateLimit-Remaining: 59

    + Body

            {
                "codigo": 1,
                "descricao": "Novo produto"
            }

### Edição de informações de um produto
#### [PUT] api_empresa/v1/produtos/{codigo}
Essa chamada tem como objetivo atualizar um produto. Onde para a realização da alteração será necessário informar todas as informações do produto no body da requisição, como é possível ver a seguir. 

Abaixo segue uma tabela com todos os parâmetros exigidos por essa rota e uma breve descrição de cada um. Caso você tenha dúvidas sobre os parâmetros x-empresa-apikey, x-empresa-correlationID, acesse o tópico ‘Informações necessárias’ para encontrar mais informações de como consegui-los.

Tabela de parâmetros: 

| Parâmetro | Tipo Parâmetro | Tipo Dado | Obrigatório | Descrição |
|---|---|---|---|---|
| `x-empresa-apikey` | header | string | sim | client_id gerado pela autenticação Oauth utilizado para autorizar o consumo de uma aplicação requisitante. |
| `x-empresa-correlationID` | header | string | sim | identificador de correlação utilizado como um agrupar dentro da estrutura de audit trail e que permite relacionar uma mesma chamada passando em diversas aplicações/sistemas diferentes. |
| `codigo` | path | string | sim | código do produto que será atualizado. |
| `status_code` | query | string | não | Campo utilizado para os testes dos casos de exceção. |

 Codigo de status:

| Código | Descrição |
|---|---|
| `200` | Requisição executada com sucesso (success).|
| `400` | Erros de validação ou os campos informados não existem no sistema.|
| `401` | Dados de acesso inválidos.|
| `404` | Registro pesquisado não encontrado (Not found).|
| `405` | Método não implementado.|
| `410` | Registro pesquisado foi apagado do sistema e não esta mais disponível.|
| `422` | Dados informados estão fora do escopo definido para o campo.|
| `429` | Número máximo de requisições atingido. (*aguarde alguns segundos e tente novamente*)|

+ Request (application/json)

  + Headers

            Authorization: Bearer [access_token]
            x-empresa-apikey:{clientId}
            x-empresa-correlationID:{correlationId}

  + Parameters
      + codigo (required, number, `1`)


+ Response 200 (application/json)

    + Headers

            X-RateLimit-Limit: 60
            X-RateLimit-Remaining: 58

    + Body

            {
              "descricao": "Novo produto",
              "codigoProprio": "",
              "estoque": 100,
              "estoqueMinimo": 0,
              "controlarEstoque": true,
              "margemLucro": 0.00,
              "precoCusto": 0.0000,
              "precoVenda": 0.0000,
              "tags": []
            }

+ Response 410 (application/json)
 Quando o registro foi apagado do sistema.

    + Body

            {
              "errCode": 410,
              "errMsg": "Registro com código 1 não existe.",
              "errObs": null,
              "errFields": null
            }

+ Response 404 (application/json)
 Quando registro não foi encontrado.

    + Body

            {
              "errCode": 404,
              "errMsg": "Nenhum registro com código 1 encontrado",
              "errObs": null,
              "errFields": null
            }

### Exclusão de um produto
#### [DELETE]  api/v1/produtos/{codigo}
Essa rota tem como objetivo excluir um produto. Onde para a realização da exclusão será necessário informar o id do mesmo, como é possível ver a seguir. 

Abaixo segue uma tabela com todos os parâmetros exigidos por essa rota e uma breve descrição de cada um. Caso você tenha dúvidas sobre os parâmetros x-empresa-apikey, x-empresa-correlationID, acesse o tópico ‘Informações necessárias’ para encontrar mais informações de como consegui-los.

Tabela de parâmetros:

| Parâmetro | Tipo Parâmetro | Tipo Dado | Obrigatório | Descrição |
|---|---|---|---|---|
| `x-empresa-apikey` | header | string | sim | client_id gerado pela autenticação Oauth utilizado para autorizar o consumo de uma aplicação requisitante. |
| `x-empresa-correlationID` | header | string | sim | identificador de correlação utilizado como um agrupar dentro da estrutura de audit trail e que permite relacionar uma mesma chamada passando em diversas aplicações/sistemas diferentes. |
| `codigo` | path | string | sim | código do produto que será excluído. |
| `status_code` | query | string | não | campo utilizado para os testes dos casos de exceção. |

 Código de status:

| Código | Descrição |
|---|---|
| `200` | Requisição executada com sucesso (success).|
| `400` | Erros de validação ou os campos informados não existem no sistema.|
| `401` | Dados de acesso inválidos.|
| `404` | Registro pesquisado não encontrado (Not found).|
| `405` | Método não implementado.|
| `410` | Registro pesquisado foi apagado do sistema e não esta mais disponível.|
| `422` | Dados informados estão fora do escopo definido para o campo.|
| `429` | Número máximo de requisições atingido. (*aguarde alguns segundos e tente novamente*)|

+ Request (application/json)

    + Headers

            Authorization: Bearer [access_token]
            x-empresa-apikey:{clientId}
            x-empresa-correlationID:{correlationId}

+ Response 200 (application/json)

    + Headers

            X-RateLimit-Limit: 60
            X-RateLimit-Remaining: 59

    + Body

            {
              "code": "200",
              "msg": "Produto com código 1 excluído com sucesso.",
              "obs": null,
              "fields": null
            }


