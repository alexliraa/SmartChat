# Teste Devops - Smarttbot

# Segue abaixo todo o trabalho efetuado para conclusão do teste. 

* # DESENVOLVIMENTO:

  Trabalhando com docker, docker-compose e Redis após observar e analisar todo o conteúdo do [repositório](https://git.smarttbot.com/recrutamento/chat-app), vi a necessidade do desenvolvimento de alguns contéudos para que a aplicação tenha o seu real funcionamento.
  
- Dentro do diretório [backend](https://git.smarttbot.com/recrutamento/chat-app/-/tree/master/backend) foi desenvolvido o arquivo [Dockerfile](https://github.com/alexliraa/SmartChat/blob/main/backend/Dockerfile) para que seja criada a própria imagem e permitir a cronstrução do container de forma isolada. 

- A mesma linha de pensamento foi utilizada no [frontend](https://github.com/alexliraa/SmartChat/tree/main/frontend) para realizar a criação do [Dockerfile](https://github.com/alexliraa/SmartChat/blob/main/frontend/Dockerfile).

- Realizei o desenvolvimento das variáseis de ambieno no arquivo [.env](https://github.com/alexliraa/SmartChat/blob/main/.env).

- Finalizando o desenvolvimento foi necessário a criação do arquivo [docker-compose.yml](https://github.com/alexliraa/SmartChat/blob/main/docker-compose.yml) no qual tem como objetivo subir os 3 ambientes (Redis, Backend e Frontend) de forma isolada. 


# h1 Requisitos para testar o chat:

1- Qualquer distro linux.
2- Docker 
3- Jenkins
4- Git


*OBS:*   Toda a configuração foi realizada no SO Ubuntu 20.04 LTS. Caso utilize o sistema operacional Windows, recomendo seguir a documentação oficial para habilitar o [WSL](https://docs.microsoft.com/pt-br/windows/wsl/install).
   
   
# h1 Configurando

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

  Para trabalhar com o docker e o Jenkis, utilizei o serviço ec2 da AWS para subir uma instância do tipo t2.medium na região de Norte da Virginia. 
  Realizei a configuração do Security group para liberar as portas 8080(jenkins), 6379(Redis), 22(SSh), 8082(Backend) e  3000(Frontend).
  
  Após realizar as configurações necessárias no terminal, comecei a configuração do pipeline no Jenkins (http://ec2-54-234-11-212.compute-1.amazonaws.com:8080/) (User: admin; PWD: admin123)
  
  Realizei a respectiva configuração do pipeline, porém sem sucesso:

pipeline {
    
    agent any
    
    environment {
        dockerImage =''
        registry='chat/backend'
        registryCredential ='alexlira'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/alexliraa/SmartChat']]])
            }
        }
        
        stage('Build Docker image') {
           steps {
               script {
                   dockerImage = docker.build registry
               }
           } 
        }
        
        stage('Uploading Image') {
            steps {
                script {
                        docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('docker stop container') {
            steps {
                sh 'docker ps -f name=DockerTestSmarttPipeline -q | xargs --no-run-if-empty docker container stop'
                sh 'docker container ls -a -fname=DockerTestSmarttPipeline -q | xargs -r docker container rm'
             }
        }
        stage('Docker Run') {
            steps{
                script {
                    dockerImage.run("-p 3000:8000 --rm --name DockerTestSmarttPipeline")
         }
      }
    }
  }
}


  
  
 
     

