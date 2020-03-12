---
title: Tutoriel - Déclencher un groupe de conteneurs par fonction Azure
description: Créer une fonction PowerShell serverless déclenchée par HTTP pour automatiser la création d’instances de conteneur Azure
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331023"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Tutoriel : Utiliser une fonction Azure déclenchée par HTTP pour créer un groupe de conteneurs

[Azure Functions](../azure-functions/functions-overview.md) est un service de calcul serverless pouvant exécuter des scripts ou du code en réponse à divers événements, notamment une requête HTTP, un minuteur ou un message dans une file d’attente Stockage Azure.

Dans ce tutoriel, vous allez créer une fonction Azure qui prend une requête HTTP et déclenche le déploiement d’un [groupe de conteneurs](container-instances-container-groups.md). Cet exemple présente les bases de l’utilisation d’Azure Functions pour créer automatiquement des ressources dans Azure Container Instances. Vous pouvez modifier ou étendre cet exemple pour des scénarios plus complexes ou d’autres déclencheurs d’événements. 

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Utiliser Visual Studio Code avec l’[extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pour créer une fonction PowerShell de base déclenchée par HTTP.
> * Activer une identité dans l’application de fonction et lui accorder les autorisations nécessaires pour créer des ressources Azure.
> * Modifier et republier la fonction PowerShell pour automatiser le déploiement d’un groupe de conteneurs monoconteneur.
> * Vérifier le déploiement déclenché par HTTP du conteneur.

> [!IMPORTANT]
> PowerShell pour Azure Functions est actuellement disponible en préversion. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="prerequisites"></a>Prérequis

Consultez [Créer votre première fonction dans Azure](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment) pour obtenir les prérequis à l’installation et à l’utilisation de Visual Studio Code avec Azure Functions sur votre système d’exploitation.

Certaines étapes présentées dans cet article utilisent l’interface de ligne de commande Azure (Azure CLI). Pour effectuer ces étapes, vous pouvez utiliser Azure Cloud Shell ou une installation locale d’Azure CLI. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Créer une fonction PowerShell de base

Suivez les étapes de la section [Créer votre première fonction PowerShell dans Azure](../azure-functions/functions-create-first-function-powershell.md) pour créer une fonction PowerShell à l’aide du modèle Déclencheur HTTP. Utilisez le nom de fonction Azure par défaut **HttpTrigger**. Comme indiqué dans le guide de démarrage rapide, testez la fonction localement, puis publiez le projet sur une application de fonction dans Azure. Cet exemple est une fonction de base déclenchée par HTTP qui retourne une chaîne de texte. Dans les étapes ultérieures de cet article, vous modifierez la fonction pour créer un groupe de conteneurs.

Cet article part du principe que vous publiez le projet avec le nom *myfunctionapp* dans un groupe de ressources Azure nommé automatiquement en fonction du nom de l’application de fonction (également *myfunctionapp*). Dans les étapes ultérieures, utilisez à la place le nom de votre application de fonction et le nom de votre groupe de ressources.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Activer une identité managée par Azure dans l’application de fonction

Activez à présent une [identité managée](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) affectée par le système dans votre application de fonction. L’hôte PowerShell qui exécute l’application peut s’authentifier automatiquement à l’aide de cette identité, ce qui permet aux fonctions d’effectuer des actions sur les services Azure auxquels l’accès a été accordé à l’identité. Dans ce tutoriel, vous allez accorder à l’identité managée les autorisations nécessaires pour créer des ressources dans le groupe de ressources de l’application de fonction. 

Commencez par utiliser la commande [az group show][az-group-show] pour obtenir l’ID du groupe de ressources de l’application de fonction et le stocker dans une variable d’environnement. Cet exemple part du principe que vous exécutez la commande dans un interpréteur de commandes bash.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Exécutez [az functionapp identity app assign][az-functionapp-identity-app-assign] pour affecter une identité locale à l’application de fonction et attribuer un rôle de contributeur au groupe de ressources. Ce rôle permet à l’identité de créer des ressources supplémentaires, notamment des groupes de conteneurs dans le groupe de ressources.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Modifier la fonction HttpTrigger

Modifiez le code PowerShell pour que la fonction **HttpTrigger** crée un groupe de conteneurs. Dans le fichier `run.ps1` de la fonction, recherchez le bloc de code suivant. Ce code affiche une valeur de nom si elle est passée en tant que chaîne de requête dans l’URL de la fonction :

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Remplacez ce code par le bloc d’exemple suivant. Ici, si une valeur de nom est passée dans la chaîne de requête, elle est utilisée pour nommer et créer un groupe de conteneurs à l’aide de l’applet de commande [New-AzContainerGroup][new-azcontainergroup]. Veillez à remplacer le nom du groupe de ressources *myfunctionapp* par le nom du groupe de ressources de votre application de fonction :

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

Cet exemple crée un groupe de conteneurs constitué d’une instance monoconteneur exécutant l’image `alpine`. Le conteneur exécute une seule commande `echo`, puis s’arrête. Dans un exemple concret, vous pouvez déclencher la création d’un ou plusieurs groupes de conteneurs pour exécuter un traitement par lots.
 
## <a name="test-function-app-locally"></a>Tester l’application de fonction localement

Vérifiez que la fonction s’exécute correctement au niveau local avant de republier le projet d’application de fonction sur Azure. Comme indiqué dans le [guide de démarrage rapide PowerShell](../azure-functions/functions-create-first-function-powershell.md), insérez un point d’arrêt local dans le script PowerShell et un appel `Wait-Debugger` au-dessus de celui-ci. Pour obtenir de l’aide sur le débogage, consultez [Déboguer localement des fonctions PowerShell Azure](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Republier l’application de fonction Azure

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps de republier le projet sur l’application de fonction existante dans Azure.

> [!NOTE]
> N’oubliez pas de supprimer tous les appels à `Wait-Debugger` avant de publier vos fonctions sur Azure.

1. Dans Visual Studio Code, ouvrez la palette de commandes. Recherchez et sélectionnez `Azure Functions: Deploy to function app...`.
1. Sélectionnez le dossier de travail actif à compresser et à déployer.
1. Sélectionnez l’abonnement, puis le nom de l’application de fonction existante (*myfunctionapp*). Confirmez que vous souhaitez remplacer le déploiement précédent.

Une notification s’affiche après que votre application de fonction a été créée et que le package de déploiement a été appliqué. Sélectionnez **Afficher la sortie** dans cette notification pour afficher les résultats de la création et du déploiement, y compris les ressources Azure que vous avez mises à jour.

## <a name="run-the-function-in-azure"></a>Exécuter la fonction dans Azure

Une fois le déploiement terminé, obtenez l’URL de la fonction. Par exemple, utilisez la zone **Azure : Fonctions** dans Visual Studio Code pour copier l’URL de fonction **HttpTrigger** ou pour obtenir l’URL de fonction dans le [Portail Azure](../azure-functions/functions-create-first-azure-function.md#test-the-function).

L’URL de fonction comprend un code unique et se présente sous la forme suivante :

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Exécuter la fonction sans passer de nom

En guise de premier test, exécutez la commande `curl` et passez l’URL de la fonction sans ajouter de chaîne de requête `name`. Veillez à inclure le code unique de votre fonction.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

La fonction retourne le code d’état 400 et le texte `Please pass a name on the query string or in the request body` :

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Exécuter la fonction et passer le nom d’un groupe de conteneurs

Exécutez à présent la commande `curl` en ajoutant le nom d’un groupe de conteneurs (*mycontainergroup*) en tant que chaîne de requête `&name=mycontainergroup` :

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

La fonction retourne le code d’état 200 et déclenche la création du groupe de conteneurs :

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Vérifiez que le conteneur a été exécuté avec la commande [az container logs][az-container-logs] :

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Exemple de sortie :

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources que vous avez créées dans ce tutoriel, vous pouvez exécuter la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Cette commande supprime le registre de conteneurs que vous avez créé, ainsi que le conteneur en cours d’exécution et toutes les ressources associées.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une fonction Azure qui prend une requête HTTP et déclenche le déploiement d’un groupe de conteneurs. Vous avez appris à :

> [!div class="checklist"]
> * Utiliser Visual Studio Code avec l’extension Azure Functions pour créer une fonction PowerShell de base déclenchée par HTTP.
> * Activer une identité dans l’application de fonction et lui accorder les autorisations nécessaires pour créer des ressources Azure.
> * Modifier le code de la fonction PowerShell pour automatiser le déploiement d’un groupe de conteneurs monoconteneur.
> * Vérifier le déploiement déclenché par HTTP du conteneur.

Pour obtenir un exemple détaillé de lancement et de supervision d’un travail conteneurisé, consultez le billet de blog [Event-Driven Serverless Containers with PowerShell Azure Functions and Azure Container Instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) et l’[exemple de code](https://github.com/anthonychu/functions-powershell-run-aci) associé.

Consultez la [documentation sur Azure Functions](/azure/azure-functions/) pour obtenir des instructions détaillées sur la création de fonctions Azure et la publication d’un projet de fonctions. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
