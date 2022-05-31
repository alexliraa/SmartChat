### Ponto de referencia ###
### https://docs.docker.com/compose/gettingstarted/ ###


# Base da Imagem.
FROM golang:alpine

# Criando diretório com Hierarquia.
RUN mkdir -p /chat

# Definindo o diretório de trabalho.
WORKDIR /chat

# Copiando
COPY go.mod ./
COPY go.sum ./

# Executando.
RUN go mod download

# Copiando o arquivo do local para o destino do container.
COPY . . 

# Especificando a porta de rede para o Docker.
EXPOSE 8080

# Construindo a imagem.
RUN go build

# Definindo executavel quando o conteiner iniciar.
ENTRYPOINT [ "./backend" ]




