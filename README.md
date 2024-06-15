# **Repositório para o Desafio de Clean Architecture da Pós de Go Expert**

Este repositório foi criado exclusivamente para hospedar o desenvolvimento do Desfio sobre Clean Architecture da **Pós Go Expert**, ministrado pela **Full Cycle**.


## Descrição do Desafio

Olá devs!

Agora é a hora de botar a mão na massa. Para este desafio, você precisará criar o usecase de listagem das orders. Esta listagem precisa ser feita com:

- Endpoint REST (GET /order)

- Service ListOrders com GRPC

- Query ListOrders GraphQL

Não esqueça de criar as migrações necessárias e o arquivo `api.http` com a request para criar e listar as orders.

Para a criação do banco de dados, utilize o Docker (Dockerfile / docker-compose.yaml), com isso ao rodar o comando docker compose up tudo deverá subir, preparando o banco de dados.

Inclua um README.md com os passos a serem executados no desafio e a porta em que a aplicação deverá responder em cada serviço.


## Informações sobre a Implementação


Abaixo seguem algumas informações sobre a implementação

- Foi criado um usecase para realizar a listagem das orders.

- A criação da tabela orders no banco de dados é gerada pelo docker-compose através do arquivo `/sql/initdb.sql`. Esta abordagem foi escolhida por ser mais simples e atender a necessidade do projeto.

- Foi adicionada a chamada para listagem das orders no arquivo `/api/api.http`, este pode ser utilizado para realizar as chamadas REST no webserver.


## Instruções para Inicialização


Após clonar o repositório, deve-se entrar na pasta raiz e executar o comando docker-compose. A seguir está um exemplo da execução do comando no projeto:


```bash
wander@bsnote283:~/desafio-clean-architecture$ docker-compose up -d
Creating network "desafio-clean-architecture_default" with the default driver
Creating mysql    ... done
Creating rabbitmq ... done
wander@bsnote283:~/desafio-clean-architecture$

```

Com essa execução, será inicializado o container do MySQL (já com o banco e a tabela criados) e do container do RabbitMQ.

Com os containers já no ar, basta entrar na pasta `/cmd/ordersystem` e executar o comando "go run main.go wire_gen.go", conforme abaixo:

```bash

wander@bsnote283:~/desafio-clean-architecture$ cd cmd/ordersystem/
wander@bsnote283:~/desafio-clean-architecture/cmd/ordersystem$ ls
main.go  wire_gen.go  wire.go
wander@bsnote283:~/desafio-clean-architecture/cmd/ordersystem$ go run main.go wire_gen.go
Starting web server on port :8000
Starting gRPC server on port 50051
Starting GraphQL server on port 8080

```

> **OBSERVAÇÕES**
>
> - O servidor WEB é sendo executado na porta **8000**
>
> - O servidor gRPC é sendo executado na porta **50051**
>
> - O servidor GraphQL é sendo executado na porta **8080**
>

Feito isso, os três servidores já estão aptos a receber as chamadas.



## Realização das Chamadas

A seguir estão algumas informações a respeito das chamdas


### WEBSERVER

Para a execução das chamadas no webserver, pode ser utilizado o próprio VSCode atrvés do arquivo `api/api.http`. Abaixo segue um exemplo da sua utilização:

![http.png](/.img/http.png)


### gRPC

Para realizar as consultas gRPC, pode ser utilizado o **Evans**. Abaixo segue um exemplo dele em execução no projeto:

![evans.png](/.img/evans.png)


### GraphQL

No caso do GraphQL, podemos acessar o playground do GraphQL, através do localhost (porta 8080) no Browser:

![graphql.png](/.img/graphql.png)


## Anotações

 - verificar a pasta de entiy
 - Verifiar a pasta de use cases - eles trabalham com interfaces e apontam para os entities


 Repository fazem os acessos ao banco de dados.
        order_repository.go que acessa o banco de daods


O order_handler.go (dentro do web) que executa o use case


## Atividades


- Adicionado funções no internal/infra/database/order_repository.go (criada a função `GetAllOrders`)
- Adicionados testes no arquivo internal/infra/database/order_repository_test.go para a função criada em `order_repository.go`
- Adicionada função `GetAllOrders() ([]Order, error)` na interface internal/entity/interface.go
- Criado o arquivo do caso de uso `get_all_orders.go`

### WebServer

- Criar a função getAll ho order_handler.go utilizando o caso de uso GetAllOrdersUseCase
- No webswerver.go, foram realizadas as seguintes modificações

Criada struct DadosHandler para armazenar o handler e o path, utilizando o método como chave. O ideal seria uma chave composta para garantir que um método não vai sobrepor ao de outra rota.

Na struct do webserver, foi alterado o item Handlers para HandlersByMethods e foi modificada a estrutura. Agora ele é um map de string (o método) para o objeto DadosHandler, que contém o path e o handler do webserver que será utilizado.

