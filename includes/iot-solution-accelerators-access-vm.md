---
title: Fichier Include
description: Fichier Include
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a58e408feadd10e6dbc9d6878b82a4d045918ea6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68781449"
---
## <a name="access-the-virtual-machine"></a>Accéder à la machine virtuelle

Les étapes suivantes utilisent Azure CLI dans Azure Cloud Shell. Si vous préférez, vous pouvez [installer Azure CLI](/cli/azure/install-azure-cli) sur votre machine de développement et exécuter les commandes localement.

Les étapes suivantes vous permettent de configurer la machine virtuelle Azure afin d’autoriser l’accès **SSH**. Les étapes présentées supposent que le nom de l’accélérateur de solution est **contoso-simulation**. Remplacez cette valeur par le nom de votre déploiement :

1. Répertoriez le contenu du groupe de ressources qui contient les ressources de l’accélérateur de solution :

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Notez le nom de la machine virtuelle, l’adresse IP publique et le nom du groupe de sécurité réseau, car vous aurez besoin de ces informations plus tard.

1. Mettez à jour le groupe de sécurité réseau afin d’autoriser l’accès SSH. La commande suivante suppose que le nom du groupe de sécurité réseau est **contoso-simulation-nsg**. Remplacez cette valeur par le nom de votre groupe de sécurité réseau :

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Activez uniquement l’accès SSH durant les phases de développement et de test. Si vous activez SSH, [vous devez le désactiver à nouveau dès que possible](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Changez le mot de passe du compte **azureuser** sur la machine virtuelle par un mot de passe de votre choix. Spécifiez votre propre mot de passe quand vous exécutez la commande suivante :

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Recherchez l’adresse IP publique de votre machine virtuelle. La commande suivante suppose que le nom de la machine virtuelle est **vm-vikxv**. Remplacez cette valeur par le nom de machine virtuelle que vous avez précédemment noté :

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Notez l’adresse IP publique de votre machine virtuelle.
