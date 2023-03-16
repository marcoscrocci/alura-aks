# Azure: Gerencie o Kubernetes com AKS e ACR

### Correção dos arquivos do curso:
https://cursos.alura.com.br/forum/topico-para-quem-nao-conseguiu-completar-a-aula-206690


## Comandos para criar os serviços localmente:

>kubectl create -f ./db/permissoes.yaml <br/>
>kubectl create -f ./db/statefulset.yaml <br/>
>kubectl create -f ./db/servico-banco.yaml <br/>
>kubectl create -f ./app/deployment.yaml <br/>
>kubectl create -f ./app/servico-aplicacao.yaml <br/> 

## Consultar os serviços criados:
>kubectl get pvc -o wide <br />
>kubectl get pods -o wide <br />
>kubectl get svc -o wide <br />

## Entrar no terminal do POD do banco de dados:
>kubectl exec -it statefulset-mysql-0 -- bash <br />
root@statefulset-mysql-0:/#

## Executar os scripts do banco de dados:
No terminal do statefulset-mysql-0, executar os scripts do banco de dados que estão no arquivo <b>./db/scripts.sql</b>

## Listar os serviços em execução:
>marcoscrocci at ubuntu-unity in ~/projetos/alura/alura-aks on main✘✘✘ 22-11-30 - 21:50:17 <br/>
╰─⠠⠵ kubectl get services <br/>
| NAME | TYPE | CLUSTER-IP | EXTERNAL-IP | PORT(S) | AGE |
| --- | --- | --- | --- | --- | --- |
| db | ClusterIP | 10.98.33.190 | <none> | 3306/TCP | 15m |
| kubernetes | ClusterIP | 10.96.0.1 | <none> | 443/TCP | 45m |
| servico-aplicacao | LoadBalancer | 10.105.43.97 | <pending> | 80:31981/TCP | 8m13s |

### Abrir o serviço dentro do minikube:
>marcoscrocci at ubuntu-unity in ~/projetos/alura/alura-aks on main✘✘✘ 22-11-30 - 21:51:09 <br/>
╰─⠠⠵ minikube service servico-aplicacao

### Serviço do Kubernetes dentro do Azure
Azure Kubernetes Services - AKS

### Instalar o Azure CLI

Documentação: https://learn.microsoft.com/en-us/cli/azure/install-azure-cli

### Fazer login no Azure CLI
No Terminal executar o comando:
>az login

Deve abrir o navegador para colocar o usuário e a senha. 
Ao retornar ao Terminal, se o login realmente for realizado com sucesso, deve mostrar que houve a autenticação.

>az aks get-credentials --name alurasports_k8s --resource-group alurasports_k8s_group

A partir de então o comando kubectl irá executar as instrução dentro do cluster do AKS.

>kubectl get pods -o wide

No resources found in default namespace.

>kubectl get nodes -o wide

| NAME | STATUS | ROLES | AGE | VERSION | INTERNAL-IP | EXTERNAL-IP | OSIMAGE | KERNEL-VERSION | CONTAINER-RUNTIME |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| aks-agentpool-97813782-vmss000001 | Ready | agent | 43m | v1.23.8 | 10.224.0.5 | <none> | Ubuntu 18.04.6 LTS | 5.4.0-1098-azure | containerd://1.6.4+azure-4

## Comandos - Reexecutar os comandos a partir da linha 7

Depois do cmando kubectl get svc -o wide, copiar o EXTERNAL-IP e colocar no navegador:
http://20.232.244.206/


### Listar os volumes persistentes

>kubectl get persistentvolume

### Obter mais detalhes sobre esse volume, repita o comando passando o nome do volume:

>kubectl get persistentvolume pvc-03f54773-9a87-450d-88d6-ed6608f69fd3 --output yaml

### Obter as classes de armazenamento disponíveis no AKS:

>kubectl get storageclass

### Mostrar os detalhes do PVC:

>kubectl get persistentvolumeclaim configuracao-mysql --output yaml

### O arquivo abaixo foi criado neste repositório para exemplificar a criação de um novo volume com uma classe premium.

Arquivo volumePremium.yaml criado.

>kubectl create -f ./db/volumePremium.yaml<br />
kubectl get persistentvolumeclaim<br />
kubectl get persistentvolume <novo-volume> --output yaml<br />
kubectl delete -f ./db/volumePremium.yaml

### Criar um Azure Container Registry - ACR (privado)

Dentro do Portal do Azure, criar um novo recurso do tipo Container Registry

