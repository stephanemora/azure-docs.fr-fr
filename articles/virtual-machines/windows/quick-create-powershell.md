---
title: 'Démarrage rapide : créer une machine virtuelle Windows avec Azure PowerShell | Microsoft Docs'
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser Azure PowerShell pour créer une machine virtuelle Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3e797b801395bf4971bfb91a8ce4b35a710ea578
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816191"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>Démarrage rapide : créer une machine virtuelle Windows dans Azure avec PowerShell

Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande PowerShell ou dans des scripts. Ce guide de démarrage rapide explique comment utiliser le module Azure PowerShell pour déployer dans Azure une machine virtuelle qui fonctionne avec Windows Server 2016. Pour voir votre machine virtuelle en action, vous établirez une connexion RDP à la machine virtuelle et installerez le serveur web IIS.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.7.0 ou version ultérieure pour les besoins de ce tutoriel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Créez une machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Indiquez les noms de chacune des ressources. La cmdlet [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) les créera si elles n’existent pas déjà.

Lorsque vous y êtes invité, fournissez un nom d’utilisateur et un mot de passe qui serviront d’informations d’identification pour la connexion à la machine virtuelle :

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Une fois le déploiement terminé, établissez une connexion RDP vers la machine virtuelle. Pour que vous puissiez voir votre machine virtuelle en action, le serveur web IIS est installé.

Pour obtenir l’adresse IP publique de la machine virtuelle, utilisez la cmdlet [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) :

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Utilisez la commande suivante pour créer une session Bureau à distance à partir de votre ordinateur local. Remplacez l’adresse IP par l’adresse IP publique de votre machine virtuelle. 

```powershell
mstsc /v:publicIpAddress
```

Dans la fenêtre **Sécurité Windows**, sélectionnez **Plus de choix**, puis **Utiliser un autre compte**. Tapez le nom d’utilisateur sous la forme **localhost**\\*username*, entrez le mot de passe créé pour la machine virtuelle, puis cliquez sur **OK**.

Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur **Oui** ou **Continuer** pour créer la connexion

## <a name="install-web-server"></a>Installer le serveur web

Pour voir votre machine virtuelle en action, installez le serveur web IIS. Ouvrez une invite PowerShell sur la machine virtuelle et exécutez la commande suivante :

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Une fois terminé, fermez la connexion RDP à la machine virtuelle.

## <a name="view-the-web-server-in-action"></a>Voir le serveur web en action

Une fois IIS installé et le port 80 ouvert d’Internet à votre machine virtuelle, utilisez le navigateur web de votre choix pour afficher la page d’accueil IIS par défaut. Utilisez l’adresse IP publique de votre machine virtuelle que vous avez obtenue précédemment. L’exemple suivant montre le site web IIS par défaut :

![Site IIS par défaut](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Avec ce guide de démarrage rapide, vous avez déployé une machine virtuelle simple, ouvert un port réseau pour le trafic web et installé un serveur web de base. Pour en savoir plus sur les machines virtuelles Azure, suivez le didacticiel pour les machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Windows](./tutorial-manage-vm.md)
