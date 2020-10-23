---
title: Mettre à jour l’extension Network Watcher vers la dernière version
description: Découvrez comment mettre à jour l’extension Network Watcher vers la dernière version.
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: fd3fff2d438bbf804e35f04db0cfae15eea5e782
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973337"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>Guide pratique pour mettre à jour l’extension Network Watcher vers la dernière version 

## <a name="overview"></a>Vue d’ensemble

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) est un service d’analyse, de diagnostic et d’analytique des performances réseau permettant de surveiller les réseaux Azure. L’extension de machine virtuelle Agent Network Watcher est obligatoire pour capturer le trafic réseau à la demande et pour assurer la prise en charge d’autres fonctionnalités avancées sur les machines virtuelles Azure. L’extension Network Watcher est utilisée par des fonctionnalités telles que Moniteur de connexion, Moniteur de connexion (préversion), Résolution des problèmes de connexion et Capture de paquets.   

## <a name="prerequisites"></a>Prérequis
Ce document part du principe que l’extension Network Watcher est installée sur votre machine virtuelle, et fournit des instructions pour la mettre à jour vers la version la plus récente. 

## <a name="latest-version"></a>Version la plus récente
La version la plus récente de l’extension Network Watcher est actuellement `1.4.1654.1`.

## <a name="updating-your-extension"></a>Mise à jour de votre extension 

### <a name="check-your-extension-version"></a>Vérifier la version de votre extension  

**À l’aide du portail Azure**

1. Accédez au panneau « Extensions » de votre machine virtuelle dans le portail Azure.   
2. Cliquez sur l’extension « AzureNetworkWatcher » pour afficher le volet d’informations.  
3. Recherchez le numéro de version dans le champ « Version ».  

**À l’aide d’Azure CLI** Exécutez la commande ci-dessous à partir d’une invite Azure CLI.   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Localisez l’extension AzureNetworkWatcher dans la sortie et identifiez le numéro de version mentionné dans le champ « TypeHandlerVersion » dans la sortie.  


**À l’aide de PowerShell** Exécutez les commandes suivantes à partir d’une invite PowerShell :   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Localisez l’extension AzureNetworkWatcher dans la sortie et identifiez le numéro de version mentionné dans le champ « TypeHandlerVersion » dans la sortie.   


### <a name="update-your-extension"></a>Mettre à jour votre extension

Dans le cas où votre version est antérieure à `1.4.1654.1` (la version la plus récente à ce jour), mettez à jour votre extension en utilisant l’une des options suivantes. 

**Option n°1 : Utiliser PowerShell**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**Option n°2 : Utiliser Azure CLI**  

Forcer la mise à niveau 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Si cela ne fonctionne pas, supprimez et réinstallez l’extension en suivant les étapes ci-dessous. La version la plus récente sera alors ajoutée automatiquement. 

Suppression de l’extension 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Réinstallation de l’extension

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**Option 3 : Redémarrer vos machines virtuelles**

Si la mise à niveau automatique est activée pour l’extension Network Watcher. Le redémarrage de votre machine virtuelle installe l’extension la plus récente.


## <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez consulter la documentation de l’extension Network Watcher ([Linux](./network-watcher-linux.md), [Windows](./network-watcher-windows.md)) ou contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).