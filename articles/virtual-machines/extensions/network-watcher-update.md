---
title: Mettre à jour l’extension Network Watcher vers la dernière version
description: Découvrez comment mettre à jour l’extension Azure Network Watcher vers la dernière version.
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
ms.openlocfilehash: 23520a0249e22b3f81c7f7c598ef10d8c3acb550
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900184"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Mettre à jour l’extension Network Watcher vers la dernière version

## <a name="overview"></a>Vue d’ensemble

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) est un service d’analyse, de diagnostic et d’analytique des performances réseau surveillant les réseaux Azure. L’extension de machine virtuelle Agent Network Watcher est obligatoire pour capturer le trafic réseau à la demande et pour assurer la prise en charge d’autres fonctionnalités avancées sur les machines virtuelles Azure. L’extension Network Watcher est utilisée par des fonctionnalités telles que Moniteur de connexion, Moniteur de connexion (préversion), Résolution des problèmes de connexion et Capture de paquets.

## <a name="prerequisites"></a>Prérequis

Cet article suppose que l’extension Network Watcher est installée sur votre machine virtuelle.

## <a name="latest-version"></a>Version la plus récente

La version la plus récente de l’extension Network Watcher est actuellement `1.4.1654.1`.

## <a name="update-your-extension"></a>Mettre à jour votre extension

Pour mettre à jour votre extension, vous devez connaître la version de votre extension.

### <a name="check-your-extension-version"></a>Vérifier la version de votre extension

Vous pouvez vérifier la version de votre extension à l’aide du portail Azure, d’Azure CLI ou de PowerShell.

#### <a name="usetheazureportal"></a>Utiliser le portail Azure

1. Accédez au volet **Extensions** de votre machine virtuelle dans le portail Azure.
1. Sélectionnez l’extension **AzureNetworkWatcher** pour afficher le volet d’informations.
1. Recherchez le numéro de version dans le champ **Version** .  

#### <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

Exécutez la commande suivante à partir d’une invite de commandes Azure CLI :

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
Localisez **AzureNetworkWatcherExtension** dans la sortie et identifiez le numéro de version mentionné dans le champ «  *TypeHandlerVersion*  » dans la sortie.  Notez ce qui suit : Les informations sur l’extension apparaissent plusieurs fois dans la sortie JSON. Regardez sous le bloc « Extensions » ; le numéro de version complet de l’extension devrait s’afficher. 

Vous devez voir quelque chose semblable à ce qui présenté ci-dessous : ![Capture d’écran d’Azure CLI](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>Utiliser PowerShell

À partir d’une invite de commande PowerShell, exécutez les commandes suivantes :

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
Localisez AzureNetworkWatcherExtension dans la sortie et identifiez le numéro de version mentionné dans le champ «  *TypeHandlerVersion*  » dans la sortie.   

Vous devez voir quelque chose semblable à ce qui présenté ci-dessous : ![Capture d’écran de PowerShell](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>Mettre à jour votre extension

Si votre version est antérieure à `1.4.1654.1`, la version la plus récente à ce jour, mettez à jour votre extension en utilisant l’une des options suivantes.

#### <a name="option-1-use-powershell"></a>Option 1 : Utiliser PowerShell

Exécutez les commandes suivantes :

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"
```

Si cela ne fonctionne pas, supprimez et réinstallez l’extension en suivant les étapes ci-dessous. La version la plus récente sera alors ajoutée automatiquement.

Suppression de l’extension 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

Réinstallation de l’extension

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>Option n°2 : Utilisation de l’interface de ligne de commande Microsoft Azure

Forcez une mise à niveau.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Si cela ne fonctionne pas, supprimez et réinstallez l’extension, puis procédez comme suit pour ajouter automatiquement la dernière version.

Supprimez l’extension.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Installez l’extension à nouveau.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>Option 3 : Redémarrer vos machines virtuelles

Si la mise à niveau automatique est définie sur true pour l’extension Network Watcher, redémarrez l’installation de votre machine virtuelle sur la dernière extension.

## <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à un moment quelconque de cet article, consultez la documentation de l’extension Network Watcher pour [Linux](./network-watcher-linux.md) ou [Windows](./network-watcher-windows.md). Vous pouvez également contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [Site du support Azure](https://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support** . Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
