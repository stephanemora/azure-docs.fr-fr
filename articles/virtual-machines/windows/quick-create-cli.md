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
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b09a85686e19ae92c3e437bedff54bff8371784f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli-20"></a>Démarrage rapide : créer une machine virtuelle Windows avec Azure CLI 2.0

Vous utilisez Azure CLI 2.0 pour créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide de démarrage rapide explique comment utiliser Azure CLI 2.0 pour déployer dans Azure une machine virtuelle qui fonctionne avec Windows Server 2016. Pour voir votre machine virtuelle en action, vous établirez une connexion RDP à la machine virtuelle et installerez le serveur web IIS.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce guide de démarrage rapide nécessite que vous exécutiez la version 2.0.30 d’Azure CLI, ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant permet de créer une machine virtuelle nommée *myVM*. Cet exemple utilise le nom d’utilisateur administratif *azureuser* et le mot de passe *myPassword12*. Mettez à jour ces valeurs avec quelque chose d’approprié pour votre environnement. Ces valeurs sont nécessaires lorsque vous vous connectez à la machine virtuelle.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

La création de la machine virtuelle et des ressources de support ne nécessite que quelques minutes. L’exemple de sortie suivant illustre la réussite de l’opération de création d’une machine virtuelle.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Notez votre propre `publicIpAddress` dans la sortie à partir de votre machine virtuelle. Cette adresse permet d’accéder à la machine virtuelle lors des étapes suivantes.

## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web

Par défaut, seules les connexions RDP sont ouvertes lorsque vous créez une machine virtuelle Windows dans Azure. Utilisez la commande [az vm open-port](/cli/azure/vm#az_vm_open_port) pour ouvrir le port TCP 80 afin de l’utiliser avec le serveur web IIS :

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Utilisez la commande suivante pour créer une session Bureau à distance à partir de votre ordinateur local. Remplacez l’adresse IP par l’adresse IP publique de votre machine virtuelle. À l’invite, saisissez les informations d’identification que vous avez utilisées lors de la création de la machine virtuelle :

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Installer le serveur web

Pour voir votre machine virtuelle en action, installez le serveur web IIS. Ouvrez une invite PowerShell sur la machine virtuelle et exécutez la commande suivante :

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ensuite, fermez la connexion RDP à la machine virtuelle.

## <a name="view-the-web-server-in-action"></a>Voir le serveur web en action

Une fois IIS installé et le port 80 ouvert d’Internet à votre machine virtuelle, utilisez le navigateur web de votre choix pour afficher la page d’accueil IIS par défaut. Utilisez l’adresse IP publique de votre machine virtuelle que vous avez obtenue précédemment. L’exemple suivant montre le site web IIS par défaut :

![Site IIS par défaut](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Avec ce guide de démarrage rapide, vous avez déployé une machine virtuelle simple, ouvert un port réseau pour le trafic web et installé un serveur web de base. Pour en savoir plus sur les machines virtuelles Azure, suivez le didacticiel pour les machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Windows](./tutorial-manage-vm.md)
