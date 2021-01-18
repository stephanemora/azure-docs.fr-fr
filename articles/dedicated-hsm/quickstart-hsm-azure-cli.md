---
title: 'Démarrage rapide : Créer un HSM dédié Azure avec Azure CLI'
description: Créer, afficher, lister, mettre à jour et supprimer des HSM dédiés Azure avec Azure CLI.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e07bc758b1ef86b3d8c605cbce72f6db564a355f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020854"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Démarrage rapide : Créer un HSM dédié Azure avec Azure CLI

Cet article explique comment créer et gérer un HSM dédié Azure à l’aide de l’extension Azure CLI [az dedicated-hsm](/cli/azure/ext/hardware-security-modules/dedicated-hsm).

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas.
  
  Si vous avez plusieurs abonnements Azure, définissez l’abonnement à utiliser pour la facturation à l’aide de la commande Azure CLI [az account set](/cli/azure/account#az_account_set).
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- Toutes les conditions relatives à un HSM dédié doivent être respectées : inscription, approbation, réseau virtuel et machine virtuelle à utiliser pour le provisionnement, etc. Pour plus d’informations sur les conditions et les prérequis liés aux HSM dédiés, consultez [Tutoriel : Déploiement de modules HSM sur un réseau virtuel existant à l’aide d’Azure CLI](tutorial-deploy-hsm-cli.md).
  

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un [groupe de ressources Azure](../azure-resource-manager/management/overview.md) est un conteneur logique qui permet de déployer et de gérer des ressources Azure sous forme d’un groupe. Si vous ne disposez pas d’un groupe de ressources pour le HSM dédié, créez-en un à l’aide de la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé `myRG` dans la région Azure `westus` :

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Créer un HSM dédié

Pour créer un HSM dédié, utilisez la commande [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create). L’exemple suivant provisionne un HSM dédié nommé `hsm1` dans la région `westus`, le groupe de ressources `myRG` ainsi que l’abonnement, le réseau virtuel et le sous-réseau spécifiés. Les paramètres obligatoires sont `name`, `location` et `resource group`.

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

Le déploiement prend environ 25 à 30 minutes.

## <a name="get-a-dedicated-hsm"></a>Obtenir un HSM dédié

Pour obtenir un HSM dédié actuel, exécutez la commande [az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show). L’exemple suivant obtient le HSM dédié `hsm1` dans le groupe de ressources `myRG`.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Mettre à jour un HSM dédié

Utilisez la commande [az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) pour mettre à jour un HSM dédié. L’exemple suivant met à jour le HSM dédié `hsm1` dans le groupe de ressources `myRG` et ses étiquettes :

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Lister des HSM dédiés

Exécutez la commande [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) pour obtenir des informations sur les HSM dédiés actuels. L’exemple suivant liste les HSM dédiés dans le groupe de ressources `myRG` :

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Supprimer un HSM dédié

Pour supprimer un HSM dédié, utilisez la commande [az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete). L’exemple suivant supprime le HSM dédié `hsm1` du groupe de ressources `myRG` :

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Supprimer le groupe de ressources

Si vous n’avez plus besoin du groupe de ressources que vous avez créé pour le HSM dédié, vous pouvez le supprimer en exécutant la commande [az group delete](/cli/azure/group#az_group_delete). Cette commande supprime le groupe et toutes les ressources qu’il contient, y compris celles qui ne sont pas liées au HSM dédié. L’exemple suivant supprime le groupe de ressources `myRG` et tout ce qui se trouve dans celui-ci :

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez [Azure Dedicated HSM](overview.md).
