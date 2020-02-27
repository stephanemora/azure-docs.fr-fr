---
title: Développer sur Azure Kubernetes Service (AKS) avec Draft
description: Utiliser Draft avec AKS et Azure Container Registry
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: b03256ee65a3c40d8a64d70b877c49e44e68f822
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595219"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Démarrage rapide : Développer sur Azure Kubernetes Service (AKS) avec Draft

Draft est un outil open source qui facilite l'empaquetage et l’exécution de conteneurs d’applications dans un cluster Kubernetes. Avec Draft, vous pouvez rapidement redéployer une application sur Kubernetes lorsque des modifications de code apparaissent, sans avoir à valider vos modifications auprès du contrôle de version. Pour plus d’informations sur Draft, consultez la [documentation Draft sur GitHub][draft-documentation].

Cet article montre comment utiliser le package Draft et exécuter une application sur AKS.


## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker installé et configuré. Docker fournit des packages qui le configurent sur un système [Mac][docker-for-mac], [Windows][docker-for-windows] ou [Linux][docker-for-linux].
* [Helm v2 installé][helm-install].
* [Draft installé][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Créer un cluster Azure Kubernetes Service

Créez un cluster AKS. Les commandes ci-dessous créent un groupe de ressources nommé MyResourceGroup et un cluster AKS nommé MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Création d’un Azure Container Registry
Pour utiliser Draft afin d’exécuter votre application dans votre cluster AKS, vous avez besoin d’un registre Azure Container Registry pour stocker vos images conteneur. L’exemple ci-dessous utilise [az acr create][az-acr-create] pour créer un ACR nommé *MyDraftACR* dans le groupe de ressources *MyResourceGroup* avec la référence SKU *De base*. Vous devez indiquer votre nom de registre unique. Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. La référence SKU *De base* est un point d’entrée au coût optimisé fourni à des fins de développement qui offre un bon équilibre entre stockage et débit.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

Le résultat ressemble à l’exemple qui suit. Notez la valeur *loginServer* pour votre ACR, car elle sera utilisée dans une étape ultérieure. Dans l’exemple ci-dessous, *mydraftacr.azurecr.io* est la valeur *loginServer* pour *MyDraftACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```


Pour que Draft utilise l’instance ACR, vous devez commencer par vous connecter. Utilisez la commande [az acr login][az-acr-login] pour vous connecter. L’exemple ci-dessous se connectera à un ACR nommé *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Après son exécution, la commande retourne le message *Login Succeeded (Connexion réussie)* .

## <a name="create-trust-between-aks-cluster-and-acr"></a>Créer une relation d’approbation entre les clusters AKS et ACR

Votre cluster AKS doit également accéder à votre ACR pour extraire les images conteneur et les exécuter. Vous autorisez l’accès à l’ACR à partir d’AKS en établissant une relation d’approbation. Pour établir une relation d’approbation entre un cluster AKS et un registre ACR, vous devez accorder des autorisations pour le principal de service Azure Active Directory utilisé par le cluster AKS pour accéder au registre ACR. Les commandes suivantes accordent des autorisations au principal de service du cluster *MyAKS* dans *MyResourceGroup* à l’ACR *MyDraftACR* dans  *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Se connecter à votre cluster AKS

Pour vous connecter au cluster Kubernetes à partir de votre ordinateur local, utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes.

Si vous utilisez Azure Cloud Shell, `kubectl` est déjà installé. Vous pouvez également l’installer en local à l’aide de la commande [az aks install-cli][] :

```azurecli
az aks install-cli
```

Pour configurer `kubectl` afin de vous connecter à votre cluster Kubernetes, exécutez la commande [az aks get-credentials][]. L’exemple suivant obtient les informations d’identification du cluster AKS nommé *myAKSCluster* dans *MyResourceGroup* :

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Créer un compte de service pour Helm

Avant de déployer Helm dans un cluster AKS où RBAC est activé, vous avez besoin d’un compte de service et d’une liaison de rôle pour le service Tiller. Pour plus d’informations sur la sécurisation de Helm / Tiller dans un cluster où RBAC est activé, consultez [Tiller, Namespaces and RBAC (Tiller, espaces de noms et RBAC)][tiller-rbac]. Si RBAC n’est pas activé sur votre cluster AKS, passez cette étape.

Créez un fichier nommé `helm-rbac.yaml`, et copiez-y le code YAML suivant :

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Créez le compte de service et la liaison de rôle avec la commande `kubectl apply` :

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Configurer Helm
Pour déployer une version basique de Tiller dans un cluster AKS, utilisez la commande [helm init][helm-init]. Si RBAC n’est pas activé sur votre cluster, supprimez l’argument et la valeur `--service-account`.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Configurer Draft

Si vous n’avez pas configuré Draft sur votre ordinateur local, exécutez `draft init` :

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Vous devez également configurer Draft pour utiliser le *loginServer* de votre ACR. La commande suivante utilise `draft config set` pour utiliser `mydraftacr.azurecr.io` comme registre.

```console
draft config set registry mydraftacr.azurecr.io
```

Vous avez configuré Draft pour utiliser votre ACR et Draft peut effectuer l’envoi (push) des images conteneur vers votre ACR. Lorsque Draft exécute votre application dans votre cluster AKS, aucun mot de passe ou secret n’est nécessaire pour l’envoi (push) vers le registre ACR ou le tirage (pull) depuis ce dernier. Comme une relation d’approbation a été créée entre votre cluster AKS et votre ACR, l’authentification se produit au niveau d’Azure Resource Manager, à l’aide d’Azure Active Directory.

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

Ce guide de démarrage rapide utilise [un exemple d’application Java issu du dépôt GitHub de Draft][example-java]. Clonez l’application à partir de GitHub, puis accédez au répertoire `draft/examples/example-java/`.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Exécuter l’exemple d’application avec Draft

Utilisez la commande `draft create` pour préparer l’application.

```console
draft create
```

Cette commande crée les artefacts utilisés pour exécuter l’application dans un cluster Kubernetes. Ces éléments incluent un fichier Dockerfile, un graphique Helm et un fichier *draft.toml* (le fichier de configuration Draft).

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Pour exécuter l’exemple d’application dans votre cluster AKS, utilisez la commande `draft up`.

```console
draft up
```

Cette commande génère le fichier Dockerfile pour créer une image conteneur, effectue l’envoi (push) de cette image vers votre ACR et installe le graphique Helm pour démarrer l’application dans AKS. La première fois que vous exécutiez cette commande, les opérations d’envoi (push) et de tirage (pull) de l’image conteneur peuvent prendre un certain temps. Une fois que les calques de base ont été mis en cache, la durée de déploiement de l’application est considérablement réduite.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Se connecter à l’exemple d’application en cours d’exécution à partir de votre ordinateur local

Pour tester l’application, utilisez la commande `draft connect`.

```console
draft connect
```

Cette commande génère un proxy pour établir une connexion sécurisée au pod Kubernetes. Lorsque ce processus est terminé, l’application est accessible via l’URL fournie.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Accédez à l’application dans un navigateur en utilisant l’URL `localhost` pour voir l’exemple d’application. Dans l’exemple ci-dessus, l’URL est `http://localhost:49804`. Arrêtez la connexion en utilisant `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Accéder à l’application sur Internet

L’étape précédente a permis d’établir une connexion proxy avec le pod d’application dans votre cluster AKS. Lorsque vous développez et testez votre application, vous pouvez rendre l’application disponible sur Internet. Pour exposer une application sur Internet, vous pouvez créer un service Kubernetes de type [LoadBalancer][kubernetes-service-loadbalancer].

Mettez à jour `charts/example-java/values.yaml` pour créer un service *LoadBalancer*. Modifiez la valeur de *service.type* en remplaçant *ClusterIP* par *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Enregistrez vos modifications, fermez le fichier et exécutez `draft up` pour réexécuter l’application.

```console
draft up
```

Le renvoi d’une adresse IP publique par le service prend plusieurs minutes. Pour surveiller la progression, utilisez la commande `kubectl get service` avec le paramètre *watch* :

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Accédez à l’équilibreur de charge de votre application dans un navigateur en utilisant *EXTERNAL-IP* pour voir l’exemple d’application. Dans l’exemple ci-dessus, l’adresse IP est `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Itération sur l’application

Vous pouvez itérer votre application en apportant des modifications localement et en réexécutant `draft up`.

Mettez à jour le message retourné à la [ligne 7 de src/main/java/helloworld/Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Exécutez la commande `draft up` pour redéployer l’application :

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Pour voir l’application mise à jour, accédez de nouveau à l’adresse IP de votre équilibreur de charge et vérifiez que vos modifications s’affichent.

## <a name="delete-the-cluster"></a>Supprimer le cluster

Quand vous n’avez plus besoin du cluster, utilisez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, le cluster AKS, le registre de conteneurs, les images conteneur qui y sont stockées et toutes les ressources associées.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Lorsque vous supprimez le cluster, le principal de service Azure Active Directory utilisé par le cluster AKS n’est pas supprimé. Pour obtenir des instructions sur la façon de supprimer le principal de service, consultez [Considérations et suppression du principal de service AKS][sp-delete].

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l'utilisation de Draft, consultez la documentation Draft sur GitHub.

> [!div class="nextstepaction"]
> [Documentation Draft][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
