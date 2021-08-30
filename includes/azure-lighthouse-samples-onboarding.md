---
title: Fichier Include
description: Fichier include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 08/26/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 3fec700c30f33d72d632c805bf2874d7d1d9dd02
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035727"
---
Nous fournissons différents modèles pour répondre à des scénarios d’intégration spécifiques. Choisissez l’option qui vous convient le mieux et veillez à modifier le fichier de paramètres pour refléter votre environnement. Pour plus d’informations sur l’utilisation de ces fichiers dans votre déploiement, consultez [Intégrer un client à Azure Lighthouse](../articles/lighthouse/how-to/onboard-customer.md).

| **Modèle** | **Description** |
|---------|---------|
| [subscription](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/subscription) | Intégrez l’abonnement d’un client à Azure Lighthouse. Un déploiement distinct doit être effectué pour chaque abonnement. |
| [rg et multi-rg](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/rg) | Intègre un ou plusieurs groupes de ressources d’un client à Azure Lighthouse. Utilisez rg.json pour intégrer un seul groupe de ressources ou multi-rg.json pour intégrer plusieurs groupes de ressources au sein d’un abonnement. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Si vous avez [publié une offre de services managés sur la Place de marché Azure ](../articles/lighthouse/how-to/publish-managed-services-offers.md), vous pouvez éventuellement utiliser ce modèle afin d’intégrer les ressources pour les clients qui ont accepté l’offre. Les valeurs de la place de marché dans le fichier de paramètres doivent correspondre aux valeurs que vous avez utilisées quand vous avez publié votre offre. |

Pour inclure des [autorisations éligibles](../articles/lighthouse/how-to/create-eligible-authorizations.md) (actuellement en préversion publique), sélectionnez le modèle correspondant dans la section [delegated-resource-management-eligible-authorizations](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations) de notre dépôt d’exemples.

En règle générale, un déploiement distinct est nécessaire pour chaque abonnement en cours d’intégration, mais vous pouvez également déployer des modèles sur plusieurs abonnements.

| **Modèle** | **Description** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Déploie des modèles Azure Resource Manager dans plusieurs abonnements. |


