---
title: Créer une machine virtuelle Linux dans Azure à partir d’un modèle | Documents Microsoft
description: Comment utiliser l’interface de ligne de commande Azure pour créer une machine virtuelle Linux à partir d’un modèle Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 334f69390e4506c6db76c1814f8ec8f1e4417ee9
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372333"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Comment créer une machine virtuelle Linux avec des modèles Azure Resource Manager

Découvrez comment créer une machine virtuelle de Linux (VM) à l’aide d’un modèle Azure Resource Manager et l’interface CLI à partir d’Azure Cloud shell. Pour créer une machine virtuelle de Windows, consultez [créer une machine virtuelle de Windows à partir d’un modèle Resource Manager](../windows/ps-template.md).

## <a name="templates-overview"></a>Vue d’ensemble des modèles

Les modèles Azure Resource Manager sont des fichiers JSON qui définissent l’infrastructure et la configuration de votre solution Azure. Un modèle vous permet de déployer votre solution à plusieurs reprises tout au long de son cycle de vie pour avoir la garantie que vos ressources présentent un état cohérent lors de leur déploiement. Pour en savoir plus sur le format du modèle et la manière de le construire, consultez [Guide de démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md). Pour afficher la syntaxe JSON pour les types de ressources, voir [Définir des ressources dans les modèles Azure Resource Manager](/azure/templates/microsoft.compute/allversions).

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Création d’une machine virtuelle Azure généralement comprend deux étapes :

1. Créez un groupe de ressources. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Un groupe de ressources doit être créé avant les machines virtuelles.
1. Création d’une machine virtuelle

L’exemple suivant crée une machine virtuelle à partir d’un [de modèle Azure Quickstart](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Seule l’authentification SSH est autorisée pour ce déploiement. À l’invite, renseignez la valeur de votre propre clé publique SSH, telle que le contenu de *~/.ssh/id_rsa.pub*. Si vous devez créer une paire de clés SSH, voir [Comment créer et utiliser une paire de clés SSH pour des machines virtuelles Linux dans Azure](mac-create-ssh-keys.md). Voici une copie du modèle :

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Pour exécuter le script CLI, sélectionnez **essayez-le** pour ouvrir Azure Cloud shell. Pour coller le script, avec le bouton droit de l’interpréteur de commandes, puis sélectionnez **collez**:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey='$key' &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

La dernière commande d’Azure CLI affiche l’adresse IP publique de la machine virtuelle qui vient d’être créée. Vous avez besoin de l’adresse IP publique pour vous connecter à la machine virtuelle. Consultez la section suivante de cet article.

Dans l’exemple précédent, vous avez spécifié un modèle stocké dans GitHub. Vous pouvez également télécharger ou créer un modèle, et spécifier le chemin d’accès local avec le paramètre `--template-file`.

Voici quelques ressources supplémentaires :

- Pour savoir comment développer des modèles Resource Manager, consultez [documentation Azure Resource Manager](/azure/azure-resource-manager/).
- Pour connaître les schémas de la machine virtuelle Azure, consultez [référence de modèle Azure](/azure/templates/microsoft.compute/allversions).
- Pour voir des exemples de modèles de machine virtuelle plus, consultez [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Vous pouvez ensuite établir une connexion SSH à votre machine virtuelle comme d’habitude. Indiquez votre propre adresse IP publique à partir de la commande précédente :

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet exemple, vous avez créé une machine virtuelle Linux de base. Pour d’autres modèles Resource Manager qui incluent des infrastructures d’application ou de créer des environnements plus complexes, parcourir le [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Pour en savoir plus sur la création de modèles, consultez la syntaxe et les propriétés JSON des types de ressource que vous avez déployés :

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
