---
title: Utiliser Draft avec AKS et Azure Container Registry
description: Utiliser Draft avec AKS et Azure Container Registry
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2ab79e3a6308d01d836a82f356f43eccb6af9791
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="use-draft-with-azure-container-service-aks"></a>Utiliser Draft avec Azure Container Service (AKS)

Draft est un outil open source qui permet de contenir et de déployer ces conteneurs dans un cluster Kubernetes, vous permettant de vous concentrer sur le cycle de développement, la « boucle intérieure » de développement concentré. Draft fonctionne lorsque le code est en cours de développement, mais avant la validation en gestion de version. Avec Draft, vous pouvez rapidement redéployer une application sur Kubernetes à mesure que le code est modifié. Pour plus d’informations sur Draft, consultez la [documentation Draft sur GitHub][draft-documentation].

Ce document détaille l'utilisation de Draft avec un cluster Kubernetes sur AKS.

## <a name="prerequisites"></a>Prérequis


Les étapes détaillées dans ce document supposent que vous ayez créé un cluster ACS et que vous ayez établi une connexion kubectl avec le cluster. Si vous avez besoin de ces éléments, consultez le [guide de démarrage rapide d’ACS][aks-quickstart].

Vous avez également besoin d'un registre Docker privé dans Azure Container Registry (ACR). Pour obtenir des instructions sur le déploiement d’une instance ACR, consultez [Démarrage rapide d’Azure Container Registry][acr-quickstart].

Helm doit également être installé dans votre cluster AKS. Pour plus d’informations sur l’installation de Helm, consultez [Use Helm with Azure Container Service and Kubernetes][aks-helm] (Utiliser Helm avec Azure Container Service et Kubernetes).

