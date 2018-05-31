---
title: Définir des variables d’environnement dans Azure Container Instances
description: Découvrez comment définir des variables d’environnement dans les conteneurs que vous exécutez dans Azure Container Instances
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/16/2018
ms.author: marsma
ms.openlocfilehash: 1a025ce647cb3c071a6549a433e6505b85409fdc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199005"
---
# <a name="set-environment-variables"></a>Définition des variables d'environnement

Définir des variables d’environnement dans vos instances de conteneur vous permet de fournir une configuration dynamique de l’application ou du script exécuté par le conteneur. Pour définir des variables d’environnement dans un conteneur, spécifiez-les au moment de créer l’instance de conteneur. Vous pouvez définir des variables d’environnement lorsque vous démarrez un conteneur avec [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)et le [portail Azure](#azure-portal-example).

Par exemple, si vous exécutez l’image conteneur [microsoft/aci-wordcount][aci-wordcount], vous pouvez modifier son comportement en spécifiant les variables d’environnement suivantes :

*NumWords* : nombre de mots envoyés à STDOUT.

*MinLength* : nombre minimal de caractères dans un mot pour que celui-ci soit comptabilisé. Cela vous permet d’ignorer les mots communs tels que « de » et « le ».

## <a name="azure-cli-example"></a>Exemple Azure CLI

Pour afficher la sortie par défaut du conteneur [microsoft/aci-wordcount][aci-wordcount], exécutez-le d’abord avec la commande [az container create][az-container-create] (aucune variable d’environnement spécifiée) :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Pour modifier la sortie, démarrez un deuxième conteneur auquel vous avez ajouté l’argument `--environment-variables`, en spécifiant des valeurs pour les variables *NumWords* et *MinLength* :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Une fois que l’état de ces deux conteneurs est *Terminé* (utilisez [az container show][az-container-show] pour vérifier l’état), affichez leurs journaux avec [az container logs][az-container-logs] pour consulter leur contenu.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

La sortie des conteneurs indique la façon dont vous avez modifié le comportement du script du deuxième conteneur en définissant les variables d’environnement.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Exemple Azure PowerShell

La définition de variables d’environnement dans PowerShell est similaire à celle effectuée dans l’interface CLI, à ceci près qu’elle utilise l’argument de ligne de commande `-EnvironmentVariable`.

Tout d’abord, lancez le conteneur [microsoft/aci-wordcount][aci-wordcount] avec sa configuration par défaut, à l’aide de cette commande [New-AzureRmContainerGroup][new-azurermcontainergroup] :

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

Ensuite, exécutez la commande [New-AzureRmContainerGroup][new-azurermcontainergroup]. Celle-ci spécifie les variables d’environnement *NumWords* et *MinLength* après le remplissage de la variable tableau `envVars` :

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Lorsque l’état de ces deux conteneurs est *Terminé* (utilisez [Get-AzureRmContainerInstanceLog][azure-instance-log] pour vérifier l’état), récupérez leurs journaux à l’aide de la commande [Get-AzureRmContainerInstanceLog][azure-instance-log].

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

La sortie de chaque conteneur indique la façon dont vous avez modifié le script exécuté par le conteneur en définissant les variables d’environnement.

```console
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Exemple du portail Azure

Pour définir des variables d’environnement lorsque vous démarrez un conteneur dans le portail Azure, spécifiez-les dans la page **Configuration** lors de la création du conteneur.

Lorsque vous effectuez un déploiement à partir du portail, vous êtes limité à trois variables, que vous devez entrer au format suivant : `"variableName":"value"`

Pour voir un exemple, démarrez le conteneur [microsoft/aci-wordcount][aci-wordcount] avec les variables *NumWords* et *MinLength*.

1. Dans **Configuration**, définissez la **Stratégie de redémarrage** sur *En cas d’échec*.
2. Entrez `"NumWords":"5"` pour la première variable, sélectionnez **Oui** sous **Ajouter des variables d’environnement supplémentaires**, puis entrez `"MinLength":"8"` pour la deuxième variable. Sélectionnez **OK** pour vérifier, puis déployer le conteneur.

![Page du portail montrant le bouton d’activation et les zones de texte des variables d’environnement][portal-env-vars-01]

Pour afficher les journaux du conteneur, sous **Paramètres**, sélectionnez **Conteneurs**, puis **Journaux**. Comme pour la sortie des sections CLI et PowerShell précédentes, vous voyez que le comportement du script a été modifié par les variables d’environnement. Seuls cinq mots sont affichés, chacun avec une longueur minimale de huit caractères.

![Sortie du journal du conteneur dans le portail][portal-env-vars-02]

## <a name="next-steps"></a>Étapes suivantes

Les scénarios basés sur des tâches, telles que le traitement par lots d’un jeu de données volumineux avec plusieurs conteneurs, peuvent bénéficier de l’utilisation de variables d’environnement personnalisées lors de l’exécution. Pour plus d’informations sur l’exécution des conteneurs basés sur des tâches, consultez [Exécuter des tâches en conteneur dans Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com