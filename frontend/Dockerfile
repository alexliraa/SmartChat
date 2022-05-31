### Ponto de referencia ###
### https://docs.docker.com/compose/gettingstarted/ ###


# Base da Imagem.
FROM node:16-alpine

# Criando diretório com hierarquia.
RUN mkdir -p /chat

# Definindo o diretório de trabalho.
WORKDIR /chat

# Copiando o arquivo do local para o destino do container.
COPY ./ .

# Construindo a imagem.
RUN npm install
RUN npm run build

# Especificando a porta de rede para o Docker.
EXPOSE 3000

# Instruindo o que deve ser executado quando o conteiner do docker for iniciado.
CMD ["npm", "start"]
