---
title: Inscrire le fournisseur de ressources Azure VMware Solution
description: Procédure d’inscription du fournisseur de ressources Azure VMware Solution.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: d2363ca2672f7f668d8f9b3816447f316d8b7347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555946"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Pour utiliser Azure VMware Solution, vous devez d’abord inscrire le fournisseur de ressources pour votre abonnement. Pour plus d’informations sur les fournisseurs de ressources, consultez [Fournisseurs et types de ressources Azure](../../azure-resource-manager/management/resource-providers-and-types.md).


### <a name="portal"></a>[Portail](#tab/azure-portal)
 
1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le menu du portail Azure, sélectionnez **Tous les services**.

1. Dans la zone **Tous les services**, entrez **Abonnement**, puis sélectionnez **Abonnements**.

1. Sélectionnez l'abonnement dans la liste.

1. Sélectionnez **Fournisseurs de ressources**, puis entrez **Microsoft.AVS** dans la zone de recherche. 
 
1. Si le fournisseur de ressources n’est pas inscrit, sélectionnez **Inscrire**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour commencer à utiliser Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Connectez-vous à l’abonnement Azure que vous utilisez pour le déploiement d’Azure VMware Solution par le biais d’Azure CLI. Inscrivez le fournisseur de ressources `Microsoft.AVS` à l’aide de la commande [az provider register](/cli/azure/provider#az_provider_register) :

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Vous pouvez utiliser la commande [az provider list](/cli/azure/provider#az_provider_list) pour afficher tous les fournisseurs disponibles.

---


 
