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
ms.openlocfilehash: 6b873508fe29ed0816a8b64b26cc5522ed23a8d6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986640"
---
Nous fournissons différents modèles pour répondre à des scénarios d’intégration spécifiques. Choisissez l’option qui vous convient le mieux et veillez à modifier le fichier de paramètres pour refléter votre environnement. Pour plus d’informations sur l’utilisation de ces fichiers dans votre déploiement, consultez [Intégrer un client dans la gestion des ressources déléguées Azure](../articles/lighthouse/how-to/onboard-customer.md).

| **Modèle** | **Description** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Intègre l’abonnement d’un client dans la gestion des ressources déléguées Azure. Un déploiement distinct doit être effectué pour chaque abonnement. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Intègre un ou plusieurs groupes de ressources du client dans la gestion des ressources déléguées Azure. Utilisez **rgDelegatedResourceManagement** pour un groupe de ressources, ou **multipleRgDelegatedResourceManagement** pour intégrer plusieurs groupes de ressources dans le même abonnement. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Si vous avez [publié une offre de services managés sur la Place de marché Azure ](../articles/lighthouse/how-to/publish-managed-services-offers.md), vous pouvez éventuellement utiliser ce modèle afin d’intégrer les ressources pour les clients qui ont accepté l’offre. Les valeurs de la place de marché dans le fichier de paramètres doivent correspondre aux valeurs que vous avez utilisées quand vous avez publié votre offre. |

En règle générale, un déploiement distinct est nécessaire pour chaque abonnement en cours d’intégration, mais vous pouvez également déployer des modèles sur plusieurs abonnements.

| **Modèle** | **Description** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Déploie des modèles Azure Resource Manager dans plusieurs abonnements. |
