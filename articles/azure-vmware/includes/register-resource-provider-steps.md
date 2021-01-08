---
title: Inscrire le fournisseur de ressources Azure VMware Solution
description: Procédure d’inscription du fournisseur de ressources Azure VMware Solution.
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770817"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Pour utiliser Azure VMware Solution, vous devez d’abord inscrire le fournisseur de ressources pour votre abonnement.  

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