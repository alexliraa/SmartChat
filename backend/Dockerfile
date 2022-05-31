### Ponto de referencia ###
### https://docs.docker.com/compose/gettingstarted/ ###


# Base da Imagem.
FROM golang:alpine

# Realizando a execução do comando.
RUN mkdir -p /chat

# Definindo o diretório de trabalho.
WORKDIR /chat

# Copiando arquivos e/ou diretórios para dentro da imagem.
COPY go.mod ./
COPY go.sum ./

# Realizando a execução do comando.
RUN go mod download

# Copiando arquivos e/ou diretórios para dentro da imagem.
COPY . . 

# Exponodo a porta para o container quando for iniciado.
EXPOSE 8080

# Realizando a execução do comando.
RUN go build

# Definindo o comando para ser executado quando o conteiner iniciar.
ENTRYPOINT [ "./backend" ]




