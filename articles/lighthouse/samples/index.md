---
title: Exemples et modèles Azure Lighthouse
description: Ces exemples et modèles Azure Resource Manager vous montrent comment intégrer des clients pour la gestion des ressources déléguée Azure et prendre en charge les scénarios Azure Lighthouse.
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 07/11/2019
ms.author: jenhayes
ms.openlocfilehash: d888fb0d50bf0336f8fe830c567586e66065871f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286141"
---
# <a name="azure-lighthouse-samples"></a>Exemples Azure Lighthouse

Le tableau suivant comprend des liens vers les principaux modèles Azure Resource Manager pour Azure Lighthouse. Vous trouverez ces fichiers et bien d’autres dans le [dépôt d’exemples Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).

| **Modèle** | **Description** |
|---------|---------|
| [create-multiple-rgs](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/create-multiple-rgs) | Crée plusieurs groupes de ressources à l’aide d’un seul modèle Azure Resource Manager. |
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Déploie des comptes de stockage dans deux différents groupes de ressources. |
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Déploie des modèles Azure Resource Manager dans plusieurs abonnements. |
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Intègre l’abonnement d’un client dans la gestion des ressources déléguées Azure. |
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Crée des services de gestion Azure, les relie et déploie des solutions supplémentaires. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Active et configure Azure Security Center dans l’abonnement Azure ciblé. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Intègre l’abonnement du client dans la gestion des ressources déléguées Azure, en se basant sur une offre de services managés publiée sur la Place de marché. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Intègre un ou plusieurs groupes de ressources du client dans la gestion des ressources déléguées Azure. |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [Gestion des ressources déléguées Azure](../concepts/azure-delegated-resource-management.md).
- Découvrez comment utiliser les modèles Azure Resource Manager pour [intégrer les clients dans la gestion des ressources déléguées Azure](../how-to/onboard-customer.md).
