---
title: 'Guide de démarrage rapide : créer son premier conteneur Azure Container Instances'
description: Dans le cadre de ce guide de démarrage rapide, vous allez utiliser Azure CLI pour déployer un conteneur dans Azure Container Instances.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 07632e85719e2d0d446b8f718dbc64d2e9d77617
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441372"
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Guide de démarrage rapide : créer son premier conteneur dans Azure Container Instances

Azure Container Instances facilite la création et la gestion de conteneurs Docker dans Azure, sans avoir à approvisionner les machines virtuelles ou à adopter un service de niveau supérieur. Dans le cadre de ce démarrage rapide, vous créez un conteneur dans Azure et l’exposez sur Internet avec un nom de domaine complet. Cette opération s’effectue en une seule commande. En l’espace de quelques secondes, ceci s’affiche dans votre navigateur :

![L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur][aci-app-browser]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][azure-account] avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Vous pouvez utiliser le service Azure Cloud Shell ou une installation locale de l’interface Azure CLI pour procéder à ce démarrage rapide. Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce démarrage rapide nécessite que vous exécutiez la version 2.0.27 minimum d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les instances Azure Container Instances,comme toutes ressources Azure, doivent être placées dans un groupe de ressources, un ensemble logique dans lequel des ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create][az-group-create].

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Créez un conteneur.

Vous pouvez créer un conteneur en attribuant un nom, une image Docker ainsi qu’un groupe de ressources Azure à la commande [az container create][az-container-create]. Si vous le souhaiter, vous pouvez exposer le conteneur sur Internet en spécifiant une étiquette de nom DNS. Dans ce démarrage rapide, vous déployez un conteneur qui héberge une petite application web écrite sur la plateforme [Node.js][node-js].

Pour démarrer une instance de conteneur, exécutez la commande suivante. La valeur `--dns-name-label` devant être unique dans la région Azure dans laquelle vous créez l’instance, il vous faudra éventuellement modifier le contenu.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

Après quelques secondes, vous obtenez une réponse à votre requête. Initialement, le conteneur est défini sur l’état **En cours de création**, mais il doit démarrer après quelques secondes. Vous pouvez vérifier le statut à l’aide de la commande [az container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Lorsque vous exécutez la commande, le nom de domaine complet du conteneur et son état d’approvisionnement s’affichent :

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Dès que l’état du conteneur est passé à **Réussi**, accédez à son nom de domaine complet dans votre navigateur :

![Capture d’écran du navigateur représentant une application exécutée dans une instance de conteneur Azure][aci-app-browser]

## <a name="pull-the-container-logs"></a>Extraire les journaux de conteneur

Il est utile d’afficher les journaux d’une instance de conteneur pour résoudre des problèmes liés au conteneur ou à l’application qu’il exécute.

Extrayez les journaux du conteneur avec la commande [az container logs][az-container-logs] :

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

La sortie affiche les journaux du conteneur et, normalement, les requêtes HTTP GET générées lorsque vous avez affiché l’application dans votre navigateur.

```console
$ az container logs --resource-group myResourceGroup -n mycontainer
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="attach-output-streams"></a>Joindre des flux de sortie

En plus de les attacher aux journaux, vous pouvez aussi joindre les flux de sorties locales standard et d’erreur standard à ceux du conteneur.

Exécutez d’abord la commande [az container attach][az-container-attach] pour joindre votre console locale aux flux de sortie du conteneur :

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

Une fois joints, actualisez votre navigateur quelques fois pour générer des sorties supplémentaires. Puis détachez votre console avec la commande `Control+C`. Le résultat ressemble à ce qui suit :

```console
$ az container attach --resource-group myResourceGroup -n mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-15 21:17:59+00:00) pulling image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Successfully pulled image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Created container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45
(count: 1) (last timestamp: 2018-03-15 21:18:06+00:00) Started container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45

Start streaming logs:
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:44 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:47 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="clean-up-resources"></a>Supprimer les ressources

Dès que vous avez fini d’utiliser le conteneur, supprimez-le à l’aide de la commande [az container delete][az-container-delete] :

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Pour vérifier que le conteneur a été supprimé, exécutez la commande [az container list](/cli/azure/container#az-container-list) :

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Le conteneur **mycontainer** ne doit pas apparaître dans la sortie de la commande. Si vous ne disposez d’aucun autre conteneur dans le groupe de ressources, aucune sortie ne s’affiche.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une instance de conteneur Azure à partir d’une image dans le registre du Hub Docker public. Si vous voulez créer une image conteneur par vous-même et la déployer dans Azure Container Instances à partir d’un registre de conteneurs Azure privé, passez au tutoriel Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Pour tester les options d’exécution des conteneurs dans un système d’orchestration sur Azure, consultez les guides de démarrage rapide [Service Fabric][service-fabric] et [Azure Kubernetes Service (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
