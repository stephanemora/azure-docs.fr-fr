---
title: Offre de services dans Azure Stack | Microsoft Docs
description: En tant qu’opérateur cloud, vous pouvez proposer des services à vos utilisateurs.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 754a0002aca52462910abe50825cff0254a7068f
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244152"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Vue d’ensemble de l’offre de services dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

[Microsoft Azure Stack](azure-stack-poc.md) est une plateforme cloud hybride qui vous permet de fournir des services à partir de votre centre de données. En tant que fournisseur de services, vous pouvez proposer des services à vos locataires. Au sein d’une entreprise ou d’une agence gouvernementale, vous pouvez proposer des services locaux à vos employés. 

Vous pouvez proposer des services [Infrastructure as a service](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS) qui autorisent vos utilisateurs à créer une infrastructure informatique à la demande, laquelle est configurée et gérée à partir du portail d’utilisateur Azure Stack.

Vous pouvez également déployer des services [Platform as a Service](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) proposés pour Azure Stack par Microsoft ou d’autres fournisseurs tiers. Vous pouvez proposer entre autres les services suivants :

- [Ajouter un fournisseur de ressources App Service à Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-overview)

- [Ajouter un fournisseur de ressources SQL Server à Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy)

- [Ajouter un fournisseur de ressources MySQL Server à Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy)


Vous pouvez même combiner des services pour intégrer et créer des solutions complexes pour différents utilisateurs.

Pour fournir ces services à vos utilisateurs, vous devez créer [des plans, des offres et des quotas](azure-stack-plan-offer-quota-overview.md). Vos utilisateurs peuvent ensuite s’abonner à vos offres pour utiliser les services.

## <a name="plan-your-service-offers"></a>Planifier vos offres de service

Quand vous planifiez vos offres, gardez à l’esprit les points suivants :

**Offres d'évaluation** : vous pouvez utiliser des offres d'évaluation pour attirer de nouveaux utilisateurs, qui peuvent ensuite effectuer une mise à niveau pour obtenir des services supplémentaires. Pour créer une offre d’évaluation, créez un petit [plan de base](azure-stack-plan-offer-quota-overview.md#base-plan) avec un plan complémentaire facultatif plus important.

**Planification de la capacité** : peut-être craignez-vous que des utilisateurs s'accaparent de grandes quantités de ressources et encombrent le système pour tous les autres utilisateurs. Pour améliorer les performances, vous pouvez [configurer vos plans avec des quotas](azure-stack-plan-offer-quota-overview.md#plans) afin de limiter l’utilisation.

**Fournisseurs délégués** : vous pouvez donner à d'autres personnes la possibilité de créer des offres dans votre environnement. Par exemple, si vous êtes fournisseur de services, vous pouvez [déléguer](azure-stack-delegated-provider.md) cette capacité à vos revendeurs. Si vous êtes une organisation, vous pouvez déléguer à d’autres divisions/filiales.

## <a name="next-steps"></a>Étapes suivantes

[Créer une offre dans Azure Stack](azure-stack-create-offer.md)
