# Docker Homepage

## Objetivo
Criar uma página inicial para o Docker, demonstrando seu uso e funcionalidades. Em seguida, fazer o deploy na AWS.

## Requisitos
- Docker instalado
- Conta na AWS
- Página estática pronta

## Descrição do projeto
Esta página foi construída com HTML e CSS simples e será servida por um container Docker.

## Fluxo do projeto
1. Criar o `Dockerfile`.
2. Construir a imagem Docker.
3. Verificar a imagem criada.
4. Rodar o container.
5. Acessar a página no navegador.

## Demonstrações
### Objetivo
<img src="./assets/objetivo.png" width="600">

### Build da imagem
O `Dockerfile` foi criado usando Python por questão de afinidade. Em seguida, executamos o build e verificamos a imagem com `docker images`.

<img src="./assets/docker-build.png" width="600">

### Execução do container
Rodamos o container e confirmamos que a página está sendo servida corretamente.
<br>

<img src="./assets/resultado.png" width="600">

## Observações sobre portas
<img src="./assets/observacao-porta.png" width="600">
<br>
No mapeamento de portas `-p 8000:8000`, a porta 8000 do host é redirecionada para a porta 8000 do container. Assim, acessar `http://localhost:8000` leva diretamente ao servidor web dentro do container.

## Próximos passos
- Trabalhar outras funcionalidades do Docker
- Publicar a imagem no Docker Hub
- Fazer o deploy na AWS

## Docker Hub
Para publicar a imagem no Docker Hub, siga os passos abaixo:
1. Crie uma conta no Docker Hub.
2. Faça login no Docker Hub usando o comando `docker login`.
3. Marque a imagem com o comando `docker tag <nome-da-imagem> <seu-usuario>/<nome-da-imagem>:<tag>`.
4. Publique a imagem com o comando `docker push <seu-usuario>/<nome-da-imagem>:<tag>`

<img src="./assets/docker-hub.png" width="600">
<img src="./assets/docker-hub-repositories.png" width="600">

## Deploy na AWS
Este projeto foi levado além do container local e preparado para deploy na AWS usando:
- Amazon ECR para armazenar a imagem Docker
- Amazon EC2 para executar a instância que serve a página
- IAM para gerenciar permissões seguras

### 1. Criar o repositório no ECR
A primeira etapa foi criar um repositório Docker no Amazon ECR. Isso permite versionar a imagem e usar o repositório como origem para a instância EC2.

<img src="./assets/criando-repositorio.png" width="600">

<img src="./assets/repositorio-criado.png" width="600">

### 2. Configurar IAM e AWS CLI
Para conectar localmente à AWS, foi criada uma chave de acesso IAM e configurado o AWS CLI com `aws configure`.
Também foi criada e ajustada uma role IAM para permitir acesso controlado aos serviços necessários.
Além disso, a configuração de rede do IAM foi revisada para permitir conexões SSH seguras à instância EC2.

<img src="./assets/accesskeys.png" width="600">
<img src="./assets/criando-role.png" width="600">
<img src="./assets/role-atualizada.png" width="600">
<img src="./assets/modificando-role-IAM.png" width="600">
<img src="./assets/configuração-de-rede.png" width="600">

```bash
aws configure
AWS Access Key ID [****************GGFM]:
AWS Secret Access Key [****************zKd9]:
Default region name [us-east-2]:
Default output format [son]: json
```

### 3. Fazer login no ECR e enviar a imagem
Após configurar as credenciais, foi feito login no ECR usando o token gerado pelo AWS CLI.
Em seguida, a imagem Docker foi tagueada e enviada ao repositório ECR.

```bash
aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 385545779029.dkr.ecr.us-east-2.amazonaws.com
```

<img src="./assets/login-succeded.png" width="600">
<img src="./assets/imagem-na-aws.png" width="600">
<img src="./assets/push-image-aws.png" width="600">

### 4. Provisionar EC2 e preparar o ambiente
A última etapa foi lançar uma instância EC2, configurar um par de chaves SSH e conectar à instância.
Depois, o Docker foi instalado na instância e o container foi preparado usando a imagem do ECR.

<img src="./assets/criando-par-de-chaves.png" width="600">
<img src="./assets/chmod-chave.png" width="600">
<img src="./assets/conectando-nainstancia-ssh.png" width="600">
<img src="./assets/instalando-docker.png" width="600">
<img src="./assets/configurando-docker.png" width="600">

### 5. Testar a imagem na EC2
Com a instância pronta, a imagem foi baixada do ECR para a EC2 e executada localmente.
Isso confirma que a imagem enviada funciona corretamente no ambiente da AWS.

<img src="./assets/docker-pull.png" width="600">
<img src="./assets/imagem-rodando-aws.png" width="600">

### 6. Limpeza e encerramento
Depois de confirmar o deploy e testar o container, a infraestrutura e os recursos temporários foram removidos para evitar cobranças.
Isso inclui a exclusão de imagens locais/remotas, a instância EC2 e a role IAM utilizada durante o processo.

<img src="./assets/deletando-imagens.png" width="600">
<img src="./assets/encerrando-instancia.png" width="600">
<img src="./assets/excluindo-role.png" width="600">

### 7. Resultado final na EC2
Com tudo configurado, o container foi iniciado na instância EC2 e a página ficou visível publicamente.

<img src="./assets/executando-instancia.png" width="600">
<img src="./assets/site-rodandoo.png" width="600">

## Conclusão
O deploy na AWS foi estruturado para usar EC2, ECR e IAM de forma integrada. Primeiro a página foi containerizada localmente, depois a imagem foi enviada ao ECR e, finalmente, a instância EC2 foi provisionada para executar o container Docker publicamente.

Este fluxo garante que a aplicação esteja pronta para rodar na nuvem com controle de acesso e provisionamento seguro.
