---
title: Utilisation de plusieurs locataires par les fournisseurs de services MSSP dans Azure Sentinel | Microsoft Docs
description: Comment les fournisseurs de services MSSP peuvent utiliser plusieurs locataires dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476013"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Utiliser plusieurs locataires dans Azure Sentinel 

Si vous êtes un fournisseur de services de sécurité managés (MSSP, managed security service provider) et que vous utilisez [Azure Lighthouse](../lighthouse/overview.md) pour gérer les centres d’opérations de sécurité de vos clients, vous pouvez gérer les ressources Azure Sentinel de vos clients sans vous connecter directement au locataire du client, à partir de votre propre locataire Azure. 

## <a name="prerequisites"></a>Prérequis
- [Intégrez Azure Lighthouse](../lighthouse/how-to/onboard-customer.md).
- Pour que le processus fonctionne correctement, votre locataire doit être inscrit auprès du fournisseur de ressources Azure Sentinel sur au moins un abonnement. Si votre locataire dispose d’une solution Azure Sentinel inscrite, vous êtes prêt à commencer. Dans le cas contraire, sélectionnez **Abonnements** dans le portail Azure, puis **Fournisseurs de ressources**.  Ensuite, dans l’écran **SOC - Fournisseurs de ressources**, recherchez et sélectionnez `Microsoft.OperationalInsights` et `Microsoft.SecurityInsights`, puis sélectionnez **Inscrire**.
   ![Vérification des fournisseurs de ressources](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Accéder à Azure Sentinel à partir d’autres locataires
1. Sous **Répertoire + abonnement**, sélectionnez les répertoires délégués et les abonnements où se trouvent les espaces de travail Azure Sentinel de votre client.

   ![Générer des incidents de sécurité](media/multiple-tenants-service-providers/directory-subscription.png)

1. Ouvrez Azure Sentinel. Vous voyez tous les espaces de travail des abonnements sélectionnés. Vous pourrez les utiliser en toute transparence comme n’importe quel espace de travail de votre propre locataire.

> [!NOTE]
> Vous ne pourrez pas déployer de connecteurs dans Azure Sentinel à partir d’un espace de travail managé. Pour déployer un connecteur, vous devez vous connecter directement au locataire sur lequel vous souhaitez le déployer et vous authentifier avec les autorisations nécessaires.





## <a name="next-steps"></a>Étapes suivantes
Ce document vous a permis d’apprendre à gérer plusieurs locataires Azure Sentinel en toute transparence. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).