Enfin, vous devez installer [Docker](https://www.docker.com).

## <a name="install-draft"></a>Installer Draft

L’interface CLI Draft est un client qui s’exécute sur votre système de développement et vous permet de rapidement déployer un code dans un cluster Kubernetes. 

> [!NOTE] 
> Si vous avez installé une version de Draft antérieure à 0.12, vous devez tout d’abord supprimer Draft de votre cluster à l’aide de `helm delete --purge draft`, puis supprimer votre configuration locale en exécutant `rm -rf ~/.draft`. Si vous êtes sur MacOS, vous pouvez exécuter `brew upgrade draft`.

Pour installer l’interface CLI Draft sur un Mac, utilisez `brew`. Pour connaître les autres options d’installation, consultez le [guide d’installation de Draft][install-draft].

```console
brew tap azure/draft
brew install draft
```

À présent, initialisez Draft avec la commande `draft init`.

```console
draft init
```

## <a name="configure-draft"></a>Configurer Draft

Draft génère les images conteneur localement, puis soit il les déploie à partir du registre local (dans le cas de Minikube), soit vous devez spécifier le registre d’image à utiliser. Cet exemple utilise Azure Container Registry (ACR), vous devez donc établir une relation d’approbation entre votre cluster AKS et le registre ACR et configurer Draft pour envoyer le conteneur à ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Créer une relation d’approbation entre les clusters AKS et ACR

Pour établir une relation d’approbation entre un cluster AKS et un registre ACR, vous devez modifier le principal de service Azure Active Directory utilisé avec AKS en y ajoutant le rôle de contributeur avec comme étendue le référentiel ACR. Pour ce faire, exécutez les commandes suivantes, en remplaçant _&lt;aks-rg-name&gt;_ et _&lt;aks-cluster-name&gt;_ par le groupe de ressources et le nom de votre cluster AKS, et _&lt;acr-rg-nam&gt;_ et _&lt;acr-repo-name&gt;_ par le groupe de ressources et le nom de référentiel de votre référentiel ACR avec lequel vous souhaitez établir une relation d’approbation.

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

(Ces étapes ainsi que d’autres mécanismes d’authentification pour accéder à ACR se trouvent sur la page [S’authentifier avec ACR](../container-registry/container-registry-auth-aks.md).)

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Configurer Draft pour envoyer vers/déployer à partir de ACR

Maintenant qu’il existe une relation d’approbation entre AKS et ACR, les étapes suivantes permettent l’utilisation d’ACR à partir de votre cluster AKS.
1. Définissez la valeur `registry` de configuration Draft en exécutant `draft config set registry <registry name>.azurecr.io`, où _&lt;registry name&lt;_ est le nom de votre registre ACR.
2. Connectez-vous au registre ACR en exécutant `az acr login -n <registry name>`. 

Puisque vous êtes désormais connecté en local à ACR et que vous avez créé une relation d’approbation entre AKS et ACR, aucun mot de passe ou secret n’est nécessaire pour la transmission ou l’extraction depuis ACR vers AKS. L’authentification s’effectue au niveau d’Azure Resource Manager, à l’aide d’Azure Active Directory. 

## <a name="run-an-application"></a>Exécuter une application

Le référentiel Draft contient plusieurs exemples d’applications qui peuvent être utilisés lors d'une démonstration de Draft. Créez une copie clonée du référentiel.

```console
git clone https://github.com/Azure/draft
```

Accédez au répertoire d’exemples Java.

```console
cd draft/examples/example-java/
```

Utilisez la commande `draft create` pour démarrer le processus. Cette commande crée les artefacts utilisés pour exécuter l’application dans un cluster Kubernetes. Ces éléments incluent un fichier Dockerfile, un graphique Helm et un fichier `draft.toml` (le fichier de configuration Draft).

```console
draft create
```

Output:

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Pour exécuter l’application sur un cluster Kubernetes, utilisez la commande `draft up`. Cette commande génère ensuite le fichier Dockerfile pour créer une image conteneur, envoie l’image vers ACR puis installe le graphique Helm pour démarrer l’application dans AKS.

La première fois que cette commande est exécutée, la transmission ou l’extraction de l’image conteneur peut prendre du temps ; une fois que les calques de base sont mis en cache, la durée est considérablement réduite.

```console
draft up
```

Output:

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>Test de l'application

Pour tester l’application, utilisez la commande `draft connect`. Cette commande génère un proxy pour établir une connexion au pod Kubernetes et permettre une connexion locale sécurisée. Lorsque ce processus est terminé, l’application est accessible via l’URL fournie.

Dans certains cas, le téléchargement de l’image de conteneur et le démarrage de l’application peuvent prendre plusieurs minutes. Si vous recevez une erreur lors de l’accès à l’application, relancez la connexion.

```console
draft connect
```

Output:

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Vous pouvez maintenant tester votre application en accédant à http://localhost:46143 (pour l’exemple précédent ; il se peut que votre port soit différent). Lorsque vous avez fini de tester l'application, utilisez la commande `Control+C` pour interrompre la connexion proxy.

> [!NOTE]
> Vous pouvez également utiliser la commande `draft up --auto-connect` pour créer et déployer votre application et vous connecter immédiatement au premier conteneur en cours d’exécution afin d’accélérer encore plus le cycle d’itération.

## <a name="expose-application"></a>Exposer l’application

Lorsque vous testez une application dans Kubernetes, vous pouvez rendre l’application disponible sur Internet. Pour cela, vous utilisez un service Kubernetes de type [LoadBalancer][kubernetes-service-loadbalancer] ou un [contrôleur d’entrée][kubernetes-ingress]. Ce document détaille l'utilisation d’un service Kubernetes.


Tout d’abord, le pack Draft doit être mis à jour pour indiquer qu’un service de type `LoadBalancer` doit être créé. Pour cela, mettez à jour le type de service dans le fichier `values.yaml`.

```console
vi charts/java/values.yaml
```

Recherchez la propriété `service.type` et remplacez la valeur `ClusterIP` par `LoadBalancer`.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

Exécutez `draft up` pour relancer l'application.

```console
draft up
```

Le renvoi d'une adresse IP publique par le service peut prendre plusieurs minutes. Pour suivre la progression de l'opération, utilisez la commande `kubectl get service` avec un espion.

```console
kubectl get service -w
```

Au début, l’*adresse IP externe* pour le service apparaît comme `pending`.

```
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Une fois que l’adresse IP externe est passée du statut `pending` à `IP address`, utilisez `Control+C` pour arrêter le processus de surveillance kubectl.

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Pour afficher l’application, accédez à l’adresse IP externe.

```console
curl 52.175.224.118
```

Output:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Itération sur l’application

Maintenant que Draft a été configuré et que l’application est en cours d’exécution dans Kubernetes, vous pouvez passer à l’itération du code. Chaque fois que vous souhaitez tester le code mis à jour, exécutez la commande `draft up` pour mettre à jour de l’application en cours d’exécution.

Pour cet exemple, mettez à jour l’application Java Hello World.

```console
vi src/main/java/helloworld/Hello.java
```

Mettez à jour le texte Hello World.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Exécutez la commande `draft up --auto-connect` pour redéployer l’application dès qu’un pod est prêt à répondre.

```console
draft up --auto-connect
```

Sortie

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

Enfin, affichez l’application pour voir les mises à jour.

```console
curl 52.175.224.118
```

Output:

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l'utilisation de Draft, consultez la documentation Draft sur GitHub.

> [!div class="nextstepaction"]
> [Documentation Draft][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[install-draft]: https://github.com/Azure/draft/blob/master/docs/install.md
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