Criado o ACR com o nome de alurasportsrc

### Registro público do Docker Hub que iremos copiar para nosso registro privado no ACR:

https://hub.docker.com/r/rafanercessian/aplicacao-loja

No Terminal: <br />
>docker pull rafanercessian/aplicacao-loja:v1

### Registrar esse container no ACR:

>docker tag rafanercessian/aplicacao-loja:v1 alurasportsrc.azurecr.io/loja/aplicacao-loja:v1

### Para fazer o download de volta:

>docker push alurasportsrc.azurecr.io/loja/aplicacao-loja:v1

Deve gerar um erro, pois não estamos autenticados no ACR:
unauthorized: authentication required, visit https://aka.ms/acr/authorization for more information.

### Fazer login no ACR:

az acr login --name alurasportsrc

>docker push alurasportsrc.azurecr.io/loja/aplicacao-loja:v1

### Criar um Secret para utilizar no arquivo deployment.yaml para o kuberctl poder usar a imagem privada:

kubectl create secret docker-registry alurasportsrc.secret --docker-server alurasportsrc.azurecr.io --docker-username alurasportsrc --docker-password <password-gerado-pelo-azure>

Adicionar no arquivo deployment.yaml:
      imagePullSecrets:
        - name: alurasportsrc.secret

### Atualizar o container:

>kubectl apply -f ./app/deployment.yaml <br />
kubectl get pods -o wide <br />
kubectl describe pod aplicacao-deployment-74bf96ddc5-gndsk

<br />

## Comandos AKS

### Criar um grupo de recursos em uma localidade
> az group create --name aluracli-rg --location eastus

### Listar os grupos de recursos existentes no formato json
> az group list

### Listar os grupos de recursos existentes no formato table
> az group list --output table

### Listar versões do AKS:
>az aks get-versions --location eastus --output table

### Criar o cluster AKS
>az aks create --name aluracli-k8s --kubernetes-version "1.23.15" --node-count 2 --resource-group aluracli-rg --location eastus --generate-ssh-keys

### Listar os clusters AKS
>az aks list --output table

### Criar o registro de container:
>az acr create --name aluracliregistry --resource-group aluracli-rg --sku Basic --location eastus

### Fazer login nesse novo registro de container:
>az acr login --name aluracliregistry

### Listar as imagens do docker:
>docker image ls

### Adicionar a tag da aplicação no registro de container:
>docker tag rafanercessian/aplicacao-loja:v1 aluracliregistry.azurecr.io/loja/aplicacao-loja:v1

### Fazer o push desta imagem:
>docker push aluracliregistry.azurecr.io/loja/aplicacao-loja:v1

### Atualizar o registro para administrador:
>az acr update --name aluracliregistry --admin-enabled true

### Mostrar as Credenciais:
>az acr credential show --name aluracliregistry

### Conectar no cluster AKS:
az aks get-credentials --name aluracli-k8s --resource-group aluracli-rg

### Mostrar as Credenciais em tabela:
>az acr credential show --name aluracliregistry --output table

### Criar o secret:
>kubectl create secret docker-registry aluracliregistry.secret --docker-server aluracliregistry.azurecr.io --docker-username aluracliregistry --docker-password [password] --docker-email alura.gicosta@outlook.com

### Comandos para criar os serviços no AKS:

>kubectl create -f ./db/permissoes.yaml <br/>
>kubectl create -f ./db/statefulset.yaml <br/>
>kubectl create -f ./db/servico-banco.yaml <br/>
>kubectl create -f ./app/deployment.yaml <br/>
>kubectl create -f ./app/servico-aplicacao.yaml <br/> 

### Entrar no terminal do POD do banco de dados:
>kubectl exec -it statefulset-mysql-0 -- bash <br />
root@statefulset-mysql-0:/#

### Executar os scripts do banco de dados:
No terminal do statefulset-mysql-0, executar os scripts do banco de dados que estão no arquivo <b>./db/scripts.sql</b>

### Verificar o serviço executando e obter o IP de acesso:
>kubectl get services <br/>

Ao obter o IP de acesso, abrir o mesmo no navegador.

### Verificar as atualizações disponíveis do AKS:
>az aks get-upgrades -n aluracli-k8s -g aluracli-rg --output table

### Fazer a atualização do AKS:
>az aks upgrade -k "1.11.3" -n aluracli-k8s -g aluracli-rg

### Verificar a versão do AKS:
kubectl version




