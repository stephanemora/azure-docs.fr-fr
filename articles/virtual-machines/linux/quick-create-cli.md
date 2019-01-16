---
title: Démarrage rapide  - Créer une machine virtuelle Linux avec Azure CLI | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser Azure CLI pour créer une machine virtuelle Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: af7f3b42aad41f103be6c86da84db2ff230ff226
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065218"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Démarrage rapide : Créer une machine virtuelle Linux avec Azure CLI

L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce démarrage rapide explique comment utiliser Azure CLI pour déployer dans Azure une machine virtuelle Linux. Dans ce tutoriel, nous allons installer Ubuntu 16.04 LTS. Pour voir la machine virtuelle en action, vous allez vous y connecter à l’aide de SSH et installer le serveur web NGINX.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/bash](https://shell.azure.com/bash). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

Si vous préférez installer et utiliser l’interface de ligne de commande en local, ce démarrage rapide nécessite au minimum la version 2.0.30 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create).

L’exemple suivant crée une machine virtuelle nommée *myVM* et ajoute un compte d’utilisateur nommé *azureuser*. Le paramètre `--generate-ssh-keys` permet de générer automatiquement une clé SSH et de la placer dans l’emplacement de clé par défaut (*~/.ssh*). Pour utiliser un ensemble spécifique de clés à la place, utilisez l’option `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La création de la machine virtuelle et des ressources de support ne nécessite que quelques minutes. L’exemple de sortie suivant illustre la réussite de l’opération de création d’une machine virtuelle.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Notez votre propre `publicIpAddress` dans la sortie à partir de votre machine virtuelle. Cette adresse permet d’accéder à la machine virtuelle lors des étapes suivantes.

## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web

Par défaut, seules les connexions SSH sont ouvertes lorsque vous créez une machine virtuelle Linux dans Azure. Utilisez la commande [az vm open-port](/cli/azure/vm#az_vm_open_port) pour ouvrir le port TCP 80 afin de l’utiliser avec le serveur web NGINX :

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Établissez une connexion SSH à votre machine virtuelle comme d’habitude. Remplacez **publicIpAddress** par l’adresse IP publique de votre machine virtuelle, comme indiqué dans la sortie précédente de votre machine virtuelle :

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Installer le serveur web

Pour voir votre machine virtuelle en action, installez le serveur web NGINX. Mettez à jour vos sources de package, puis installez le dernier package NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Lorsque vous avez terminé, tapez `exit` pour quitter la session SSH.

## <a name="view-the-web-server-in-action"></a>Voir le serveur web en action

Utilisez le navigateur web de votre choix pour visualiser la page d’accueil NGINX par défaut. Utilisez l’adresse IP publique de votre machine virtuelle comme adresse web. L’exemple suivant montre le site web NGINX par défaut :

![Site par défaut NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Avec ce guide de démarrage rapide, vous avez déployé une machine virtuelle simple, ouvert un port réseau pour le trafic web et installé un serveur web de base. Pour en savoir plus sur les machines virtuelles Azure, suivez le didacticiel pour les machines virtuelles Linux.


> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Linux](./tutorial-manage-vm.md)
