---
title: 'Démarrage rapide : Exécuter une application dans Azure Container Instances - CLI'
description: Dans ce guide de démarrage rapide, vous utilisez l'interface de ligne de commande Azure pour déployer une application de conteneur Docker pour l’exécuter dans un conteneur isolé dans Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 70d1bc9003d98f0154b9f38738f1b8e82b0c506d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189606"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-with-the-azure-cli"></a>Démarrage rapide : Exécuter une application de conteneur dans Azure Container Instances à l'aide de l'interface de ligne de commande Azure

Utilisez Azure Container Instances pour exécuter, facilement et rapidement, des conteneurs Docker dans Azure. Vous n’avez pas besoin de déployer de machines virtuelles ou d’utiliser une plateforme d’orchestration de conteneur complète telle que Kubernetes. Dans ce guide de démarrage rapide, vous utilisez l'interface de ligne de commande Azure pour créer un conteneur dans Azure et mettez à disposition son application avec un nom de domaine complet. Quelques secondes après l’exécution d’une seule commande de déploiement, vous pouvez accéder à l’application en cours d’exécution :

![Application déployée dans Azure Container Instances affichée dans le navigateur][aci-app-browser]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][azure-account] avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Vous pouvez utiliser le service Azure Cloud Shell ou une installation locale de l’interface Azure CLI pour procéder à ce démarrage rapide. Si vous souhaitez l’utiliser en local, vous avez besoin de la version 2.0.27 ou versions ultérieures. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les instances de conteneur Azure, comme toutes les ressources Azure, doivent être déployées dans un groupe de ressources. Les groupes de ressources vous permettent d’organiser et de gérer les ressources Azure connexes.

Commencez par créer un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* à l’aide de la commande [az group create][az-group-create] suivante :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Créez un conteneur.

Maintenant que vous avez un groupe de ressources, vous pouvez exécuter un conteneur dans Azure. Pour créer une instance de conteneur avec Azure CLI, fournissez un nom de groupe de ressources, un nom d’instance de conteneur et l’image de conteneur Docker à la commande [az container create][az-container-create]. Vous pouvez exposer vos conteneurs sur Internet en spécifiant un ou plusieurs ports à ouvrir, une étiquette de nom DNS ou les deux. Dans ce démarrage rapide, vous déployez un conteneur avec une étiquette de nom DNS qui héberge une petite application web écrite sur la plateforme Node.js.

Pour démarrer une instance de conteneur, exécutez la commande suivante. La valeur `--dns-name-label` doit être unique au sein de la région Azure dans laquelle vous créez l’instance. Si vous recevez un message d’erreur « Étiquette de nom DNS indisponible », essayez d’utiliser une autre étiquette de nom DNS.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

Après quelques secondes, vous devez recevoir une réponse d’Azure CLI indiquant que le déploiement est terminé. Vérifiez son état à l’aide de la commande [az container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Lorsque vous exécutez la commande, le nom de domaine complet du conteneur et son état d’approvisionnement s’affichent.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Si l’élément `ProvisioningState` du conteneur est défini sur **Réussite**, accédez à son nom de domaine complet dans votre navigateur. Si vous voyez une page web similaire à l’image suivante, félicitations ! Cela signifie que vous avez réussi à déployer une application s’exécutant dans un conteneur Docker sur Azure.

![Capture d’écran du navigateur représentant une application exécutée dans une instance de conteneur Azure][aci-app-browser]

Si l’application n’apparaît pas dans un premier temps, vous devrez peut-être patienter quelques secondes, le temps que le DNS se propage, puis essayez d’actualiser votre navigateur.

## <a name="pull-the-container-logs"></a>Extraire les journaux de conteneur

Lorsque vous avez besoin de résoudre les problèmes relatifs à un conteneur ou à l’application qu’il exécute (ou simplement afficher son résultat), commencez par consulter les journaux de l’instance de conteneur.

Extrayez les journaux d’instance de conteneur avec la commande [az container logs][az-container-logs] :

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

La sortie affiche les journaux du conteneur et, normalement, les requêtes HTTP GET générées lorsque vous avez affiché l’application dans votre navigateur.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.105 - - [01/Oct/2018:18:25:51 +0000] "GET / HTTP/1.0" 200 1663 "-" "-"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
```

## <a name="attach-output-streams"></a>Joindre des flux de sortie

En plus de consulter les journaux, vous pouvez joindre vos flux de sortie locale standard et d’erreur standard à ceux du conteneur.

Exécutez d’abord la commande [az container attach][az-container-attach] pour joindre votre console locale aux flux de sortie du conteneur :

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

Une fois joints, actualisez votre navigateur quelques fois pour générer des sorties supplémentaires. Ensuite, détachez votre console avec la commande `Control+C`. Le résultat ressemble à ce qui suit :

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

## <a name="clean-up-resources"></a>Supprimer des ressources

Dès que vous avez fini d’utiliser le conteneur, supprimez-le à l’aide de la commande [az container delete][az-container-delete] :

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Pour vérifier que le conteneur a été supprimé, exécutez la commande [az container list](/cli/azure/container#az-container-list) :

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Le conteneur **mycontainer** ne doit pas apparaître dans la sortie de la commande. Si vous ne disposez d’aucun autre conteneur dans le groupe de ressources, aucune sortie ne s’affiche.

Si vous avez terminé avec le groupe de ressources *myResourceGroup* et toutes les ressources qu’il contient, supprimez-le avec la commande [az group delete][az-group-delete] :

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé une instance de conteneur Azure à l’aide d’une image dans le registre du Hub Docker public. Si vous voulez créer une image conteneur et la déployer à partir d’un registre de conteneurs Azure privé, passez au didacticiel Azure Container Instances.

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
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
