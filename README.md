# Azure: Gerencie o Kubernetes com AKS e ACR

### Correção dos arquivos do curso:
https://cursos.alura.com.br/forum/topico-para-quem-nao-conseguiu-completar-a-aula-206690


## Comandos para criar os serviços localmente:

kubectl create -f ./db/permissoes.yaml <br/>
kubectl create -f ./db/statefulset.yaml <br/>
kubectl create -f ./db/servico-banco.yaml <br/>
kubectl create -f ./app/deployment.yaml <br/>
kubectl create -f ./app/servico-aplicacao.yaml <br/> 

## Consultar os serviços criados:
kubectl get pvc -o wide
kubectl get pods -o wide
kubectl get svc -o wide

## Entrar no terminal do POD do banco de dados:
kubectl exec -it statefulset-mysql-0 -- bash
root@statefulset-mysql-0:/#

## Executar os scripts do banco de dados:
No terminal do statefulset-mysql-0, executar os scripts do banco de dados que estão no arquivo <b>./db/scripts.sql</b>

## Listar os serviços em execução:
marcoscrocci at ubuntu-unity in ~/projetos/alura/alura-aks on main✘✘✘ 22-11-30 - 21:50:17 <br/>
╰─⠠⠵ kubectl get services <br/>
| NAME | TYPE | CLUSTER-IP | EXTERNAL-IP | PORT(S) | AGE |
| --- | --- | --- | --- | --- | --- |
| db | ClusterIP | 10.98.33.190 | <none> | 3306/TCP | 15m |
| kubernetes | ClusterIP | 10.96.0.1 | <none> | 443/TCP | 45m |
| servico-aplicacao | LoadBalancer | 10.105.43.97 | <pending> | 80:31981/TCP | 8m13s |

### Abrir o serviço dentro do minikube:
marcoscrocci at ubuntu-unity in ~/projetos/alura/alura-aks on main✘✘✘ 22-11-30 - 21:51:09 <br/>
╰─⠠⠵ minikube service servico-aplicacao

### Serviço do Kubernetes dentro do Azure
Azure Kubernetes Services - AKS

### Instalar o Azure CLI

Documentação: https://learn.microsoft.com/en-us/cli/azure/install-azure-cli

### Fazer login no Azure CLI
No Terminal executar o comando:
az login

Deve abrir o navegador para colocar o usuário e a senha. 
Ao retornar ao Terminal, se o login realmente for realizado com sucesso, deve mostrar que houve a autenticação.

az aks get-credentials --name alurasports_k8s --resource-group alurasports_k8s_group

A partir de então o comando kubectl irá executar as instrução dentro do cluster do AKS.

kubectl get nodes -o wide

kubectl get pods -o wide