Foi necessário altear a chamada ao método AddHandler a partir do main, para que o verbo HTTP também fosse enviado por parâmetro.

Ajustada também a conexão do rabbit para utilizar as variáveis de ambiente.


Endpoint REST (GET /order)



### Graphql


Criar o arquivo tools.go para colocar as dependências. Ele gera a estratura de pastas, mas não necessariamento serão utilizados on programa. São módulos de apoio para gerar código, por exemplo. é utilizado pelo gqlgen para rodar o comando abaixo.

```bash

printf '// +build tools\npackage tools\nimport (_ "github.com/99designs/gqlgen"\n _ "github.com/99designs/gqlgen/graphql/introspection")' | gofmt > tools.go

go mod tidy



wander@bsnote283:~$ go run github.com/99designs/gqlgen init
Creating gqlgen.yml
Creating graph/schema.graphqls
Creating server.go
Generating...

Exec "go run ./server.go" to start GraphQL server
wander@bsnote283:~$ 

go mod tidy


```
Foi criado o arquivo gqlgen.yml, graph/schema.graphqls e server.go



o arquivo `schema.graphqls` gera o conteúdo da pasta model. Os arquivos de model não podem ser editados diretamente;


O type query no do `schema.graphqls` no módulo de graphql volta todas as categorias e cursos. POde ser um bom exemplo.

O type mutation é uma edição nos dados que também fica nesse arquivo. 

O arquivo model_gen tem as structs que foram geradas automaticamente pelo arquivo shcema para serem utilziadas.

Após as edições, temos que executar o generate para ajustar os arquivos (pode apresentar alguns erros referentes a todo list):

```bash

wander@bsnote283:~$ go run github.com/99designs/gqlgen generate

go mod tidy


```

Remover os dados antigos  antigos do `schema.resolvers.go`. Quando gera novamente, ele move os daods para baixo e ifnorma que são antigos.

O resolver é o que o schema executa para tratar processar os métodos e funções.

No arquivo `resolver.go` são injetadas as dependências para serem utilizadas no `schema.resolvers.go`


### gRPC

Adicionadas informações no arquivo order.proto

O comando para gerar os arquivos, deve ser executado a partir da raiz do projeto para garantir que os arquivos fiquem nas pastas corretas.

```bash

protoc --go_out=. --go-grpc_out=. internal/infra/grpc/protofiles/order.proto

go mod tidy


```

Após a execução será criada uma entrada no arquivo `order_grpc.pb.go`, dentro da interface `OrderServiceClient`referente ao serviço. Essa interface deve ser implementada no arquivo `order_service.go`. 


## Use cases list orders


Para usar o usecase, no order_handler.go foi adicionado o dto usecase.OrderInputDTO para entada dos dados. Na saída ele recebeu o retorno
output, err := createOrder.Execute(dto), que devolveu o DTO OrderOutputDTO

+++++++++++++++++++++++++

Projeto DI (Dependece Injection) tem uma estrutura próxima do clean architect

No main ficou assim:

- Criou um repositório
Criou um usecase passando o repositorio como parametro
usou o usecase.GetProducti(1)

Essas dependências foram criadas pelo wire. Ficou assim:

func NewUseCase(db *sql.DB) *product.ProductUseCase {
	productRepository := product.NewProductRepository(db)
	productUseCase := product.NewProductUseCase(productRepository)
	return productUseCase
}


## Migrate

Instalar o migrate

Criar o migrate
```bash

wander@bsnote283:~/pos-golang-sqlc$ migrate create -ext=sql -dir=sql/migrations -seq init
/home/wander/pos-golang-sqlc/sql/migrations/000001_init.up.sql
/home/wander/pos-golang-sqlc/sql/migrations/000001_init.down.sql
wander@bsnote283:~/pos-golang-sqlc$ 
wander@bsnote283:~/pos-golang-sqlc$ tree
.
├── README.md
└── sql
    └── migrations
        ├── 000001_init.down.sql
        └── 000001_init.up.sql

2 directories, 3 files
wander@bsnote283:~/pos-golang-sqlc$ 


# criar migration com docker compose

docker compose -f docker-compose.yaml --profile tools run --rm migrate create -ext=sql -dir=sql/migrations -seq init
docker compose -f docker-compose.yaml --profile tools run --rm migrate up


```
Executando o migrate up no MySQL


```bash

docker-compose exec mysql bash
mysql -uroot -p orders
show tables;
desc orders;
select * from orders;
```

```bash

migrate -path=sql/migrations -database "mysql://root:root@tcp(localhost:3306)/orders" -verbose up
migrate -path=sql/migrations -database "mysql://root:root@tcp(localhost:3306)/orders" -verbose down


```

Executando o migrate (via Makefile):

```bash

make migrate
make migratedown

```