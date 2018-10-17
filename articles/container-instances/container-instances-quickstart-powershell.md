---
title: 'Démarrage rapide : Exécuter une application dans Azure Container Instances'
description: Dans ce démarrage rapide, vous allez utiliser Azure PowerShell pour déployer une application s’exécutant dans un conteneur Docker sur Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 33444e810a2deebee11e535c73ce3e249f42b340
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854641"
---
# <a name="quickstart-run-an-application-in-azure-container-instances"></a>Démarrage rapide : Exécuter une application dans Azure Container Instances

Utilisez Azure Container Instances pour exécuter, facilement et rapidement, des conteneurs Docker dans Azure. Vous n’avez pas besoin de déployer de machines virtuelles ou d’utiliser une plateforme d’orchestration de conteneur complète telle que Kubernetes. Dans ce démarrage rapide, vous utilisez le Portail Azure pour créer un conteneur Windows dans Azure et mettez à disposition son application avec un nom de domaine complet. Quelques secondes après l’exécution d’une seule commande de déploiement, vous pouvez accéder à l’application en cours d’exécution :

![Application déployée dans Azure Container Instances affichée dans le navigateur][qs-powershell-01]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.5 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les instances de conteneur Azure, comme toutes les ressources Azure, doivent être déployées dans un groupe de ressources. Les groupes de ressources vous permettent d’organiser et de gérer les ressources Azure connexes.

Commencez par créer un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*, à l’aide de la commande [New-AzureRmResourceGroup][New-AzureRmResourceGroup] suivante :

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Créez un conteneur.

Maintenant que vous avez un groupe de ressources, vous pouvez exécuter un conteneur dans Azure. Pour créer une instance de conteneur avec Azure PowerShell, fournissez un nom de groupe de ressources, un nom d’instance de conteneur et l’image de conteneur Docker à la cmdlet [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Vous pouvez exposer vos conteneurs sur Internet en spécifiant un ou plusieurs ports à ouvrir, une étiquette de nom DNS ou les deux. Dans ce démarrage rapide, vous déployez un conteneur avec une étiquette de nom DNS qui héberge Internet Information Services (IIS) s’exécutant dans Nano Server.

Pour démarrer une instance de conteneur, exécutez la commande suivante. La valeur `-DnsNameLabel` doit être unique au sein de la région Azure dans laquelle vous créez l’instance. Si vous recevez un message d’erreur « Étiquette de nom DNS indisponible », essayez d’utiliser une autre étiquette de nom DNS.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Après quelques secondes, vous devez recevoir une réponse d’Azure. L’élément `ProvisioningState` du conteneur est initialement défini sur **Création**, mais doit passer à **Réussite** après une ou deux minutes. Vérifiez l’état du déploiement à l’aide de la cmdlet [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] :

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

L’état d’approvisionnement, le nom de domaine complet et l’adresse IP du conteneur apparaissent dans la sortie de l’applet de commande :

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Une fois que l’élément `ProvisioningState` du conteneur est défini sur **Réussite**, accédez à `Fqdn` dans votre navigateur. Si vous voyez une page web similaire à l’image suivante, félicitations ! Cela signifie que vous avez réussi à déployer une application s’exécutant dans un conteneur Docker sur Azure.

![IIS déployé à l’aide d’Azure Container Instances affiché dans un navigateur][qs-powershell-01]

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous avez fini d’utiliser le conteneur, vous pouvez le supprimer avec l’applet de commande [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] :

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une instance de conteneur Azure à partir d’une image dans le registre du Hub Docker public. Si vous voulez créer une image conteneur et la déployer à partir d’un registre de conteneurs Azure privé, passez au didacticiel Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
