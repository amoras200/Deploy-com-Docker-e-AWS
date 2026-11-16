# Docker Homepage

![CD - Build e push para o ECR](https://github.com/amoras200/Deploy-com-Docker-e-AWS/actions/workflows/cd-ecr.yml/badge.svg)

## Objetivo
Criar uma página inicial para o Docker, demonstrando seu uso e funcionalidades. Em seguida, fazer o deploy na AWS.

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
<img src="docker-homepage/assets/objetivo.png" width="600">

### Build da imagem
O `Dockerfile` foi criado usando Python por questão de afinidade. Em seguida, executamos o build e verificamos a imagem com `docker images`.

<img src="docker-homepage/assets/docker-build.png" width="600">

### Execução do container
Rodamos o container e confirmamos que a página está sendo servida corretamente.
<br>

<img src="docker-homepage/assets/resultado.png" width="600">

## Observações sobre portas
<img src="docker-homepage/assets/observacao-porta.png" width="600">
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

<img src="docker-homepage/assets/docker-hub.png" width="600">
<img src="docker-homepage/assets/docker-hub-repositories.png" width="600">

## Deploy na AWS
Este projeto foi levado além do container local e preparado para deploy na AWS usando:
- Amazon ECR para armazenar a imagem Docker
- Amazon EC2 para executar a instância que serve a página
- IAM para gerenciar permissões seguras

### 1. Criar o repositório no ECR
A primeira etapa foi criar um repositório Docker no Amazon ECR. Isso permite versionar a imagem e usar o repositório como origem para a instância EC2.

<img src="docker-homepage/assets/criando-repositorio.png" width="600">

<img src="docker-homepage/assets/repositorio-criado.png" width="600">

### 2. Configurar IAM e AWS CLI
Para conectar localmente à AWS, foi criada uma chave de acesso IAM e configurado o AWS CLI com `aws configure`.
Também foi criada e ajustada uma role IAM para permitir acesso controlado aos serviços necessários.
Além disso, a configuração de rede do IAM foi revisada para permitir conexões SSH seguras à instância EC2.

<img src="docker-homepage/assets/accesskeys.png" width="600">
<img src="docker-homepage/assets/criando-role.png" width="600">
<img src="docker-homepage/assets/role-atualizada.png" width="600">
<img src="docker-homepage/assets/modificando-role-IAM.png" width="600">
<img src="docker-homepage/assets/configuração-de-rede.png" width="600">

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

<img src="docker-homepage/assets/login-succeded.png" width="600">
<img src="docker-homepage/assets/imagem-na-aws.png" width="600">
<img src="docker-homepage/assets/push-image-aws.png" width="600">

### 4. Provisionar EC2 e preparar o ambiente
A última etapa foi lançar uma instância EC2, configurar um par de chaves SSH e conectar à instância.
Depois, o Docker foi instalado na instância e o container foi preparado usando a imagem do ECR.

<img src="docker-homepage/assets/criando-par-de-chaves.png" width="600">
<img src="docker-homepage/assets/chmod-chave.png" width="600">
<img src="docker-homepage/assets/conectando-nainstancia-ssh.png" width="600">
<img src="docker-homepage/assets/instalando-docker.png" width="600">
<img src="docker-homepage/assets/configurando-docker.png" width="600">

### 5. Testar a imagem na EC2
Com a instância pronta, a imagem foi baixada do ECR para a EC2 e executada localmente.
Isso confirma que a imagem enviada funciona corretamente no ambiente da AWS.

<img src="docker-homepage/assets/docker-pull.png" width="600">
<img src="docker-homepage/assets/imagem-rodando-aws.png" width="600">

### 6. Limpeza e encerramento
Depois de confirmar o deploy e testar o container, a infraestrutura e os recursos temporários foram removidos para evitar cobranças.
Isso inclui a exclusão de imagens locais/remotas, a instância EC2 e a role IAM utilizada durante o processo.

<img src="docker-homepage/assets/deletando-imagens.png" width="600">
<img src="docker-homepage/assets/encerrando-instancia.png" width="600">
<img src="docker-homepage/assets/excluindo-role.png" width="600">

### 7. Resultado final na EC2
Com tudo configurado, o container foi iniciado na instância EC2 e a página ficou visível publicamente.

<img src="docker-homepage/assets/executando-instancia.png" width="600">
<img src="docker-homepage/assets/site-rodandoo.png" width="600">

## Conclusão
O deploy na AWS foi estruturado para usar EC2, ECR e IAM de forma integrada. Primeiro a página foi containerizada localmente, depois a imagem foi enviada ao ECR e, finalmente, a instância EC2 foi provisionada para executar o container Docker publicamente.

Este fluxo garante que a aplicação esteja pronta para rodar na nuvem com controle de acesso e provisionamento seguro.

## Continuous Delivery (Fase 1) — imagem no ECR

Esta fase implementa **CD do artefato**: a cada push na `main` (ou disparo manual), o GitHub Actions faz build da imagem e publica no **Amazon ECR**. **Não é deploy automático na EC2** (isso seria uma Fase 2, ex.: atualizar o container via AWS SSM com a instância ativa).

### Diagrama

```text
Commit (main) → GitHub Actions (workflow cd-ecr.yml)
                      │  OIDC: AssumeRoleWithWebIdentity (sem chaves longas)
                      ▼
               Amazon ECR (aws_cd, us-east-1)
                      │
                      └─ tags: <commit-sha> e latest
```

### Pré-requisitos e configuração

**Valores deste repositório**
- Conta AWS: `385545779029`
- Região: `us-east-1`
- Repositório ECR: `aws_cd` (`385545779029.dkr.ecr.us-east-1.amazonaws.com/aws_cd`)
- Repo GitHub: `amoras200/Deploy-com-Docker-e-AWS` (branch `main`)

1. **Repositório no ECR (recriar se foi apagado)**
   - Console AWS → ECR → *Create repository* (privado), nome `aws_cd`, região `us-east-1`.
   - Via CLI (exemplo): `aws ecr create-repository --repository-name aws_cd --region us-east-1`

2. **Provedor de identidade OIDC do GitHub no IAM** (uma vez por conta; reutilize se já existir)
   - IAM → *Identity providers* → *Add provider* → tipo **OpenID Connect**.
   - **Provider URL:** `https://token.actions.githubusercontent.com` (clique em *Get thumbprint* se solicitado pelo console).
   - **Audience:** `sts.amazonaws.com`.
   - Confirme que o provider criado aparece como `arn:aws:iam::385545779029:oidc-provider/token.actions.githubusercontent.com`.

3. **Role IAM para o GitHub Actions assumir via OIDC (permissão mínima)**
   - IAM → *Roles* → *Create role* → *Web identity*.
   - Identity provider: `token.actions.githubusercontent.com`, Audience: `sts.amazonaws.com`.
   - Restrinja ao repositório/branch (trust policy) — use a política abaixo (cobre execuções na ref `refs/heads/main`, incluindo push na `main`; `workflow_dispatch` deve ser disparado a partir da `main` para assumir a role com esta condição):

   **Trust policy (colar/ajustar na role):**
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Federated": "arn:aws:iam::385545779029:oidc-provider/token.actions.githubusercontent.com"
         },
         "Action": "sts:AssumeRoleWithWebIdentity",
         "Condition": {
           "StringEquals": {
             "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
             "token.actions.githubusercontent.com:sub": "repo:amoras200/Deploy-com-Docker-e-AWS:ref:refs/heads/main"
           }
         }
       }
     ]
   }
   ```

   - Anexe uma política inline/gerenciada com permissão **apenas** para push no repo ECR `aws_cd`:

   **Permission policy:**
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "ECRAuthToken",
         "Effect": "Allow",
         "Action": "ecr:GetAuthorizationToken",
         "Resource": "*"
       },
       {
         "Sid": "ECRPushPullRepo",
         "Effect": "Allow",
         "Action": [
           "ecr:BatchCheckLayerAvailability",
           "ecr:CompleteLayerUpload",
           "ecr:InitiateLayerUpload",
           "ecr:PutImage",
           "ecr:UploadLayerPart",
           "ecr:BatchGetImage",
           "ecr:GetDownloadUrlForLayer"
         ],
         "Resource": "arn:aws:ecr:us-east-1:385545779029:repository/aws_cd"
       }
     ]
   }
   ```
   - Copie o **ARN da role** (ex.: `arn:aws:iam::385545779029:role/<nome-da-role>`).

