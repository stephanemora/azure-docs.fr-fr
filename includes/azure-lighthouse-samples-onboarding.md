---
title: Fichier Include
description: Fichier Include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456758"
---
Nous fournissons différents modèles pour répondre à des scénarios d’intégration spécifiques. Choisissez l’option qui vous convient le mieux et veillez à modifier le fichier de paramètres pour refléter votre environnement. Pour plus d’informations sur l’utilisation de ces fichiers dans votre déploiement, consultez [Intégrer un client dans la gestion des ressources déléguées Azure](../articles/lighthouse/how-to/onboard-customer.md).

| **Modèle** | **Description** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Intègre l’abonnement d’un client dans la gestion des ressources déléguées Azure. Un déploiement distinct doit être effectué pour chaque abonnement. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Intègre un ou plusieurs groupes de ressources du client dans la gestion des ressources déléguées Azure. Utilisez **rgDelegatedResourceManagement** pour un groupe de ressources, ou **multipleRgDelegatedResourceManagement** pour intégrer plusieurs groupes de ressources dans le même abonnement. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Si vous avez [publié une offre de services managés sur la Place de marché Azure ](../articles/lighthouse/how-to/publish-managed-services-offers.md), vous pouvez éventuellement utiliser ce modèle afin d’intégrer les ressources pour les clients qui ont accepté l’offre. Les valeurs de la place de marché dans le fichier de paramètres doivent correspondre aux valeurs que vous avez utilisées quand vous avez publié votre offre. |

En règle générale, un déploiement distinct est nécessaire pour chaque abonnement en cours d’intégration, mais vous pouvez également déployer des modèles sur plusieurs abonnements.

| **Modèle** | **Description** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Déploie des modèles Azure Resource Manager dans plusieurs abonnements. |
