---
title: Utiliser Draft avec AKS et Azure Container Registry
description: Utiliser Draft avec AKS et Azure Container Registry
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: d94f70771cf3ee364dbb3e4c4256cd2248ce3828
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164770"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Utiliser Draft avec Azure Kubernetes Service (AKS)

Draft est un outil open source qui permet d’empaqueter et de déployer des conteneurs d’applications dans un cluster Kubernetes, vous permettant de vous concentrer sur le cycle de développement, la « boucle intérieure » de développement concentré. Draft fonctionne lorsque le code est en cours de développement, mais avant la validation en gestion de version. Avec Draft, vous pouvez rapidement redéployer une application sur Kubernetes à mesure que le code est modifié. Pour plus d’informations sur Draft, consultez la [documentation Draft sur GitHub][draft-documentation].

Cet article vous montre comment utiliser Draft avec un cluster Kubernetes AKS.

## <a name="prerequisites"></a>Prérequis

Les étapes détaillées dans cet article supposent que vous avez créé un cluster AKS et que vous avez établi une connexion `kubectl` avec le cluster. Si vous avez besoin de ces éléments, consultez le [guide de démarrage rapide d’ACS][aks-quickstart].

Vous avez besoin d’un registre Docker privé dans Azure Container Registry (ACR). Pour savoir comment créer une instance ACR, consultez [Démarrage rapide d’Azure Container Registry][acr-quickstart].

Helm doit également être installé dans votre cluster AKS. Pour plus d’informations sur l’installation et la configuration de Helm, consultez [Utiliser Helm avec Azure Kubernetes Service (AKS)][aks-helm].