4. **Configuração no GitHub (Settings do repositório)**
   - *Settings → Secrets and variables → Actions*.
   - **Secret:** `AWS_ROLE_ARN` = ARN da role criada.
   - **Variables:** `AWS_REGION` = `us-east-1`, `ECR_REPOSITORY` = `aws_cd`.

5. **Workflow**
   - Arquivo: `.github/workflows/cd-ecr.yml` (gatilhos: push em `main` e `workflow_dispatch`; permissões `id-token: write`, `contents: read`; build com `context: ./docker-homepage` e push das tags `<sha>` e `latest`).
   - Antes de confiar em produção, confirme as versões das actions na documentação oficial (`actions/checkout`, `aws-actions/configure-aws-credentials`, `aws-actions/amazon-ecr-login`, `docker/build-push-action`), pois podem mudar.

### Como testar
- Faça um commit pequeno na `main` e acompanhe a execução na aba **Actions**.
- No ECR, confirme que aparecem as tags `<commit-sha>` e `latest`.
- Puxe e rode localmente para validar a imagem:
  ```bash
  aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 385545779029.dkr.ecr.us-east-1.amazonaws.com
  docker pull 385545779029.dkr.ecr.us-east-1.amazonaws.com/aws_cd:latest
  docker run --rm -p 8080:80 385545779029.dkr.ecr.us-east-1.amazonaws.com/aws_cd:latest
  ```
- **Teste de falha (recomendado):** remova/restrinja temporariamente uma permissão da role (ex.: `ecr:PutImage`) e verifique o erro no workflow para entender falhas de autorização.

### Fase 2 (opcional) — Deploy automático na EC2
Para CD completo, seria necessário atualizar o container na EC2 (ex.: via **AWS Systems Manager/SSM**) após o push no ECR. Isso exige instância ligada, agente/permissões SSM e cuidado com custos; só considere "deploy automático" quando estiver funcionando de ponta a ponta.

### Limpeza (evitar custos)
- **ECR:** apagar imagens e, se não for mais usar, excluir o repositório `aws_cd`.
- **IAM:** remover a role criada para o workflow e, se não houver outros usos, o provedor OIDC do GitHub na conta.
- **EC2/recursos manuais:** encerrar instâncias e remover chaves/roles temporárias criadas nos testes anteriores.

> **Segurança:** não versione chaves privadas (ex.: `.pem`). O repositório inclui `.gitignore` para `*.pem`; se algum arquivo sensível já foi commitado, remova do versionamento e rotacione a credencial. Evite commitar `AWS_ACCESS_KEY_ID`/`AWS_SECRET_ACCESS_KEY` — prefira OIDC como acima.
