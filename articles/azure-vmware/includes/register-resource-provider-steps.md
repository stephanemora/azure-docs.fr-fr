---
title: Inscrire le fournisseur de ressources Azure VMware Solution
description: Procédure d’inscription du fournisseur de ressources Azure VMware Solution.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: cd4a6f3003945973f0fe5367eb198823595a412e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750299"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Pour utiliser Azure VMware Solution, vous devez d’abord inscrire le fournisseur de ressources pour votre abonnement. Pour plus d’informations sur les fournisseurs de ressources, consultez [Fournisseurs et types de ressources Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Portail Azure
 
1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le menu du portail Azure, sélectionnez **Tous les services**.

1. Dans la zone **Tous les services**, entrez **Abonnement**, puis sélectionnez **Abonnements**.

1. Sélectionnez l'abonnement dans la liste.

1. Sélectionnez **Fournisseurs de ressources**, puis entrez **Microsoft.AVS** dans la zone de recherche. 
 
1. Si le fournisseur de ressources n’est pas inscrit, sélectionnez **Inscrire**.