Enfin, vous devez installer [Docker](https://www.docker.com).

## <a name="install-draft"></a>Installer Draft

L’interface CLI Draft est un client qui s’exécute sur votre système de développement et vous permet de déployer un code dans un cluster Kubernetes. Pour installer l’interface CLI Draft sur un Mac, utilisez `brew`. Pour connaître les autres options d’installation, consultez le [guide d’installation de Draft][draft-documentation].

> [!NOTE]
> Si vous avez installé une version de Draft antérieure à la version 0.12, vous devez tout d’abord supprimer Draft de votre cluster à l’aide de `helm delete --purge draft`, puis supprimer votre configuration locale en exécutant `rm -rf ~/.draft`. Si vous êtes sur MacOS, exécutez ensuite `brew upgrade draft`.

```console
brew tap azure/draft
brew install draft
```

À présent, initialisez Draft avec la commande `draft init` :

```console
draft init
```

## <a name="configure-draft"></a>Configurer Draft

Draft génère les images conteneur localement, puis les déploie à partir du registre local (par exemple avec Minikube) ou utilise un registre d’image que vous spécifiez. Cet article utilise Azure Container Registry (ACR). Vous devez donc établir une relation d’approbation entre votre cluster AKS et le registre ACR, puis configurer Draft pour envoyer vos images conteneur à ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Créer une relation d’approbation entre les clusters AKS et ACR

Pour établir une relation d’approbation entre un cluster AKS et un registre ACR, vous devez accorder des autorisations pour le principal de service Azure Active Directory utilisé par le cluster AKS pour accéder au registre ACR. Dans les commandes suivantes, indiquez votre propre `<resourceGroupName>`, remplacez `<aksName>` par le nom de votre cluster AKS et remplacez `<acrName>` par le nom de votre registre ACR :

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

Pour plus d’informations sur la procédure à suivre pour accéder au registre ACR, consultez [S’authentifier avec ACR](../container-registry/container-registry-auth-aks.md).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Configurer Draft pour envoyer vers/déployer à partir de ACR

Maintenant qu’il existe une relation d’approbation entre AKS et ACR, vous devez autoriser l’utilisation d’ACR à partir de votre cluster AKS.

1. Définissez la valeur de *Registre* de configuration Draft. Dans les commandes suivantes, remplacez `<acrName>` par le nom de votre registre ACR :

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. Connectez-vous au registre ACR en exécutant [az acr login][az-acr-login] :

    ```azurecli
    az acr login --name <acrName>
    ```

Comme une relation d’approbation a été créée entre AKS et ACR, aucun mot de passe ou secret n’est nécessaire pour la transmission ou l’extraction depuis le registre ACR. L’authentification s’effectue au niveau d’Azure Resource Manager, à l’aide d’Azure Active Directory.

## <a name="run-an-application"></a>Exécuter une application

Pour voir comment fonctionne Draft, nous allons déployer un exemple d’application à partir du [référentiel Draft][draft-repo]. Commencez par cloner le référentiel :

```console
git clone https://github.com/Azure/draft
```

Accédez au répertoire d’exemples Java :

```console
cd draft/examples/example-java/
```

Utilisez la commande `draft create` pour démarrer le processus. Cette commande crée les artefacts utilisés pour exécuter l’application dans un cluster Kubernetes. Ces éléments incluent un fichier Dockerfile, un graphique Helm et un fichier *draft.toml* (le fichier de configuration Draft).

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Pour exécuter l’exemple d’application dans votre cluster AKS, utilisez la commande `draft up`. Cette commande génère ensuite le fichier Dockerfile pour créer une image conteneur, envoie l’image vers ACR puis installe le graphique Helm pour démarrer l’application dans AKS.

Lors de la première exécution de cette commande, les opérations de transmission et d’extraction de l’image conteneur peuvent prendre un certain temps. Une fois que les calques de base ont été mis en cache, la durée de déploiement de l’application est considérablement réduite.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Si vous rencontrez des problèmes lors de la transmission de l’image Docker, assurez-vous que vous vous êtes bien connecté à votre registre ACR à l’aide de la commande [az acr login][az-acr-login], puis exécutez à nouveau la commande `draft up`.

## <a name="test-the-application-locally"></a>Tester localement l’application

Pour tester l’application, utilisez la commande `draft connect`. Cette commande génère un proxy pour établir une connexion sécurisée au pod Kubernetes. Lorsque ce processus est terminé, l’application est accessible via l’URL fournie.

> [!NOTE]
> Le téléchargement de l’image conteneur et le démarrage de l’application peuvent prendre plusieurs minutes. Si vous recevez une erreur lors de l’accès à l’application, relancez la connexion.

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Pour accéder à votre application, ouvrez un navigateur web avec l’adresse et le port spécifiés dans la sortie `draft connect`, tels que *http://localhost:49804*. 

![Exemple d’application Java en cours d’exécution avec Draft](media/kubernetes-draft/sample-app.png)

Utilisez `Control+C` pour arrêter la connexion proxy.

> [!NOTE]
> Vous pouvez également utiliser la commande `draft up --auto-connect` pour créer et déployer votre application et vous connecter immédiatement au premier conteneur en cours d’exécution.

## <a name="access-the-application-on-the-internet"></a>Accéder à l’application sur Internet

L’étape précédente a permis d’établir une connexion proxy avec le pod d’application dans votre cluster AKS. Lorsque vous développez et testez votre application, vous pouvez rendre l’application disponible sur Internet. Pour exposer une application sur Internet, vous devez créer un service Kubernetes de type [LoadBalancer][kubernetes-service-loadbalancer] ou créer un [contrôleur d’entrée][kubernetes-ingress]. Créons un service *LoadBalancer*.

Tout d’abord, mettez à jour le pack Draft *values.yaml* pour indiquer qu’un service de type *LoadBalancer* doit être créé :

```console
vi charts/java/values.yaml
```

Recherchez la propriété *service.type* et remplacez la valeur *ClusterIP* par *LoadBalancer*, comme illustré dans l’exemple condensé suivant :

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

Enregistrez et fermez le fichier, puis utilisez `draft up` pour réexécuter l’application :

```console
draft up
```

Le renvoi d’une adresse IP publique par le service prend plusieurs minutes. Pour surveiller la progression, utilisez la commande `kubectl get service` avec le paramètre *watch* :

```console
kubectl get service --watch
```

Au début, l’adresse IP externe (*EXTERNAL-IP*) du service apparaît comme *En attente* :

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

Une fois que l’adresse IP externe est passée du statut *En attente* à Adresse IP, utilisez `Control+C` pour arrêter le processus de surveillance `kubectl` :

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Pour afficher l’application, accédez à l’adresse IP externe de votre équilibreur de charge avec `curl` :

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Itération sur l’application

Maintenant que Draft a été configuré et que l’application est en cours d’exécution dans Kubernetes, vous pouvez passer à l’itération du code. Chaque fois que vous souhaitez tester le code mis à jour, exécutez la commande `draft up` pour mettre à jour l’application en cours d’exécution.

Dans cet exemple, mettez à jour l’exemple d’application Java pour modifier le texte d’affichage. Ouvrez le fichier *Hello.java* :

```console
vi src/main/java/helloworld/Hello.java
```

Mettez à jour le texte de sortie à afficher, *Hello World, I'm Java in AKS!* :

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
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

Pour voir l’application mise à jour, exécutez à nouveau la commande curl sur l’adresse IP de votre équilibreur de charge :

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l'utilisation de Draft, consultez la documentation Draft sur GitHub.

> [!div class="nextstepaction"]
> [Documentation Draft][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress-basic.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
