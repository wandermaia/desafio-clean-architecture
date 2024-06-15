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

wander@bsnote283:~/desafio-clean-architecture$ go mod tidy
wander@bsnote283:~/desafio-clean-architecture$ 
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


## Referências

Golang chi

https://github.com/go-chi/chi

Viper

https://github.com/spf13/viper

gqlgen

https://gqlgen.com/

gRPC IO

https://grpc.io

Protocol Buffers

https://protobuf.dev/


Protocol Buffer Compiler Installation

https://grpc.io/docs/protoc-installation/


Evans

https://github.com/ktr0731/evans


Wire: Automated Initialization in Go

https://github.com/google/wire