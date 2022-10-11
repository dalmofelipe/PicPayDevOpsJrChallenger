## PicPay DevOps JR Challenger

#### Dalmo Felipe Torres de Paula


## COMMIT #1

Desde que o clonamos e visualizamos os codigos dos serviços, a propria IDE já indica a falta das libs e que também não possui os manifestos `go.mod` ou `requirement.txt` em suas respectivas pastas e nem foram descritos no dockerfiles.

### READER em Golang

Para o serviço READER foi gerado o go.mod que indicará as dependências a serem instaladas. Este arquivo será copiado o container build.

O dockerfile foi ajustado, para instalar as depedências com `RUN go mod tidy` durante o build.

Fiz por esta abordagem, pois até onde sei, cada comando RUN no dockerfile gera uma nova layer no processo de build. Desta forma crio apenas um camada dedicada as depedências do projeto, além de deixa o build mais limpo e agil, além do dockerfile ficar mais legivel.

Modifiquei a versão da imagem golang somente por compatibilidade com a versão do meu computador.

### WRITER em Python

Não foi gerado o `requirements.txt` para WRITER, somente adicionado o comando `RUN pip install redis` no dockerfile para instalar dependência do redis, com objetivo de ilustrar os passos realizados com READER.

Dockerfile foi modificado os valores do ENTRYPOINT para "python" e CMD passando o argumento "main.py" como parametro para o ENTRYPOINT. 

### DOCKER-COMPOSE

#### PORTAS

Todas portas descritas no docker-compose foram verificadas no codigos-fonte e dockerfiles dos serviços. Assim foi modificadas, como:

1.  `service.writer.ports:"8080:8080"` >>> `service.writer.ports:"8081:8081"`
2.  `service.reader.ports:"8081:8081"` >>> `service.reader.ports:"8080:8080"`

#### NETWORK

1. criar network de frontend
2. removida a rede frontend do READER e adicionado a de backend
3. adicionado a backend o serviço do "reids" 
4. frontend mantido nas duas redes

#### SERVICE NAME

Nome do serviço do "reids" foi mofidicado para "redis", passo importante pois os serviços indicam o hostname para conectar ao redis.


### FRONTEND em JavaScript

Ajuste realizado no `CMD` do dockerfile. Configurado para executar o script 'start' do packege.json

```dockerfile
CMD ["npm", "start"]
```

<br>

PRIMERIO COMPOSE UP!!!

```dockerfile
docker compose up
```


### RESULTADO

FRONTEND ouvindo a porta 3000; Site não abre na porta 3000 e nem 5000

READER muito parametros para a função client.Get; Healthcheck não responde 

WRITE OK repondendo o Healthcheck

```bash

picpaydevopsjrchallenger-web-1     | INFO: Accepting connections at http://localhost:3000.
picpaydevopsjrchallenger-reader-1  | # command-line-arguments
picpaydevopsjrchallenger-reader-1  | ./main.go:27:39: too many arguments in call to client.Get
picpaydevopsjrchallenger-reader-1  |    have (context.Context, string)
picpaydevopsjrchallenger-reader-1  |    want (string)

```
<br>


**Limpando tudo**

```bash
docker container rm picpay-jr-devops-challenge-web-1 picpay-jr-devops-challenge-redis-1 picpay-jr-devops-challenge-reader-1 picpay-jr-devops-challenge-writer-1 && docker image rm picpay-jr-devops-challenge-web:latest picpay-jr-devops-challenge-reader:latest picpay-jr-devops-challenge-writer:latest
```