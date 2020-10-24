# analysing-data
Projeto utilizando Metabase + PostgreSQL + Apache Spark + Portainer

# Introdução 
O projeto tem como intuito realizar a inserção de dados utilizando processamento memória através dos Apache Spark, foi realizado um estudo de caso com base nos dados de vendas de video games (open dataset disponibilizado no Kaggle). 

O Apache Spark está realizando o processamento dos dados que estão contidos nos arquivo/arquivos de vendas e realiza a inserção dos dados no banco de dados PostgreSQL. Após o armazenamento dos dados no PostgreSQL é realizada a visualização destes dados utilizando o Metabase.

Por fim, será utilizado o Prometheus para monitorar todos os serviços em produção.

# Proposta
A proposta é realizar a implantação dos serviços utilizando Docker.

# Processo de Instalação:
Para realizar a instalação dos serviços abaixo é necessário realizar a instalação do docker, para isso utilize estarei explicando como instalar o Docker Engine no Linux.

1. Faca uma atualização dos pacotes e utilize o apt para realizar a instalação dos pacotes informados abaixo:
```sh
$ sudo apt-get update

$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

2. Adicione a chave GPG oficial do Docker:
```sh
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

3. Para encontrar a chave use o comando:
```sh
$ apt-key list
```

4. Verifique que você agora tem a chave como a impressão digital do tipo ```9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88```, pegue os oito últimos digitos.
```sh
$ apt-key fingerprint 0EBFCD88
```

6. Use o seguinte comando para configurar o repositório estável:
```sh
$ add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

7. Atualize os pacotes novamente, utilizando o ```apt```:
```sh
$ apt-get update
```

8. Para instalar a ultima versão podemos executar o seguinte comando::
```sh
$ apt-get install docker-ce docker-ce-cli containerd.io
```

9. Verifique a versão do docker para "confirmar" a instalação:
```sh
$ docker --version
```

# PostgreSQL
Com o Docker instalado, vamos instalar o PostgreSQL

1. Realizar o pull da imagem:
```sh
$ docker pull postgres
```

2. Verifique se a imagem foi baixada com sucesso:
```sh
$ docker images
```

3. Após isso vamos criar uma pasta para servir de volume para o docker:
```sh
$ mkdir -p $HOME/docker/volumes/postgres
```

3. Vamos executar o container ```(Lembrando, em "POSTGRES_PASSWORD", utilize a senha que preferir)```:
```sh
$ docker run --name pg-docker-meta -e POSTGRES_PASSWORD=docker -d -p 5432:5432 -v $HOME/docker/volumes/postgres:/var/lib/postgresql/data  postgres
```

# Metabase
Com o Docker e o PostgreSQL instalados, podemos dar continuidade e realizar a instalação do Metabase, nosso principal serviço de visualização de dados 😄.

1. Vamos criar uma pasta para alocar o volume: 
```sh
$ mkdir -p $HOME/docker/volumes/metabase-data
```

2. Vamos executar o container: 
```sh
$ docker run -d -p 3000:3000 --name metabase metabase/metabase
```

3. Aguarde até subir o serviço na porta 3000

4. Clique em "Vamos comecar" para realizar todos os processos de acesso, primeiramente você irá definir o idioma e após isso definirá um e-mail de administrador.

# Apache Spark
Para realizarmos a instalação do Apache Spark, estaremos utilizando uma distribuição do mesmo que é PySpark, o PySpark é o Spark em Python, trazendo uma versatilidade ao realizarmos a criação dos códigos, o Apache Spark foi escrito em Scala, porém não estaremos abordando neste tutorial.

Vamos realizar a instalação do Jupyter Notebook com o PySpark.

1. Podemos executar o container:
```sh
$ docker run -d -p 8888:8888 jupyter/pyspark-notebook
```

2. Ao executar o container, utilize o seguinte comando para realizar a captura do Token de acesso a dashboard do Jupyter Notebook.
```sh
$ docker logs *nome_do_container*
```

Analisando os logs, pode captar o Token através da url que contém o mesmo, geralmente está descrito como ```"?token="```

Após realizar todos os procedimentos, basta realizar o upload do notebook e do dataset disponibilizado neste repositório, para comecar a realizar os processamentos. 

# Portainer

Vamos instalar o nosso serviço de monitoramento.

1. Podemos baixar a imagem:
```sh
$ docker pull portainer/portainer
```

2. Vamos executar o container:
```sh
$ docker run -d -p 9000:9000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v /home/renatogroffe/Desenvolvimento/Portainer/data:/data portainer/portainer
```

3. Defina um usuário e uma senha de administrador

4. Selecione a opção Local e clique em "Connect"