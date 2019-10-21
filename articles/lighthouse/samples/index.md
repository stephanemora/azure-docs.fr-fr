---
title: Exemples et modèles Azure Lighthouse
description: Ces exemples et modèles Azure Resource Manager vous montrent comment intégrer des clients pour la gestion des ressources déléguée Azure et prendre en charge les scénarios Azure Lighthouse.
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 10/17/2019
ms.author: jenhayes
ms.openlocfilehash: 6d47534026b6fe815f9756a74ba3438dc67a8e02
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553688"
---
# <a name="azure-lighthouse-samples"></a>Exemples Azure Lighthouse

Le tableau suivant comprend des liens vers les principaux modèles Azure Resource Manager pour Azure Lighthouse. Vous trouverez ces fichiers et bien d’autres dans le [dépôt d’exemples Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Intégration de clients dans la gestion des ressources déléguées Azure

Nous fournissons différents modèles pour répondre à des scénarios d’intégration spécifiques. Choisissez l’option qui vous convient le mieux et veillez à modifier le fichier de paramètres pour refléter votre environnement. Pour plus d’informations sur l’utilisation de ces fichiers dans votre déploiement, consultez [Intégrer un client dans la gestion des ressources déléguées Azure](../how-to/onboard-customer.md).

| **Modèle** | **Description** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Intègre l’abonnement d’un client dans la gestion des ressources déléguées Azure. Un déploiement distinct doit être effectué pour chaque abonnement. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Intègre un ou plusieurs groupes de ressources du client dans la gestion des ressources déléguées Azure. Utilisez **rgDelegatedResourceManagement** pour un groupe de ressources, ou **multipleRgDelegatedResourceManagement** pour intégrer plusieurs groupes de ressources dans le même abonnement. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Si vous avez [publié une offre de services managés sur la Place de marché Azure ](../how-to/publish-managed-services-offers.md), vous pouvez éventuellement utiliser ce modèle afin d’intégrer les ressources pour les clients qui ont accepté l’offre. Les valeurs de la place de marché dans le fichier de paramètres doivent correspondre aux valeurs que vous avez utilisées quand vous avez publié votre offre. |

En règle générale, un déploiement distinct est nécessaire pour chaque abonnement en cours d’intégration, mais vous pouvez également déployer des modèles sur plusieurs abonnements.

| **Modèle** | **Description** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Déploie des modèles Azure Resource Manager dans plusieurs abonnements. |

## <a name="azure-policy"></a>Azure Policy

Ces exemples montrent comment utiliser Azure Policy avec des abonnements qui ont été intégrés pour la gestion des ressources déléguées Azure.

| **Modèle** | **Description** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Attribue une stratégie qui ajoute ou supprime une étiquette (avec l’effet de modification) dans un abonnement délégué. Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Attribue une stratégie qui effectue un audit pour les attributions de délégation. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Attribue une stratégie qui active les diagnostics sur les ressources Azure Key Vault dans un abonnement délégué (avec l’effet deployIfNotExists). Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Attribue plusieurs stratégies pour activer les diagnostics sur un abonnement délégué, et connecte toutes les machines virtuelles Windows et Linux à l’espace de travail Log Analytics créé par la stratégie. Pour plus d’informations, consultez [Déployer une stratégie pouvant être corrigée dans un abonnement délégué](../how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Applique une initiative (plusieurs définitions de stratégie associées) à un abonnement délégué. |

## <a name="azure-monitor"></a>Azure Monitor

Ces exemples montrent comment utiliser Azure Monitor pour créer des alertes avec des abonnements qui ont été intégrés pour la gestion des ressources déléguées Azure.

| **Modèle** | **Description** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Ce modèle crée une alerte Azure et se connecte à un groupe d’actions existant.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Crée plusieurs alertes de journal basées sur des requêtes Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Déploie une alerte dans un locataire lorsqu’un utilisateur délègue un abonnement à un locataire gérant.|

## <a name="additional-cross-tenant-scenarios"></a>Autres scénarios interlocataires

Ces exemples illustrent différentes tâches qui peuvent être effectuées dans des scénarios de gestion interlocataires.

| **Modèle** | **Description** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Déploie des comptes de stockage dans deux différents groupes de ressources.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Crée des services de gestion Azure, les relie et déploie des solutions supplémentaires. Pour un déploiement de bout en bout, utilisez le modèle **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Active et configure Azure Security Center dans l’abonnement Azure ciblé. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Déploie et active Azure Sentinel sur un espace de travail Log Analytics existant dans un abonnement délégué. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Ces modèles vous permettent de déployer des extensions de machine virtuelle Log Analytics sur vos machines virtuelles Windows et Linux, en les connectant à l’espace de travail Log Analytics désigné. |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [Gestion des ressources déléguées Azure](../concepts/azure-delegated-resource-management.md).
- Explorez le [Dépôt d’exemples Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).
