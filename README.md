# Teste Devops - Smarttbot

# Segue abaixo todo o trabalho efetuado para conclusão do teste. 

* # DESENVOLVIMENTO:

  Trabalhando com docker, docker-compose e Redis após observar e analisar todo o conteúdo do [repositório](https://git.smarttbot.com/recrutamento/chat-app), vi a necessidade do desenvolvimento de alguns contéudos para que a aplicação tenha o seu real funcionamento.
  
- Dentro do diretório [backend](https://git.smarttbot.com/recrutamento/chat-app/-/tree/master/backend) foi desenvolvido o arquivo [Dockerfile](https://github.com/alexliraa/SmartChat/blob/main/backend/Dockerfile) para que seja criada a própria imagem e permitir a cronstrução do container de forma isolada. 

- A mesma linha de pensamento foi utilizada no [frontend](https://github.com/alexliraa/SmartChat/tree/main/frontend) para realizar a criação do [Dockerfile](https://github.com/alexliraa/SmartChat/blob/main/frontend/Dockerfile).

- Realizei o desenvolvimento das variáseis de ambieno no arquivo [.env](https://github.com/alexliraa/SmartChat/blob/main/.env).

- Finalizando o desenvolvimento foi necessário a criação do arquivo [docker-compose.yml](https://github.com/alexliraa/SmartChat/blob/main/docker-compose.yml) no qual tem como objetivo subir os 3 ambientes (Redis, Backend e Frontend) de forma isolada. 


# Requisitos para testar o chat:

1- Qualquer distro linux.
2- Docker 
3- Jenkins
4- Git


*OBS:*   Toda a configuração foi realizada no SO Ubuntu 20.04 LTS. Caso utilize o sistema operacional Windows, recomendo seguir a documentação oficial para habilitar o [WSL](https://docs.microsoft.com/pt-br/windows/wsl/install).
   
   
# Configurando

## Realizando a instalação do Docker:

  Segue abaixo o link da documentação oficial para instalação do docker.
https://docs.docker.com/engine/install/ubuntu/

## Realizando a Jenkis:

  Segue abaixo o link da documentação oficial para a instalação do Jenkins.
https://pkg.jenkins.io/debian-stable/  

## Realizando a Jenkis:

  Segue abaixo o link para instalação do Git.
 https://ubunlog.com/pt/git-instala-control-versiones-ubuntu-20-04/
 
 
 Após seguir os passoas acima, faça o download do meu repositório do GIT para o diretório do linux;
 
  *Execute o Comando:*
 _$ git clone https://github.com/alexliraa/SmartChat.git_
 
  Entre no diretório *SmartChat* e execute o comando:
_$ docker-compose up_

  Verifique se os containers subiram com o comando *docker ps*. O resultado esperado é:
  
CONTAINER ID   IMAGE                 COMMAND                  CREATED         STATUS         PORTS                    NAMES
2490106036dd   chatci-cd2_frontend   "docker-entrypoint.s…"   9 seconds ago   Up 5 seconds   0.0.0.0:3000->3000/tcp   Smartt-Frontend
d63fa21befb0   chatci-cd2_backend    "./backend"              9 seconds ago   Up 6 seconds   0.0.0.0:8080->8080/tcp   Smartt-Backend
4e54515a67a0   redis:latest          "docker-entrypoint.s…"   9 seconds ago   Up 7 seconds   0.0.0.0:6379->6379/tcp   Smartt-Redis

# Etapa final:

Abra o chat no seu navegador de preferência e digite o link:
_http://localhost:3000/_
 
 
* # PRODUÇÃO:

### Para esta etapa executei os respectivos passos.:

1. Subi uma instância ec2 da AWS do tipo t2.medium em Norte Virginia.
2. Instalei o servidor Jenkins e o Docker.
3. Configurei as portas 8080(jenkins), 6379(Redis), 22(SSh), 8082(Backend) e  3000(Frontend) no Security group.
4. Criei uma conta free no Docker Hub.
    * Crei o repositório  publico com o nome _pipelinesmarttbot_. 
    * Gerei o token de acesso com a permissão de leitura/escrita.
5. Acessei o Jenkins.:
    * Acessei _Jenkins > Credentials > System > GLobal credentials > Add Credentials_ configuei o usuário, senha e token do Docker hub.
    * Ainda no gerenciador do Jenkins na sessão _Gerenciador de Plugins_ instalei os plugins "Docker Pipeline", "Docker Plugin" e "Git plugin".
    * Criei o Job _PipelineSmarttBot_ do tipo Pipeline.
    * Escrevi o respectivo código:

```
pipeline {
    
    agent any
    
    environment {
        dockerImage = ''
        registry = 'alexlira/pipelinesmarttbot'
        registryCredential = '2ef75517-967d-4bfc-90b0-1a0ed620992c'
    }
    
    stages {
        stage('Checkout'){
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/alexliraa/SmartChat/']]])
            }
        }
        stage('Build image') {
                steps {
                    script {
                        def Back = docker.build("my-image:${env.BUILD_ID}","-f ${env.WORKSPACE}/backend/Dockerfile .")
                        def Front = docker.build("my-image:${env.BUILD_ID}","-f ${env.WORKSPACE}/frontend/Dockerfile .") 
                    }
                }
            }
    }
}
```

  Porém não obtive sucesso.
  Segue abaixo parte do log:

```
+ docker build -t my-image:30 -f /var/lib/jenkins/workspace/PipelineSmarttBot/backend/Dockerfile .
Sending build context to Docker daemon   1.05MB

Step 1/10 : FROM golang:alpine
 ---> 5432f005be2d
Step 2/10 : RUN mkdir -p /chat
 ---> Using cache
 ---> 6fae89a00d8e
Step 3/10 : WORKDIR /chat
 ---> Using cache
 ---> 31402e2acc8a
Step 4/10 : COPY go.mod ./
COPY failed: file not found in build context or excluded by .dockerignore: stat go.mod: file does not exist
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
ERROR: script returned exit code 1
Finished: FAILURE
```
  Outro ponto de falha obtido foi no do push da imagem. Acredito por ser uma conta free. 

Evidência:
![dockerfail](https://user-images.githubusercontent.com/40442995/171582070-da9b3a39-c3af-4c02-8671-8b9f859a6911.jpg)
![dockerevidenc](https://user-images.githubusercontent.com/40442995/171582124-b8f1c3fa-96dc-4d41-847e-5f62e5853536.jpg)


Segue abaixo o link e a credencial de acesso para o Jenkins:
(http://ec2-54-234-11-212.compute-1.amazonaws.com:8080/) (User: admin; PWD: admin123)

Att,
Alex Lira
