Olá devs!
Agora é a hora de botar a mão na massa. Para este desafio, você precisará criar o usecase de listagem das orders.
Esta listagem precisa ser feita com:
- Endpoint REST (GET /order)
- Service ListOrders com GRPC
- Query ListOrders GraphQL
Não esqueça de criar as migrações necessárias e o arquivo api.http com a request para criar e listar as orders.

Para a criação do banco de dados, utilize o Docker (Dockerfile / docker-compose.yaml), com isso ao rodar o comando docker compose up tudo deverá subir, preparando o banco de dados.
Inclua um README.md com os passos a serem executados no desafio e a porta em que a aplicação deverá responder em cada serviço.


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

Ao tentar inicializar copiando os arquivos, o erro abaixo é apresentado:

```bash

wander@bsnote283:~/repos/CURSO-GO/desafios/desafio-clean-architecture/cmd/ordersystem$ go run main.go wire_gen.go 
# github.com/wandermaia/desafio-clean-architecture/internal/infra/graph
../../internal/infra/graph/generated.go:2497:2: not enough arguments in call to out.Dispatch
	have ()
	want (context.Context)
../../internal/infra/graph/generated.go:2546:2: not enough arguments in call to out.Dispatch
	have ()
	want (context.Context)
../../internal/infra/graph/generated.go:2588:2: not enough arguments in call to out.Dispatch
	have ()
	want (context.Context)
../../internal/infra/graph/generated.go:2641:2: not enough arguments in call to out.Dispatch
	have ()
	want (context.Context)
../../internal/infra/graph/generated.go:2684:2: not enough arguments in call to out.Dispatch
	have ()
	want (context.Context)
../../internal/infra/graph/generated.go:2741:2: not enough arguments in call to out.Dispatch
	have ()
	want (context.Context)
../../internal/infra/graph/generated.go:2784:2: not enough arguments in call to out.Dispatch
	have ()
	want (context.Context)
../../internal/infra/graph/generated.go:2838:2: not enough arguments in call to out.Dispatch
	have ()
	want (context.Context)
../../internal/infra/graph/generated.go:2902:2: not enough arguments in call to out.Dispatch
	have ()
	want (context.Context)
wander@bsnote283:~/repos/CURSO-GO/desafios/desafio-clean-architecture/cmd/ordersystem$ 



```


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